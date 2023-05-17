
# Linux Agent

A Linux agent can build and deploy different kinds of apps, including Java and Android apps. Microsoft support Ubuntu, Red Hat, and CentOS.



## Prepare permissions

As a one-time step, you must register the agent [Description](https://learn.microsoft.com/en-us/azure/devops/organizations/security/about-security-roles?view=azure-devops#agent-queue-security-roles)

  
## Authenticate with a personal access token (PAT)

- Sign in with the user account you plan to use in your Azure DevOps organization (https://dev.azure.com/{your_organization}).
- From your home page, open your user settings, and then select Personal access tokens.
- Create a personal access token.
- Tüm platformlara destek

  
## Download and configure the agent

- Log on to the machine using the account for which you've prepared permissions as explained above.
- in your web browser, sign in to Azure Pipelines, and navigate to the Agent pools tab:
- Choose Azure DevOps, Organization settings.
- Choose Agent pools.
- Select the Default pool, select the Agents tab, and choose New agent.
- On the Get the agent dialog box, click Linux.
- On the right pane, click the Download button.
- Follow the instructions on the page.
- Unpack the agent into the directory of your choice. cd to that directory and run ./config.sh.

## Server URL
Azure Pipelines: https://dev.azure.com/{your-organization}

## Run as a systemd service
If your agent is running on these operating systems you can run the agent as a systemd service:
Change to the agent directory

```bash
  cd ~/myagent$
```

install
```bash
  sudo ./svc.sh install [username]
```
this command creates a service file that points to ./runsvc.sh. This script sets up the environment (more details below) and starts the agents host. If username parameter is not specified then the username is taken from the $SUDO_USER environment variable which is set by sudo command. This variable is always equal to the name of the user who invoked the sudo command.

start
```bash
  sudo ./svc.sh start
```

status
```bash
  sudo ./svc.sh status
```

stop
```bash
  sudo ./svc.sh stop
```

uninstall
```bash
  sudo ./svc.sh uninstall
```

rm -r -f 14

df -h --total


sudo apt-get install git


## Docker Engine Set up the repository
Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

```bash
  sudo apt-get update
  sudo apt-get install ca-certificates curl gnupg
```

Add Docker’s official GPG key:

```bash
  sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```


Use the following command to set up the repository:
```bash
  echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## Install Docker Engine
Update the apt package index:

```bash
  sudo apt-get update
```

To install the latest version, run:
```bash
  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify that the Docker Engine installation is successful by running the hello-world image.

```bash
  sudo docker run hello-world
```


sudo usermod -a -G docker $USER
sudo chown root:docker /var/run/docker.sock
chmod 777 /var/run/docker.sock