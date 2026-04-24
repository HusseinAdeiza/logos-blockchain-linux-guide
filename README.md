# Logos Blockchain Node Setup Guide for Linux

A comprehensive guide to run the Logos Blockchain Node v0.1.2 on Linux (including WSL2, Ubuntu, Debian, Fedora, and other distributions).

## 📋 Table of Contents

- [System Requirements](#system-requirements)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Node](#running-the-node)
- [Restarting the Node](#restarting-the-node)
- [Monitoring](#monitoring)
- [Getting Testnet Funds](#getting-testnet-funds)
- [Block Production](#block-production)
- [Publishing Messages](#publishing-messages)
- [Performance Optimization](#performance-optimization)
- [Troubleshooting](#troubleshooting)
- [Resources](#resources)

## 🖥️ System Requirements

### Minimum Requirements
| Component | Specification |
|-----------|---------------|
| **OS** | Ubuntu 20.04 LTS+, Debian 11+, Fedora 36+, CentOS 8+, or WSL2 |
| **Architecture** | x86_64 (64-bit) |
| **Processor** | Intel i5/AMD Ryzen 5 (or equivalent) - 4+ cores |
| **RAM** | 8 GB minimum |
| **Storage** | 50 GB SSD minimum (fast I/O critical) |
| **Network** | Stable internet, UDP ports 3000-3003 accessible |
| **Uptime** | 99%+ availability recommended |

### Recommended Requirements
| Component | Specification |
|-----------|---------------|
| **Processor** | Intel i7/AMD Ryzen 7 or better - 8+ cores |
| **RAM** | 32 GB |
| **Storage** | 100 GB+ NVMe SSD |
| **Network** | Fiber or 1Gbps+ connection |
| **Uptime** | 24/7 dedicated machine |
| **OS** | Ubuntu 22.04 LTS or Debian 12 |

### Linux Distribution Support

| Distribution | Status | Notes |
|-------------|--------|-------|
| **Ubuntu 20.04 LTS** | ✅ Fully Supported | Recommended for beginners |
| **Ubuntu 22.04 LTS** | ✅ Fully Supported | Recommended - Latest LTS |
| **Debian 11 (Bullseye)** | ✅ Fully Supported | Stable option |
| **Debian 12 (Bookworm)** | ✅ Fully Supported | Latest Debian |
| **Fedora 36+** | ✅ Supported | RPM-based |
| **CentOS 8+** | ✅ Supported | Enterprise option |
| **WSL2 (Windows)** | ✅ Fully Supported | Ubuntu 22.04 LTS recommended |
| **Rocky Linux 8+** | ✅ Supported | RHEL alternative |
| **AlmaLinux 8+** | ✅ Supported | RHEL alternative |

### Network Requirements
- **Inbound**: UDP ports 3000-3003 must be open
- **Outbound**: Unrestricted to GitHub and peer nodes
- **Bandwidth**: Minimum 10 Mbps, recommended 100+ Mbps
- **Latency**: <200ms optimal

### Storage Considerations
- **Type**: SSD (NVMe preferred) - HDD not recommended
- **IOPS**: 5000+ IOPS minimum
- **File system**: ext4 (ext3 supported but slower)
- **Growth**: Plan for ~10-20 GB/month

## 📦 Prerequisites

### Common to All Distributions

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y   # Debian/Ubuntu
# OR
sudo dnf update -y                       # Fedora/CentOS/Rocky/Alma

# Install required tools
sudo apt install -y wget curl tar gzip   # Debian/Ubuntu
# OR
sudo dnf install -y wget curl tar gzip   # Fedora/CentOS/Rocky/Alma
```

### WSL2 Specific Setup

```bash
# Open PowerShell as Administrator and run:
wsl --install

# Restart computer, then verify:
wsl --list --verbose

# Install Ubuntu 22.04 LTS
wsl --install -d Ubuntu-22.04

# Update WSL2 kernel
wsl --update
```

### Ubuntu/Debian Setup

```bash
# Ensure sudo is installed
sudo apt install -y sudo

# Add your user to sudoers (if needed)
sudo usermod -aG sudo $USER

# Verify installation
sudo whoami
```

### Fedora/CentOS/RHEL Setup

```bash
# Verify sudo is installed
sudo dnf install -y sudo

# Add user to wheel group (fedora/rhel equivalent of sudoers)
sudo usermod -aG wheel $USER
```

### Check System Information

```bash
# Verify OS and kernel
uname -a

# Check CPU cores
nproc

# Check available RAM
free -h

# Check disk space
df -h

# Check internet connectivity
curl -I https://github.com
```

## 💾 Installation

### Step 1: Prepare Working Directory

```bash
# Create directory for the node
mkdir -p ~/logos-blockchain && cd ~/logos-blockchain

# Verify space available
df -h .
```

### Step 2: Download Release Files

The node requires two files. Download them based on your architecture:

**For x86_64 (most common):**
```bash
# Node binary (16.47 MB)
wget https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-node-linux-x86_64-0.1.2.tar.gz

# Circuits (31.14 MB)
wget https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz
```

**For ARM64 (e.g., Raspberry Pi 4, AWS Graviton):**
```bash
# Node binary (if available)
wget https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-node-linux-aarch64-0.1.2.tar.gz

# Circuits (if available)
wget https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-circuits-v0.4.2-linux-aarch64.tar.gz
```

**Alternative with curl:**
```bash
curl -L -O https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-node-linux-x86_64-0.1.2.tar.gz
curl -L -O https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz
```

**Check download integrity:**
```bash
# Verify file sizes
ls -lh *.tar.gz
```

### Step 3: Extract Files

```bash
# Extract the circuits
tar -xzf logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz

# Move circuits to home directory with correct name
mv logos-blockchain-circuits-v0.4.2-linux-x86_64 ~/.logos-blockchain-circuits

# Extract the node binary
tar -xzf logos-blockchain-node-linux-x86_64-0.1.2.tar.gz

# Make binary executable
chmod +x ./logos-blockchain-node

# Verify extraction
ls -lah
file ./logos-blockchain-node
```

Expected output:
```
-rwxr-xr-x  1 user user 44694880 Apr 13 17:07 logos-blockchain-node
-rw-r--r--  1 user user 17271536 Apr 13 17:19 logos-blockchain-node-linux-x86_64-0.1.2.tar.gz
-rw-r--r--  1 user user 32657964 Apr 13 17:20 logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz
drwxr-xr-x  5 user user     4096 Apr  8 08:17 .logos-blockchain-circuits
```

### Step 4: Clean Up (Optional)

```bash
rm logos-blockchain-node-linux-x86_64-0.1.2.tar.gz
rm logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz
```

### Step 5: Verify Binary Compatibility

```bash
# Check binary format
file ./logos-blockchain-node

# Check dependencies
ldd ./logos-blockchain-node

# Test binary runs
./logos-blockchain-node --version 2>/dev/null || echo "Binary ready"
```

## ⚙️ Configuration

### Initialize Your Node

Generate a default configuration by connecting to the testnet bootstrap peers:

```bash
./logos-blockchain-node init \
    -p /ip4/65.109.51.37/udp/3000/quic-v1/p2p/12D3KooWFrouXfmrR4nsLMtE7wu15DoMJ6VtoUtHinREZCvbWHar \
    -p /ip4/65.109.51.37/udp/3001/quic-v1/p2p/12D3KooWJRGau8M1rjT7R5e4YYsgdFhsMX35nRDtMwCDjxQkXAHz \
    -p /ip4/65.109.51.37/udp/3002/quic-v1/p2p/12D3KooWQXJavMDTRscjauFSgVAB1VLB6Rzpy2uY5SU9Tk7927tb \
    -p /ip4/65.109.51.37/udp/3003/quic-v1/p2p/12D3KooWSQc7CcGtvWDPF1yCbBthFnQjprfCVHmfmNDUrSmqQsU1
```

### Behind NAT/Firewall (WSL2, Home Networks)

If your node will not have a publicly reachable IP address:

```bash
./logos-blockchain-node init \
    -p /ip4/65.109.51.37/udp/3000/quic-v1/p2p/12D3KooWFrouXfmrR4nsLMtE7wu15DoMJ6VtoUtHinREZCvbWHar \
    -p /ip4/65.109.51.37/udp/3001/quic-v1/p2p/12D3KooWJRGau8M1rjT7R5e4YYsgdFhsMX35nRDtMwCDjxQkXAHz \
    -p /ip4/65.109.51.37/udp/3002/quic-v1/p2p/12D3KooWQXJavMDTRscjauFSgVAB1VLB6Rzpy2uY5SU9Tk7927tb \
    -p /ip4/65.109.51.37/udp/3003/quic-v1/p2p/12D3KooWSQc7CcGtvWDPF1yCbBthFnQjprfCVHmfmNDUrSmqQsU1 \
    --no-public-ip-check
```

This creates a `user_config.yaml` file in your working directory.

### Open Firewall Ports

**For UFW (Ubuntu/Debian):**
```bash
sudo ufw allow 3000:3003/udp comment 'Logos Blockchain P2P'
sudo ufw allow 8080/tcp comment 'Logos API'
sudo ufw status
```

**For Firewalld (Fedora/CentOS/RHEL):**
```bash
sudo firewall-cmd --permanent --add-port=3000-3003/udp
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
sudo firewall-cmd --list-ports
```

**For iptables (Manual):**
```bash
sudo iptables -A INPUT -p udp --dport 3000:3003 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
```

### Breaking Changes from v0.1.1

⚠️ **Important:** If you previously ran a v0.1 node, clean up the old state:

```bash
rm -r ~/.logos-blockchain-circuits
rm -r ~/.logos-blockchain-state 2>/dev/null || true
rm ~/user_config.yaml 2>/dev/null || true
```

## 🚀 Running the Node

### Basic Startup

```bash
cd ~/logos-blockchain
./logos-blockchain-node user_config.yaml
```

### Running in Background (screen)

```bash
# Install screen if not available
sudo apt install -y screen  # Ubuntu/Debian
# OR
sudo dnf install -y screen  # Fedora/CentOS

# Create a new screen session
screen -S logos-node

# Run the node
cd ~/logos-blockchain
./logos-blockchain-node user_config.yaml

# Detach from session: Press Ctrl+A then D
# Reattach: screen -r logos-node
# List sessions: screen -ls
# Kill session: screen -X -S logos-node quit
```

### Running in Background (tmux)

```bash
# Install tmux
sudo apt install -y tmux  # Ubuntu/Debian
# OR
sudo dnf install -y tmux  # Fedora/CentOS

# Create a new tmux session
cd ~/logos-blockchain
tmux new-session -d -s logos-node './logos-blockchain-node user_config.yaml'

# Attach: tmux attach -t logos-node
# Detach: Press Ctrl+B then D
# List: tmux list-sessions
# Kill: tmux kill-session -t logos-node
```

### Systemd Service (Recommended for Production)

Create `/etc/systemd/system/logos-blockchain.service`:

```bash
sudo tee /etc/systemd/system/logos-blockchain.service > /dev/null <<EOF
[Unit]
Description=Logos Blockchain Node
After=network.target
Wants=network-online.target

[Service]
Type=simple
User=logos
Group=logos
WorkingDirectory=/home/logos/logos-blockchain
ExecStart=/home/logos/logos-blockchain/logos-blockchain-node /home/logos/logos-blockchain/user_config.yaml
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal

# Security hardening
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=strict
ProtectHome=yes
ReadWritePaths=/home/logos/logos-blockchain ~/.logos-blockchain-circuits

# Resource limits
LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target
EOF
```

Setup and start the service:

```bash
# Create logos user
sudo useradd -m -s /bin/bash logos 2>/dev/null || true

# Copy node directory
sudo mkdir -p /home/logos/logos-blockchain
sudo cp ~/logos-blockchain/* /home/logos/logos-blockchain/
sudo cp -r ~/.logos-blockchain-circuits /home/logos/
sudo chown -R logos:logos /home/logos/logos-blockchain
sudo chown -R logos:logos /home/logos/.logos-blockchain-circuits

# Enable and start service
sudo systemctl daemon-reload
sudo systemctl enable logos-blockchain
sudo systemctl start logos-blockchain
sudo systemctl status logos-blockchain

# View logs
sudo journalctl -u logos-blockchain -f
```

### Docker Container (Optional)

```bash
# Create Dockerfile
cat > Dockerfile <<EOF
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y wget && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Download binaries
RUN wget https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-node-linux-x86_64-0.1.2.tar.gz && \
    wget https://github.com/logos-blockchain/logos-blockchain/releases/download/0.1.2/logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz && \
    tar -xzf logos-blockchain-node-linux-x86_64-0.1.2.tar.gz && \
    tar -xzf logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz && \
    mv logos-blockchain-circuits-v0.4.2-linux-x86_64 ~/.logos-blockchain-circuits && \
    chmod +x logos-blockchain-node

EXPOSE 3000-3003/udp 8080

ENTRYPOINT ["./logos-blockchain-node"]
EOF

# Build image
docker build -t logos-blockchain:0.1.2 .

# Run container
docker run -d \
  --name logos-node \
  -v ~/.logos-blockchain-circuits:/root/.logos-blockchain-circuits \
  -v /path/to/config:/app/config \
  -p 3000-3003:3000-3003/udp \
  -p 8080:8080 \
  logos-blockchain:0.1.2 /app/user_config.yaml
```

## 🔄 Restarting the Node

When you restart your computer or need to restart the node, here are the commands based on your setup:

### Systemd Service (Recommended & Auto-Start)

```bash
# Start the node
sudo systemctl start logos-blockchain

# Check if it's running
sudo systemctl status logos-blockchain

# View logs
sudo journalctl -u logos-blockchain -f
```

**Enable auto-start on boot:**
```bash
sudo systemctl enable logos-blockchain
```

After rebooting your computer, the node will start automatically! ✅

### Manual Startup (Foreground)

```bash
cd ~/logos-blockchain
./logos-blockchain-node user_config.yaml
```

### Screen Session

```bash
# Create and attach to a new screen session
screen -S logos-node

# Run the node
cd ~/logos-blockchain
./logos-blockchain-node user_config.yaml

# Detach: Press Ctrl+A then D

# To reattach later:
screen -r logos-node

# List all sessions:
screen -ls
```

### Tmux Session

```bash
# Create a new tmux session (runs in background)
cd ~/logos-blockchain
tmux new-session -d -s logos-node './logos-blockchain-node user_config.yaml'

# To view it:
tmux attach -t logos-node

# Detach: Press Ctrl+B then D

# List sessions:
tmux list-sessions

# Kill session:
tmux kill-session -t logos-node
```

### Summary Table

| Method | Auto-Start | Command | Pros | Cons |
|--------|-----------|---------|------|------|
| **Systemd** | ✅ Yes | `sudo systemctl start logos-blockchain` | Best for production, auto-restarts on failure | Requires setup |
| **Foreground** | ❌ No | `./logos-blockchain-node user_config.yaml` | Simple, see logs directly | Must stay in terminal |
| **Screen** | ❌ No | `screen -S logos-node` | Easy detach/reattach | Manual restart needed |
| **Tmux** | ❌ No | `tmux new-session -d -s logos-node` | More features than screen | Manual restart needed |

### My Recommendation

Use **Systemd** for production setups:

```bash
# Enable auto-start on boot
sudo systemctl enable logos-blockchain

# Start now
sudo systemctl start logos-blockchain

# Check status
sudo systemctl status logos-blockchain
```

Then after every computer restart, your node will automatically start! 🚀

## 📊 Monitoring

### Check Node Status

```bash
curl -w "\n" http://localhost:8080/cryptarchia/info
```

Expected response:
```json
{
  "lib": "c598a17888d3654d829cf0efd9bbc4a3d4b0f17e0c138e440a809e12c9bcccfe",
  "lib_slot": 971267,
  "tip": "ca7ce410e3c4b542eddafec06a77d7190aae2350d3407e937c3eaff1c655a56d",
  "slot": 971854,
  "height": 49217,
  "mode": "Online"
}
```

**Status Meanings:**
- `mode: "Bootstrapping"` - Syncing with network (slot and height increasing)
- `mode: "Online"` - Fully synced and ready to produce blocks

### Real-time Monitoring Script

```bash
#!/bin/bash
# Save as monitor.sh and chmod +x monitor.sh

while true; do
    clear
    echo "=== Logos Blockchain Node Monitor ==="
    echo "Time: $(date)"
    echo ""
    
    response=$(curl -s http://localhost:8080/cryptarchia/info)
    
    mode=$(echo $response | grep -o '"mode":"[^"]*' | cut -d'"' -f4)
    slot=$(echo $response | grep -o '"slot":[0-9]*' | cut -d':' -f2)
    height=$(echo $response | grep -o '"height":[0-9]*' | cut -d':' -f2)
    
    echo "Mode: $mode"
    echo "Slot: $slot"
    echo "Height: $height"
    echo ""
    
    # System resources
    echo "=== System Resources ==="
    ps aux | grep logos-blockchain-node | grep -v grep | awk '{printf "CPU: %.1f%%, Memory: %.1f%% (%.0fMB)\n", $3, $4, $6/1024}'
    
    sleep 5
done
```

Run it:
```bash
./monitor.sh
```

### View Logs

```bash
# Last 50 lines
tail -n 50 *.log

# Follow logs in real-time
tail -f *.log

# Search logs
grep -i "error\|warning" *.log

# For systemd service
sudo journalctl -u logos-blockchain -f
```

### System Resource Monitoring

```bash
# Real-time process monitor
top -p $(pgrep -f logos-blockchain-node)

# Disk usage
du -sh ~/.logos-blockchain-circuits
du -sh ~/logos-blockchain

# Network connections
netstat -tuln | grep -E '3000|3001|3002|3003|8080'

# Open files
lsof -p $(pgrep -f logos-blockchain-node)
```

## 💰 Getting Testnet Funds

### Step 1: Find Your Wallet Key

```bash
grep -A3 known_keys user_config.yaml
```

Output example:
```yaml
known_keys:
    43c3379eaabf3977e4c81c22fe221f2fa13a11db06e8174c2959427bc1aa3720: ...
    de3233cec107e6589f83d4f3094caa65c633b5b33601211353779dc01972ca14: ...
```

Copy any of the listed key IDs - this is your wallet address.

### Step 2: Request Funds from Faucet

1. Visit [Logos Testnet Faucet](https://testnet.blockchain.logos.co/web/faucet/)
2. Enter credentials provided by Logos team on [Discord](https://discord.com/channels/973324189794697286/1468535289604735038)
3. Paste your wallet key
4. Submit request

⚠️ **Important:** One request per block! Wait for balance increase before requesting again.

### Step 3: Verify Balance

Wait 1-2 minutes for transaction to land in a block:

```bash
curl -w "\n" http://localhost:8080/wallet/<my_key>/balance
```

Replace `<my_key>` with your wallet key.

Expected response:
```json
{
  "tip": "71b96a69acf603ff762d5b2e06318edd99a3c14a3cf5cd0510be4f479d89e6d8",
  "balance": 1000,
  "notes": {
    "cb38f536b30e583a2dd836ed963c3ad95b26a758a20463ba0cb7d44ca6de8602": 1000
  },
  "address": "43c3379eaabf3977e4c81c22fe221f2fa13a11db06e8174c2959427bc1aa3720"
}
```

## 🧱 Block Production

After receiving funds, your node will automatically produce blocks approximately **3.5 hours (2 epochs)** later.

**Timeline:**
1. ✅ Receive funds via faucet
2. ⏳ Wait ~3.5 hours
3. 🎉 Node automatically produces blocks

**Monitor production:**

```bash
# Check node status
curl -w "\n" http://localhost:8080/cryptarchia/info

# Compare against fleet on dashboard
# https://testnet.blockchain.logos.co/web/
```

## 📝 Publishing Messages

Publish messages to the blockchain using the built-in text sequencer:

```bash
cd ~/logos-blockchain
./logos-blockchain-node inscribe
```

Follow the interactive prompts to enter and publish your messages.

## 🚀 Performance Optimization

### Linux Kernel Tuning

For production nodes, optimize network performance:

```bash
# Increase file descriptors
sudo sysctl -w fs.file-max=2097152
sudo sysctl -w fs.nr_open=2097152

# Optimize network buffers
sudo sysctl -w net.core.rmem_max=134217728
sudo sysctl -w net.core.wmem_max=134217728
sudo sysctl -w net.ipv4.tcp_rmem="4096 87380 134217728"
sudo sysctl -w net.ipv4.tcp_wmem="4096 65536 134217728"

# Increase connection backlog
sudo sysctl -w net.core.somaxconn=4096
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=8192

# Persist settings
sudo tee -a /etc/sysctl.conf > /dev/null <<EOF
fs.file-max=2097152
fs.nr_open=2097152
net.core.rmem_max=134217728
net.core.wmem_max=134217728
net.ipv4.tcp_rmem=4096 87380 134217728
net.ipv4.tcp_wmem=4096 65536 134217728
net.core.somaxconn=4096
net.ipv4.tcp_max_syn_backlog=8192
EOF
```

### Storage Optimization

```bash
# Enable SSD TRIM (if supported)
sudo fstrim -v /

# Check I/O scheduler
cat /sys/block/sda/queue/scheduler

# Change to deadline scheduler (for SSD)
echo deadline | sudo tee /sys/block/sda/queue/scheduler
```

### CPU Affinity (Advanced)

```bash
# Pin process to specific CPUs
taskset -pc 0-7 $(pgrep -f logos-blockchain-node)

# Verify
taskset -pc $(pgrep -f logos-blockchain-node)
```

## 🛠️ Troubleshooting

### Node Won't Start

**Binary not executable:**
```bash
chmod +x ./logos-blockchain-node
file ./logos-blockchain-node
```

**Circuits not found:**
```bash
ls -la ~/.logos-blockchain-circuits
# If missing:
tar -xzf logos-blockchain-circuits-v0.4.2-linux-x86_64.tar.gz
mv logos-blockchain-circuits-v0.4.2-linux-x86_64 ~/.logos-blockchain-circuits
```

**Insufficient disk space:**
```bash
df -h
# Free up space or expand partition
```

**Library dependencies missing:**
```bash
ldd ./logos-blockchain-node
# Install missing dependencies
sudo apt install libssl-dev  # Ubuntu/Debian
```

### Node Won't Connect to Network

**Behind firewall:**
```bash
# Open ports
sudo ufw allow 3000:3003/udp
sudo ufw allow 8080/tcp

# Reinit with --no-public-ip-check
./logos-blockchain-node init ... --no-public-ip-check
```

**Bootstrap peers unreachable:**
```bash
# Check connectivity
ping 65.109.51.37
curl -I https://github.com

# Check DNS
nslookup github.com
```

### API Endpoint Returns Connection Refused

**Node not running:**
```bash
ps aux | grep logos-blockchain-node
# Start node if not running
```

**Wrong port:**
```bash
grep -i "port\|listen" user_config.yaml
netstat -tuln | grep 8080
```

### High Memory Usage

Expected: 2-4 GB after full sync.

If higher:
```bash
# Check memory usage
top -p $(pgrep -f logos-blockchain-node)

# Restart node
killall logos-blockchain-node
```

### Slots Not Increasing

**Not syncing:**
```bash
# Check logs
tail -f *.log | grep -i "error\|sync"

# Verify peers
curl -s http://localhost:8080/cryptarchia/info

# Restart with fresh config
rm user_config.yaml
./logos-blockchain-node init ... --no-public-ip-check
```

### Systemd Service Issues

```bash
# Check status
sudo systemctl status logos-blockchain

# View logs
sudo journalctl -u logos-blockchain -e

# Restart
sudo systemctl restart logos-blockchain

# Check for errors
sudo systemctl status logos-blockchain --full
```

### Network Issues on WSL2

```bash
# Check WSL2 connectivity
wsl -l -v

# Restart WSL2
wsl --shutdown
# Then reopen WSL2 terminal

# Forward ports (if needed)
netsh interface portproxy add v4tov4 listenport=3000 listenaddress=0.0.0.0 connectport=3000 connectaddress=<WSL2_IP>
```

## 📚 Resources

- **GitHub Repository:** https://github.com/logos-blockchain/logos-blockchain
- **Release Page:** https://github.com/logos-blockchain/logos-blockchain/releases/tag/0.1.2
- **Testnet Dashboard:** https://testnet.blockchain.logos.co/web/
- **Testnet Faucet:** https://testnet.blockchain.logos.co/web/faucet/
- **Discord Community:** https://discord.com/channels/973324189794697286/1468535289604735038
- **Linux Kernel Documentation:** https://www.kernel.org/doc/
- **Systemd Documentation:** https://www.freedesktop.org/wiki/Software/systemd/

## 🤝 Support

Having issues? Reach out to the Logos Blockchain team on [Discord](https://discord.com/channels/973324189794697286/1468535289604735038).

## 📄 License

This guide is provided as-is for educational purposes. See the [Logos Blockchain Repository](https://github.com/logos-blockchain/logos-blockchain) for license details.

---

**Last Updated:** April 24, 2026
**Node Version:** 0.1.2
**Circuits Version:** 0.4.2
**Supported Platforms:** Linux (Ubuntu, Debian, Fedora, CentOS, RHEL, Rocky, AlmaLinux), WSL2
