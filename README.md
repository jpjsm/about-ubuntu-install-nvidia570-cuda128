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

    - For Dell XPS-8950: press [F12]

1. Insert USB drive in computer, turn-on, and press the key to select USB drive

1. Follow instructions on screen

1. Check computer boots correctly

    - > ⚠️***WARNING***⚠️: Don't run `sudo apt upgrade -y` until all NVIDIA and
        CUDA   drivers are installed, and a `hold` is placed on them.
    - > ⚠️***WARNING***⚠️: Disable all automatic updates !!
        - > To avoid NVIDIA drivers to be updated
        - > Only security updates are to be allowed
    - Uninstall the Unattended-Upgrades Package: `sudo apt remove unattended-upgrades`
    - Check Unattended-Upgrades is removed: `sudo systemctl status unattended-upgrades`

1. Install GPU

1. Check computer boots correctly *(again)*

## Install Software

### 1st. Install SSH Server and disable sleep modes

#### SSH server

```sh
    sudo apt update
    sudo apt install openssh-server
    sudo systemctl enable ssh
```

#### Disable sleep modes

```bash
    sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
    sudo powerprofilesctl set performance

    # Reboot to make system changes available
    sudo reboot now
```

### 2nd Enable Windows RDP access

- steps:

```bash
sudo apt update
# Install xrdp
sudo apt install -y xrdp

# Install XFCE
sudo apt install -y xfce4 xfcd4-goodies

# If asked about package manager (`gdm3` or `lightdm`)
# Choose `lightdm`

echo "startxfce4" > ~/.xsession

# Force presentation mode in XFCE (aka no-sleep, no-suspend, etc.) for all users
# --> remote sessions should not hang-out on anyone
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/presentation-mode -T
sudo cp ~/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-power-manager.xml /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/
sudo chown root:root /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-power-manager.xml
sudo chmod 644 /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-power-manager.xml

# Configure the firewall, if active
sudo ufw status
# sudo ufw allow 3389
# sudo ufw reload

# Start and enable xrdp
sudo systemctl enable xrdp
sudo systemctl start xrdp

# Reboot
sudo reboot now
```

### 3rd. NVIDIA 570 and CUDA 12.8

This is the last driver made native with nvcc 12.8; which makes it the optimal
one to be installed for the current GPU card: RTX 4060 Ti.

See: [NVIDIA 570 driver install](<./NVIDIA-570-driver-install.md>)

### 4th. Data disk and global configs

#### Connect DATA disk

- Using `Disks` gui-app, mount DATA disk under `/mnt/data`
- Create links to access DATA folders

```sh
    sudo ln -s /mnt/data/developers /developers
    sudo ln -s /mnt/data/learning /learning
    sudo ln -s /mnt/data/medusa /medusa
    sudo ln -s /mnt/data/models /models
    sudo ln -s /mnt/data/nltk_data /nltk_data
    sudo ln -s /mnt/data/repos /repos
    sudo ln -s /mnt/data/sample_data /sample_data
    sudo ln -s /mnt/data/shared /shared
    sudo chown -Rv jp:jp /mnt/data/{developers,learning,medusa,models,nltk_data,repos,sample_data,shared}
    sudo chmod -Rv 770 /mnt/data/{developers,learning,medusa,models,nltk_data,repos,sample_data,shared}
```

#### Global config download areas for AI frameworks

- Add `NLTK_DATA=/nltk` to `/etc/environment`
- Add `HF_HOME=/models/huggingface` to `/etc/environment`

### 5th. Conda-Forge, GIT, GITHUB, and VS Code

#### Conda

- See [conda-forge downloads](https://conda-forge.org/download/)
  - From a browser select the correct installer and download it
  - from terminal, in the same folder the downloaded file is: `bash Miniforge3-$(uname)-$(uname -m).sh`

#### GIT

- `sudo apt update`
- `sudo apt install -y git`
- `git config --global user.email "juanpablo.jofre@live.com"`
- `git config --global user.name "Juan Pablo 'JP' Jofre @ xps-8950"`

#### GITHUB

- Generate user key `ssh-keygen -t ed25519 -C "juanpablo.jofre@live.com" -f jp_github`
- Add key to key-agent:
  - Edit `.bashrc` and add:
    - `eval "$(ssh-agent -s)"`
    - `ssh-add ~/.ssh/jp_github`
- Add public key to Github user keys, see: [Adding a new SSH key to your GitHub account](<https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?platform=linux&tool=webui>)
- Test SSH Key access: `ssh -T git@github.com`

#### VS Code

- Download [.deb package (64-bit)](<https://go.microsoft.com/fwlink/?LinkID=760868>)
- `sudo apt install ./<file>.deb`

### 5th. Containarization

- `sudo apt-get -y install podman`
- test installation:
  - `podman build -t hello https://github.com/containers/PodmanHello.git`
  - `podman run -it hello`

### 6th. PostgreSql, PGAdmin, and PGVector

#### PostgreSql Server

- reference: [Install PostgreSQL](<https://documentation.ubuntu.com/server/how-to/databases/install-postgresql/>)

  - steps:
    - `sudo apt -y install postgresql`
    - edit: `/etc/postgresql/16/main/postgresql.conf`
      - locate and change: `#listen_addresses = 'localhost'` for `listen_addresses = '*'`
    - edit: `/etc/postgresql/16/main/pg_hba.conf`
      - locate and change: `# hostssl  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]` for `hostssl all all   0.0.0.0/0   scram-sha-256`
    - change postgres user password
      - `sudo -u postgres psql template1`
      - `ALTER USER postgres with encrypted password 'your_password';`
    - restart service
      - `sudo systemctl restart postgresql.service`

#### Postgresql Client (mostly needed for python access to DB)

- `sudo apt -y install postgresql-client`

#### PGAdmin

> reference: [PGAdmin setup](<https://www.pgadmin.org/download/pgadmin-4-apt/>)

##### steps

```bash
    sudo apt-get -y install curl
    # Install the public key for the repository (if not done previously):
    curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

    # Create the repository configuration file:
    sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

    #
    # Install pgAdmin
    #

    # Install for both desktop and web modes:
    sudo apt -y install pgadmin4
```

#### PGVector

> reference: [PGVector install](<https://github.com/pgvector/pgvector?tab=readme-ov-file#apt>)

##### Install package

```sh
sudo apt install postgresql-16-pgvector
```

##### Enable PGVector in desired Database

> ⚠️***WARNING***⚠️: Needs to be done in each database where vector searches are desired

- [Getting Started](<https://github.com/pgvector/pgvector#getting-started>)

### 7th. EXIF tool
