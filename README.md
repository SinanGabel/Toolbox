# Linux & nodejs tips

## linux: cron

* A common error is not to set PATH correctly because cron runs at default with a very limited PATH and env.

```
# first view your env in terminal
$ env

# now create a env check in cron
$ crontab -e
* * * * * env >> env.txt

# By comparing the two above you will see a big difference in the two environments
# A solution is e.g. to place a PATH within each shell script e.g.

    #!/bin/bash
    PATH=/bin:/usr/bin:/usr/local/bin:/snap/bin:$PATH
    
# Use whereis to find your required command path 
# $ whereis [command]    

```

## nodejs: debug nodejs with chrome browser

* Open Chrome browser and devtools

```
# 1. Open Chrome browser and type in URL: chrome://inspect/#devices

# 2. Click on link: "Open dedicated DevTools for Node"

# 3. In terminal type
node --inspect-brk index.js

```

* That is it, you are now in Chrome developer mode, and your index.js file is open for debugging etc.

## nodejs: handle mix of callback and async functions

Wrap the "traditional" callback functions in a function that returns a Promise e.g.

```
// Example

const fs = require("fs");

function fun1(str) {
    
    return new Promise((resolve, reject) => {
        fs.writeFile("./tmp.txt", "some text", "utf8", (err) => {

            if (! err) { 
                resolve(str); 
                console.log("fun1 ok message, time: ", Date.now());
                
            } else { 
                reject("explain failure");
                console.error("fun1 error message");
            }
        });
    });
}

async function fun2() {

    let v = await fun1("calling");

    console.log("fun2, time: ", Date.now());

    return v;
}

fun2().then((v) => {/* do something with v */}).catch((e) => {/* handle error */});


```

## nodejs: large files line-by-line

```
"use strict";

// https://nodejs.org/api/fs.html#fs_fs_createwritestream_path_options

const fs = require('fs');
const readline = require('readline');

const rl = readline.createInterface({
  input: fs.createReadStream('path/big-file-in.csv', {encoding: "utf8"}),
  output: fs.createWriteStream('path/big-file-out.csv'),                                  
  crlfDelay: Infinity
});

rl.on('line', (line) => {
  
    // process line prior to writing it out
    
    rl.output.write(line + "\n");
    
    // console.log(`Line from file: ${line}`);
});
```

## sqlite3

* Note difference between real numbers and integers.

select 1/10  = 0
select 1/10. = 0.1
select 1./10 = 0.1


# linux: some commands

* They have been used on Linux Debian or Ubuntu, various versions.

* Some require root access, simply use sudo per command or 'sudo -i' once and then commands

* WARNING: be careful with some of the commands, best read about them elsewhere first!

