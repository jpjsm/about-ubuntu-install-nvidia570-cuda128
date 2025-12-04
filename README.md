# XPS-8950 Setup

With AI and ML in mind, the setup of this machine is geared towards
Pyhton and GPU (CUDA) usage.

Most AI chains use pythorch or tensorflow, either at top level or bottom
level; and for those purposes CUDA 12.8 is the most common and tested 
version, and the one that works for most Python libraries.

## Install Ubuntu

1. Remove the GPU, if installed in the computer

1. Prepare a bootable USB drive with latest Ubuntu 24.04

1. Search for the function key that needs to be pressed to allow to boot
from USB

1. Insert USB drive in computer, turn-on, and press the key to select USB drive

1. Follow instructions on screen

1. Check computer boots correctly

1. Install GPU

## Install Software

### Install SSH Server


### 1st. NVIDIA 570 and CUDA 12.8

This is the last driver made native with nvcc 12.8; which makes it the optimal 
one to be installed for the current GPU card: RTX 4060 Ti.

See: [NVIDIA 570 driver install](./NVIDIA-570-driver-install.md)

### 2nd. Conda-Forge, GIT, and VS Code

- See [conda-forge downloads](https://conda-forge.org/download/)
  - From a browser select the correct installer and download it
  - from terminal, in the same folder the downloaded file is: `bash Miniforge3-$(uname)-$(uname -m).sh`

- GIT
  - `sudo apt update`
  - `sudo apt install -y git`
  
- VS Code
  - Download [.deb package (64-bit)](https://go.microsoft.com/fwlink/?LinkID=760868)
  - `sudo apt install ./<file>.deb` 

### 3rd Containarization

- `sudo apt-get -y install podman`
- test installation: 
  - `podman build -t hello https://github.com/containers/PodmanHello.git` 
  - `podman run -it hello`


### 4th PostgreSql, PGAdmin, and PGVector

- reference: [Install PostgreSQL](https://documentation.ubuntu.com/server/how-to/databases/install-postgresql/)

  - steps:
    - `sudo apt install postgresql` 
    - edit: `/etc/postgresql/16/main/postgresql.conf`
      - locate and change: `#listen_addresses = 'localhost'` for `listen_addresses = '*'`
    - edit: `/etc/postgresql/16/main/pg_hba.conf`
      - locate and change: `# hostssl  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]` for `hostssl all all   0.0.0.0/0   scram-sha-256`
    - change postgres user password
      - `sudo -u postgres psql template1`
      - `ALTER USER postgres with encrypted password 'your_password';`
    - restart service
      - `sudo systemctl restart postgresql.service`
      
- reference: [PGAdmin setup](https://www.pgadmin.org/download/pgadmin-4-apt/) 

  - steps:
```bash
    # Install the public key for the repository (if not done previously):
    curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

    # Create the repository configuration file:
    sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

    #
    # Install pgAdmin
    #

    # Install for both desktop and web modes:
    sudo apt install pgadmin4
```

### 5th Enable Windows RDP access

- steps:

```bash
sudo apt update
# Install xrdp
sudo apt install -y xrdp

# Configure the firewall
sudo ufw allow 3389
sudo ufw reload

# Start and enable xrdp
sudo systemctl enable xrdp
sudo systemctl start xrdp

``` 





