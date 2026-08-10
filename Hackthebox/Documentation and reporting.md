#cwes #hackthebox 

#### Relevant points of a rapport:
- `Attack Path` - An outline of the entire path if you gain a foothold during an external penetration test or compromise one or more hosts (or the AD domain) during an internal penetration test. Outline the path as closely as possible using screenshots and command output will make it easier to paste into the report later and only need to worry about formatting.
- `Credentials` - A centralized place to keep your compromised credentials and secrets as you go along.
- `Findings` - We recommend creating a subfolder for each finding and then writing our narrative and saving it in the folder along with any evidence (screenshots, command output). It is also worth keeping a section in your notetaking tool for recording findings information to help organize them for the report.
- `Vulnerability Scan Research` - A section to take notes on things you've researched and tried with your vulnerability scans (so you don't end up redoing work you already did).
- `Service Enumeration Research` - A section to take notes on which services you've investigated, failed exploitation attempts, promising vulnerabilities/misconfigurations, etc.
- `Web Application Research` - A section to note down interesting web applications found through various methods, such as subdomain brute-forcing. It's always good to perform thorough subdomain enumeration externally, scan for common web ports on internal assessments, and run a tool such as Aquatone or EyeWitness to screenshot all applications. As you review the screenshot report, note down applications of interest, common/default credential pairs you tried, etc.
- `AD Enumeration Research` - A section for showing, step-by-step, what Active Directory enumeration you've already performed. Note down any areas of interest you need to run down later in the assessment.
- `OSINT` - A section to keep track of interesting information you've collected via OSINT, if applicable to the engagement.
- `Administrative Information` - Some people may find it helpful to have a centralized location to store contact information for other project stakeholders like Project Managers (PMs) or client Points of Contact (POCs), unique objectives/flags defined in the Rules of Engagement (RoE), and other items that you find yourself often referencing throughout the project. It can also be used as a running to-do list. As ideas pop up for testing that you need to perform or want to try but don't have time for, be diligent about writing them down here so you can come back to them later.
- `Scoping Information` - Here, we can store information about in-scope IP addresses/CIDR ranges, web application URLs, and any credentials for web applications, VPN, or AD provided by the client. It could also include anything else pertinent to the scope of the assessment so we don't have to keep re-opening scope information and ensure that we don't stray from the scope of the assessment.
- `Activity Log` - High-level tracking of everything you did during the assessment for possible event correlation.
- `Payload Log` - Similar to the activity log, tracking the payloads you're using (and a file hash for anything uploaded and the upload location) in a client environment is critical. More on this later.


#### Tips and tricks:
- Log scanning and attack attempts and keep raw tool outputs when possible, it can use useful if the client asks later.
- Tmux logging - has logs if every command we ran in the terminal session
- Add arrows and boxes in pictures to show what is important.
- Do not blure out stuff, instead black-bar things that should not be included in a screenshot. You can for example leave the last 3 characters of a password, just to prove you saw it.
- Make sure that we include commands in a way that can be copied 
- Use a terminal view that makes screenshots readable; color-coded highlighting 

#### Things to clean up after the assessment:
- IP address of the host(s)/hostname(s) where the change was made
- Timestamp of the change
- Description of the change
- Location on the host(s) where the change was made
- Name of the application or service that was tampered with
- Name of the account (if you created one) and perhaps the password in case you are required to surrender it

#### Suggested structure of folders
```
├── Admin
├── Deliverables
├── Evidence
│   ├── Findings
│   ├── Logging output
│   ├── Misc Files
│   ├── Notes
│   ├── OSINT
│   ├── Scans
│   │   ├── AD Enumeration
│   │   ├── Service
│   │   ├── Vuln
│   │   └── Web
│   └── Wireless
└── Retest
```

- `Admin`
    - Scope of Work (SoW) that you're working off of, your notes from the project kickoff meeting, status reports, vulnerability notifications, etc
- `Deliverables`
    - Folder for keeping your deliverables as you work through them. This will often be your report but can include other items such as supplemental spreadsheets and slide decks, depending on the specific client requirements.
- `Evidence`
	- Findings, Scans, Notes, etc.
- `Retest`
    - This is an optional folder if you need to return after the original assessment and retest the previously discovered findings. You may want to replicate the folder structure you used during the initial assessment in this directory to keep your retest evidence separate from your original evidence.

Script to create the structure:
```
mkdir -p ACME-IPT/{Admin,Deliverables,Evidence/{Findings,Scans/{Vuln,Service,Web,'AD Enumeration'},Notes,OSINT,Wireless,'Logging output','Misc Files'},Retest}
```


#### Components of a report:

NOTE: everything in the report should have a reason to be there.

**Executive summary**: Top level summary, for non-technical people
Tips:
- Be specific when we talk about metrics 
- It should not be too long
- Give examples of things we managed to access
- Describe general migrations and how much time are needed to improve the vulnerabilities we found. 
- DO NOT use to many acronyms

**Summary of Recommendations:** Short, medium and long-term recommendations based on our findings. 

**Findings:** After the executive summary, findings is most important. More details below.

**Appendices:**
- Scope: urls, network ranges,..
- Methodology: Explain the repeatable process, to show the assessment is thorough and consistent 
- Severity ratings

#### How to write up a finding: 

It should include:
- Description of the finding and what platform(s) the vulnerability affects
- Impact if the finding is left unresolved
- Affected systems, networks, environments, or applications
- Recommendation for how to address the problem
- Reference links with additional information about the finding and resolving it
- Steps to reproduce the issue and the evidence that you collected

Tips and tricks:
- Include more steps and explanations such that inexperienced readers understand how you did the compromise.
- 