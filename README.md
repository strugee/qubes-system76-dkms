# qubes-system76-dkms

This repository contains the [System76 DKMS kernel module](https://github.com/pop-os/system76-dkms), packaged for the [Qubes security operating system](https://www.qubes-os.org/). Per upstream's description, "this DKMS driver provides airplane mode, keyboard backlight, and fan support for System76 laptops." However, airplane mode currently does not work under Qubes in the same way it does in Pop!_OS.

## Installation

This repository is designed to be used with [Qubes Builder](https://www.qubes-os.org/doc/qubes-builder/), so you need to set that up first. If you haven't done this before and are going through the linked documentation, you can stop where it says to edit the `builder.conf`. In fact, if you're only using Qubes Builder to build this module, you can even skip copying one of the suggested `builder.conf`s and instead use this (extremely) minimal one:

```
GIT_BASEURL ?= https://github.com
GIT_PREFIX ?= QubesOS/qubes-
NO_SIGN ?= 1
DIST_DOM0 ?= fc25
BRANCH ?= release4.0

# Uncomment this if you don't have my key and are going to audit the source manually
#NO_CHECK=system76-dkms
COMPONENTS ?= system76-dkms builder-rpm
BUILDER_PLUGINS = builder-rpm
GIT_URL_system76_dkms = https://github.com/strugee/qubes-system76-dkms.git
BRANCH_system76_dkms = master
```

Once you have set up Qubes Builder, run these commands in the `qubes-builder` directory:

```
$ make get-sources COMPONENTS=system76-dkms
$ make system76-dkms-dom0
```

This will create `qubes-src/system76-dkms/pkgs/dom0-fc25/noarch/qubes-system76-dkms-1.0.6-1.fc25.noarch.rpm` in the Qubes Builder directory. Next, use `qvm-run` in dom0 to copy the package from the qube where you built the package (here assumed to be named `qubesdev`):

```
$ qvm-run --pass-io qubesdev 'cat /home/user/qubes-builder/qubes-src/system76-dkms/pkgs/dom0-fc25/noarch/qubes-system76-dkms-1.0.6-1.fc25.noarch.rpm' > qubes-system76-dkms-1.0.6-1.fc25.noarch.rpm
```

Make sure to modify the path to your `qubes-builder` directory if necessary.

The last step is to install the just-copied package, so in dom0 run:

```
$ sudo dnf install ./qubes-system76-dkms-1.0.6-1.fc25.noarch.rpm
```

If you run into problems, please [report a bug](https://github.com/strugee/qubes-system76-dkms/issues/new).

## Security

I have audited the upstream source for any obvious backdoors and as expected, found no problems (trust, but verify). I am not a kernel developer and have only beginner to intermediate experience in C, so this cannot be even remotely considered a decent security audit. However, given that only dom0 can talk to this kernel module, my inability to spot subtle C bugs is probably not a real problem and so looking for obviously malicious behavior will be enough.

Based on the above, _I_ trust that this code is not malicious. However, the next problem for _you_ is of course whether you can trust _me_. If you do, all git commits and tags are signed with my public [GPG key](https://strugee.net/gpg), which is available in a number of places (see the link for a list). If you don't trust me - and you probably shouldn't unless you have special reason to - I suggest you perform an audit similar to the one I did to independently verify the trustworthiness of this repository.

I do not distribute binary RPMs because most people would not be able to trust them. Additionally the RPMs built from this repository are not reproducible yet (see [bug #10](https://github.com/strugee/qubes-system76-dkms/issues/10)) which only compounds the aforementioned trust problem.

## License

GPL version 2 or later

## Author

Written by Jeremy Soller <jeremy@system76.com> and packaged for Qubes by AJ Jordan <alex@strugee.net>
