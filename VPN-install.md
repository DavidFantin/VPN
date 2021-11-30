# Installing the VPN
## Creating a DigitalOcean account

Sign up for a new account using the URL:
- https://m.do.co/c/4d7f4ff9cfe4 

## Creating a DigitalOcean Ubuntu 20.04 droplet

Create the cheapest droplet ($5/month)
- Ubuntu 20.04
- Basic
- Regular Intel CPU

### Create SSH key

1. Open Powershell
2. Generate a new SSH Key
    - Command: ssh-keygen
3. Save it to a new file
4. Create a password for that file
5. Read the contents of the file
    - Command: cat *path_to_file*//*filename*.pub
6. Copy the contents into the SSH key creator and create the droplet

## Installing Docker on droplet
(Note: I am following the instructions from: https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/)

1. Install necessary tools:
    - Command: 
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```
2. Add Docker key:
    - Command: 
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
3. Add Docker repo:
    - Command: 
```    
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
4. Switch to correct repo:
    - Command: 
```
apt-cache policy docker-ce
```
5. Install Docker:
    - Command: 
```
sudo apt install docker-ce -y
```

### Install Docker-Compose
1. Installation:
    - Command: 
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
2. Set permission:
    - Command: 
```
sudo chmod +x /usr/local/bin/docker-compose
```
## Installing Wireguard using docker-compose
(Note: I am following the instructions from: https://thematrix.dev/setup-wireguard-vpn-server-with-docker/)

1. Launch Droplet Console from the "Access" tab under the droplet that was just created
2. Create *wireguard* directory in home directory
    - Command: mkdir -p ~/wireguard/
3. Create *config* subdirectory
    - Command: mkdir -p ~/wireguard/config/
4. Create and edit a file called *docker-compose.yml*
    - Command: nano ~/wireguard/docker-compose.yml
    - Paste the following into the docker-compose.yml file:
```
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=__TIMEZONE__
      - SERVERURL=__DROPLET_IP-ADDRESS__
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```
5. Replace __TIMEZONE__ with the desired timezone
6. Replace __DROPLET_IP-ADDRESS__ with the IP Adress assigned to your droplet
7. Start Wireguard
    - Command:
```
cd ~/wireguard/
docker-compose up -d
```
## Test your VPN connection
### Test your VPN connection on a mobile device
1. View the execution log and generate QR code:
    -Command: docker-compose logs -f wireguard
2. Download the "WireGuard" app from the Google Play Store
3. Open Wireguard app on your phone
4. Click + >> Create from QR code
5. Scan one of the QR codes generated on the screen to connect to the VPN
    - Note: I scanned the QR code generated for the "phone1" peer
### Test your VPN connection on a laptop
1. Install WireGuard program:
    - Download link: https://www.wireguard.com/install/
2. Find the config file for the user you want to connect to (in my case I cose to connect to peer_pc1)
    - Command: cd ~/wireguard/config/*User_you_want*
3. Open the *User_you_want*.conf file with nano (for me it was peer_pc1.conf)
    - Command: nano *User_you_want*.conf (for me: nano peer_pc1.conf)
3. Copy all the contents of the file into a new file on the computer you want to connect to the VPN with
4. Launch the WireGuard program you just installed
5. Select the "Add Tunnel" button and open the new .conf file you just created
6. Activate the connection and you are finished!


    
    
    
    
    
    
    
    
    
    
    
