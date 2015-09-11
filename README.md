# dobby_rest_node

Dobby Erlang node that depends on `dobby_rest_lib` intended for running
Dobby Rest as standalone service.

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc/generate-toc again -->
**Table of Contents**

- [dobby_rest_node](#dobbyrestnode)
    - [Requirements](#requirements)
    - [Building](#building)
    - [Running](#running)
    - [Connecting via ssh](#connecting-via-ssh)
    - [Configuration](#configuration)

<!-- markdown-toc end -->


## Requirements
- Erlang R17+
- [dobby](https://github.com/FlowForwarding/dobby)

## Building
At the beginning get rebar:
```shell
make rebar
```

If you want to connect to the dobby_rest_core Erlang shell using ssh with
keys, you must
generate keys for `deps/erl_sshd`.  Using make:
```shell
make
```
Using rebar and relx directly:
```shell
./rebar get-deps
./rebar compile
deps/erl_sshd/make_keys
./relx -c _rel/relx.config
```
You may add your own public keys to the `authorized_keys` file in
`priv/erl_sshd` (remember to `./relx -c _rel/relx.config` or `make rel`
afterwards).

If you want to connect to the dobby Erlang shell using ssh with
a username and password,
add or modify the usernames and passwords
to the `erl_sshd` section of `rel/files/sys.config`.

## Running
```shell
_rel/dobby_rest_node/bin/dobby_rest_node
```

To access the Dobby Visualizer go to http://localhost:8080/static/www/index.html

## Connecting via ssh
If you genereated keys in erl_sshd before generating the dobby release,
you can connect to the dobby server's Erlang shell using ssh.
```shell
ssh 127.0.0.1 -p 11144 -i id_rsa
```

or

```shell
ssh lincx@127.0.0.1 -p 11144
```

> The username and password is set via `config/sys.conifg`. Generate
> a new release after changing this file.

To exit the Erlang shell obtained via ssh call `exit().`

## Configuration

The `sys.config` and the `vm.args` files are stored in the `config/` directory.

dobby_rest_node tries to connect to a `dobby` node when starting. The dobby
node name is stored in the sys.config.


## Configuring TLS

To enable TLS support for the HTTP interface you have to configure it in the `erl_cowboy`
application and provide the following options:

* certificate file name (expected in the `priv/erl_cowboy`)
* key file name (expected in the `priv/erl_cowboy`)
* password to the key if it is password protected

The configuration has to be placed in the sys.config file. Below is an example:
```erlang
[
...
 {erl_cowboy, [
               {port, 8080},
               {listeners, 10},
               {app, 'dobby_rest_node'},
               {tls_enabled, true},
               {tls_opts, [{certfile, "dummy.crt"},
                           {keyfile, "dummy.key"},
                           {password, ""}]}
               ]},
...
]
```

There is a sample certificate and key generator that you can run with:
`make tls`.
The above example config works with the generated files. To test the TLS,
put the config snippet into the `config/sys.config`. Remember
to re-generated the release after the change.

With TLS enabled, the Visualizer can be accessed via https://localhost:8080/static/www/index.html.
