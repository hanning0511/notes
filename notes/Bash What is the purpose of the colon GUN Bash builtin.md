**Historically**: Bourne shells didn't have `true` and `false` as built-in commands. `true` was instead simply aliased to `:`, and `false` to something like `let 0`.

`:` is slightly better than `true` for portability to ancient Bourne-derived shells. As a simple example, consider having neither the `!` pipeline operator nor the `||` list operator (as was the case for some ancient Bourne shells). This leaves the `else` clause of the `if` statement as the only means for branching based on exit status:

```sh
if command; then
	:;
else
	...;
fi
```

Since `if` requires a non-empty `then` clause and comments don't count as non-empty, `:` serves as a no-op.

**Nowadays**: (that is : in a modern context) you can usually use either `:` or `true`. Both are specified by POSIX, and some find `true` easier to read. However there is one interesting difference: `:` is a so-called POSIX *special built-in*, whereas `true` is a regular *built-in*.

- Special built-ins are required to be built into the shell; Regular built-ins are only "typically" built in, but it isn't strictly guaranteed. There usually shouldn't be a regular program named `:` with the function of `true` in PATH of most systems.

- Probably the most crucial difference is that with special built-ins, any variable set by the built-in - even in the environment during simple command evaluation - persists after the command completes, as demonstrated here using ksh93:

```bash
$ unset x; ( x=hi :; echo "$x" )
hi
$ ( x=hi true; echo "$x" )

$
```

 Note that zsh ignores this requirement, as does GNU Bash except when operating in POSIX compatibility mode, but all other major "POSIX sh derived" shells observe this including dash, ksh93, and mksh.

- Another difference is that regular built-ins must be compatible with `exec` - demonstrated here using Bash:

```bash
$ ( exec : )
-bash: exec: :: not found
$ ( exec true )
$
```

- POSIX also explicitly notes that `:` may be faster than `true`, though this is of course an implementation-specific detail.

Reference: [What is the purpose of the :(colon) GUN Bash builtin](https://stackoverflow.com/questions/3224878/what-is-the-purpose-of-the-colon-gnu-bash-builtin)