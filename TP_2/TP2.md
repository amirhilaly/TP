## I. Prérequis

**🌞 Déterminer quel algorithme de chiffrement utiliser pour vos clés**

ed25519

**🌞 Générer une paire de clés pour ce TP**

```
[amir@bomboclat ~]$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (): cloud_tp1
Enter passphrase for "cloud_tp1" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in cloud_tp1
Your public key has been saved in cloud_tp1.pub
The key's randomart image is:
+--[ED25519 256]--+
```

pas besoin de rajouter l'option ``-t ed25519`` à la commande car ``ssh-keygen`` utilise l'algo ed25519 par défaut, tavu


**🌞 Configurer un agent SSH sur votre poste**


**🌞 Connectez-vous en SSH à la VM pour preuve**

```
[amir@bomboclat ~]$ ssh amir@9.205.16.10
The authenticity of host '9.205.16.10 (9.205.16.10)' can't be established.
ED25519 key fingerprint is: SHA256:FCqPjFY+c3dLoohCMA5g05IOGnSdjhNreamEw00rwzQ
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '9.205.16.10' (ED25519) to the list of known hosts.
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.17.0-1008-azure x86_64)

amir@alternance:~$ 
```

**🌞 Créez une VM depuis le Azure CLI**

déjà j'montre que j'ai pas az cli d'install

```
[amir@bomboclat ~]$ sudo pacman -U azure-cli
loading packages...
error: 'azure-cli': could not find or read package
[amir@bomboclat ~]$ cat ~/.bashrc | grep az
alias az='~/.local/bin/az'
[amir@bomboclat ~]$ az
This command is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Installing the Interactive extension...
No stable version of 'interactive' to install. Preview versions allowed.
The installed extension 'interactive' is in preview.

Do you agree to sending telemetry (yes/no)? Default answer is yes: no

Loading command table... Expected time around 1 minute.
az>> 
```

ensuite paf commandes pour créer un groupe et une vm dans ce groupe:

```
az>> vm create -g cloud_tp -n en_larmes --size Standard_B1s --image almalinux:almalinux-x86_64:10-gen2:10.1.202512150 --admin-username amir --ssh-key-values /tmp/ssh/cloud_tp1.pub
Consider upgrading security for your workloads using Azure Trusted Launch VMs. To know more about Trusted Launch, please visit https://aka.ms/TrustedLaunch.
{
  "fqdns": "",
  "id": "",
  "location": "denmarkeast",
  "macAddress": "",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "9.205.X.X",
  "resourceGroup": "cloud_tp"
}
```

**🌞 Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique**
```
[amir@bomboclat]$ ssh silvousplait
Last login: Mon Mar 23 10:10:01 2026 from 159.117.224.23
[amir@enlarmes ~]$ exit
logout
Connection to 9.205.X.X closed.
```

**🌞 Une fois connecté, prouvez la présence...**

```
[amir@enlarmes ~]$ sudo systemctl status waagent.service | head -n 3
● waagent.service - Azure Linux Agent
     Loaded: loaded (/usr/lib/systemd/system/waagent.service; enabled; preset: enabled)
     Active: active (running) since Mon 2026-03-23 10:02:28 UTC; 13min ago
```

```
[amir@enlarmes ~]$ sudo systemctl status cloud-init.service | head -n 3
● cloud-init.service - Cloud-init: Network Stage
     Loaded: loaded (/usr/lib/systemd/system/cloud-init.service; enabled; preset: enabled)
     Active: active (exited) since Mon 2026-03-23 10:02:28 UTC; 13min ago
```

**🌞 Utilisez Terraform pour créer une VM dans Azure**

```
[amir@bomboclat fichiers_tp2]$ terraform init
Initializing the backend...
Initializing provider plugins...
- Finding latest version of hashicorp/azurerm...
- Installing hashicorp/azurerm v4.65.0...
- Installed hashicorp/azurerm v4.65.0 (signed by HashiCorp)
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

[amir@bomboclat fichiers_tp2]$ terraform apply
azurerm_resource_group.main: Refreshing state... 

...

azurerm_virtual_network.main: Creating...
azurerm_public_ip.main: Creating...
azurerm_public_ip.main: Creation complete after 2s 
azurerm_virtual_network.main: Creation complete after 5s
azurerm_subnet.main: Creating...
azurerm_subnet.main: Creation complete after 4s 
azurerm_network_interface.main: Creating...
azurerm_network_interface.main: Creation complete after 4s 
azurerm_linux_virtual_machine.main: Creating...
azurerm_linux_virtual_machine.main: Still creating... [00m10s elapsed]
azurerm_linux_virtual_machine.main: Creation complete after 18s 

...

Apply complete! Resources: 5 added, 0 changed, 0 destroyed.
```


**📁 Fichiers à rendre**

**🌞 Prouvez avec une connexion SSH sur l'IP publique que la VM est up**

```
[amir@bomboclat ~]$ ssh amir@9.205.156.33
[amir@super-vm ~]$ 
```