# Custom SSH Connection Manager

## Features
- Create aliases for SSH connections
- Connect using hostnames, IPs, or aliases
- Automatic CNAME → IP resolution
- Tabular listing of all servers
- Supports both macOS and Ubuntu

## 📥 Installation

### macOS

#### 1. First install required dependencies:
```bash
brew install hudochenkov/sshpass/sshpass
```

#### 2. Download and install the script:
```bash
sudo curl -o /usr/local/bin/sabid https://raw.githubusercontent.com/sabid-ansari/custom_ssh_bash/main/sabid
sudo chmod +x /usr/local/bin/sabid
mkdir -p ~/.ssh/quickssh
```

#### 3. Verify installation:
```bash
sabid ssh help
```

### Ubuntu/Debian

#### 1. Install dependencies:
```bash
sudo apt-get update
sudo apt-get install sshpass dnsutils
```

#### 2. Download and install the script:
```bash
sudo curl -o /usr/local/bin/sabid https://raw.githubusercontent.com/sabid-ansari/custom_ssh_bash/main/sabid
sudo chmod +x /usr/local/bin/sabid
mkdir -p ~/.ssh/quickssh
```

### Usage

#### Add a server
```bash
sabid ssh add <alias> <user> <host> <password> [port]
```
Default port is 22, set automatically if not passed

Example:
```bash
sabid ssh add myserver root example.com 'P@ssw0rd123' 22
```

#### List servers
```bash
sabid ssh list       # Compact view
sabid ssh list -a    # Show passwords
```

#### Connect to server
```bash
sabid ssh <alias|hostname|ip>
```

Examples:
```bash
sabid ssh myserver
sabid ssh example.com 
sabid ssh 192.168.1.1
```

#### Remove server
```bash
sabid ssh remove <alias>
```

### Examples

#### 1. Add a production server:
```bash
sabid ssh add prod-web root web.example.com 'S3cur3P@ss!' 2222
```

#### 2. Connect using different methods:
```bash
sabid ssh prod-web
sabid ssh web.example.com
sabid ssh 192.168.1.100
```

### Security

* Credentials stored in: ~/.ssh/quickssh/servers.csv
* File permissions automatically set to 600
* For production use consider SSH keys

### Troubleshooting

**"sshpass not found" error**
```bash
# On macOS:
brew install hudochenkov/sshpass/sshpass

# On Ubuntu:
sudo apt-get install sshpass
```

**Permission issues**
```bash
chmod 600 ~/.ssh/quickssh/servers.csv
```

### License

MIT
