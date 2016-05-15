# Using TREZOR as a hardware SSH agent

[![Build Status](https://travis-ci.org/romanz/trezor-agent.svg?branch=master)](https://travis-ci.org/romanz/trezor-agent)
[![Python Versions](https://img.shields.io/pypi/pyversions/trezor_agent.svg)](https://pypi.python.org/pypi/trezor_agent/)
[![Package Version](https://img.shields.io/pypi/v/trezor_agent.svg)](https://pypi.python.org/pypi/trezor_agent/)
[![Development Status](https://img.shields.io/pypi/status/trezor_agent.svg)](https://pypi.python.org/pypi/trezor_agent/)
[![Downloads](https://img.shields.io/pypi/dm/trezor_agent.svg)](https://pypi.python.org/pypi/trezor_agent/)
[![Chat](https://badges.gitter.im/romanz/trezor-agent.svg)](https://gitter.im/romanz/trezor-agent)
[![License](https://img.shields.io/github/license/romanz/trezor-agent.svg)](https://github.com/romanz/trezor-agent/blob/master/LICENSE)

See SatoshiLabs' blog post about this feature:

- https://medium.com/@satoshilabs/trezor-firmware-1-3-4-enables-ssh-login-86a622d7e609

## Screencast demo usage

### Simple usage (single SSH session)
[![Demo](https://asciinema.org/a/22959.png)](https://asciinema.org/a/22959)

### Advanced usage (multiple SSH sessions from a sub-shell)
[![Subshell](https://asciinema.org/a/33240.png)](https://asciinema.org/a/33240)

## Installation

Install trezor-agent and it's dependencies into a virtualenv:

	pip install virtualenvwrapper

	mkvirtualenv trezor-agent
	pip install --upgrade setuptools
	pip install --upgrade pip
	pip install Cython "trezor>=0.6.6"

	pip install -e git+https://github.com/WyseNynja/trezor-agent.git#egg=trezor_agent
	mkdir ~/bin
	ln -sfv $WORKON_HOME/trezor-agent/bin/trezorctl ~/bin/
	ln -sfv $WORKON_HOME/trezor-agent/bin/trezor-agent ~/bin/

Make sure ~/bin is on your PATH.

Finally, verify that you are running the latest TREZOR firmware version (at least v1.3.4):

	$ trezorctl get_features
	vendor: "bitcointrezor.com"
	major_version: 1
	minor_version: 3
	patch_version: 4
	...

## Public key generation

Add an environment variable to your ~/.bash_profile, ~/.zshrc, or whatever:

	$ export TREZOR_SSH_IDENTITY=username@trezorname.trezor

Run:

	$ trezor-agent ssh.hostname.com -v > ${TREZOR_SSH_IDENTITY}.pub
	2015-09-02 15:03:18,929 INFO         getting "ssh://${TREZOR_SSH_IDENTITY}" public key (nist256p1) from Trezor...
	Use the numeric keypad to describe number positions. The layout is:
	    7 8 9
	    4 5 6
	    1 2 3
	Please enter current PIN: 

	2015-09-02 15:03:23,342 INFO         disconnected from Trezor

	$ cat ${TREZOR_SSH_IDENTITY}.pub
	ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGSevcDwmT+QaZPUEWUUjTeZRBICChxMKuJ7dRpBSF8+qt+8S1GBK5Zj8Xicc8SHG/SE/EXKUL2UU3kcUzE7ADQ= ssh://${TREZOR_SSH_IDENTITY}

Append `${TREZOR_SSH_IDENTITY}.pub` contents to `~/.ssh/authorized_keys` configuration file at `ssh.hostname.com` or any other server to allow you to login using the corresponding private key signature.

## Usage

Run:

	/tmp $ trezor-agent ssh.hostname.com -v -c
	2015-09-02 15:09:39,782 INFO         getting "ssh://${TREZOR_SSH_IDENTITY}" public key (nist256p1) from Trezor...
	2015-09-02 15:09:44,430 INFO         please confirm user "roman" login to "ssh://ssh.hostname.com" using Trezor...
	2015-09-02 15:09:46,152 INFO         signature status: OK
	Linux lmde 3.16.0-4-amd64 #1 SMP Debian 3.16.7-ckt11-1+deb8u3 (2015-08-04) x86_64

	The programs included with the Debian GNU/Linux system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
	permitted by applicable law.
	Last login: Tue Sep  1 15:57:05 2015 from localhost
	~ $

Make sure to confirm SSH signature on the Trezor device when requested.
