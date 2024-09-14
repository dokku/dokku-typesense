# dokku typesense [![Build Status](https://img.shields.io/github/actions/workflow/status/dokku/dokku-typesense/ci.yml?branch=master&style=flat-square "Build Status")](https://github.com/dokku/dokku-typesense/actions/workflows/ci.yml?query=branch%3Amaster) [![IRC Network](https://img.shields.io/badge/irc-libera-blue.svg?style=flat-square "IRC Libera")](https://webchat.libera.chat/?channels=dokku)

Official typesense plugin for dokku. Currently defaults to installing [typesense/typesense 27.0](https://hub.docker.com/r/typesense/typesense/).

## Requirements

- dokku 0.19.x+
- docker 1.8.x

## Installation

```shell
# on 0.19.x+
sudo dokku plugin:install https://github.com/dokku/dokku-typesense.git typesense
```

## Commands

```
typesense:app-links <app>                          # list all typesense service links for a given app
typesense:backup-schedule-cat <service>            # cat the contents of the configured backup cronfile for the service
typesense:clone <service> <new-service> [--clone-flags...] # create container <new-name> then copy data from <name> into <new-name>
typesense:create <service> [--create-flags...]     # create a typesense service
typesense:destroy <service> [-f|--force]           # delete the typesense service/data/container if there are no links left
typesense:enter <service>                          # enter or run a command in a running typesense service container
typesense:exists <service>                         # check if the typesense service exists
typesense:expose <service> <ports...>              # expose a typesense service on custom host:port if provided (random port on the 0.0.0.0 interface if otherwise unspecified)
typesense:info <service> [--single-info-flag]      # print the service information
typesense:link <service> <app> [--link-flags...]   # link the typesense service to the app
typesense:linked <service> <app>                   # check if the typesense service is linked to an app
typesense:links <service>                          # list all apps linked to the typesense service
typesense:list                                     # list all typesense services
typesense:logs <service> [-t|--tail] <tail-num-optional> # print the most recent log(s) for this service
typesense:pause <service>                          # pause a running typesense service
typesense:promote <service> <app>                  # promote service <service> as TYPESENSE_URL in <app>
typesense:restart <service>                        # graceful shutdown and restart of the typesense service container
typesense:set <service> <key> <value>              # set or clear a property for a service
typesense:start <service>                          # start a previously stopped typesense service
typesense:stop <service>                           # stop a running typesense service
typesense:unexpose <service>                       # unexpose a previously exposed typesense service
typesense:unlink <service> <app>                   # unlink the typesense service from the app
typesense:upgrade <service> [--upgrade-flags...]   # upgrade service <service> to the specified versions
```

## Usage

Help for any commands can be displayed by specifying the command as an argument to typesense:help. Plugin help output in conjunction with any files in the `docs/` folder is used to generate the plugin documentation. Please consult the `typesense:help` command for any undocumented commands.

### Basic Usage

### create a typesense service

```shell
# usage
dokku typesense:create <service> [--create-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-m|--memory MEMORY`: container memory limit in megabytes (default: unlimited)
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-p|--password PASSWORD`: override the user-level service password
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-r|--root-password PASSWORD`: override the root-level service password
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for typesense docker container

Create a typesense service named lollipop:

```shell
dokku typesense:create lollipop
```

You can also specify the image and image version to use for the service. It *must* be compatible with the typesense/typesense image.

```shell
export TYPESENSE_IMAGE="typesense/typesense"
export TYPESENSE_IMAGE_VERSION="${PLUGIN_IMAGE_VERSION}"
dokku typesense:create lollipop
```

You can also specify custom environment variables to start the typesense service in semicolon-separated form.

```shell
export TYPESENSE_CUSTOM_ENV="USER=alpha;HOST=beta"
dokku typesense:create lollipop
```

### print the service information

```shell
# usage
dokku typesense:info <service> [--single-info-flag]
```

flags:

- `--config-dir`: show the service configuration directory
- `--data-dir`: show the service data directory
- `--dsn`: show the service DSN
- `--exposed-ports`: show service exposed ports
- `--id`: show the service container id
- `--internal-ip`: show the service internal ip
- `--initial-network`: show the initial network being connected to
- `--links`: show the service app links
- `--post-create-network`: show the networks to attach to after service container creation
- `--post-start-network`: show the networks to attach to after service container start
- `--service-root`: show the service root directory
- `--status`: show the service running status
- `--version`: show the service image version

Get connection information as follows:

```shell
dokku typesense:info lollipop
```

You can also retrieve a specific piece of service info via flags:

```shell
dokku typesense:info lollipop --config-dir
dokku typesense:info lollipop --data-dir
dokku typesense:info lollipop --dsn
dokku typesense:info lollipop --exposed-ports
dokku typesense:info lollipop --id
dokku typesense:info lollipop --internal-ip
dokku typesense:info lollipop --initial-network
dokku typesense:info lollipop --links
dokku typesense:info lollipop --post-create-network
dokku typesense:info lollipop --post-start-network
dokku typesense:info lollipop --service-root
dokku typesense:info lollipop --status
dokku typesense:info lollipop --version
```

### list all typesense services

```shell
# usage
dokku typesense:list
```

List all services:

```shell
dokku typesense:list
```

### print the most recent log(s) for this service

```shell
# usage
dokku typesense:logs <service> [-t|--tail] <tail-num-optional>
```

flags:

- `-t|--tail [<tail-num>]`: do not stop when end of the logs are reached and wait for additional output

You can tail logs for a particular service:

```shell
dokku typesense:logs lollipop
```

By default, logs will not be tailed, but you can do this with the --tail flag:

```shell
dokku typesense:logs lollipop --tail
```

The default tail setting is to show all logs, but an initial count can also be specified:

```shell
dokku typesense:logs lollipop --tail 5
```

### link the typesense service to the app

```shell
# usage
dokku typesense:link <service> <app> [--link-flags...]
```

flags:

- `-a|--alias "BLUE_DATABASE"`: an alternative alias to use for linking to an app via environment variable
- `-q|--querystring "pool=5"`: ampersand delimited querystring arguments to append to the service link
- `-n|--no-restart "false"`: whether or not to restart the app on link (default: true)

A typesense service can be linked to a container. This will use native docker links via the docker-options plugin. Here we link it to our `playground` app.

> NOTE: this will restart your app

```shell
dokku typesense:link lollipop playground
```

The following environment variables will be set automatically by docker (not on the app itself, so they won’t be listed when calling dokku config):

```
DOKKU_TYPESENSE_LOLLIPOP_NAME=/lollipop/DATABASE
DOKKU_TYPESENSE_LOLLIPOP_PORT=tcp://172.17.0.1:8108
DOKKU_TYPESENSE_LOLLIPOP_PORT_8108_TCP=tcp://172.17.0.1:8108
DOKKU_TYPESENSE_LOLLIPOP_PORT_8108_TCP_PROTO=tcp
DOKKU_TYPESENSE_LOLLIPOP_PORT_8108_TCP_PORT=8108
DOKKU_TYPESENSE_LOLLIPOP_PORT_8108_TCP_ADDR=172.17.0.1
```

The following will be set on the linked application by default:

```
TYPESENSE_URL=typesense://:SOME_PASSWORD@dokku-typesense-lollipop:8108
```

The host exposed here only works internally in docker containers. If you want your container to be reachable from outside, you should use the `expose` subcommand. Another service can be linked to your app:

```shell
dokku typesense:link other_service playground
```

It is possible to change the protocol for `TYPESENSE_URL` by setting the environment variable `TYPESENSE_DATABASE_SCHEME` on the app. Doing so will after linking will cause the plugin to think the service is not linked, and we advise you to unlink before proceeding.

```shell
dokku config:set playground TYPESENSE_DATABASE_SCHEME=typesense2
dokku typesense:link lollipop playground
```

This will cause `TYPESENSE_URL` to be set as:

```
typesense2://:SOME_PASSWORD@dokku-typesense-lollipop:8108
```

### unlink the typesense service from the app

```shell
# usage
dokku typesense:unlink <service> <app>
```

flags:

- `-n|--no-restart "false"`: whether or not to restart the app on unlink (default: true)

You can unlink a typesense service:

> NOTE: this will restart your app and unset related environment variables

```shell
dokku typesense:unlink lollipop playground
```

### set or clear a property for a service

```shell
# usage
dokku typesense:set <service> <key> <value>
```

Set the network to attach after the service container is started:

```shell
dokku typesense:set lollipop post-create-network custom-network
```

Set multiple networks:

```shell
dokku typesense:set lollipop post-create-network custom-network,other-network
```

Unset the post-create-network value:

```shell
dokku typesense:set lollipop post-create-network
```

### Service Lifecycle

The lifecycle of each service can be managed through the following commands:

### enter or run a command in a running typesense service container

```shell
# usage
dokku typesense:enter <service>
```

A bash prompt can be opened against a running service. Filesystem changes will not be saved to disk.

> NOTE: disconnecting from ssh while running this command may leave zombie processes due to moby/moby#9098

```shell
dokku typesense:enter lollipop
```

You may also run a command directly against the service. Filesystem changes will not be saved to disk.

```shell
dokku typesense:enter lollipop touch /tmp/test
```

### expose a typesense service on custom host:port if provided (random port on the 0.0.0.0 interface if otherwise unspecified)

```shell
# usage
dokku typesense:expose <service> <ports...>
```

Expose the service on the service's normal ports, allowing access to it from the public interface (`0.0.0.0`):

```shell
dokku typesense:expose lollipop 8108
```

Expose the service on the service's normal ports, with the first on a specified ip adddress (127.0.0.1):

```shell
dokku typesense:expose lollipop 127.0.0.1:8108
```

### unexpose a previously exposed typesense service

```shell
# usage
dokku typesense:unexpose <service>
```

Unexpose the service, removing access to it from the public interface (`0.0.0.0`):

```shell
dokku typesense:unexpose lollipop
```

### promote service <service> as TYPESENSE_URL in <app>

```shell
# usage
dokku typesense:promote <service> <app>
```

If you have a typesense service linked to an app and try to link another typesense service another link environment variable will be generated automatically:

```
DOKKU_TYPESENSE_BLUE_URL=typesense://:ANOTHER_PASSWORD@dokku-typesense-other-service:8108/other_service
```

You can promote the new service to be the primary one:

> NOTE: this will restart your app

```shell
dokku typesense:promote other_service playground
```

This will replace `TYPESENSE_URL` with the url from other_service and generate another environment variable to hold the previous value if necessary. You could end up with the following for example:

```
TYPESENSE_URL=typesense://:ANOTHER_PASSWORD@dokku-typesense-other-service:8108/other_service
DOKKU_TYPESENSE_BLUE_URL=typesense://:ANOTHER_PASSWORD@dokku-typesense-other-service:8108/other_service
DOKKU_TYPESENSE_SILVER_URL=typesense://:SOME_PASSWORD@dokku-typesense-lollipop:8108/lollipop
```

### start a previously stopped typesense service

```shell
# usage
dokku typesense:start <service>
```

Start the service:

```shell
dokku typesense:start lollipop
```

### stop a running typesense service

```shell
# usage
dokku typesense:stop <service>
```

Stop the service and removes the running container:

```shell
dokku typesense:stop lollipop
```

### pause a running typesense service

```shell
# usage
dokku typesense:pause <service>
```

Pause the running container for the service:

```shell
dokku typesense:pause lollipop
```

### graceful shutdown and restart of the typesense service container

```shell
# usage
dokku typesense:restart <service>
```

Restart the service:

```shell
dokku typesense:restart lollipop
```

### upgrade service <service> to the specified versions

```shell
# usage
dokku typesense:upgrade <service> [--upgrade-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-R|--restart-apps "true"`: whether or not to force an app restart (default: false)
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for typesense docker container

You can upgrade an existing service to a new image or image-version:

```shell
dokku typesense:upgrade lollipop
```

### Service Automation

Service scripting can be executed using the following commands:

### list all typesense service links for a given app

```shell
# usage
dokku typesense:app-links <app>
```

List all typesense services that are linked to the `playground` app.

```shell
dokku typesense:app-links playground
```

### create container <new-name> then copy data from <name> into <new-name>

```shell
# usage
dokku typesense:clone <service> <new-service> [--clone-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-m|--memory MEMORY`: container memory limit in megabytes (default: unlimited)
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-p|--password PASSWORD`: override the user-level service password
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-r|--root-password PASSWORD`: override the root-level service password
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for typesense docker container

You can clone an existing service to a new one:

```shell
dokku typesense:clone lollipop lollipop-2
```

### check if the typesense service exists

```shell
# usage
dokku typesense:exists <service>
```

Here we check if the lollipop typesense service exists.

```shell
dokku typesense:exists lollipop
```

### check if the typesense service is linked to an app

```shell
# usage
dokku typesense:linked <service> <app>
```

Here we check if the lollipop typesense service is linked to the `playground` app.

```shell
dokku typesense:linked lollipop playground
```

### list all apps linked to the typesense service

```shell
# usage
dokku typesense:links <service>
```

List all apps linked to the `lollipop` typesense service.

```shell
dokku typesense:links lollipop
```
### Backups

Datastore backups are supported via AWS S3 and S3 compatible services like [minio](https://github.com/minio/minio).

You may skip the `backup-auth` step if your dokku install is running within EC2 and has access to the bucket via an IAM profile. In that case, use the `--use-iam` option with the `backup` command.

Backups can be performed using the backup commands:

### cat the contents of the configured backup cronfile for the service

```shell
# usage
dokku typesense:backup-schedule-cat <service>
```

Cat the contents of the configured backup cronfile for the service:

```shell
dokku typesense:backup-schedule-cat lollipop
```

### Disabling `docker image pull` calls

If you wish to disable the `docker image pull` calls that the plugin triggers, you may set the `TYPESENSE_DISABLE_PULL` environment variable to `true`. Once disabled, you will need to pull the service image you wish to deploy as shown in the `stderr` output.

Please ensure the proper images are in place when `docker image pull` is disabled.
