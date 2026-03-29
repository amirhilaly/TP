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
The authenticity of host '9.205.X.X (9.205.X.X)' can't be established.
ED25519 key fingerprint is: SHA256:PKJFuioC7Bx14Y4w+D6IwSrCEHKDeJw2AbkwPSS1VlI
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '9.205.X.X' (ED25519) to the list of known hosts.
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
  "id": "",
  "location": "denmarkeast",
  "macAddress": "7C-ED-8D-6A-87-37",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "9.205.X.X",
  "resourceGroup": "cloud_tp"
}
[amir@bomboclat ~]$ vim .ssh/config 
[amir@bomboclat ~]$ vim .ssh/config 
[amir@bomboclat ~]$ ssh single_spe_pokemon
The authenticity of host '9.205.X.X (9.205.X.X)' can't be established.
ED25519 key fingerprint is: SHA256:bfQ1O9cazsnmQagUbuu4J5/QIyEbpYD+FveW0wf6KDk
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '9.205.X.X' (ED25519) to the list of known hosts.
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
[amir@chrysacier ~]$ sudo cat /etc/ssh/sshd_config
Port 67
AuthenticationMethods   publickey
PubkeyAuthentication    yes
PasswordAuthentication  no
AllowGroups     skibidi
PermitRootLogin no
[amir@chrysacier ~]$ 

```

(fail2ban)

**🌞 Installer et configurer fail2ban**
```
amir@chrysacier ~]$ sudo systemctl start fail2ban
[amir@chrysacier ~]$ sudo systemctl enable fail2ban
Created symlink '/etc/systemd/system/multi-user.target.wants/fail2ban.service' → '/usr/lib/systemd/system/fail2ban.service'.
[amir@chrysacier ~]$ sudo systemctl status fail2ban
● fail2ban.service - Fail2Ban Service
     Loaded: loaded (/usr/lib/systemd/system/fail2ban.service; enabled; preset: disabled)
     Active: active (running) since Sun 2026-03-29 14:47:24 UTC; 10s ago


[amir@chrysacier ~]$ sudo cat /etc/fail2ban/jail.local
[DEFAULT]
ignoreip = 127.0.0.1/8 ::1
bantime = -1
maxretry = 2
findtime = 1h

[sshd]
enabled = true
port    = 67
maxretry =2
logpath = /var/log/auth.log


```


(Harden Kernel Parameters)

**🌞 Proposer une conf sysctl**

```
[amir@chrysacier sysctl.d]$ sudo cat 99-sysctl.conf 
# sysctl settings are defined through files in
# /usr/lib/sysctl.d/, /run/sysctl.d/, and /etc/sysctl.d/.
#
# Vendors settings live in /usr/lib/sysctl.d/.
# To override a whole file, create a new file with the same in
# /etc/sysctl.d/ and put new settings there. To override
# only specific settings, add a file with a lexically later
# name in /etc/sysctl.d/ and put new settings there.
#
# For more information, see sysctl.conf(5) and sysctl.d(5).


# Auto-reboot linux 30 seconds after a kernel panic
kernel.panic = 30
kernel.panic_on_oops = 30


# Controls the maximum size of a message, in bytes
kernel.msgmnb = 65536

# Controls the default maxmimum size of a mesage queue
kernel.msgmax = 65536

```

(IDS)

**🌞 Installer l'IDS AIDE**

```
[amir@chrysacier ~]$ sudo dnf install aide -y
Last metadata expiration check: 0:47:26 ago on Sun 29 Mar 2026 02:47:19 PM UTC.
Dependencies resolved.
====================================================================================================================================================================
 Package                           Architecture                        Version                                         Repository                              Size
====================================================================================================================================================================
Installing:
 aide                              x86_64                              0.18.6-8.el10_1.2                               appstream                              145 k


