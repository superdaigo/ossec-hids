# OSSEC agent local build

The following step describes how to build unofficial OSSEC agent (v3.7.0) for Ubuntu Arm64 servers(18.04, 20.04, 22.04).

Expected results:
- /var/cache/pbuilder/bionic-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1bionic_arm64.deb
- /var/cache/pbuilder/focal-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1focal_arm64.deb
- /var/cache/pbuilder/jammy-arm64/result/ossec-hids-agent/ossec-hids-agent_3.7.0-1jammy_arm64.deb

## Requirements

- Ubuntu server (I've tested the script by using Ubuntu 22.04 Arm64 VM)
- pbuilder

Install required packages.

``` shell
sudo apt install -y pbuilder
```

## Steps

### Pull repository

Set working directory `WORK_DIR`.

``` shell
export WORK_DIR=$HOME
```

``` shell
cd "$WORK_DIR"
git pull https://github.com/superdaigo/ossec-hids
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
./generate_ossec.sh -a
cp -r "$WORK_DIR/ossec-hids/debian_files/3.7.0/ossec-hids-agent/debian" \
  "$WORK_DIR/ossec-hids/contrib/debian-packages/ossec-hids-agent/ossec-hids-agent-3.7.0"g
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
