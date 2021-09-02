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

The equivalent of starting up and shutting down Docker desktop will now be
`vagrant up` and `vagrant halt` in this directory.

## Provisioning Notes

IP Address, memory and CPU for the VM are set at the top of the `Vagrantfile`.
Edit them, and then run `vagrant reload` to modify the VM. It would probably
result in restarting the VM. If you change the address, remember to update it in
your local `containers.conf`.

## Differences from docker

While you can use `podman` as a drop-in replacement for `docker` this way, the
only thing you need to watch out for is that published ports will only be
exposed on the VM IP address.

So if your vagrant vm IP address is `192.168.10.10` and your are running `nginx`
like this:

```console
$ podman run -it --rm -p 8888:80 nginx
Resolving "nginx" using unqualified-search registries (/etc/containers/registries.conf)
Trying to pull docker.io/library/nginx:latest...
Getting image source signatures
...
```

Then your website will be accessible on `http://192.168.10.10:8888` and not
`http://localhost:8888` as in docker.

```console
$ curl -sI 192.168.10.10:8888
HTTP/1.1 200 OK
Server: nginx/1.21.1
Date: Thu, 02 Sep 2021 01:36:55 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 06 Jul 2021 14:59:17 GMT
Connection: keep-alive
ETag: "60e46fc5-264"
Accept-Ranges: bytes

$ curl -sv localhost:8888
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8888 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connection failed
* connect to 127.0.0.1 port 8888 failed: Connection refused
* Failed to connect to localhost port 8888: Connection refused
* Closing connection 0
```

As mentioned in [the article][this article] I had references in the top of this
document, you can add an entry is `/etc/hosts` to make it easier to reach ports
on running containers.

## Further customizations

[The article][this article] describes turning this setup into a Mac app using
Automator. You should check it out!
