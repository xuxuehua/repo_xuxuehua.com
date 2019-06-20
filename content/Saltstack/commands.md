---
title: "commands"
date: 2019-06-19 13:59
---
[TOC]



# salt



```
Usage: salt [options] '<target>' <function> [arguments]
```

## 

## Options

```
Options:
  --version             show program's version number and exit
  --versions-report     show program's dependencies version number and exit
  -h, --help            show this help message and exit
  --saltfile=SALTFILE   Specify the path to a Saltfile. If not passed, one
                        will be searched for in the current working directory
  -c CONFIG_DIR, --config-dir=CONFIG_DIR
                        Pass in an alternative configuration directory.
                        Default: /etc/salt
  -t TIMEOUT, --timeout=TIMEOUT
                        Change the timeout, if applicable, for the running
                        command; default=5
  --hard-crash          Raise any original exception rather than exiting
                        gracefully Default: False
  -s, --static          Return the data from minions as a group after they all
                        return.
  -p, --progress        Display a progress graph. [Requires `progressbar`
                        python package.]
  --failhard            Stop batch execution upon first "bad" return
  --async               Run the salt command but don't wait for a reply
  --subset=SUBSET       Execute the routine on a random subset of the targeted
                        minions. The minions will be verified that they have
                        the named function before executing
  -v, --verbose         Turn on command verbosity, display jid and active job
                        queries
  --hide-timeout        Hide minions that timeout
  --show-jid            Display jid without the additional output of --verbose
  -b BATCH, --batch=BATCH, --batch-size=BATCH
                        Execute the salt job in batch mode, pass either the
                        number of minions to batch at a time, or the
                        percentage of minions to have running
  -a EAUTH, --auth=EAUTH, --eauth=EAUTH, --external-auth=EAUTH
                        Specify an external authentication system to use.
  -T, --make-token      Generate and save an authentication token for re-use.
                        The token is generated and made available for the
                        period defined in the Salt Master.
  --return=RETURNER     Set an alternative return method. By default salt will
                        send the return data from the command back to the
                        master, but the return data can be redirected into any
                        number of systems, databases or applications.
  --return_config=RETURNER_CONF
                        Set an alternative return method. By default salt will
                        send the return data from the command back to the
                        master, but the return data can be redirected into any
                        number of systems, databases or applications.
  -d, --doc, --documentation
                        Return the documentation for the specified module or
                        for all modules if none are specified.
  --args-separator=ARGS_SEPARATOR
                        Set the special argument used as a delimiter between
                        command arguments of compound commands. This is useful
                        when one wants to pass commas as arguments to some of
                        the commands in a compound command.
  --summary             Display summary information about a salt command
  --username=USERNAME   Username for external authentication
  --password=PASSWORD   Password for external authentication
  --metadata=METADATA   Pass metadata into Salt, used to search jobs.

```

## 

Logging Options 日志相关

```
  Logging Options:
    Logging options which override any settings defined on the
    configuration files.

    -l LOG_LEVEL, --log-level=LOG_LEVEL
                        Console logging log level. One of 'all', 'garbage',
                        'trace', 'debug', 'info', 'warning', 'error',
                        'critical', 'quiet'. Default: 'warning'.
    --log-file=LOG_FILE
                        Log file path. Default: /var/log/salt/master.
    --log-file-level=LOG_LEVEL_LOGFILE
                        Logfile logging log level. One of 'all', 'garbage',
                        'trace', 'debug', 'info', 'warning', 'error',
                        'critical', 'quiet'. Default: 'warning'.


```



## Target 

```

  Target Options:
    Target Selection Options

    -E, --pcre          Instead of using shell globs to evaluate the target
                        servers, use pcre regular expressions
    -L, --list          Instead of using shell globs to evaluate the target
                        servers, take a comma or space delimited list of
                        servers.
    -G, --grain         Instead of using shell globs to evaluate the target
                        use a grain value to identify targets, the syntax for
                        the target is the grain key followed by a
                        globexpression: "os:Arch*"
    --grain-pcre        Instead of using shell globs to evaluate the target
                        use a grain value to identify targets, the syntax for
                        the target is the grain key followed by a pcre regular
                        expression: "os:Arch.*"
    -N, --nodegroup     Instead of using shell globs to evaluate the target
                        use one of the predefined nodegroups to identify a
                        list of targets.
    -R, --range         Instead of using shell globs to evaluate the target
                        use a range expression to identify targets. Range
                        expressions look like %cluster
    -C, --compound      The compound target option allows for multiple target
                        types to be evaluated, allowing for greater
                        granularity in target matching. The compound target is
                        space delimited, targets other than globs are preceded
                        with an identifier matching the specific targets
                        argument type: salt 'G@os:RedHat and webser* or
                        E@database.*'
    -I, --pillar        Instead of using shell globs to evaluate the target
                        use a pillar value to identify targets, the syntax for
                        the target is the pillar key followed by a glob
                        expression: "role:production*"
    -J, --pillar-pcre   Instead of using shell globs to evaluate the target
                        use a pillar value to identify targets, the syntax for
                        the target is the pillar key followed by a pcre
                        regular expression: "role:prod.*"
    -S, --ipcidr        Match based on Subnet (CIDR notation) or IPv4 address.

  Additional Target Options:
    Additional Options for Minion Targeting

    --delimiter=DELIMITER
                        Change the default delimiter for matching in multi-
                        level data structures. default=':'

  Output Options:
    Configure your preferred output format

    --out=OUTPUT, --output=OUTPUT
                        Print the output from the 'salt' command using the
                        specified outputter. The builtins are 'key', 'yaml',
                        'overstatestage', 'newline_values_only', 'pprint',
                        'txt', 'raw', 'virt_query', 'compact', 'json',
                        'highstate', 'nested', 'quiet', 'no_return'.
    --out-indent=OUTPUT_INDENT, --output-indent=OUTPUT_INDENT
                        Print the output indented by the provided value in
                        spaces. Negative values disables indentation. Only
                        applicable in outputters that support indentation.
    --out-file=OUTPUT_FILE, --output-file=OUTPUT_FILE
                        Write the output to the specified file
    --out-file-append, --output-file-append
                        Append the output to the specified file
    --no-color, --no-colour
                        Disable all colored output
    --force-color, --force-colour
                        Force colored output
    --state-output=STATE_OUTPUT, --state_output=STATE_OUTPUT
                        Override the configured state_output value for minion
                        output. One of full, terse, mixed, changes or filter.
                        Default: full.
```











## cp.get_file 复制文件到客户端

```
 salt 'nb1' cp.get_file salt://apache.sls /tmp/cp.txt
```





## cp.get_dir 复制目录到客户端

```
salt 'nb1' cp.get_dir salt://test /tmp
```



## salt-run 显示存活的客户端

```
salt-run manage.up
```







