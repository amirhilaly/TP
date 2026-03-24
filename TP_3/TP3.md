# TP3B

## I. Create the base VM

**🌞 Créez une VM azure (une commande az)**

```
[amir@bomboclat ~]$ az vm create -g cloud_tp -n en_larmes --size Standard_B1s --image almalinux:almalinux-x86_64:10-gen2:10.1.202512150 --admin-username amir --ssh-key-values ~/.ssh/cloud_tp1.pub
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
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

**🌞 Connexion SSH**

```
[amir@bomboclat ~]$ ssh le_fisc
Last login: Tue Mar 24 08:09:49 2026 from 159.117.224.23
[amir@enlarmes ~]$ 

```

## II. Prepare the VM

**🌞 Effectuez la conf suivante :**

```
[amir@enlarmes ~]$ dig

; <<>> DiG 9.18.33 <<>>
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52130
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 27

[amir@enlarmes ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=52 time=9.54 ms

[amir@enlarmes ~]$ sudo find / -name "htop"
/usr/bin/htop
/usr/share/licenses/htop
/usr/share/doc/htop
/home/amir/.config/htop

[amir@enlarmes ~]$ sudo find / -name "vim"
/usr/bin/vim
/usr/share/vim

```

**🌞 Go lancer ça :**

```
[amir@enlarmes ~]$ sudo cloud-init clean --logs
[amir@enlarmes ~]$ sudo rm -rf /var/lib/cloud/*
[amir@enlarmes ~]$ sudo systemctl enable cloud-init
[amir@enlarmes ~]$
```

**🌞 Proposer une suite de commandes**
```
[amir@enlarmes ~]$ ls /var/log
audit  btmp    cron             dnf.log      hawkey.log  messages  samba   spooler  tuned        wtmp
azure  chrony  dnf.librepo.log  dnf.rpm.log  lastlog     private   secure  sssd     waagent.log
[amir@enlarmes ~]$ sudo rm -rf /var/log/*

[amir@enlarmes ~]$ ls -la
total 20
drwx------. 4 amir amir 126 Mar 24 08:24 .
drwxr-xr-x. 3 root root  18 Mar 24 08:05 ..
-rw-------. 1 amir amir 724 Mar 24 08:25 .bash_history
-rw-r--r--. 1 amir amir  18 Oct 29  2024 .bash_logout
-rw-r--r--. 1 amir amir 144 Oct 29  2024 .bash_profile
-rw-r--r--. 1 amir amir 522 Oct 29  2024 .bashrc
drwx------. 3 amir amir  18 Mar 24 08:24 .config
drwx------. 2 amir amir  29 Mar 24 08:05 .ssh
-rw-------. 1 amir amir 556 Mar 24 08:16 .viminfo
[amir@enlarmes ~]$ cat /dev/null > ~/.bash_history 
[amir@enlarmes ~]$ cat .bash_history
[amir@enlarmes ~]$ 

```

update, apres reboot la vm avait l'air fine, pas d'erreur ou quoi:

```
[amir@enlarmes ~]$ ls /var/log
audit  btmp    cloud-init.log         cron     messages  secure  waagent.log
azure  chrony  cloud-init-output.log  lastlog  private   tuned   wtmp

```

## III. Create a template

**🌞 Let's go, balancez :**

```

az>> vm deallocate --resource-group cloud_tp --name en_larmes
az>> vm generalize --resource-group cloud_tp --name en_larmes
az>> image create --resource-group cloud_tp --name alma_chad --source en_larmes --hyper-v-generation V2
{
  "hyperVGeneration": "V2",
  "id": "",
  "location": "denmarkeast",
  "name": "alma_chad",
  "provisioningState": "Succeeded",
  "resourceGroup": "cloud_tp",
...
```

**🌞 Lancer une VM à partir de votre template**

```
[amir@bomboclat ~]$ az vm create -g cloud_tp -n chapeau_chimique --size Standard_B1s --image alma_chad --admin-username amir --ssh-key-values ~/.ssh/cloud_tp1.pub
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
{
  "fqdns": "",
  "id": "",
  "location": "denmarkeast",
  "macAddress": "X",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "9.205.X.X",
  "resourceGroup": "cloud_tp"
}
```

**🌞 Vérification !**

```
[amir@bomboclat ~]$ ssh chad_fisc
The authenticity of host '9.205.154.220 (9.205.154.220)' can't be established.
ED25519 key fingerprint is: SHA256:PKJFuioC7Bx14Y4w+D6IwSrCEHKDeJw2AbkwPSS1VlI
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '9.205.154.220' (ED25519) to the list of known hosts.
Warning: Permanently added '9.205.154.220' (ED25519) to the list of known hosts.
[amir@chapeauchimique ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=52 time=9.95 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=52 time=9.46 ms
[amir@chapeauchimique ~]$ sudo systemctl status cloud-init
● cloud-init.service - Cloud-init: Network Stage
     Loaded: loaded (/usr/lib/systemd/system/cloud-init.service; enabled; preset: enabled)
     Active: active (exited) since Tue 2026-03-24 09:42:40 UTC; 4min 16s ago
[amir@chapeauchimique ~]$ sudo systemctl status waagent
● waagent.service - Azure Linux Agent
     Loaded: loaded (/usr/lib/systemd/system/waagent.service; enabled; preset: enabled)
     Active: active (running) since Tue 2026-03-24 09:42:40 UTC; 5min ago
```


## IV. Hardened template

(Intro)

**🌞 Créez une VM qui servira à créer le template**
```
[amir@bomboclat ~]$ az vm create -g cloud_tp -n chrysacier --size Standard_B1s --image almalinux:almalinux-x86_64:10-gen2:10.1.202512150 --admin-username amir --ssh-key-values ~/.ssh/cloud_tp1.pub
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
Consider upgrading security for your workloads using Azure Trusted Launch VMs. To know more about Trusted Launch, please visit https://aka.ms/TrustedLaunch.
{
  "fqdns": "",
  "id": "/subscriptions/204b03a8-e7ae-466d-ab21-6999000feaba/resourceGroups/cloud_tp/providers/Microsoft.Compute/virtualMachines/chrysacier",
  "location": "denmarkeast",
  "macAddress": "7C-ED-8D-6A-87-37",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "9.205.153.244",
  "resourceGroup": "cloud_tp"
}
[amir@bomboclat ~]$ vim .ssh/config 
[amir@bomboclat ~]$ vim .ssh/config 
[amir@bomboclat ~]$ ssh single_spe_pokemon
The authenticity of host '9.205.153.244 (9.205.153.244)' can't be established.
ED25519 key fingerprint is: SHA256:bfQ1O9cazsnmQagUbuu4J5/QIyEbpYD+FveW0wf6KDk
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '9.205.153.244' (ED25519) to the list of known hosts.
[amir@chrysacier ~]$ 
```

(Firewall baby)


**🌞 Firewall conf**
```
[amir@chrysacier ~]$ sudo firewall-cmd --permanent --remove-service=dhcpv6-client
success
[amir@chrysacier ~]$ sudo firewall-cmd --permanent --remove-service=cockpit
success
[amir@chrysacier ~]$ sudo firewall-cmd --permanent --remove-service=ssh
success
[amir@chrysacier ~]$ sudo firewall-cmd --permanent --add-port=22/tcp
success
[amir@chrysacier ~]$ sudo firewall-cmd --reload
success
[amir@chrysacier ~]$ 
[amir@chrysacier ~]$ sudo firewall-cmd --list-all
public (default, active)
  target: default
  ingress-priority: 0
  egress-priority: 0
  icmp-block-inversion: no
  interfaces: eth0
  sources: 
  services: 
  ports: 22/tcp
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 

```

(Stronk SSH)

**🌞 Proposez une conf OpenSSH forte**

```

```