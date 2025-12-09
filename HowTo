# Installation et Configuration Samba - Simple

Guide minimal pour installer et configurer Samba avec un partage personnalisé.

---

## 1. Installer Samba

### Debian/Ubuntu
```bash
sudo apt update
sudo apt install samba
```

### CentOS/RedHat
```bash
sudo dnf install samba
```

---

## 2. Démarrer et Activer Samba

```bash
sudo systemctl start smbd nmbd
sudo systemctl enable smbd nmbd
```

Vérifier :
```bash
sudo systemctl status smbd nmbd
```

---

## 3. Créer un Utilisateur

```bash
sudo useradd -m -s /usr/sbin/nologin user1
sudo smbpasswd -a user1
```

Entrez le mot de passe deux fois.

---

## 4. Créer le Dossier Partagé

```bash
sudo mkdir -p /srv/samba/shared
sudo chmod 755 /srv/samba/shared
```

---

## 5. Ajouter le Partage à smb.conf

Gardez votre configuration existante, ajoutez seulement à la fin :

```bash
sudo nano /etc/samba/smb.conf
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
    
    # Utilisateurs autorisés
    valid users = user1
    
    # Permissions des fichiers créés
    create mask = 0644
    
    # Permissions des dossiers créés
    directory mask = 0755
```

**Explication des paramètres clés :**

| Paramètre | Valeur | Signification |
|-----------|--------|---------------|
| `comment` | Partage Général | Description visible aux clients |
| `path` | /srv/samba/shared | Chemin du dossier à partager |
| `browseable` | yes | Visible dans le navigateur réseau |
| `read only` | no | Lecture ET écriture autorisées |
| `valid users` | user1 | Seul user1 peut accéder |
| `create mask` | 0644 | rw-r--r-- (permissions des fichiers) |
| `directory mask` | 0755 | rwxr-xr-x (permissions des dossiers) |

---

## 6. Valider et Redémarrer

```bash
sudo testparm
```

Vous devriez voir : `Loaded services file OK.`

Redémarrer :
```bash
sudo systemctl restart smbd nmbd
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
    valid users = user1
    create mask = 0644
    directory mask = 0755
```

C'est prêt ! 🎯
