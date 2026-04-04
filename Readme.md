# Server Download

![Platform](https://img.shields.io/badge/platform-linux-lightgrey.svg)
![Go](https://img.shields.io/badge/Go-1.24-blue.svg)

Worker service สำหรับดาวน์โหลดวิดีโอจาก HLS/m3u8 stream, merge segments ด้วย FFmpeg, และอัพโหลดไฟล์ไปยัง storage server ผ่าน SCP

## ✨ Features

- ดาวน์โหลด HLS segments แบบ concurrent
- Merge segments ด้วย FFmpeg
- อัพโหลดไฟล์ผ่าน SCP (Node.js `node-scp`)
- รองรับหลาย workers พร้อมกัน (systemd template)
- Auto-retry เมื่อ download ล้มเหลว
- รองรับ x86_64 และ ARM64

## 📋 Requirements

- Linux (Ubuntu/Debian recommended)
- FFmpeg
- Node.js (ติดตั้งอัตโนมัติผ่าน NVM)
- MongoDB

## 🚀 Quick Install (One-line)

```bash
curl -fsSL https://raw.githubusercontent.com/zergolf1994/server-download-releases/main/install.sh | sudo -E bash -s -- \
    --mongodb-uri "mongodb+srv://user:pass@host/dbname" \
    --storage-id "storage1" \
    -n 2
```

## 🛠️ Manual Installation

```bash
# Download install script
curl -fsSL https://raw.githubusercontent.com/zergolf1994/server-download-releases/main/install.sh -o install.sh
chmod +x install.sh

# Install with default settings (1 worker)
sudo ./install.sh --mongodb-uri "mongodb+srv://user:pass@host/dbname"

# Install with 3 workers
sudo ./install.sh \
    --mongodb-uri "mongodb+srv://user:pass@host/dbname" \
    --storage-id "storage1" \
    --storage-path "/home/files" \
    -n 3
```

## 🗑️ Uninstall

```bash
curl -fsSL https://raw.githubusercontent.com/zergolf1994/server-download-releases/main/install.sh | sudo -E bash -s -- \
    --uninstall
```

## ⚙️ Service Management

```bash
# Status (worker 1)
systemctl status server-download@1

# Logs (all workers)
journalctl -u "server-download@*" -f

# Logs (worker 1 only)
journalctl -u "server-download@1" -f

# Restart all workers (2 workers example)
for i in $(seq 1 2); do sudo systemctl restart server-download@$i; done

# Stop all workers
for i in $(seq 1 2); do sudo systemctl stop server-download@$i; done
```

## 📂 File Structure

```
/opt/server-download/
├── server-download          # Main binary (Go)
├── .env                     # Environment config
└── scripts/
    ├── package.json         # npm dependencies
    ├── node_modules/        # npm packages (node-scp)
    └── scp-upload.js        # SCP upload script
```

## 🔧 Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `MONGODB_URI` | — | MongoDB connection string |
| `STORAGE_ID` | — | Storage server identifier |
| `STORAGE_PATH` | `/home/files` | Remote storage path for uploaded files |
| `WORKER_ID` | `hostname-download-N` | Unique worker identifier (auto-set) |

## 📋 Install Options

| Option | Default | Description |
|--------|---------|-------------|
| `-n, --count` | `1` | จำนวน worker instances |
| `--mongodb-uri` | — | MongoDB connection string |
| `--storage-id` | — | Storage ID |
| `--storage-path` | `/home/files` | Remote storage path |
| `--node-version` | `22` | Node.js version |
| `--uninstall` | — | ลบทั้งหมด (binary, service, config) |
| `-h, --help` | — | แสดง help |

## 🔄 Update

```bash
# Re-run install script (will download latest binary & restart workers)
curl -fsSL https://raw.githubusercontent.com/zergolf1994/server-download-releases/main/install.sh | sudo -E bash -s -- \
    --mongodb-uri "mongodb+srv://user:pass@host/dbname" \
    -n 2
```
