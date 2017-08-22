Title: Setup sudo with google-authenticator for 2 Factor authentication on CentOS 7.
Date: 2015-12-21
Status: Published

By configuring the google-authenticator PAM module with sudo, you can
force system users to have to authenticate with one-time passcode and
their system password in order to use sudo.

From [https://github.com/google/google-authenticator](https://github.com/google/google-authenticator):

> The Google Authenticator project includes implementations of one-time
passcode generators for several mobile platforms, as well as a pluggable
authentication module (PAM).

To set this up on CentOS7, we'll install the
google-authenticator PAM module and update your server's PAM
configuration.

First, install the tools required to build the google-authenticator PAM module.

```
# yum install -y git autoconf automake make libtool pam-devel
```

Clone the google-authenticator git repo, build and install the plugin.

```
# git clone https://github.com/google/google-authenticator.git
# cd google-authenticator/libpam
# ./bootstrap.sh
# ./configure
# make
# make install
```

This will install the google-authenticator binary and the
pam_google_authenticator.so PAM module under /usr/local.

Before continuing, login as root and do not exit from this login whilst making
changes to your system. A mistake could lock you out from your root
account.

Add pam_google_authenticator.so to /etc/pam.d/sudo.

```
auth       required     /usr/local/lib/security/pam_google_authenticator.so forward_pass nullok
auth       include      system-auth
account    include      system-auth
password   include      system-auth
session    optional     pam_keyinit.so revoke
session    required     pam_limits.so
```

Its important that the path used to define pam_google_authenticator.so
is correct or else PAM may not be able to find
pam_google_authenticator.so and sudo will log an error.

```
Dec 21 09:25:34 server sudo: PAM unable to dlopen(/usr/lib64/security/pam_google_authenticator.so): /usr/lib64/security/pam_google_authenticator.so: cannot open shared object file: No such file or directory
Dec 21 09:25:34 server sudo: PAM adding faulty module: /usr/lib64/security/pam_google_authenticator.so
```

It is also important that the pam_google_authenticator.so is found
before the 'auth include system-auth' line within /etc/pam.d/sudo.

Any user who will need to use sudo now needs to setup their secret key
and google-authenticator settings which live in ~/.google_authenticator
by simply running the google-authenticator binary on the server. They will
be shown a QRCode that can be scanned into their two-factor authentication
mobile app such as [Authy](https://www.authy.com/) or [Google
Authenticator](https://support.google.com/accounts/answer/1066447).

Next time the user uses sudo, they will be asked for their system
password and one-time passcode.

Once all your users who use sudo have setup their google-authenticator
secret key, you should remove nullok from /etc/pam.d/sudo.
