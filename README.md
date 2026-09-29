# Custom SSH Connection Manager

A simple Bash-based SSH/SCP connection manager for **macOS and Ubuntu/Debian**.

`sabid` lets you store SSH server details in a central CSV file and connect to servers using simple aliases.

## Features

* Create aliases for SSH connections
* Store SSH credentials in `~/.ssh/quickssh/servers.csv`
* Connect using server aliases
* Support username and port overrides
* SSH debug mode
* SCP file transfers
* Add and remove server configurations
* List configured servers
* Automatically clean Windows/CRLF line endings from `servers.csv`
* Manual CSV cleanup with `sabid ssh clean`
* Passwords masked when listing servers
* Works on macOS and Ubuntu/Debian
* CSV file automatically protected with `600` permissions

---

# 📥 Installation

## macOS

### 1. Install `sshpass`

```bash
brew install hudochenkov/sshpass/sshpass
```

### 2. Install `sabid`

```bash
sudo curl -o /usr/local/bin/sabid https://raw.githubusercontent.com/sabid-ansari/custom_ssh_bash/main/sabid
sudo chmod +x /usr/local/bin/sabid
mkdir -p ~/.ssh/quickssh
chmod 700 ~/.ssh/quickssh
```

### 3. Verify installation

```bash
sabid ssh help
```

You can also check:

```bash
sabid --help
```

---

# Ubuntu / Debian

### 1. Install dependencies

```bash
sudo apt-get update
sudo apt-get install -y sshpass perl
```

`perl` is used by `sabid` to automatically remove Windows/CRLF line endings from the CSV file.

### 2. Install `sabid`

```bash
sudo curl -o /usr/local/bin/sabid https://raw.githubusercontent.com/sabid-ansari/custom_ssh_bash/main/sabid
sudo chmod +x /usr/local/bin/sabid
mkdir -p ~/.ssh/quickssh
chmod 700 ~/.ssh/quickssh
```

### 3. Verify installation

```bash
sabid ssh help
```

---

# 📁 Server Configuration

Server information is stored in:

```text
~/.ssh/quickssh/servers.csv
```

The CSV format is:

```csv
alias_name,server_username,server_ip,password,port
```

Example:

```csv
alias_name,server_username,server_ip,password,port
us,sabid,10.165.197.58,P@ssw0rd,22
prod,root,10.165.197.100,AnotherPassword,2222
```

The file is automatically protected:

```bash
chmod 600 ~/.ssh/quickssh/servers.csv
```

> **Note:** Passwords are stored as plaintext in the CSV file. Make sure the file is protected and never commit `servers.csv` to Git.

---

# ➕ Add a Server

## Interactive password prompt

Recommended:

```bash
sabid ssh add <alias> <user> <host>
```

Example:

```bash
sabid ssh add myserver root example.com
```

You will be prompted for the password securely.

The default SSH port is:

```text
22
```

## Specify password

You can also provide the password:

```bash
sabid ssh add <alias> <user> <host> <password>
```

Example:

```bash
sabid ssh add myserver root example.com 'P@ssw0rd123'
```

## Specify password and port

```bash
sabid ssh add <alias> <user> <host> <password> <port>
```

Example:

```bash
sabid ssh add prod-web root web.example.com 'S3cur3P@ss!' 2222
```

---

# 📋 List Servers

List all configured servers:

```bash
sabid ssh list
```

Example output:

```text
Server Details:

  Alias    : us
  User     : sabid
  Host     : 10.165.197.58
  Port     : 22
  Password : ********
```

List a specific server:

```bash
sabid ssh list us
```

Passwords are intentionally masked.

---

# 🧹 Clean CSV

`servers.csv` may sometimes be edited on Windows or copied from another system and contain Windows CRLF line endings.

This can cause problems such as:

```text
Invalid port
```

Run:

```bash
sabid ssh clean
```

Output:

```text
CSV cleaned successfully:
  /Users/username/.ssh/quickssh/servers.csv
```

The script also performs this cleanup automatically before reading the CSV, so normally you don't need to run this command manually.

The cleanup works on both:

* macOS
* Ubuntu/Debian

---

# 🔐 Connect to a Server

Connect using an alias:

```bash
sabid ssh myserver
```

Example:

```bash
sabid ssh us
```

