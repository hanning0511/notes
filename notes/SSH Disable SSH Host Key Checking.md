Tags: #ssh

---

By default, the **SSH client verifies the identity** of the host to which it connects.

If the remote host key is unknown to your SSH client, you would be asked to accept it by typing “yes” or “no”.

This could cause a trouble when running from script that automatically connects to a remote host over SSH protocol.

This article explains how to bypass this verification step by **disabling host key checking**.

## The Authenticity Of Host Can’t Be Established

When you log into a remote host that you have never connected before, the remote host key is most likely unknown to your SSH client, and you would be asked to **confirm its fingerprint**:

```
The authenticity of host ***** can't be established.
RSA key fingerprint is *****.
Are you sure you want to continue connecting (yes/no)?
```

If your answer is ‘yes’, the SSH client continues login, and stores the host key locally in the file `~/.ssh/known_hosts`.

If your answer is ‘no’, the connection will be terminated.

If you would like to **bypass this verification step**, you can set the “_StrictHostKeyChecking_” option to “_no_” on the command line:

```shell
$ ssh -o "StrictHostKeyChecking=no" user@host
```

This option **disables the prompt** and automatically adds the host key to the `~/.ssh/known_hosts` file.

## Remote Host Identification Has Changed

However, even with “_StrictHostKeyChecking=no_“, you may be refused to connect with the following warning message:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that the RSA host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
*****
Please contact your system administrator.
Add correct host key in /home/user/.ssh/known_hosts to get rid of this message.
Offending key in /home/user/.ssh/known_hosts:1
RSA host key for ***** has changed and you have requested strict checking.
Host key verification failed.
```

If you are sure that it is harmless and the remote host key has been changed in a legitimate way, you can **skip the host key checking** by sending the key to a null `known_hosts` file:

```shell
$ ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" user@host
```

You can also set these options permanently in `~/.ssh/config` (for the current user) or in `/etc/ssh/ssh_config` (for all users).

Also the option can be set either for the all hosts or for a given set of IP addresses.

### Disable SSH host key checking for all hosts

```
Host *
   StrictHostKeyChecking no
   UserKnownHostsFile=/dev/null
```

### Disable SSH host key checking For 192.168.0.0/24

```
Host 192.168.0.*
	StrictHostKeyChecking no
	UserKnownHostsFile=/dev/null
```
   