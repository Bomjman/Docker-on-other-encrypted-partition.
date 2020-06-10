## Docker on other encrypted partition.

For convenience, switch to superuser.

1. First you need to create the partition itself:

   ```
   cfdisk /dev/youDisk (example /dev/sda)
   ```

   Select the disk that we want to trim for the new one if there is no free space. Select "resize" and allocate disk space for the new one. Select "free space" and create a new disk. Then select "write" and enter yes.

2. Let's check if it was created:

   ```
   fdisk -l
   ```

   Install the program to encrypt the disk:

   ```
   apt-get install cryptsetup
   ```

3. Encrypting the disk:

   ```
   cryptsetup -y -v luksFormat /dev/sdaX
   ```
   
   Enter "YES" and come up with a password
   
   Open our encrypted disk and enter the password you previously created.
   
   ```
   cryptsetup luksOpen /dev/sdaX docker
   ```
   
   Checking the status:
   
   ```
    cryptsetup -v status docker
   ```
   
   Format the partition to ext4:
   
   mkfs.ext4 /dev/mapper/docker
   
4. Create a folder where we will mount the partition:

   ```
   mkdir /docker
   ```

   ```
   mount /dev/mapper/docker /docker
   ```

   ##########################################################################

   Disabling the partition

   ```
   umount /docker
   ```

   ```
   cryptsetup luksClose docker
   ```

   Re-mounting

   ```
   ryptsetup luksOpen /dev/sdaX docker
   ```

   ```
   mount /dev/mapper/docker /docker
   ```

5. Installing docker: 

   ```
   apt update
   ```

   ```
   apt install apt-transport-https ca-certificates curl software-properties-common
   ```

   ```
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

   ```
   add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
   ```

   ```
   apt update
   ```

   ```
   apt-cache policy docker-ce
   ```

   ```
   apt install docker-ce
   ```

   If you want to avoid typing `sudo` whenever you run the `docker` command, add your username to the `docker` group:

   ```
   sudo usermod -aG docker ${USER}
   ```

   To apply the new group membership,  log out of the server and back in, or type the following:

   ```
   sudo su - ${USER}
   ```

6. Moving docker to an encrypted partition:

   Creating a folder where docker will be located

   ```bash
   mkdir /docker/docker
   ```

   Stop docker 

   ```
   sudo service docker stop
   ```

   Copy docker directory

   ```
   cp -r /var/lib/docker/* /docker/docker
   ```

   Unmount all old docker overlays `umount -f /var/lib/docker/overlay/*/*` and `umount -f /var/lib/docker/containers/*/mounts/shm`

   Change in`/etc/docker/daemon.json` graph to new path

   ```
   {
     "graph": "/docker/docker/"
   }
   ```

   Remove old `/var/lib/docker/`

   ```
   rm -r /var/lib/docker
   ```

   Start docker 

   ```
   sudo service docker start
   ```

7. Install the kali linux image:

   ```
   docker pull kalilinux/kali-rolling
   ```

   

   