The connection uses the username, host, password, and port stored in:

```text
~/.ssh/quickssh/servers.csv
```

---

# 👤 Override Username

Use a different username without changing the CSV:

```bash
sabid ssh us --user root
```

Short form:

```bash
sabid ssh us -u root
```

You can also provide the username positionally:

```bash
sabid ssh us root
```

---

# 🔌 Override Port

Use a different SSH port without changing the CSV:

```bash
sabid ssh us --port 2222
```

Short form:

```bash
sabid ssh us -p 2222
```

---

# 🐛 Debug Mode

For SSH troubleshooting:

```bash
sabid ssh us --debug
```

Short form:

```bash
sabid ssh us -d
```

This enables OpenSSH verbose mode (`-vvv`) so you can see detailed SSH connection information.

---

# 📤 SCP

`sabid` also supports SCP file transfers.

## Upload a file

```bash
sabid scp us ./file.txt /tmp/file.txt
```

This copies:

```text
./file.txt
```

to:

```text
sabid@10.165.197.58:/tmp/file.txt
```

using the credentials stored for `us`.

## Override username

```bash
sabid scp --user root us ./file.txt /tmp/file.txt
```

## Override port

```bash
sabid scp --port 2222 us ./file.txt /tmp/file.txt
```

---

# ➖ Remove a Server

Remove a specific server:

```bash
sabid ssh remove us
```

Remove all configured servers:

```bash
sabid ssh remove --all
```

The `--all` operation asks for confirmation before deleting the server entries.

---

# 📖 Command Reference

| Command                         | Description                     |
| ------------------------------- | ------------------------------- |
| `sabid ssh list`                | List all configured servers     |
| `sabid ssh list <alias>`        | Show one server                 |
| `sabid ssh clean`               | Remove CRLF characters from CSV |
| `sabid ssh <alias>`             | Connect to server               |
| `sabid ssh <alias> <user>`      | Connect using another user      |
| `sabid ssh <alias> --user USER` | Override username               |
| `sabid ssh <alias> --port PORT` | Override SSH port               |
| `sabid ssh <alias> --debug`     | Enable SSH debug mode           |
| `sabid ssh add ...`             | Add a server                    |
| `sabid ssh remove <alias>`      | Remove a server                 |
| `sabid ssh remove --all`        | Remove all servers              |
| `sabid scp ...`                 | Copy files using SCP            |
| `sabid --help`                  | Show help                       |

---

# 🔒 Security

Server credentials are stored in:

```text
~/.ssh/quickssh/servers.csv
```

The directory is protected with:

```bash
chmod 700 ~/.ssh/quickssh
```

The CSV file is protected with:

```bash
chmod 600 ~/.ssh/quickssh/servers.csv
```

Passwords are masked when using:

```bash
sabid ssh list
```

### Important

The passwords are still stored as **plaintext** inside the CSV.

Do **not** commit the following file to Git:

```text
~/.ssh/quickssh/servers.csv
```

For production environments, SSH keys are recommended over password authentication.

---

# 🛠 Troubleshooting

## `sshpass` not found

### macOS

```bash
brew install hudochenkov/sshpass/sshpass
```

### Ubuntu/Debian

```bash
sudo apt-get update
sudo apt-get install -y sshpass
```

---

## CSV permission issues

Run:

```bash
chmod 700 ~/.ssh/quickssh
chmod 600 ~/.ssh/quickssh/servers.csv
```

---

## Invalid port / strange CSV values

Run:

```bash
sabid ssh clean
```

You can also inspect the CSV:

```bash
cat -vet ~/.ssh/quickssh/servers.csv
```

If you see `^M` at the end of lines, the CSV contained Windows CRLF line endings. `sabid ssh clean` removes them.

---

## SSH connection troubleshooting

Run:

```bash
sabid ssh <alias> --debug
```

Example:

```bash
sabid ssh us --debug
```

You can also test SSH directly:

```bash
ssh -p 22 user@host
```

---

# 📝 Notes

### Passwords containing commas

The current implementation uses a simple comma-separated CSV parser.

Therefore, passwords containing a comma are **not supported reliably**.

For example, avoid:

```text
MyPassword,123
```

Characters such as these are fine:

```text
@
$
!
#
%
&
*
```

provided the password does not contain a comma.

---

# 📄 License

MIT
