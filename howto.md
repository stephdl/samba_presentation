# Installation et Configuration Samba - Simple

Guide minimal pour installer et configurer Samba avec un partage personnalisé.

---

## 1. Installer Samba

### Debian/Ubuntu
```bash
su -
apt update
apt install samba smbclient -y
```

### CentOS/RedHat
```bash
su -
dnf install samba smbclient
```

---

## 2. Démarrer et Activer Samba

```bash
systemctl start smbd nmbd
systemctl enable smbd nmbd
```

Vérifier :
```bash
systemctl status smbd nmbd
```

---

## 3. Créer un Utilisateur

```bash
useradd -m -s /usr/sbin/nologin user1
smbpasswd -a user1
```

Entrez le mot de passe deux fois.

Ajouter l'utilisateur dans un groupe
```bash
groupadd samba_group
usermod -aG samba_group user1
```

---

## 4. Créer le Dossier Partagé

Donner les permissions au groupes samba_group
```bash
mkdir -p /srv/samba/shared
chown root:samba_group -R /srv/samba/shared
chmod 775 -R /srv/samba/shared
```

---

## 5. Ajouter le Partage à smb.conf

Gardez votre configuration existante, ajoutez seulement à la fin :

```bash
nano /etc/samba/smb.conf
```

Allez à la fin du fichier et ajoutez :

```ini
# ============================================
# PARTAGE PERSONNALISÉ
# ============================================
[shared]
    # Description du partage
    comment = Partage Général
    
    # Chemin physique du dossier
    path = /srv/samba/shared
    
    # Visible dans le navigateur réseau
    browseable = yes
    
    # Autoriser la lecture ET l'écriture
    read only = no
    
    # groupes autorisés
    valid groups = samba_group

    # forcer le groupe pour eviter des conflits de permissions
    force group = samba_group

    # Permissions des fichiers créés
    create mask = 0664
    
    # Permissions des dossiers créés
    directory mask = 0775
```

**Explication des paramètres clés :**

| Paramètre | Valeur | Signification |
|-----------|--------|---------------|
| `comment` | Partage Général | Description visible aux clients |
| `path` | /srv/samba/shared | Chemin du dossier à partager |
| `browseable` | yes | Visible dans le navigateur réseau |
| `read only` | no | Lecture ET écriture autorisées |
| `valid groups` | samba_group | Seul les mebres de samba_group peuvent y acceder|
| ` force group` | samba_group | le groupe sera forcé sur samba_group pour permette a tous les membres d'editer les fichiers et repertoires|
| `create mask` | 0664 | rw-rw--r-- (permissions des fichiers) |
| `directory mask` | 0775 | rwxrwxr-x (permissions des dossiers) |

---

## 6. Valider et Redémarrer

```bash
testparm
```

Vous devriez voir : `Loaded services file OK.`

Redémarrer :
```bash
systemctl restart smbd nmbd
```

---

## 7. Tester

```bash
smbclient -L localhost -U user1
```

Vous devriez voir le partage `[shared]` dans la liste.

---

## Fichier smb.conf Complet (Référence)

Voici à quoi ressemble votre fichier avec le nouveau partage :

```ini
[global]
    workgroup = WORKGROUP
    security = user
    passdb backend = tdbsam
    printing = cups
    printcap name = cups
    load printers = yes
    cups options = raw
    include = /etc/samba/usershares.conf

[homes]
    comment = Home Directories
    valid users = %S, %D%w%S
    browseable = No
    read only = No
    inherit acls = Yes

[printers]
    comment = All Printers
    path = /var/tmp
    printable = Yes
    create mask = 0600
    browseable = No

[print$]
    comment = Printer Drivers
    path = /var/lib/samba/drivers
    write list = printadmin root
    force group = printadmin
    create mask = 0664
    directory mask = 0775

[shared]
    comment = Partage Général
    path = /srv/samba/shared
    browseable = yes
    read only = no
    valid groups = samba_group
    force group = samba_group
    create mask = 0664
    directory mask = 0775
```

C'est prêt ! 🎯
