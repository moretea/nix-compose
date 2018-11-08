# Nix Compose

Nix compose is modeled afer [docker-compose](https://docs.docker.com/compose/overview/); it can be used set up an isolated enviornment that is not _exactly_ like a production environment, but close enough to faciliate easy development.

It's goal is to have the same smooth experience offered by `docker-compose`, by deploying NixOS configurations to ephemeral KVM-machines.

In contrast to docker-compose, nix-compose:
- Ships with a set of pre-build services to quickly get started with the important part -- your service
- And enables you to leverage nix-shell to have a reproducible environment

## Examples

Given a `compose.nix` file:

```nix
{ compositions }:
{
  version = "1.0";
  services = {
    database = compositions.postgresDB {};
    cache = compositions.redis {};
    api = self :{
      system = import ./api.nix;
      ports = [ { host =  8080; service = 80; }];
    };
  };
}
```

You can perform the following steps:

* Start up all services, great to demo an application.:

  ```
  nix-compose up
  ```

* Just start the database & cache, so that you can use it from a shell.

  ```
  nix-compose up database cache
  ```

* Enter a virtualized development shell; handy for e.g. MacOS users.

  ```
  nix-compose shell
  ```

  This will start a KVM machine and:

  * Take your `shell.nix` to make the development dependencies available
  * Mount the current directory R/W in the machine.

# Credits
- Originated during discussions with [manveru](https://github.com/manveru/) on NixCon 2018
- Very much inspired by [github.com/Mic92/nixo-shell](https://github.com/Mic92/nixos-shell)
