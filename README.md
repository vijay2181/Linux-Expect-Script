# Linux-Expect-Script

- You have scenario where you need to login to registy

```
method 1:
----------
podman login docker-eu.artifactory.vijay.com/vijay-docker-local
username:
password:
- here it will ask username and password, but the thing is it will save your credentials.
  Credentials will be cached in memory or managed by Podman’s internal mechanisms.
  so for next login sessions it won't prompt you for username and password untill token expires.

method 2:
---------
ARTI_USER=vijay
ARTI_PASS=xxxx
ARTI_SERVER=docker-eu.artifactory.vijay.com/vijay-docker-local

podman login -u "$ARTI_USER" -p "$ARTI_PASS" "$ARTI_SERVER"



root@jenkins-master1:~# podman login -u "$ARTI_USER" -p "$ARTI_PASS" "$ARTI_SERVER"
Login Succeeded!

root@jenkins-master1:~# podman login docker-eu.artifactory.vijay.com/vijay-docker-local
Authenticating with existing credentials for docker-eu.artifactory.vijay.com/vijay-docker-local
Existing credentials are valid. Already logged in to docker-eu.artifactory.vijay.com/vijay-docker-local
```

- first method is interactive/manual way
- second method is automated way

- But if you ran into a situation where you need to surely use first method, but in a automated way of passing username and password, then we can go for expect script


```
sudo apt-get install expect

touch script-expect.sh
chmod +x script-expect.sh
vi script-expect.sh

```

```
#!/usr/bin/expect -f

set timeout -1
set timeout -1
set username "vijay"
set password "xxxxx"
set registry "docker-eu.artifactory.vijay.com/vijay-docker-local"

spawn sudo podman login $registry
expect "Username:"
send "$username\r"
expect "Password:"
send "$password\r"
expect eof

```

```
./script-expect.sh
```


- exception Handle


  
```
#!/usr/bin/expect -f
set timeout -1
set username "vijay"
set password "xxxxx"
set registry "docker-eu.artifactory.vijay.com/vijay-docker-local"

spawn sudo podman login $registry
expect {
    "Username:" {
        send "$username\r"
        exp_continue
    }
    "Password:" {
        send "$password\r"
        exp_continue
    }
    "Login Succeeded!" {
        # If login succeeded, exit successfully
        exit
    }
    "Already logged in" {
        # If already logged in, exit successfully
        exit
    }
    timeout {
        # If there's a timeout, exit with an error
        exit 1
    }
}

```