```

**🌞 Proposer une conf AIDE**

```
[amir@chrysacier ~]$ sudo cat /etc/aide.conf | grep sshd && sudo cat /etc/aide.conf | grep sysctl
/etc/ssh/sshd_config$ p+i+n+u+g+s+b+m+c+acl+selinux+xattrs
/etc/sysctl.conf p+i+n+u+g+s+b+m+c+acl+selinux+xattrs
```

**🌞 Initialiser la base de données AIDE**
```
[amir@chrysacier ~]$ sudo aide --init
WARNING: /var/lib/aide/aide.db.new.gz: gnutls_hash_init (stribog256) failed for '/var/lib/aide/aide.db.new.gz'
WARNING: /var/lib/aide/aide.db.new.gz: gnutls_hash_init (stribog512) failed for '/var/lib/aide/aide.db.new.gz'
Start timestamp: 2026-03-29 16:23:23 +0000 (AIDE 0.18.6)
AIDE successfully initialized database.
New AIDE database written to /var/lib/aide/aide.db.new.gz

Number of entries:      40980

---------------------------------------------------
The attributes of the (uncompressed) database(s):
---------------------------------------------------

/var/lib/aide/aide.db.new.gz

```

**🌞 Jouer avec les tests d'intégrité AIDE**

```
[amir@chrysacier ~]$ aide --check
  ERROR: cannot open config file '/etc/aide.conf': Permission denied
[amir@chrysacier ~]$ sudo aide --check
Start timestamp: 2026-03-29 16:30:00 +0000 (AIDE 0.18.6)
AIDE found NO differences between database and filesystem. Looks okay!!

[amir@chrysacier ~]$ sudo aide --check
WARNING: /var/lib/aide/aide.db.gz: gnutls_hash_init (stribog256) failed for '/var/lib/aide/aide.db.gz'
WARNING: /var/lib/aide/aide.db.gz: gnutls_hash_init (stribog512) failed for '/var/lib/aide/aide.db.gz'
Start timestamp: 2026-03-29 16:32:39 +0000 (AIDE 0.18.6)
AIDE found differences between database and filesystem!!

Summary:
  Total number of entries:      40980
  Added entries:                0
  Removed entries:              0
  Changed entries:              1

---------------------------------------------------
Changed entries:
---------------------------------------------------

f <.... mci. ... : /etc/ssh/sshd_config

```

**🌞 Créer un service systemd pour lancer un test AIDE**

```
[amir@chrysacier ~]$ sudo vim /etc/systemd/system/aide-test.service
[amir@chrysacier ~]$ sudo cat /etc/systemd/system/aide-test.service
[Unit]
Description=Run an AIDE integrity check

[Service]
# On met oneshot en type : ça indique que ce sera pas un service qui va run en permanence, mais un truc ponctuel (il se lance puis se termine)
Type=oneshot

# En ExecStart on met la commande qui sera exécutée quand on fait un `systemctl start`
# Mettez le chemin absolu vers la commande
ExecStart=/usr/sbin/aide --check

# Ptit tip pour que le service reste indiqué comme "actif" même après qu'il soit terminé
RemainAfterExit=true

[Install]
WantedBy=multi-user.target

```

**🌞 Indiquer à systemd qu'on a modifié les services**

```
[amir@chrysacier ~]$ sudo systemctl daemon-reload
[amir@chrysacier ~]$ 

