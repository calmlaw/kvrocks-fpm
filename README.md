# Apache Kvrocks Distro Packages by FPM

Automated packaging pipeline that builds and distributes Debian (`.deb`) and RedHat (`.rpm`) packages for [Apache Kvrocks](https://github.com/apache/kvrocks) using [FPM](https://github.com/jordansissel/fpm) and GitHub Actions.

---

## ✨ Features

- **Multi-Architecture Support**: Native packages for both `x86_64` (`amd64`) and `aarch64` (`arm64`).
- **CPU Microarchitecture Optimization**:
  - **Standard (`generic`)**: Baseline compatibility with all `x86_64` CPUs (`PORTABLE=1`).
  - **Optimized (`v3`)**: Compiled with `-march=x86-64-v3 -O3` (enabling AVX, AVX2, BMI1/2, FMA, SSE4.2) for modern servers (~15-30% higher RocksDB throughput).
- **Standard Linux Filesystem Layout**: Conforms to FHS (Filesystem Hierarchy Standard).
- **Systemd Integration & Lifecycle Hooks**: Automatic system user `kvrocks` creation, permission initialization, and `systemd` daemon reload.
- **Enterprise Capabilities**: Built with OpenSSL/TLS (`ENABLE_OPENSSL=ON`), Link-Time Optimization (`ENABLE_LTO=ON`), and Jemalloc memory allocator.
- **Automated Checksums**: Every release includes `SHA256SUMS` for integrity verification.

---

## 📦 Package Variants

| Package Name Pattern | Architecture | Target / CPU | Description |
| :--- | :--- | :--- | :--- |
| `kvrocks_<ver>-<iter>_amd64.deb`<br>`kvrocks-<ver>-<iter>.x86_64.rpm` | `x86_64` | `generic` | Baseline x86-64, maximum compatibility across all machines. |
| `kvrocks-v3_<ver>-<iter>_amd64.deb`<br>`kvrocks-v3-<ver>-<iter>.x86_64.rpm` | `x86_64` | `x86-64-v3` | Optimized for modern servers (Intel Haswell+, AMD Zen+). |
| `kvrocks_<ver>-<iter>_arm64.deb`<br>`kvrocks-<ver>-<iter>.aarch64.rpm` | `aarch64` | `generic` | 64-bit ARM (AWS Graviton, Aliyun/Tencent ARM, Kunpeng, etc.). |

---

## 🚀 Installation & Usage

### 1. Debian / Ubuntu (`.deb`)

```bash
# Install generic x86_64 or arm64 package
sudo dpkg -i kvrocks_<version>-<iteration>_amd64.deb
# Or install optimized v3 package (for modern x86_64 servers)
sudo dpkg -i kvrocks-v3_<version>-<iteration>_amd64.deb

# Fix missing dependencies if any
sudo apt-get install -f
```

### 2. RHEL / CentOS / Rocky Linux / Fedora (`.rpm`)

```bash
# Install via dnf / yum / rpm
sudo dnf install ./kvrocks-<version>-<iteration>.x86_64.rpm
# Or install optimized v3 package
sudo dnf install ./kvrocks-v3-<version>-<iteration>.x86_64.rpm
```

---

## ⚙️ Service Management

The package automatically sets up the `systemd` service unit:

```bash
# Start Kvrocks
sudo systemctl start kvrocks

# Enable autostart on boot
sudo systemctl enable kvrocks

# Check status
sudo systemctl status kvrocks

# View logs
sudo journalctl -u kvrocks -f
```

Test connection using `redis-cli`:
```bash
redis-cli -p 6666 ping
# PONG
```

---

## 📁 Filesystem Layout

| Path | Purpose |
| :--- | :--- |
| `/usr/bin/kvrocks` | Main Kvrocks server binary |
| `/usr/bin/kvrocks2redis` | Data migration utility to sync Kvrocks to Redis |
| `/etc/kvrocks/kvrocks.conf` | Configuration file (protected during package upgrades) |
| `/usr/lib/systemd/system/kvrocks.service` | Systemd service unit |
| `/var/lib/kvrocks/` | Working & database storage directory (owned by `kvrocks:kvrocks`) |
| `/var/log/kvrocks/` | Server log directory (owned by `kvrocks:kvrocks`) |
| `/usr/share/doc/kvrocks/` | License and Notice documentation |

---

## 🛠️ Build & Release

### Releasing a New Version

1. Update `VERSION` (e.g. `2.15.0`) and `ITERATION` (e.g. `1`).
2. Push changes to GitHub:
   ```bash
   git commit -am "chore(release): bump version to 2.15.0"
   git push origin master
   ```
3. Trigger the workflow manually via GitHub Actions **Run workflow** (`workflow_dispatch`), or push a tag:
   ```bash
   git tag v2.15.0-1
   git push origin v2.15.0-1
   ```

---

## 📄 License

This repository and packaging scripts are licensed under the [Apache-2.0 License](LICENSE). Apache Kvrocks is licensed under Apache-2.0.
