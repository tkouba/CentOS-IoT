# Přihlášení pomocí SSH key-based authentication

Volně podle [SSH Passwordless Login Using SSH Keygen in 5 Easy Steps](https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/)

## Vygenerování klíče

Vygenerovat klíč pro klientskou stanici je nutné pouze jednou. 
Potom se dá rozkopírovat veřejná část na více serverů.

Na klientské stanici spustíme příkaz:
```
ssh-keygen -t rsa
```

Výsledek:
```
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\<username>/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\<username>/.ssh/id_rsa.
Your public key has been saved in C:\Users\<username>/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX <domain>\<username>@<HOSTNAME>
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
+----[SHA256]-----+
```

Na Windows je cesta k souboru `%USERPROFILE%\.ssh`

# Vytvoření souboru authorized_keys
Na cílovém počítači vytvoříme adresář .ssh
```
mkdir .ssh
```

Založíme soubor
```
touch .ssh/authorized_keys
```

Editujeme soubor authorized_keys 
```
nano .ssh/authorized_keys
```
a vložíme do něj obsah souboru `%USERPROFILE%\.ssh\id_rsa.pub`. Já jsem použil `Ctrl+C` a `Ctrl+V` přímo do editoru `nano` v okně terminálu. Jako terminál používám [Windows Terminal](https://docs.microsoft.com/en-us/windows/terminal/).

Změníme práva na adresář
```
chmod 700 .ssh
```
i soubor
```
chmod 640 .ssh/authorized_keys
```

