## Node System Configuration
We want a basic system configuration for the grid node elements. What we want:
* applying SD card overclocking  
To do this, just add the line `dtoverlay=sdhost,overclock_50=100` to `/boot/config.txt`
* start up an ssh daemon  
you must add an empty file in `boot/ssh`

Both can be done with commands (like):
```
touch /Volumes/boot/ssh
echo "dtoverlay=sdhost,overclock_50=100" | cat >> /Volumes/boot/config.txt
```
..if the sd card is mounted on /Volumes/boot

* changing the node host name  
to do that, boot up the image with a Raspberry Pi and then edit the file `/etc/hostname`

For our cluster we want different host names for each node. We define that every node shall be named with names like
`node00`, `node01`, `node02`, `node03`, .. `node15` for a 16-node cluster.

Our cluster shall have a management node, one which has the purpose to do a steering of the other nodes.
That node shall be the node `node00`.

## Node Accounts
The Raspberry Pi has the default user `pi` and the default password `raspberry`. We can therefore use this with a clear-text ssh command to do the basic configuration steps. To do so, you need `sshpass`. Install it with
```
apt-get install sshpass
```
or  on a Mac with
```
brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb
```
You can now log into your Raspberry Pi with the command
```
sshpass -p raspberry ssh pi@raspberrypi.local
```
We do so for node `node00` and configure passwordless access for all other nodes in the cluster.
We create a key pair on `node00` and distribute that key to all other cluster nodes.
Create the key pair with
```
ssh-keygen
```
The created key can be copied to another node `nodeXX` with
```
cat ~/.ssh/id_rsa.pub | ssh pi@nodeXX.local 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```
Because we want to do this passwordless, call
```
cat ~/.ssh/id_rsa.pub | sshpass -p raspberry ssh pi@nodeXX.local 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```
This must be done with all nodes.
