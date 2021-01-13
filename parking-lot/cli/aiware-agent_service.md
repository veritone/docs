## aiware-agent service

Contains subcommands to manipulate services.

### Synopsis

Contains subcommands to manipulate services.

### Options

```
  -h, --help   help for service
```

### Options inherited from parent commands

```
  -c, --config string             CLI Config file to use.  By default it will be /home/ubuntu/.config/aiware-cli.yaml
      --controller-token string   Controller URL to use for the CLI.  If specified, this will be used instead of what is in the configuration.
      --controller-url string     Controller URL to use for the CLI.  If specified, this will be used instead of what is in the configuration. (default "http://localhost:9000/edge/v1")
  -d, --debug                     Enables debug output
  -f, --format string             The output format.  The values are text, log or json. (default "text")
  -p, --profile string            The profile to use.  If not specified, the profile named 'default' will be used. (default "default")
  -q, --quiet                     Disables output extra output except the main output
```

### SEE ALSO

* [aiware-agent](/cli/aiware-agent.md)	 - Provides CLI as well as launching edge services as agent
* [aiware-agent service install](/cli/aiware-agent_service_install.md)	 - Install a new service

###### Auto generated by spf13/cobra on 13-Jan-2021