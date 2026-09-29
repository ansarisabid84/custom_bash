# Sabid SSH Manager

A lightweight Bash CLI for managing and connecting to multiple SSH servers using simple aliases.

`sabid` stores server connection details in a local CSV file and provides quick SSH/SCP access without repeatedly typing usernames, IP addresses, ports, and passwords.

---

## Features

* Quick SSH connections using aliases
* Partial alias matching
* Interactive alias selection when multiple servers match
* Exact alias matching
* Alphabetically sorted alias suggestions
* SCP file transfers
* Custom SSH username
* Custom SSH port
* SSH debug mode
* Add/remove servers from CLI
* List configured servers
* Remove all servers
* Automatic CSV CRLF cleanup
* Automatic SSH host-key handling for new servers
* Password prompt when password is not stored
* macOS and Ubuntu/Debian compatible
* No server credentials hardcoded inside the script

---

# Installation

## macOS

### Install `sshpass`

Using Homebrew:

```bash
brew install hudochenkov/sshpass/sshpass
```

### Install `sabid`

```bash
sudo curl -o /usr/local/bin/sabid https://raw.githubusercontent.com/sabid-ansari/custom_ssh_bash/main/sabid
```

Make it executable:

```bash
sudo chmod +x /usr/local/bin/sabid
```

Verify:

```bash
sabid --help
```

---

## Ubuntu / Debian

Install required packages:

```bash
sudo apt-get update
sudo apt-get install -y sshpass perl
```

Install `sabid`:

```bash
sudo curl -o /usr/local/bin/sabid https://raw.githubusercontent.com/sabid-ansari/custom_ssh_bash/main/sabid
```

Make it executable:

```bash
sudo chmod +x /usr/local/bin/sabid
```

Verify:

```bash
sabid --help
```

---

# Configuration

`sabid` stores the server configuration here:

```text
~/.ssh/quickssh/servers.csv
```

The directory is automatically created with:

```text
700
```

The CSV file is automatically created with:

```text
600
```

---

# CSV Format

The CSV format is:

```csv
alias_name,server_username,server_ip,password,port
```

Example:

```csv
alias_name,server_username,server_ip,password,port
us,sabid,10.165.197.58,password123,22
uk,sabid,10.165.197.36,password456,22
ind,sabid,10.165.197.50,password789,22
us-mysql,sabid,10.165.197.59,password123,22
us-redis,sabid,10.165.197.60,password123,22
```

You can edit the file directly:

```bash
vi ~/.ssh/quickssh/servers.csv
```

---

# SSH Usage

## Connect using an exact alias

```bash
sabid ssh ind
```

Example:

```text
Connecting...

  Alias    : ind
  User     : sabid
  Host     : 10.165.197.50
  Port     : 22
```

---

# Partial Alias Matching

You don't need to type the complete alias.

For example, if your CSV contains:

```text
ind
ind-mysql
ind-redis
indexer
intl-ts-db
```

You can run:

```bash
sabid ssh i
```

Since multiple aliases match, `sabid` displays:

```text
What do you mean by 'i'?

  1) ind
  2) ind-mysql
  3) ind-redis
  4) indexer
  5) intl-ts-db

Enter number or exact alias:

>
```

You can enter the number:

```text
> 1
```

or the exact alias:

```text
> ind-mysql
```

---

# Single Partial Match

If only one alias matches, no menu is displayed.

For example:

```bash
sabid ssh ind-m
```

If the only matching alias is:

```text
ind-mysql
```

`sabid` automatically connects to it.

---

# Exact Alias Priority

Exact aliases always take priority.

For example, if you have:

```text
us
us-mysql
us-redis
```

Running:

```bash
sabid ssh us
```

connects directly to:

```text
us
```

It does not show the partial-match menu.

---

# Cancel Alias Selection

When the interactive selection menu is displayed, you can cancel by entering:

```text
q
```

or:

```text
quit
```

or:

```text
exit
```

Example:

```text
Enter number or exact alias:

> q

Cancelled.
```

---

# SSH With Custom User

Use:

```bash
sabid ssh ind root
```

or:

```bash
sabid ssh ind --user root
```

This overrides the username configured in the CSV.

---

# SSH With Custom Port

Use:

```bash
sabid ssh ind --port 2222
```

This overrides the port configured in the CSV.

---

# SSH Debug Mode

For troubleshooting SSH connections:

```bash
sabid ssh ind --debug
```

This runs SSH with verbose debugging:

```text
ssh -vvv
```

---

# SCP

`sabid` can also be used for SCP file transfers.

## Copy local file to server

```bash
sabid scp ind /tmp/test.txt /tmp/
```

Equivalent conceptually to:

```bash
scp /tmp/test.txt sabid@10.165.197.50:/tmp/
```

---

## Copy file from server

```bash
sabid scp ind /var/log/application.log /tmp/
```

---

## SCP With Custom User

```bash
sabid scp --user root ind /tmp/test.txt /tmp/
```

---

## SCP With Custom Port

```bash
sabid scp --port 2222 ind /tmp/test.txt /tmp/
```

---

# Managing Servers

## List all servers

```bash
sabid ssh list
```

Example:

```text
ALIAS                USERNAME             HOST                      PORT
-------------------- -------------------- ------------------------- --------
ind                  sabid                10.165.197.50             22
ind-mysql             sabid                10.165.197.51             22
ind-redis             sabid                10.165.197.52             22
uk                   sabid                10.165.197.36             22
us                   sabid                10.165.197.58             22
```

Passwords are not displayed.

---

## List servers matching a prefix

```bash
sabid ssh list u
```

For example:

```text
us
us-mongodb
us-mysql
us-redis
uk
uk-mongodb
uk-mysql
uk-redis
```

