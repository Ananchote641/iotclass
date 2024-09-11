# Install Server and Docker


## How to install Server
Step 1: Download Ubuntu Server ISO
You can download the Ubuntu Server ISO file from the official site of Ubuntu.

Step 2: Create a Bootable USB Drive
To create a bootable USB drive, use Etcher. First, install Etcher on Windows or MacOS.

Step 3: Boot the System From USB

Step 4: Language and Keyboard Configuration

Step 5: Choose Keyboard Layout
Select your preferred keyboard layout and variant using the UP and DOWN keys. Select “Done” and press ENTER.

Step 6: Choose the Installation Type
Choose Ubuntu Server as a base for the installation. Then, select “Done” and press ENTER.

Step 7: Network Configuration

Step 8: Configure the Storage
Opt for “Use an entire disk” to let the installer automatically create partitions on the entire disk.

Step 9: Configure Profile settings

Step 10: SSH Setup
Select “Install OpenSSH Server”. Then choose “Done” and hit ENTER.

Step 11: Finish Installation
When the installation is completed select “Reboot Now” and press ENTER.




## How to install Docker
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

```bash
# Install the Docker packages:
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

```bash
# Verify that the Docker Engine installation is successful by running the hello-world image
sudo docker run hello-world
```



