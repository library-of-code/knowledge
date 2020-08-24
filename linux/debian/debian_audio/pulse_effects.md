# Pulseeffects

## Step By Step

###  Download Necessary Packages

```
wget https://launchpad.net/~yunnxx/+archive/ubuntu/gnome3/+files/pulseeffects_1.313entornosgnulinuxenial-1ubuntu1_amd64.deb
```
### Install Binary Package

```
   dpkg -i pulseeffects_1.313entornosgnulinuxenial-1ubuntu1_amd64.deb
```

### Dependency errors then follow below

```
   apt-get -f install
   (or)
   apt-fast -f install
   
   and Install again

   dpkg -i pulseeffects_1.313entornosgnulinuxenial-1ubuntu1_amd64.deb
```

### Check Installation

```
   apt list --installed pulseeffects

   Output:
   Listing... Done
   pulseeffects/now 1.313entornosgnulinuxenial-1ubuntu1 amd64 [installed,local]
```

