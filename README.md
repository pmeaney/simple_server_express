# simple_server_express

This project requires the following Repository Secrets to be updated(Github Secrets > Actions > Repository Secrets).


- DEV_GITHUB_USER
  - Set to pmeaney
  - Doesn't need update
- DEV_KEY_PRIVKEY_NOPASS_3_26_23
  - When we generated the Server with terraform, we provide in the terraform file a public key we use to boot the server (and then log into it-- via our laptop).  This is the ssh private key associated with that public key.
  - Needs update (If we intend to pull this onto a new server and is not the same as key pair where public key used in terraform script)
  - Note: I need to go back and check if it will work with a key pair which has a pw
- DEV_SERVER_IP
  - Needs update (If we intend to pull this onto a new server)
- DEV_SSH_USER
  - Doesn't need update (as long as its the same one as terraform script)
- GH_PAT_3_26_23
  - Doesn't need update

Several of the above items are used for the following:
- In a Github Action, github spins up a tempoary CICD Server to do some work for us: Package up our app into a Container, and Deploy our app to our server.
- So, we need that temporary Github Ubuntu Server to first be able to access our Hosting Server (e.g. Digital Ocean server).
- What we have to do first though, before we can access Hosting Server, is provide the necessary secrets & keys to the temporary Github CICD server.

```yml
- name: Take ssh key from github repo, copy it into the ubuntu VM, so we can access the server & download the docker image
  run: |
    mkdir -p ~/.ssh/
    echo "$SSH_PRIV_KEY" > ~/.ssh/id_ed25519
    chmod 600 ~/.ssh/id_ed25519
    cat >>~/.ssh/config <<END
    Host dev
      HostName $DEV_SERVER_IP
      User $SSH_USER
      IdentityFile ~/.ssh/id_ed25519
      StrictHostKeyChecking no
    END
  env:
    SSH_USER: ${{ secrets.DEV_SSH_USER }}
    SSH_PRIV_KEY: ${{ secrets.DEV_KEY_PRIVKEY_NOPASS_3_26_23 }}
    DEV_SERVER_IP: ${{ secrets.DEV_SERVER_IP }}
```