# OSSEC agent local build for Ubuntu Arm64

The following step describes how to build unofficial OSSEC agent (v3.7.0) for Ubuntu Arm64 servers(18.04, 20.04, 22.04).

Expected results:
- /var/cache/pbuilder/bionic-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1bionic_arm64.deb
- /var/cache/pbuilder/focal-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1focal_arm64.deb
- /var/cache/pbuilder/jammy-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1jammy_arm64.deb

## Requirements

- Ubuntu server (I've tested the script by using Ubuntu 20.04 and 22.04 VM)
- debootstrap, qemu-user-static, schroot, pbuilder

Install required packages.

``` shell
sudo apt install -y debootstrap qemu-user-static schroot pbuilder
```

## Steps

### Pull repository

Set working directory `WORK_DIR`.

``` shell
export WORK_DIR="$HOME/work"
```

``` shell
mkdir -p "$WORK_DIR"
cd "$WORK_DIR"
git clone https://github.com/superdaigo/ossec-hids
```

### Create archive

Checkout `ubutnu-arm64` branch and create tar.gz file.

``` shell
cd "$WORK_DIR/ossec-hids"
git checkout ubuntu-arm64
git archive \
  --format tar.gz \
  --prefix ossec-hids-3.7.0/ \
  --output contrib/debian-packages/ossec-hids-3.7.0.tar.gz \
  HEAD
```


# Unarchive source codes and organize files

``` shell
cd "$WORK_DIR/ossec-hids/contrib/debian-packages"
sudo mkdir -p /home/ubuntu/debian_files/3.7.0/ossec-hids-agent/debian
./generate_ossec.sh -a
cp -r "$WORK_DIR/ossec-hids/debian_files/3.7.0/ossec-hids-agent/debian" \
  "$WORK_DIR/ossec-hids/contrib/debian-packages/ossec-hids-agent/ossec-hids-agent-3.7.0"
```


# Create virtual environments

Create 3 virtual environments (bionic, focal, jammy)

``` shell
./generate_ossec.sh -u
```


# Build debian packages

``` shell
./generate_ossec.sh -b
```


List packages

``` shell
sudo ls -lh /var/cache/pbuilder/*-*/result/ossec-hids-agent/*_arm64.deb
```

Example output:

``` shell
-rw-r--r-- 1 root root 221K May  6 10:05 /var/cache/pbuilder/bionic-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1bionic_arm64.deb
-rw-r--r-- 1 root root 238K May  6 10:09 /var/cache/pbuilder/focal-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1focal_arm64.deb
-rw-r--r-- 1 root root 269K May  6 10:13 /var/cache/pbuilder/jammy-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1jammy_arm64.deb
```