```

# use > to write to a file and use >> to append to a file
echo "some text" >  file.txt
echo "some text" >> file.txt

# Add text to beginning and/or end of text file_old and output as file_new
(echo 'some text at beginning' ; cat file_old ; echo 'some text at end') > file_new

# compare text files line by line => use commands comm or diff depending on usage, see documentation
man comm
man diff

# Ubuntu apt-get command
# see also: https://help.ubuntu.com/community/AptGet/Howto 

# List file placements in couchdb (which is an open source apache database)
dpkg -L couchdb

# Check package version installed
dpkg -s openssl

# List (current) upgradable packages
apt list --upgradable

# Upgrade a specific package
apt-get install openssl

# Check Linux or Linux kernel version:
lsb_release -a
cat /proc/version
uname -a
cat /etc/os-release

# Upgrade to new Linux release:
do-release-upgrade

# Need to reboot?
if [ -f /var/run/reboot-required ]; then echo 'Restart required'; fi

# Check which services start at boot time
systemctl list-units --type service

# How to kill an application by name:
sudo killall -9 scp

# 10 oldest processes
ps -elf | sort -r -k12 | head -n 10

# Kill a process by <pid>
kill <pid>

# Reboot Linux:
sudo reboot

#Power off:
poweroff, halt or shutdown -h now

# Ports:
netstat -ntlp | grep LISTEN

# DNS
nslookup -query=ns google.com
nslookup google.com
nslookup -query=mx google.com

# More at: http://www.thegeekstuff.com/2012/07/nslookup-examples/
internet listen
netstat -l

# Internet connections
netstat -plant

# My terminals IP address
wget -qO - http://ipecho.net/plain; echo

# Disk size
df -H

# Sum of .csv files sizes (in current directory)
du -hc *.csv | tail -1

# Count # of lines in file
wc -l <filename>

# Count # of lines in *.csv files in current directory
wc -l *.csv

# Count # of files in directory
ls -1 | wc -l

# bash script: Loop over files (*.csv) in current directory (.) and
# do something with each file (file -i) e.g. list the encoding of each file
    #!/bin/bash
    for xx in ./*.csv; do
        file -i "$xx"
    done

# bash script function: difference between return and exit
# return -> returns a value from a function
# exit -> abandons the current shell

# Compress files and folders in directory to named file backup.tgz’
tar -cvzf backup.tgz *

# Uncompress the above tar file. Flag f must be the last flag
tar -xvzf backup.tgz

# check IP addresses
nslookup riskbutler.com

# Memory check
cat /proc/meminfo 

# A simple CPU speed test - calculation of Pi with (eg.) 1000 decimals
time echo "scale=1000; 4*a(1)" | bc -l &

# Services
service --status-all 

# Linux node/system limitations
# soft limits are simply the currently enforced limits
# hard limits mark the maximum value which cannot be exceeded by setting a soft limit
ulimit -a

# Tell who current user is: print userid
whoami

# List all users 
cut -d: -f1 /etc/passwd

# delete a user
userdel user_name

# --- make a user with restricted access ---
ln -s /bin/bash /bin/rbash

useradd -d /home/user_name -s /bin/rbash -m user_name
passwd user_name

mkdir /home/user_name/bin

chown root. /home/user_name/.profile
chmod 755 /home/user_name/.profile

## set PATH
nano /home/user_name/.profile

--- .bash_profile ---
PATH=$HOME/bin
---

## allow above user: "ls", "mkdir", "ping" commands
ln -s /bin/ls /home/user_name/bin/ls
ln -s /bin/mkdir /home/user_name/bin/mkdir
ln -s /bin/ping /home/user_name/bin/ping

--- end of: make a user with restricted access ... ---

# Users of processes
ps aux

# local a command e.g. nodejs
which nodejs

# Check if folder exists, if not then create folder
[ ! -d /path_to_folder/folder ] && mkdir -p /path_to_folder/folder

# Find folder named “radar”
find / -name "radar"

# Find file named textfile.txt
find / -name textfile.txt

#Find files not named jquery-ui*.css
find . -type f -not -name 'jquery-ui*.css'

# Delete files not named logo.png
find . -type f ! -name 'logo.png' -delete
delete a lot of files
find . -name "*.pdf" -delete

# Rename all files with ending .txt to .csv
# [optionally] To find placement of perl command rename: $ which rename => /usr/bin/rename
rename 's/txt/csv/' *.txt

# Add text in more.txt to the end of a text file orig.txt
cat more.txt >> orig.txt

# convert test.json file to test.csv 
cat test.json | jq -r '(map(keys) | add | unique) as $cols | map(. as $row | $cols | map($row[.])) as $rows | $cols, $rows[] | @csv' > test.csv


# Find and replace
# http://unix.stackexchange.com/questions/112023/how-can-i-replace-a-string-in-a-files 
# http://www.brunolinux.com/02-The_Terminal/Find_and%20Replace_with_Sed.html 
cat filename | sed  's/oldstring/newstring/g'  >  newfilename  
… >> filename
echo ‘oldstring’ | sed ...

# Look for particular string in a group of files
# ... this finds all cases of the string “echo” in the .php files in all folders from “.”
# note: w is to match whole word (not only part of text), and l is to only return the file name
grep -rn --color 'echo' *.php
grep -rnw '/path/to/somewhere/' -e "pattern"

# get the difference between two files e.g. two .csv files, remove the "+" signs and the first line, output to diff.csv
diff -u old.csv new.csv | grep -e "^\+" | sed -e 's/^\+//' | sed -e'' 1d > diff.csv

# delete first line in text file
sed -i'' 1d file

# insert line first in text file
sed -i -e 1i"some text for first line in file" file

# Copy directory but exclude a directory within
rsync -av --progress sourcefolder /destinationfolder --exclude thefoldertoexclude    

# Check encoding of file
file -i YourFile.txt

# Change encoding of text file f: from t: to o: output
iconv -f ISO_8859-1 -t UTF-8 -o YourNewFile.txt YourOldFile.txt 

# ... list all iconv known encodings
iconv -l

# ... combine with curl
curl URL | iconv -f ISO_8859-1 -t UTF-8 > YourCurlDownloadedFile.txt

# curl post file
curl -X POST -H "Content-Type: application/json" -d @FILENAME DESTINATION

# Check date of ssl certificate validity
# returns eg:
# notBefore=Jan  8 09:57:51 2014 GMT
# notAfter=Jan  8 09:57:51 2015 GMT
# Note: I personally now use Let's Encrypt and its auto cron job that fully automates updates
# read more at: https://letsencrypt.org/
echo | openssl s_client -connect site:port 2>/dev/null | openssl x509 -noout -dates

# Start graphical environment
startx

# Install desktop on Ubuntu
sudo apt-get install ubuntu-desktop

# Time
# https://help.ubuntu.com/community/UbuntuTime 
echo "UTC" | sudo tee /etc/timezone
sudo dpkg-reconfigure --frontend noninteractive tzdata

# is UTC time in seconds since 1970
date +%s

# Full date YYYY-MM-DD
date +%F

# Download file(s)
# http://www.thegeekstuff.com/2012/07/wget-curl/
# Upload file index.html to ftp server
curl --user username:password --ftp-ssl --ftp-pasv -T "index.html" ftp://ftp_server_address/

# tar and compress, and uncompress: j (bzip2) and z (gzip)
tar -cvjf ../yahoo.tar.bz2 yahoo*
tar cvzf MyImages.tgz /home/img/ 
tar -xvf thumbnails.tar.gz

# tar and compress many files
find . -name 'my_prefix*' > filelist.txt
tar czvf archive.tar.gz --files-from filelist.txt

# Working with strings and conversions
# Note the arrow and following text is not part of the command but shows expected output
YY="20160201"

echo ${YY:2:2}  => “16”

echo ${YY:0:4}"-"${YY:4:2}"-"${YY:6:2}  => “2016-02-01”

# The below also works on complete .csv and other text files i.e. simply pipe the full .csv file in "one go"

echo "YYYYMMDD" | sed -n -e "s_\(....\)\(..\)\(..\)_\2/\3/\1_p" => "MM/DD/YYYY"

echo "YYYYMMDD" | sed -n -e "s_\(....\)\(..\)\(..\)_\1-\2-\3_p" => YYYY-MM-DD

echo "YYYYMMDDHHMMSS" | sed -n -e "s_\(....\)\(..\)\(..\)\(..\)\(..\)\(..\)_\1-\2-\3 \4:\5:\6_p" => YYYY-MM-DD HH:MM:SS

echo "YYYYMMDD,HHMMSS" | sed -n -e "s_\(....\)\(..\)\(..\)\(.\)\(..\)\(..\)\(..\)_\1-\2-\3 \5-\6-\7_p" => YYYY-MM-DD HH-MM-SS

echo "20160201,235432" | sed -n -e "s_\(....\)\(..\)\(..\)\(.\)\(..\)\(..\)\(..\)_\1-\2-\3 \5:\6:\7_p" => 2016-02-01 23:54:32

```

## some git commands

```
# roll back Y number of commits

git reset --soft HEAD~Y

# then one can e.g. do 

git push origin master

```


## Move repo to github.com

```

# git push all changes on local drive

# create a new private repository on Github.com. It’s important to keep the repository empty, e.g. don’t check option Initialize this repository with a README when creating the repository.

# go to root of existing repo
git remote add upstream https://github.com/[USER]/[PROJECT].git
git push upstream master
git push --tags upstream

git remote rm origin
git remote add origin https://github.com/[USER]/[PROJECT].git


git config master.remote origin
git config master.merge refs/heads/master

# now check contents of path_to_repo/.git/config
less config

git push origin master

# delete old repo (not on local desktop machine but at other service provider e.g. bitbucket if the old repo is there)

```