```

**🌞 Tester le service**

```
[amir@chrysacier ~]$ sudo systemctl start aide-test
Job for aide-test.service failed because the control process exited with error code.
See "systemctl status aide-test.service" and "journalctl -xeu aide-test.service" for details.
[amir@chrysacier ~]$ ournalctl -xeu aide-test.service
-bash: ournalctl: command not found
[amir@chrysacier ~]$ journalctl -xeu aide-test.service
Mar 29 16:48:48 chrysacier aide[36156]: Detailed information about changes:
Mar 29 16:48:48 chrysacier aide[36156]: ---------------------------------------------------
Mar 29 16:48:48 chrysacier aide[36156]: File: /etc/ssh/sshd_config
Mar 29 16:48:48 chrysacier aide[36156]:  Size      : 143                              | 117
Mar 29 16:48:48 chrysacier aide[36156]:  Mtime     : 2026-03-29 15:21:00 +0000        | 2026-03-29 16:32:37 +0000
Mar 29 16:48:48 chrysacier aide[36156]:  Ctime     : 2026-03-29 15:21:00 +0000        | 2026-03-29 16:32:37 +0000
Mar 29 16:48:48 chrysacier aide[36156]:  Inode     : 16944916                         | 16944912
Mar 29 16:48:48 chrysacier aide[36156]: ---------------------------------------------------
Mar 29 16:48:48 chrysacier aide[36156]: The attributes of the (uncompressed) database(s):
Mar 29 16:48:48 chrysacier aide[36156]: ---------------------------------------------------
Mar 29 16:48:48 chrysacier aide[36156]: /var/lib/aide/aide.db.gz


```

**🌞 Créer un timer systemd**

```
[amir@chrysacier ~]$ sudo cat /etc/systemd/system/aide-test.timer
[Unit]
Description=check moi tout ca tte les hesurres

[Timer]
OnCalendar=hourly

[Install]
WantedBy=timers.target

```

## V. Deploy!

**🌞 Clean la VM**

```
[amir@chrysacier ~]$ sudo cloud-init clean --logs
[amir@chrysacier ~]$ sudo rm -rf /var/lib/cloud/*
[amir@chrysacier ~]$ sudo systemctl status cloud-init
● cloud-init.service - Cloud-init: Network Stage
     Loaded: loaded (/usr/lib/systemd/system/cloud-init.service; enabled; preset: enabled)
[amir@chrysacier ~]$ cat /dev/null > ~/.bash_history 
[amir@chrysacier ~]$ cat ~/.bash_history 
[amir@chrysacier ~]$ 
```

**🌞 Faire de la VM un template**


```
[amir@bomboclat ~]$ az vm deallocate --resource-group cloud_tp --name chrysacier
[amir@bomboclat ~]$ az vm generalize --resource-group cloud_tp --name chrysacier
[amir@bomboclat ~]$ az image create --resource-group cloud_tp --name alma-hardened --source chrysacier --hyper-v-generation V2
{
  "hyperVGeneration": "V2",
  "id": 
  "location": "denmarkeast",
  "name": "alma-hardened",
  "provisioningState": "Succeeded",
  "resourceGroup": "cloud_tp",
  "sourceVirtualMachine": {
    "id": 
    "resourceGroup": "cloud_tp"
  },
  "storageProfile": {
    "dataDisks": [],
    "osDisk": {
      "caching": "ReadWrite",
      "diskSizeGB": 30,
      "managedDisk": {
        "id":
        "resourceGroup": "cloud_tp"
      },
      "osState": "Generalized",
      "osType": "Linux",
      "storageAccountType": "Premium_LRS"
    }
  },
  "tags": {},
  "type": "Microsoft.Compute/images"
}
```


**🌞 Lancer une VM à partir de cette image**

```
[amir@bomboclat ~]$ az vm create -g cloud_tp -n chapeau_tiramisu --size Standard_B1s --image alma-hardened --admin-username amir --ssh-key-values ~/.ssh/cloud_tp1.pub
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
{
  "fqdns": "",
  "id": 
  "location": "denmarkeast",
  "macAddress": "",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "9.205.X.X",
  "resourceGroup": "cloud_tp"
}

```

**🌞 Vérif**

```
[amir@bomboclat ~]$ ssh -p 6767 amir@9.205.153.134
The authenticity of host '[9.205.X.X]:6767 ([9.205.X.X]:6767)' can't be established.
ED25519 key fingerprint is: SHA256:Uf+6wOspyXFURfQ7tE8StyLptsnkNaX058NKi5oJRDI
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[9.205.X.X]:6767' (ED25519) to the list of known hosts.
Last login: Sun Mar 29 16:04:34 2026 from 176.166.X.X
[amir@chapeautiramisu ~]$ 

```