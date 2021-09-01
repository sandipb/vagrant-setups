# Creating a podman instance for working on your desktop

This is based on [this article] by [Dave Meurer] to replace
[Docker] with [Podman] on the desktop.

[this article]: https://www.redhat.com/sysadmin/replace-docker-podman-macos
[Dave Meurer]: https://twitter.com/davemeurer
[Docker]: https://www.docker.com
[Podman]: https://podman.io

This Vagrant setup lets you set up Podman in a Virtualbox VM and then configures
it for remote (ssh) access.

## Prerequisites

On Mac:

- `brew install vagrant`
- `brew install ansible`
- `brew install podman`

## Steps

1. Checkout the repository and `cd` to this directory.
2. (optional) If you want to use your own private image repositories, create a
   file in `provisioning/files/registries.conf` (an example file has been
   provided), and set `override_registries_conf` in `provisioning/vars.yml` to
   `true`.
3. Run `vagrant up`. This will create the VM and then use Ansible to provision
   it to run Podman as a service.
4. Copy `containers.conf.example` to `~/.config/containers/containers.conf` and
   edit the path for `identity` key to point the directory of your checkout.

That is it. You should now be able to run `podman ps` and see running
containers, similar to `docker ps`. There should be none :/ because you just set
this up. But the lack of error would let you know that your host to vm podman
communication is working.

## Provisioning Notes

IP Address, memory and CPU for the VM are set at the top of the `Vagrantfile`.
Edit them, and then run `vagrant reload` to modify the VM. It would probably
result in restarting the VM. If you change the address, remember to update it in
your local `containers.conf`.
