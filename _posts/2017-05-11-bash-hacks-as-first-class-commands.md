---
title: BASH Hacks as First Class Commands
categories:
- How-to Guides
- Utilities And Other Useful Things
- IT/Software Projects
tags:
- BASH

---

In my new job at [Zendesk](https://www.zendesk.com/), I deal with a lot of Ruby
projects that leverage [bundler](http://bundler.io/) for dependency management.
Specifically, right now I'm working with
[SparkleFormation](http://www.sparkleformation.io/),
[stack_master](https://github.com/envato/stack_master), and
[aws-vault](https://github.com/99designs/aws-vault). With that toolset, each
command starts with a staggering **_43 static characters_** that don't change
between commands, like this:

```shell
aws-vault exec <AWS profile> -- bundle exec stack_master apply \
<region or alias> <stack name> -c <stack_master config file>
```

That's way too long to type out every time I need to act on a stack. Initially I
tried to solve this by setting an alias in my BASH profile to abstract the
stack_master command:

```shell
alias be=”bundle exec stack_master”
```

One problem with this approach - if you use a command or binary that spawns a
new shell (like aws-vault does), your bash_profile is ignored, defeating the
entire purpose.

Fortunately this is an easy problem to solve. Because the default PATH is the
same for all shells, we can recreate our alias as a script and place it in
`/usr/local/bin` (or any other directory in the default PATH). We can also
leverage BASH's $@ variable, which captures all positional arguments that come
after a script is invoked.

```shell
#!/bin/bash

bundle exec stack_master “$@”
```

Once I created `/usr/local/bin/be` and populated it with those lines, executing
`be` behaved exactly as the original alias, but is also available in all shells.

For those of you using the same toolset of aws-vault, stack_master, and
SparkleFormation, I further condensed my command. Note that this only works if
your aws-vault profile, region alias, and stack_master config files share the
same name.

```shell
#!/bin/bash

# Example command for reference
# aws-vault exec dev -- bundle exec stack_master apply dev stack_name -c dev.yml

aws-vault exec $1 -- bundle exec stack_master $2 $1 $3 -c $1.yml
```

I put the above code in `/usr/local/bin/zsm`, so now my stack commands
are clean and short:

```shell
zsm dev apply my_stack
```

As always, I hope you found this useful.
