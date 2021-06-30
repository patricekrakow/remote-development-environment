# My Remote Development Environment(s)

That's how I configure my remote development environment(s).

## Create an Ubuntu VM on Azure

Let's first create resource group called `pk-group-dev-01` within the `westeurope` region:

```text
$ GROUP=pk-group-dev-01
$ echo $GROUP
$ az group create --name $GROUP --location westeurope
...
```

Then, let's create an Ubuntu virtual machine called `pk-vm-dev-01` within the `pk-group-dev-01` group:

```text
$ VM=pk-vm-dev-01
$ echo VM
pk-vm-dev-01
$ az vm create \
  --resource-group $GROUP \
  --name $VM \
  --image UbuntuLTS \
  --admin-username radicel \
  --generate-ssh-keys
{
  ...
  "publicIpAddress": <Public IP adrress>
  ...
}
```

or, you can retrieve the public IP address later with:

```text
$ az vm show --show-details --name $VM --resource-group $GROUP
{
  ...
  "publicIps": <Public IP adrress>
  ...
}
```

Let's save this public IP address into a variable:

```text
$ PUBLIC_IP=$(az vm show --show-details --name $VM --resource-group $GROUP | jq '.publicIps' -r)
$ echo $PUBLIC_IP
<Public IP adrress>
```

Then, connect to the machine

```text
$ ssh radicel@$PUBLIC_IP
...
radicel@pk-vm-dev-01:~$
```

and upgrade it:

```text
$ sudo apt-get update
...
$ sudo apt-get upgrade -y
...
```

## Add the Laptop SSH Public Key to the VM for VS Code Remote Development

Retrieve the laptop SSH public key:

```text
USER@MACHINE MINGW64 ~
$ cd ~/.ssh/

UW31RY@WPU8L0058343 MINGW64 ~/.ssh
$ ls
... config ... id_rsa.pub ...

UW31RY@WPU8L0058343 MINGW64 ~/.ssh
$ cat id_rsa.pub
ssh-rsa AAAA...your_email@example.com
```

Copy the `ssh-rsa AAAA...your_email@example.com` string in the clipboard.

```text
radicel@pk-vm-pulsar-01:~$ cd ~/.ssh/

radicel@pk-vm-pulsar-01:~/.ssh$ vi authorized_keys
```

Paste the `ssh-rsa AAAA...your_email@example.com` string in the clipboard at the end of the authorized_keys file.

```text
UW31RY@WPU8L0058343 MINGW64 ~/.ssh
$ vi config
```

Add the following lines at the end of the `config` file:

```text
Host pk-vm-dev-01
  HostName <Public IP adrress>
  User radicel
  IdentityFile ~/.ssh/id_rsa
```

```text
UW31RY@WPU8L0058343 MINGW64 ~/.ssh
$ ssh radicel@pk-vm-dev-01
...
radicel@pk-vm-dev-01:~$
```

## VSCode Remote Development over SSH

<https://code.visualstudio.com/docs/remote/remote-overview>

## Connecting VM to GitHub with SSH

<https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh>

Generating a new SSH key:

```text
$ ssh-keygen -t ed25519 -C "your_email@example.com"
...
```

Start the ssh-agent in the background.

```text
$ eval "$(ssh-agent -s)"
Agent pid 29136
```

Add your SSH private key to the ssh-agent.

```text
$ ssh-add ~/.ssh/id_ed25519
Identity added: /home/radicel/.ssh/id_ed25519 (your_email@example.com)
```

Add the SSH key to your account on GitHub. For more information, see "[Adding a new SSH key to your GitHub account](https://docs.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)."
