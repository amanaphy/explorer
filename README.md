mao
===================


Iquidus Explorer - 1.6.1
================

An open source block explorer written in node.js.

NB: Much depends on your server.

- SSD cached
- At least 1 GB of memory
- A good idea to configure swap
[Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-14-04)
- use the same amount of swap memory as you have as RAM (e.g 1gb, 2gb)
- Using an open VZ is possible, but you have to set up the whole OS first, which takes ages

-----

### Requires

*  **Ubuntu 14.04 server**
*  node.js >= 0.10.28
*  mongodb 2.6.x
*  *maod

------

### Install some deps

    $ sudo apt-get install unzip git build-essential libssl-dev libboost-all-dev libqrencode-dev libdb++-dev libminiupnpc-dev qt-sdk -y
    
-----

**git clone mao**

    $ git clone https://github.com/mao-zedongx/mao-zedong.git
    
    $ cd mao-zedong/src
    
    $ make -f makefile.unix

**Start maod**

    $ ./maod

You get an error saying to make a .conf file. It will give you an rpcusername and rpcpassword. Copy paste them onto a text document.

    $ nano /root/.mao/mao.conf

// If you do not have **nano**

// $ sudo apt-get install nano

Make the mao.conf (you could use the exact details below, you do not need to use the ones given by the error message)

    rpcuser=maorpc
    rpcpassword=FeYNNEkueCMrdU7su9gtNQyaKeCQAR87fzRrjBkNxStS
    daemon=1
    server=1
    txindex=1
    rpcport=9669


- **You can add nodes if you want - addnode=**

Quit

Ctrl + X

Leave mao for now

    $ cd (and press return button)

-------



### Nodejs

node.js with PPA // **ignore depreciation messages**

    $ curl -sL https://deb.nodesource.com/setup | sudo bash -
    $ sudo apt-get install nodejs
    $ sudo apt-get install build-essential

-----


### Create database with Mongodb (these are settings for Ubuntu 14)

see also at: [mongodb](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

    $ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

    $ echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

    $ sudo apt-get update
    
    $ sudo apt-get install -y mongodb-org


-----


**Start mongodb**:

    $ mongo

Create databse:

    > use explorerdb

Create user with read/write access:

    > db.createUser( { user: "iquidus", pwd: "3xp!0reR", roles: [ "readWrite" ] } )


**Croot**

    $ Ctrl + C

------

### Get the explorer source

 

    git clone https://github.com/mao-zedongx/explorer.git explorer
    

### Update

    $ cd explorer
    $ npm update
    $ cd (return)


### Install node modules

    cd explorer && npm install --production
    

### Configure

    $ cd settings.json

**I have filled these in, so unless you change password or user for mongo, they are ready**

**I have also put a mao favicon in the public folder, and mao logo in the images folder**

-----

### Start mao

    $ cd (return)
    $ cd mao-zedong/src
    $ ./maod

-----

### cron

    $ cd (return)
    
    $ crontab-e

**Copy paste these into the cron at the bottom**:

 

    */1 * * * * cd /root/explorer && /usr/bin/nodejs scripts/sync.js index update > /dev/null 2>&1
     */2 * * * * cd /root/explorer && /usr/bin/nodejs scripts/sync.js market > /dev/null 2>&1
     */5 * * * * cd /root/explorer && /usr/bin/nodejs scripts/peers.js > /dev/null 2>&1

 
 **Exit**
 
 

    $ Ctrl + X

 
 
 -------


### Start the explorer

    $ cd explorer
    
    $ screen -S explorer
    $ npm start
    $ Ctrl + A + D // to quit the running process



**If you do not have** **screen**

    $ sudo apt-get install screen

------



**If you get errors and want to start again, completely remove mongodb**

    $ sudo service mongod stop
    $ sudo apt-get purge mongodb-org*
    $ sudo rm -r /var/log/mongodb
    $ sudo rm -r /var/lib/mongodb
    $ cd /etc/apt/sources.list.d/
    $ rm -r mongodb-org-3.4.list
    $ sudo apt-get update


The mongodb site forgets to tell you to remove the mongodb-org-3.4.list, buyt it is necessary if you want to reinstall mongodb.

------


[IQUIDUS INSTRUCTIONS](https://github.com/mao-zedongx/explorer/blob/master/IQUIDUS_INSTRUCTIONS.md)


-----


