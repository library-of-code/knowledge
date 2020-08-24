## Configuring Podman

* Update the system

```sh
sudo pacman -Syu

sudo pacman -S podman
```

* To run podman in rootless mode, add the uid and gid in the following files

```sh
sudo touch /etc/subuid

sudo usermod --add-subuids 10000-75535 angad

sudo touch /etc/subgid

sudo usermod --add-subgids 10000-75535 angad
```

* To create namespace for signatures

```sh
podman system migrate
```

NOTE: Only root binds to ports < 1024 so if you want to bind to such a port then you have to use podman in the root mode
