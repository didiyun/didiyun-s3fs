didiyun-s3fs
====

didiyun-s3fs allows Linux and macOS to mount an didiyun S3 bucket via FUSE. You can conveniently operate on objects in didiyun s3 like local file through didiyun-s3fs.

Features
--------

The didiyun-s3fs is build based on S3FS(Release version 1.85) and has all the features of S3FS.
* large subset of POSIX including reading/writing files, directories, symlinks, mode, uid/gid, and extended attributes
* compatible with Amazon S3, Google Cloud Storage, and other S3-based object stores
* large files via multi-part upload
* renames via server-side copy
* optional server-side encryption
* data integrity via MD5 hashes
* in-memory metadata caching
* local disk data caching
* user-specified regions, including Amazon GovCloud
* authenticate via v2 or v4 signatures

Compilation and installation from sources
-----------------------------------------

These are generic instructions to compile from the master branch, and should work on almost any GNU/Linux, macOS, BSD, or similar.

If you want specific instructions for some distributions, check the [wiki](https://github.com/s3fs-fuse/s3fs-fuse/wiki/Installation-Notes).

Keep in mind using the pre-built packages when available.

1. Ensure your system satisfies build and runtime dependencies for:

   * fuse >= 2.8.4
   * automake
   * gcc-c++
   * make
   * libcurl
   * libxml2
   * openssl

2. Install dependencies

- Ubuntu 14.04
    ```
    sudo apt-get install automake autotools-dev g++ git libcurl4-gnutls-dev \
                        libfuse-dev libssl-dev libxml2-dev make pkg-config
    ```

- CentOS
    ```
    sudo yum install automake fuse fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel
    ```

3. Then compile from master via the following commands:

   ```
   git clone https://github.com/didiyun/didiyun-s3fs.git
   cd didiyun-s3fs
   ./autogen.sh
   ./configure
   make
   # if you do not want to install, don't execute the following command
   sudo make install
   ```
4. the binary is in src directory

Run didiyun-s3fs
--------

The default location for the didiyun-s3fs password file can be created:

* using a .passwd-s3fs file in the users home directory (i.e. ${HOME}/.passwd-s3fs)
* using the system-wide /etc/passwd-s3fs file

Enter your credentials in a file `${HOME}/.passwd-s3fs` and set
owner-only permissions:

```
echo ACCESS_KEY_ID:SECRET_ACCESS_KEY > ${HOME}/.passwd-s3fs
chmod 600 ${HOME}/.passwd-s3fs
```

Example
--------

Run s3fs with an existing bucket `mybucket` and directory `/path/to/mountpoint`:

```
s3fs mybucket /path/to/mountpoint -o passwd_file=${HOME}/.passwd-s3fs -o url=http://s3.didiyunapi.com -o use_path_request_style
```

If you encounter any errors, enable debug output:

```
s3fs mybucket /path/to/mountpoint -o passwd_file=${HOME}/.passwd-s3fs -o url=http://s3.didiyunapi.com -o use_path_request_style -o dbglevel=info -f -o curldbg
```

umount
--------
- umount directory

```
sudo umount /path/to/mountpoint
```

if you can not umount directory, you can try this command
```
sudo umount -l /home/ec2-user/s3mnt
```

Advanced settings
-------------
- You can add the '-o f2 -o allow_other -o umask=000' parameters to let anyone has the permission of the mount directory

Limitations
-----------

Generally S3 cannot offer the same performance or semantics as a local file system.  More specifically:

* random writes or appends to files require rewriting the entire file
* metadata operations such as listing directories have poor performance due to network latency
* [eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency) can temporarily yield stale data([Amazon S3 Data Consistency Model](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel))
* no atomic renames of files or directories
* no coordination between multiple clients mounting the same bucket
* no hard links
* inotify detects only local modifications, not external ones by other clients or tools


Frequently Asked Questions
--------------------------
* [FAQ wiki page](https://github.com/didiyun/didiyun-s3fs/wiki/FAQ)

License
-------

Copyright (C) 2010 Randy Rizun <rrizun@gmail.com>

Licensed under the GNU GPL version 2

