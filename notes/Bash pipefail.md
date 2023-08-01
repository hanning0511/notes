# Bash built-in variable *pipefail*

If set, the return value of a pipeline is the value of the last (rightmost) command to exit with a non-zero status, or zero if all commands in the pipeline exit successfully. This option is disabled by default. 

To enable it: `set -o pipefail`
To disable it: `set +o pipefail`

Here's an example:

```bash
$ false | tee /dev/null ; echo $?
0
$ set -o pipefail
$ false | tee /dev/null ; echo $?
1
```