# opsmonster

Run a simple stack of nginx + uwsgi in Docker containers using letsencrypt certs.

Due to a very strict convention of service structure, new services can easily be added.

## Service Requirements

* Service must be a vuejs (or compatible) front-end using a RESTful Flask back-end.
* Back-end API URIs must begin with /api
* The only currently supported Docker Env variable (and is needed) is MONSTERKNIFE.  This is a really weird thing that will eventually go away.

## Initial Setup

### Create VM / Droplet / Whatever

Creating this is out of scope of this document.  Make sure you can ssh to it as root.

### Bootstrap VM

Edit `ansible_hosts`.  Add the IP/hostname of the server you want to bootstrap under [bootstrapservers]

Run:
```
ansible-playbook -i ansible_hosts bootstrap-vm.yaml
```

### Deploy Service(s)

Edit `ansible_hosts`.  Move the IP/hostname of the server from [bootstrapservers] to [deployservers]

Ensure DNS for BaseURL is configured to correctly point at the VM's IP.

For each service, do the following:

1. Create a <i>service</i>-vars file in the vars/ subfolder, including the following five variables (The values below are for exmample only, use your own values here):
```
BaseURL: housemonster.thex.link
GitHubUser: DranoTheCat
FrontEndServiceName: TheHouseMonster
APIServiceName: housemonsterapi
APIServicePort: 31080
MONSTERKNIFE: <presharedkey>
```

The FrontEndServiceName and APIServiceName *must* be the same as the GitHub Repo names for the respective GitHub repos.

2. Run:
```
ansible-playbook -i ansible_hosts --extra-vars "@vars/<service>-vars.yaml" deploy-new-service.yaml
```

### Update Service(s)

Going forward, you can update your services (from the GitHub repos) by running the following:
```
ansible-playbook -i ansible_hosts --extra-vars "@vars/<service>-vars.yaml" update-service.yaml
```

### TODO
* Need to enable cron to certbot renew