---

# Add a Server

## Interactive password

```bash
sabid ssh add ind sabid 10.165.197.50
```

The command asks for the password:

```text
Password for sabid@10.165.197.50:
```

Port defaults to:

```text
22
```

---

## Add Server With Password

```bash
sabid ssh add ind sabid 10.165.197.50 'MyPassword'
```

---

## Add Server With Password and Port

```bash
sabid ssh add ind sabid 10.165.197.50 'MyPassword' 2222
```

---

# Remove a Server

```bash
sabid ssh remove ind
```

You will be asked to confirm:

```text
Remove server 'ind'? [y/N]:
```

---

# Remove All Servers

```bash
sabid ssh remove --all
```

For safety, `sabid` asks you to type:

```text
YES
```

before removing all configured servers.

---

# Clean CSV

If the CSV was copied from Windows or another system and contains Windows CRLF line endings:

```bash
sabid ssh clean
```

`sabid` also automatically performs this cleanup before reading the CSV.

---

# SSH Host Key Handling

`sabid` uses:

```text
StrictHostKeyChecking=accept-new
```

This provides the following behavior.

### New server

If the server has never been connected to before, its SSH host key is automatically accepted and added to:

```text
~/.ssh/known_hosts
```

For example:

```text
Warning: Permanently added '10.165.197.50' (ED25519) to the list of known hosts.
```

No manual:

```text
Are you sure you want to continue connecting?
```

prompt is required.

### Existing server

If the host key is already known, normal SSH host-key verification is performed.

### Changed host key

If a server's host key changes, SSH will reject the connection rather than silently accepting the new key.

This is intentionally different from:

```text
StrictHostKeyChecking=no
```

because `sabid` should not blindly trust changed host keys.

---

# Password Handling

Passwords can be stored in:

```text
~/.ssh/quickssh/servers.csv
```

Example:

```csv
ind,sabid,10.165.197.50,MyPassword,22
```

If the password field is empty:

```csv
ind,sabid,10.165.197.50,,22
```

`sabid` prompts for the password when connecting.

Example:

```text
Password required for sabid@10.165.197.50
Password:
```

The password is not displayed while typing.

---

# Security

The CSV contains passwords in plaintext.

Protect the configuration:

```bash
chmod 700 ~/.ssh/quickssh
chmod 600 ~/.ssh/quickssh/servers.csv
```

`sabid` automatically applies these permissions.

## Important

Do not commit:

```text
servers.csv
```

to Git.

Add this to `.gitignore` if the directory is ever stored in a repository:

```gitignore
servers.csv
```

You should also avoid sharing the CSV publicly because it contains server credentials.

---

# Supported Password Characters

The simple CSV parser supports common password characters such as:

```text
@
$
!
#
%
&
*
spaces
```

However, passwords containing commas are **not supported** by the current simple CSV format.

For example, avoid:

```text
MyPassword,123
```

because the comma is interpreted as a CSV separator.

---

# File Permissions

`sabid` uses:

```text
~/.ssh/quickssh/
```

with:

```text
drwx------ 
```

and:

```text
servers.csv
```

with:

```text
-rw-------
```

Equivalent permissions:

```bash
chmod 700 ~/.ssh/quickssh
chmod 600 ~/.ssh/quickssh/servers.csv
```

---

# Command Reference

| Command                           | Description                 |
| --------------------------------- | --------------------------- |
| `sabid ssh <alias>`               | Connect using alias         |
| `sabid ssh <partial>`             | Connect using partial alias |
| `sabid ssh <alias> <user>`        | Connect using custom user   |
| `sabid ssh <alias> --user <user>` | Connect using custom user   |
| `sabid ssh <alias> --port <port>` | Connect using custom port   |
| `sabid ssh <alias> --debug`       | SSH debug mode              |
| `sabid ssh list`                  | List all servers            |
| `sabid ssh list <prefix>`         | List matching servers       |
| `sabid ssh add ...`               | Add server                  |
| `sabid ssh remove <alias>`        | Remove server               |
| `sabid ssh remove --all`          | Remove all servers          |
| `sabid ssh clean`                 | Clean CSV                   |
| `sabid scp ...`                   | Copy files using SCP        |
| `sabid --help`                    | Show help                   |

---

# Quick Examples

Connect:

```bash
sabid ssh ind
```

Partial alias:

```bash
sabid ssh i
```

Custom user:

```bash
sabid ssh ind root
```

Custom port:

```bash
sabid ssh ind --port 2222
```

Debug:

```bash
sabid ssh ind --debug
```

List:

```bash
sabid ssh list
```

Add:

```bash
sabid ssh add new-server sabid 10.165.197.100
```

Remove:

```bash
sabid ssh remove new-server
```

SCP:

```bash
sabid scp ind ./backup.tar.gz /tmp/
```

---

# Configuration Location

All server configuration is stored locally:

```text
~/.ssh/quickssh/servers.csv
```

The executable is:

```text
/usr/local/bin/sabid
```

SSH's known host keys remain managed by OpenSSH in:

```text
~/.ssh/known_hosts
```

---

# Requirements

### macOS

* Bash
* OpenSSH
* `sshpass`
* `perl`

Install:

```bash
brew install hudochenkov/sshpass/sshpass
```

### Ubuntu/Debian

* Bash
* OpenSSH
* `sshpass`
* `perl`

Install:

```bash
sudo apt-get update
sudo apt-get install -y sshpass perl
```

---

# License

Use and modify this script according to your organization's security policies and requirements.

```

One thing I deliberately **didn't include** is the actual passwords from your `servers.csv`. Keep the README safe to commit publicly, and keep `servers.csv` private.
```
