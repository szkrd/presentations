# Getting started with Deis

---
---

* Deis is an open source platform as a service (PaaS)
* makes it easy to deploy and manage applications on your own servers
* builds upon Docker + CoreOS

---
---

## Setup

`curl -sSL http://deis.io/deis-cli/install.sh | sh`

* the install will put the Deis client into the current directory.
  You have to move it to a directory which is on the PATH
* or just brew install deis.

---
---

## Using the client

* it has comprehensive documentation for every command
* use `deis help` or `deis -h` to explore the commands
* with any command you can use --help, like `deis users -h`

---
---

## Connecting with a Deis cluster

Our application domain for the Deis cluster is *foobar.com*.

### Register (create Deis user)

`deis register https://deis.foobar.com`
(will ask for username, password, email)

After registration check the list of applications: `deis apps:list`
(This list might be empty (or rather short), be sure to ask for proper permissions from devops)

Add your public key: `deis keys:add ~/.ssh/id_rsa.pub`

You can log out with `deis logout`,
and login with `deis auth:login https://deis.foobar.com`

---
---

## Deploying your first application

1. create a git repository, put some code in it, like you would start with any heroku application
2. add Dockerfile
3. `deis create <appname>`
  (if you want to use a custom buildpack, you can set it here using the --buildpack flag)
4. `git push deis master` (if you are not using codeship)

---
---

## The complete workflow

---
---

## 1
### Create both the staging app and the production app

* `deis create my-app`
* `deis create my-app-staging`

Later on you must specify which app you are working with (adding env vars for example) with the --app parameter.

---
---

## 2
### Expose your app to https://my-app.foobar.com

touch ~/bin/create-cname.sh && chmod +x ~/bin/create-cname.sh

```bash
#!/bin/bash
echo "Type the name of the application:"
read appname
echo "Creating CNAME for $appname.foobar.com"
curl -X POST "https://api.cloudflare.com/client/v4/zones/12345/dns_records" \
-H "X-Auth-Email: devops@foobar.com" \
-H "X-Auth-Key: 12345" \
-H "Content-Type: application/json" \
--data '{"type":"CNAME","name":"'$appname'.foobar.com","content":"deis-deiswebelb-123-456.eu-west-x.elb.amazonaws.com","ttl":1, "proxiable": true, "proxied": true}'
```

---
---

## 3
### Adding it to Codeship

1. copy Codeship's SSH public key (ssh key under general in CodeShip)
2. save it to a temp file: `pbpaste > codeship.pub`
3. logout and login with the codeship user (password)
4. add the key: `deis keys:add ./codeship.pub && rm ./codeship.pub && deis logout`
5. add custom deploy script on Codeship UI

```bash
git fetch --unshallow || true
git fetch origin "+refs/heads/*:refs/remotes/origin/*"
# deploy
git remote add deis ssh://git@deis.foobar.com:2222/my-app-staging.git
git push deis master -f
```

---
---

## 4
### Let codeship push here

1. now login with your user (you must have superuser rights)
2. `deis perms:create codeship -a my-app`
3. `deis perms:create codeship -a my-app-staging`

---
---

## 5
### Promoting to production

* List releases: deis releases -a my-app-staging
* Deis has a "private docker hub", hence the static IP: `deis pull 10.21.1.231:5000/my-app-staging:vXX -a my-app`

---
---

## 6
### Setting environment variables

Create an .env file:
```
KEY=VALUE
ANOTHER_KEY=another_value
```

Save it to deis: `deis config:push -a my-app`

Rinse and repeat for staging. Throw away the .env file to prevent confusion, you can download it later with `deis config:pull -a my-app -o` (o is not output, but overwrite).

Add `*.env` to your `~/.gitignore_global`!

---
---

## 7
### Initialize docker process

* Dockerfile app is a 'cmd' process type, 1 is the number of processes allowed.
* `deis ps:scale cmd=1 -a my-app-staging`
* (cmd=0 halts the container, but deis ps:reset -a my-app-staging may be more useful)

---
---

## 8
### Rollbacks

Check releases, then: `deis releases:rollback vXX -a my-app-staging`
Which might take quite some time.

---
---

## Troubleshooting

* The *Dockerfile* MUST contain the **NPM_TOKEN**, since deis env vars are for the running node application, not for the docker container deployment.
* Check the **application log** with `deis logs -a my-app-staging`, if it is empty or only the env vars are there then it means that the docker container couldn't start.
* If the docker container **didn't start**, you should try it locally (see docker page), do not forget to manually add all the env vars from deis (pull it to a .env file first).
* Always **scale up the docker process** using `ps:scale cmd=1`, this way `ps:list` has a more meaningful output. Use `ps:restart` to restart the container.
