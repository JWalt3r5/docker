# chef-docker [![Build Status](https://secure.travis-ci.org/bflad/chef-docker.png?branch=master)](http://travis-ci.org/bflad/chef-docker)

## Description

Installs/Configures [Docker](http://docker.io). Please see [COMPATIBILITY.md](COMPATIBILITY.md) for more information about Docker versions that are tested and supported by cookbook versions along with LWRP features.

This cookbook was inspired by @thoward's docker-cookbook: https://github.com/thoward/docker-cookbook

## Requirements

### Chef

* Chef 11+

### Platforms

* CentOS 6
* Debian 7 (experimental)
* Fedora 19
* Fedora 20
* Oracle 6 (experimental)
* RHEL 6
* Ubuntu 12.04
* Ubuntu 12.10
* Ubuntu 13.04
* Ubuntu 13.10 (experimental)

### Cookbooks

[Opscode Cookbooks](https://github.com/opscode-cookbooks/)

* [apt](https://github.com/opscode-cookbooks/apt)
* [git](https://github.com/opscode-cookbooks/git)
* [yum-epel](https://github.com/opscode-cookbooks/yum-epel)

Third-Party Cookbooks

* [golang](https://github.com/NOX73/chef-golang)
* [lxc](https://github.com/hw-cookbooks/lxc)
* [modules](https://github.com/Youscribe/modules-cookbook)
* [sysctl](https://github.com/onehealth-cookbooks/sysctl)

## Attributes

These attributes are under the `node['docker']` namespace.

Attribute | Description | Type | Default
----------|-------------|------|--------
arch | Architecture for docker binary (note: Docker only currently supports x86_64) | String | auto-detected (see attributes/default.rb)
bind_socket | Socket path that docker should bind | String | unix:///var/run/docker.sock
bind_uri | TCP URI docker should bind | String | nil
container_cmd_timeout | container LWRP default cmd_timeout seconds | Fixnum | 60
container_init_type | Init type for docker containers (nil, "systemd", or "upstart") | NilClass or String | `node['docker']['init_type']`
http_proxy | HTTP_PROXY environment variable | String | nil
image_cmd_timeout | image LWRP default cmd_timeout seconds | Fixnum | 300
init_type | Init type for docker ("systemd", "sysv", or "upstart") | String | auto-detected (see attributes/default.rb)
install_dir | Installation directory for docker binary | String | auto-detected (see attributes/default.rb)
install_type | Installation type for docker ("binary", "package" or "source") | String | "package"
options | Additional options to pass to docker. These could be flags like "-api-enable-cors". | String | nil
registry_cmd_timeout | registry LWRP default cmd_timeout seconds | Fixnum | 60
version | Version of docker | String | nil

### Binary Attributes

These attributes are under the `node['docker']['binary']` namespace.

Attribute | Description | Type | Default
----------|-------------|------|--------
version | Version of docker binary | String | `node['docker']['version'] || latest`
url | URL for downloading docker binary | String | auto-detected (see attributes/default.rb)

### Package Attributes

These attributes are under the `node['docker']['package']` namespace.

Attribute | Description | Type | Default
----------|-------------|------|--------
distribution | Distribution for docker packages | String | auto-detected (see attributes/default.rb)
repo_url | Repository URL for docker packages | String | auto-detected (see attributes/default.rb)

### Source Attributes

These attributes are under the `node['docker']['source']` namespace.

Attribute | Description | Type | Default
----------|-------------|------|--------
ref | Repository reference for docker source | String | "master"
url | Repository URL for docker source | String | "https://github.com/dotcloud/docker.git"

## Recipes

* `recipe[docker]` Installs/Configures Docker
* `recipe[docker::aufs]` Installs/Loads AUFS Linux module
* `recipe[docker::binary]` Installs Docker binary
* `recipe[docker::cgroups]` Installs/configures default platform Control Groups support
* `recipe[docker::lxc]` Installs/configures default platform LXC support
* `recipe[docker::package]` Installs Docker via package
* `recipe[docker::source]` Installs Docker via source
* `recipe[docker::systemd]` Installs/Starts Docker via systemd
* `recipe[docker::sysv]` Installs/Starts Docker via SysV
* `recipe[docker::upstart]` Installs/Starts Docker via Upstart

## LWRPs

### docker_container

By default, this will handle creating a service for the container when action is run or start. `set['docker']['container_init_type'] = false` or add `init_type false` for LWRP to disable this behavior.

These attributes are under the `docker_container` LWRP namespace.

Attribute | Description | Type | Default
----------|-------------|------|--------
attach | Attach container's stdout/stderr and forward all signals to the process | TrueClass, FalseClass | nil
author | Author for commit | String | nil
cidfile | File to store container ID | String | nil
cmd_timeout | Timeout for docker commands (catchable exception: `Chef::Provider::Docker::Container::CommandTimeout`)| Integer | `node['docker']['container_cmd_timeout']`
command | Command to run in container | String | nil
container_name | Name for container/service | String | nil
cookbook | Cookbook to grab any templates | String | docker
cpu_shares | CPU shares for container | Fixnum | nil
destination | Destination path/URL for file operation | String | nil
detach | Detach from container when starting | TrueClass, FalseClass | nil
dns | DNS servers for container | String, Array | nil
entrypoint | Overwrite the default entrypoint set by the image | String | nil
env | Environment variables to pass to container | String, Array | nil
expose | Expose a port from the container without publishing it to your host | Fixnum, String, Array | nil
hostname | Container hostname | String | nil
id | Container ID (internally set by LWRP) | String | nil
image | Image for container | String | LWRP name
init_type | Init type for container service handling | FalseClass, String | `node['docker']['container_init_type']`
init_template | Template to use for init configuration | String | nil
link | Add link to another container | String, Array | nil
lxc_conf | Custom LXC options | String, Array | nil
memory | Set memory limit for container | Fixnum | nil
message | Message for commit | String | nil
port | Map network port(s) to the container | Fixnum (*DEPRECATED*), String, Array | nil
privileged | Give extended privileges | TrueClass, FalseClass | nil
public_port (*DEPRECATED*) | Map host port to container | Fixnum | nil
publish_exposed_ports | Publish all exposed ports to the host interfaces | TrueClass, FalseClass | false
remove_automatically | Automatically remove the container when it exits (incompatible with detach) | TrueClass, FalseClass | false
running | Container running status (internally set by LWRP) | TrueClass, FalseClass | nil
socket_template | Template to use for configuring socket (relevent for init_type systemd only) | String | nil
source | Source path/URL for file operation | String | nil
stdin | Attach container's stdin | TrueClass, FalseClass | nil
tty | Allocate a pseudo-tty | TrueClass, FalseClass | nil
user | User to run container | String | nil
volume | Create bind mount(s) with: [host-dir]:[container-dir]:[rw|ro]. If "container-dir" is missing, then docker creates a new volume. | String, Array | nil
volumes_from | Mount all volumes from the given container(s) | String | nil
working_directory | Working directory inside the container | String | nil

Run a container:

    docker_container "busybox" do
      command "sleep 9999"
      detach true
    end

Run a container from image (docker-registry for example):

    docker_container "docker-registry" do
      image "samalba/docker-registry"
      detach true
      hostname "xx.xx.xx.xx"
      port 5000
      env "SETTINGS_FLAVOR=local"
      volume "/mnt/docker:/docker-storage"
    end

Commit a container with optional repository and tag:

    docker_container 'myApp' do
      repository 'myRepo'
      tag Time.new.strftime("%Y%m%d%H%M")
      action :commit
    end

Copying a file from container to host:

    docker_container 'myApp' do
      source '/path/to/container/file'
      destination '/path/to/save/on/host'
      action :cp
    end

Exporting container to host:

    docker_container 'myApp' do
      destination '/path/to/save/on/host.tgz'
      action :export
    end

Stop a running container:

    docker_container "busybox" do
      command "sleep 9999"
      action :stop
    end

Kill a running container:

    docker_container "busybox" do
      command "sleep 9999"
      action :kill
    end

Start a stopped container:

    docker_container "busybox" do
      command "sleep 9999"
      action :start
    end

Restart a container:

    docker_container "busybox" do
      command "sleep 9999"
      action :restart
    end

Remove a container:

    docker_container "busybox" do
      command "sleep 9999"
      action :remove
    end

### docker_image

These attributes are under the `docker_image` LWRP namespace.

Attribute | Description | Type | Default
----------|-------------|------|--------
cmd_timeout | Timeout for docker commands (catchable exception: `Chef::Provider::Docker::Image::CommandTimeout`) | Integer | `node['docker']['image_cmd_timeout']`
destination | Destination path/URL | String | nil
dockerfile (*DEPRECATED*) | Dockerfile to build image | String | nil
force | Force operation | Boolean | false
id | Image ID (internally set by LWRP) | String | nil
image_name | Image name | String | LWRP name
image_url (*DEPRECATED*) | URL to grab image | String | nil
installed | Image installation status (internally set by LWRP) | TrueClass, FalseClass | nil
installed_tag | - | String | nil
path (*DEPRECATED*) | Local path to files | String | nil
registry | Registry server | String | nil
repository | Remote repository | String | nil
source | Source path/URL | String | nil
tag | Specific tag for image | String | nil

Build image from Dockerfile:

    docker_image "myImage" do
      tag "myTag"
      source "myImageDockerfile"
      action :build
    end

Build image from remote repository:

    docker_image "myImage" do
      source "example.com/foo/myImage"
      tag "myTag"
      action :build
    end

Pull latest image:

    docker_image "busybox"

Pull tagged image:

    docker_image "bflad/test" do
      tag "not-latest"
    end

Push image (after logging in with `docker_registry`):

    docker_image "bflad/test" do
      action :push
    end

Tag image:

    docker_image 'test' do
      repository 'bflad'
      tag '1.0.0'
      action :tag
    end

Import image from local directory:

    docker_image 'test' do
      source '/path/to/test'
      action :import
    end

Import image from local file:

    docker_image 'test' do
      source '/path/to/test.tgz'
      action :import
    end

Import image from remote URL:

    docker_image "test" do
      source "https://example.com/testimage.tgz"
      action :import
    end

Insert file from remote URL:

    docker_image 'test' do
      source 'http://example.com/some/file.txt'
      destination '/container/path/for/some/file.txt'
      action :insert
    end

Load repository from path:

    docker_image "test" do
      source "/path/to/test.tgz"
      action :load
    end

Save repository to path:

    docker_image "test" do
      destination "/path/to/test.tgz"
      action :save
    end

Remove image:

    docker_image "busybox" do
      action :remove
    end

### docker_registry

These attributes are under the `docker_registry` LWRP namespace.

Attribute | Description | Type | Default
----------|-------------|------|--------
cmd_timeout | Timeout for docker commands (catchable exception: `Chef::Provider::Docker::Registry::CommandTimeout`) | Integer | `node['docker']['registry_cmd_timeout']`
email | Registry email | String | nil
password | Registry password | String | nil
username | Registry username | String | nil

Log into public registry:

    docker_registry 'https://index.docker.io/v1/' do
      username 'publicme'
      password 'hope_this_is_in_encrypted_databag'
    end

Log into private registry with optional port:

    docker_registry 'https://docker-registry.example.com:8443/' do
      username 'privateme'
      password 'still_hope_this_is_in_encrypted_databag'
    end

Register with registry:

    docker_registry 'https://index.docker.io/v1/' do
      email 'publicme@example.com'
      username 'publicme'
      password 'hope_this_is_in_encrypted_databag'
    end

## Usage

### Default Installation

* Add `recipe[docker]` to your node's run list

## Testing and Development

* Quickly testing with Vagrant: [VAGRANT.md](VAGRANT.md)
* Full development and testing workflow with Test Kitchen and friends: [TESTING.md](TESTING.md)

## Contributing

Please see contributing information in: [CONTRIBUTING.md](CONTRIBUTING.md)

## Maintainers

* Brian Flad (<bflad417@gmail.com>)

## License

Please see licensing information in: [LICENSE](LICENSE)
