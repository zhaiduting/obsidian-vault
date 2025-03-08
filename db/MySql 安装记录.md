``` hl:' run:','To '
Last login: Thu Mar  6 07:50:44 on console
~ > brew install mysql
HOMEBREW_BREW_GIT_REMOTE set: using https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git as the Homebrew/brew Git remote.
remote: Enumerating objects: 300, done.
remote: Counting objects: 100% (140/140), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 300 (delta 133), reused 123 (delta 123), pack-reused 160
Receiving objects: 100% (300/300), 517.93 KiB | 5.57 MiB/s, done.
Resolving deltas: 100% (167/167), completed with 55 local objects.
From https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew
 * [new branch]            global_env_cleanup -> origin/global_env_cleanup
   29fa76c948..46eb7ec4d8  master             -> origin/master
==> Auto-updated Homebrew!
Updated 2 taps (homebrew/core and homebrew/cask).
==> New Formulae
fortitude           rhai                semver              typioca
largetifftools      rpds-py             sequoia-sqv         unciv
==> New Casks
consul              focu                macskk              trae-cn

You have 11 outdated formulae installed.

==> Fetching dependencies for mysql: abseil, ca-certificates, protobuf, zlib and zstd
==> Fetching abseil
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/abseil-202
######################################################################### 100.0%
==> Fetching ca-certificates
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/ca-certifi
######################################################################### 100.0%
==> Fetching protobuf
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/protobuf-2
######################################################################### 100.0%
==> Fetching zlib
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/zlib-1.3.1
######################################################################### 100.0%
==> Fetching zstd
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/zstd-1.5.7
######################################################################### 100.0%
==> Fetching mysql
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/mysql-9.2.
######################################################################### 100.0%
==> Installing dependencies for mysql: abseil, ca-certificates, protobuf, zlib and zstd
==> Installing mysql dependency: abseil
==> Pouring abseil-20240722.1.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/abseil/20240722.1: 772 files, 11.8MB
==> Installing mysql dependency: ca-certificates
==> Pouring ca-certificates-2025-02-25.all.bottle.tar.gz
==> Regenerating CA certificate bundle from keychain, this may take a while...
🍺  /opt/homebrew/Cellar/ca-certificates/2025-02-25: 4 files, 235.8KB
==> Installing mysql dependency: protobuf
==> Pouring protobuf-29.3.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/protobuf/29.3: 445 files, 15.4MB
==> Installing mysql dependency: zlib
==> Pouring zlib-1.3.1.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/zlib/1.3.1: 14 files, 403.9KB
==> Installing mysql dependency: zstd
==> Pouring zstd-1.5.7.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/zstd/1.5.7: 32 files, 2.2MB
==> Installing mysql
==> Pouring mysql-9.2.0.arm64_sequoia.bottle.1.tar.gz
==> /opt/homebrew/Cellar/mysql/9.2.0/bin/mysqld --initialize-insecure --user=zdt
==> Caveats
Upgrading from MySQL <8.4 to MySQL >9.0 requires running MySQL 8.4 first:
 - brew services stop mysql
 - brew install mysql@8.4
 - brew services start mysql@8.4
 - brew services stop mysql@8.4
 - brew services start mysql

We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -u root

To start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  /opt/homebrew/opt/mysql/bin/mysqld_safe --datadir\=/opt/homebrew/var/mysql
==> Summary
🍺  /opt/homebrew/Cellar/mysql/9.2.0: 329 files, 286.3MB
==> Running `brew cleanup mysql`...
Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
==> Upgrading 3 dependents of upgraded formulae:
Disable this behaviour by setting HOMEBREW_NO_INSTALLED_DEPENDENTS_CHECK.
Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
glib 2.82.4 -> 2.82.5, node 23.7.0 -> 23.9.0, qemu 9.2.1 -> 9.2.2
==> Fetching dependencies for glib: gettext
==> Fetching gettext
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/gettext-0.
######################################################################### 100.0%
==> Fetching glib
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/glib-2.82.
######################################################################### 100.0%
==> Fetching dependencies for node: libnghttp2
==> Fetching libnghttp2
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/libnghttp2
######################################################################### 100.0%
==> Fetching node
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/node-23.9.
######################################################################### 100.0%
==> Fetching dependencies for qemu: capstone and libpng
==> Fetching capstone
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/capstone-5
######################################################################### 100.0%
==> Fetching libpng
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/libpng-1.6
######################################################################### 100.0%
==> Fetching qemu
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/qemu-9.2.2
######################################################################### 100.0%
==> Upgrading glib
  2.82.4 -> 2.82.5 
==> Installing dependencies for glib: gettext
==> Installing glib dependency: gettext
==> Pouring gettext-0.24.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/gettext/0.24: 2,189 files, 19.9MB
==> Installing glib
==> Pouring glib-2.82.5.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/glib/2.82.5: 504 files, 36.3MB
==> Running `brew cleanup glib`...
Removing: /opt/homebrew/Cellar/glib/2.82.4... (504 files, 36.3MB)
Removing: /Users/zdt/Library/Caches/Homebrew/glib--2.82.4.arm64_sequoia.bottle.1.tar.gz... (8.5MB)
==> Upgrading node
  23.7.0 -> 23.9.0 
==> Installing dependencies for node: libnghttp2
==> Installing node dependency: libnghttp2
==> Pouring libnghttp2-1.65.0.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/libnghttp2/1.65.0: 14 files, 767KB
==> Installing node
==> Pouring node-23.9.0.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/node/23.9.0: 2,602 files, 71.3MB
==> Running `brew cleanup node`...
Removing: /opt/homebrew/Cellar/node/23.7.0... (2,602 files, 70.6MB)
Removing: /Users/zdt/Library/Caches/Homebrew/node_bottle_manifest--23.7.0... (15.7KB)
Removing: /Users/zdt/Library/Caches/Homebrew/node--23.7.0... (18.5MB)
==> Upgrading qemu
  9.2.1 -> 9.2.2 
==> Installing dependencies for qemu: capstone and libpng
==> Installing qemu dependency: capstone
==> Pouring capstone-5.0.5.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/capstone/5.0.5: 31 files, 22.4MB
==> Installing qemu dependency: libpng
==> Pouring libpng-1.6.47.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/libpng/1.6.47: 28 files, 1.3MB
==> Installing qemu
==> Pouring qemu-9.2.2.arm64_sequoia.bottle.tar.gz
🍺  /opt/homebrew/Cellar/qemu/9.2.2: 167 files, 669.5MB
==> Running `brew cleanup qemu`...
Removing: /opt/homebrew/Cellar/qemu/9.2.1... (167 files, 669.5MB)
Removing: /Users/zdt/Library/Caches/Homebrew/qemu_bottle_manifest--9.2.1... (49.6KB)
Removing: /Users/zdt/Library/Caches/Homebrew/qemu--9.2.1... (103.4MB)
==> Checking for dependents of upgraded formulae...
==> No broken dependents found!
==> Caveats
==> mysql
Upgrading from MySQL <8.4 to MySQL >9.0 requires running MySQL 8.4 first:
 - brew services stop mysql
 - brew install mysql@8.4
 - brew services start mysql@8.4
 - brew services stop mysql@8.4
 - brew services start mysql

We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -u root

To start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  /opt/homebrew/opt/mysql/bin/mysqld_safe --datadir\=/opt/homebrew/var/mysql
~ > 

```