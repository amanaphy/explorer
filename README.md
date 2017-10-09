Iquidus Explorer - 1.6.1
================

An open source block explorer written in node.js.

### Requires

*  **Ubuntu 14.04 server**
*  node.js >= 0.10.28
*  mongodb 2.6.x
*  *maod

------

### Install some deps

$ sudo apt-get install unzip git build-essential libssl-dev libboost-all-dev libqrencode-dev libdb++-dev libminiupnpc-dev qt-sdk -y

git clone mao

$ git clone https://github.com/mao-zedongx/mao-zedong.git

$ cd mao-zedong/src

$ make -f makefile.unix

Start maod

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


- You can add nodes if you want - addnode=

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


Start mongodb:

    $ mongo

Create databse:

    > use explorerdb

Create user with read/write access:

    > db.createUser( { user: "iquidus", pwd: "3xp!0reR", roles: [ "readWrite" ] } )


Croot

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

*I have filled these in, so unless you change password or user for mongo, they are ready*

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

Copy paste these into the cron at the bottom:

 */1 * * * * cd /root/explorer && /usr/bin/nodejs scripts/sync.js index update > /dev/null 2>&1
 */2 * * * * cd /root/explorer && /usr/bin/nodejs scripts/sync.js market > /dev/null 2>&1
 */5 * * * * cd /root/explorer && /usr/bin/nodejs scripts/peers.js > /dev/null 2>&1
 
 Exit
 
 $ Ctrl + X
 
 
 -------


### Start the explorer

$ cd explorer

$ screen -S explorer
$ npm start

$ Ctrl + A + D (to quit the running process)

If you do not have **screen**

$ sudo apt-get install screen

------




**IQUIDUS BELOW**

You can hopefully ignore all of it

-----
-----
-----
-----

As of version 1.4.0 the explorer defaults to cluster mode, forking an instance of its process to each cpu core. This results in increased performance and stability. Load balancing gets automatically taken care of and any instances that for some reason die, will be restarted automatically. For testing/development (or if you just wish to) a single instance can be launched with

    node --stack-size=10000 bin/instance

To stop the cluster you can use

    npm stop

### Syncing databases with the blockchain

sync.js (located in scripts/) is used for updating the local databases. This script must be called from the explorers root directory.

    Usage: node scripts/sync.js [database] [mode]

    database: (required)
    index [mode] Main index: coin info/stats, transactions & addresses
    market       Market data: summaries, orderbooks, trade history & chartdata

    mode: (required for index database only)
    update       Updates index from last sync to current block
    check        checks index for (and adds) any missing transactions/addresses
    reindex      Clears index then resyncs from genesis to current block

    notes:
    * 'current block' is the latest created block when script is executed.
    * The market database only supports (& defaults to) reindex mode.
    * If check mode finds missing data(ignoring new data since last sync),
      index_timeout in settings.json is set too low.


*It is recommended to have this script launched via a cronjob at 1+ min intervals.*

**crontab**

*Example crontab; update index every minute and market data every 2 minutes*

    */1 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/sync.js index update > /dev/null 2>&1
    */2 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/sync.js market > /dev/null 2>&1
    */5 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/peers.js > /dev/null 2>&1

### Wallet

Iquidus Explorer is intended to be generic so it can be used with any wallet following the usual standards. The wallet must be running with atleast the following flags

    -daemon -txindex

### Donate

    BTC: 168hdKA3fkccPtkxnX8hBrsxNubvk4udJi
    JBS: JZp9893FMmrm1681bDuJBU7c6w11kyEY7D

### Known Issues

**script is already running.**

If you receive this message when launching the sync script either a) a sync is currently in progress, or b) a previous sync was killed before it completed. If you are certian a sync is not in progress remove the index.pid from the tmp folder in the explorer root directory.

    rm tmp/index.pid

**exceeding stack size**

    RangeError: Maximum call stack size exceeded

Nodes default stack size may be too small to index addresses with many tx's. If you experience the above error while running sync.js the stack size needs to be increased.

To determine the default setting run

    node --v8-options | grep -B0 -A1 stack_size

To run sync.js with a larger stack size launch with

    node --stack-size=[SIZE] scripts/sync.js index update

Where [SIZE] is an integer higher than the default.

*note: SIZE will depend on which blockchain you are using, you may need to play around a bit to find an optimal setting*

### License

Copyright (c) 2015, Iquidus Technology  
Copyright (c) 2015, Luke Williams  
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of Iquidus Technology nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
