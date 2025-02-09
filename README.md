# openresty-build-tools

Reusable script for bootstrapping core components needed for Kong development.

This script builds different flavors of OpenSSL, OpenResty and LuaRocks depends on command
line arguments passed to it. It does this independently of what the base O/S library version
is so you always ends up with the same binary, every time.

# Synopsis
```
./kong-ngx-build -p buildroot --openresty 1.13.6.2 --openssl 1.1.1b --luarocks 3.0.4 --pcre 8.43 --debug
```

# Usage
```
$ ./kong-ngx-build -h
Build basic components (OpenResty, OpenSSL and LuaRocks) for Kong.

Usage: ./kong-ngx-build [options...] -p <prefix> --openresty <openresty_ver> --openssl <openssl_ver> --pcre <pcre_ver>

Required arguments:
  -p, --prefix <prefix>            Location where components should be installed.
      --openresty <openresty_ver>  Version of OpenResty to build, such as 1.13.6.2.
      --openssl <openssl_ver>      Version of OpenSSL to build, such as 1.1.1c.

Optional arguments:
      --no-build-cache             Build from scratch.
                                   (WARNING: this removes everything inside the work directory)

      --no-artifact-cache          Disable artifact caching and re-install all the softwares.
                                   (WARNING: this removes everything inside the prefix directory)

      --no-openresty-patches       Do not apply openresty-patches while compiling OpenResty.
                                   (Patching is enabled by default)

      --openresty-patches <branch> Specify an openresty-patches branch to use when applying patches.
                                   (Defaults to "master")

      --luarocks <luarocks_ver>    Version of LuaRocks to build, such as 3.1.2. If absent, LuaRocks
                                   will not be built.

      --add-module <module_path>   Path to additional NGINX module to be built. This option can be
                                   repeated and will be passed to NGINX's configure in the order
                                   they were specified.

      --debug                      Disable compile-time optimizations and memory pooling for NGINX,
                                   LuaJIT and OpenSSL to help debugging.

  -j, --jobs                       Concurrency level to use when building.
                                   (Defaults to number of CPU cores available: 8)

      --work <work_dir>            The working directory to use while compiling.
                                   (Defaults to "work")

  -h, --help                       Show this message.

Optional ENV:

The following ENV's are likely only utilized when building for the purposes of packaging Kong

  LUAROCKS_INSTALL                 Overrides the `./config --prefix` value (default is `<prefix>/luarocks`)
  
  LUAROCKS_DESTDIR                 Overrides the `make install DESTDIR` (default is `/`)
  
  OPENRESTY_INSTALL                Overrides the `./config --prefix` value (default is `--prefix/openresty)
  
  OPENRESTY_DESTDIR                Overrides the `make install DESTDIR` (default is `/`)
  
  OPENSSL_INSTALL                  Overrides the `./config --prefix` value (default is `--prefix/openssl)
  
  OPENRESTY_RPATH                  Overrides the `make install DESTDIR` (default is `/`)
  
```

# Caching
This script supports two level of caching: artifact caching and build caching
in order to speed up re-compilation time for various different scenarios.

By default, both artifact caching and build caching are enabled.

Artifact caching checks the installation directory inside `prefix`, if that
directory is found, then it is assumed that software has been built already
and no more work is done on it.

Build caching saves the source code directory of the software to be built.
Repeated run will simply call `make` and `make install` again which supports
incremental rebuild Make provides. This is especially useful when developing
on OpenResty or NGINX C code.

# License

```
Copyright 2019 Kong Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
