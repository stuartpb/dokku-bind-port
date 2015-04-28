## This project is deprecated

When this plugin was written, Dokku restarted app servers by first stopping the old build, then starting the new build. However, Dokku has since changed the way it restarts app servers to start the new build *before* stopping the old build: if the app's port is bound, this causes a conflict between the old instance and the new one (as the old instance doesn't relinquish the port until it is stopped), and **new builds will fail to start**. (See [issue #5][].)

[issue #5]: https://github.com/stuartpb/dokku-bind-port/issues/5

Besides that major issue with port binding, this plugin's functionality can entirely be duplicated by setting `-p` options for `docker run` using the [dokku-docker-options][] plugin, which is also significantly more simple and versatile (allowing one to achieve other goals, such as mounting a volume in an app).

[dokku-docker-options]: https://github.com/dyson/dokku-docker-options

Ultimately, since mid/late 2014, I've personally stopped using Dokku altogether, switching to [Plushu][] instead. If you want this plugin's functionality in Plushu, you can use the [plushu-app-docker-opts][] plugin to add a `-p 5000:80` option to the app. You will also need to create a file named `SINGLE_INSTANCE` in the app's directory (using a direct shell account to the Plushu server):

```bash
ssh root@plushu.example.com su -s /bin/sh -c "touch /home/plushu/apps/$app/SINGLE_INSTANCE" plushu
```

This tells the Plushu local app deployment plugin to stop the old build before starting the new one, avoiding the aforementioned issue with concurrent builds seen with Dokku.

[Plushu]: https://github.com/plushu/plushu
[plushu-app-docker-opts]: https://github.com/plushu/plushu-app-docker-opts

# dokku-bind-port

Dokku plugin for binding app container ports to host interfaces

The code for this plugin is largely based on the
[dokku-link](https://github.com/rlaneve/dokku-link) plugin, but for `-p`
arguments rather than `-link`, and with the redeployment behavior from
`dokku config`.

## Requirements

As this plugin uses the `docker-args` hook, it requires a development version
of dokku at or after https://github.com/progrium/dokku/commit/c77cbf1.

## Installation

```
cd /var/lib/dokku/plugins
sudo git clone https://github.com/stuartpb/dokku-bind-port.git bind-port
```

## Usage

```
$ dokku help
    bind <app>                                    display host bindings for an app
    bind:create <app> CONTAINER_PORT [HOST_PORT]  create a host binding for an app
    bind:delete <app> CONTAINER_PORT              delete a host binding for an app
```

The `HOST_PORT` argument defaults to the port specified in `CONTAINER_PORT`.

The `HOST_PORT` argument may include a specific interface to bind to, prefixed
with the interface and a colon.

By default, a port binding specifies TCP: to bind a UDP port, suffix the
`CONTAINER_PORT` argument with `/udp`.

For more information, see the Docker documentation on [port redirection][].

[port redirection]: http://docs.docker.io/en/latest/use/port_redirection/#binding-a-port-to-a-host-interface

## License

The MIT License (MIT)

Copyright (c) 2014 Stuart P. Bentley

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
