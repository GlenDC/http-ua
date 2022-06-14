# HTTP User Agent

> The User-Agent request header is a characteristic string that lets servers an
> network peers identify the application, operating system, vendor,
> and/or version of the requesting user agent.

This crate allows you to parse a User-Agent (UA) string as found in a typical HTTP Request header,
and report the found information. Ideally this allows you to extract all of the following:

- the operating system information: name and version;
- the browser information: name and version;

However:

- At times we might only be able to report the browser info, and not the platform info;
- At times we might be able to report version information but know it is incorrect;
    - e.g. Some browsers started freezing the Operating System Version in the name of privacy;

```
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.3 Safari/605.1.15
```

This is an example User-Agent, a standard User-Agent,
Identifying a Safari 15.3 browser on MacOS 10.15.7.
The actual host MacOS version is however 11.6.4, but Apple decided to freeze
the OS version in the User-Agent string for the sake of privacy, it's fine really,
User-Agents are monsters.

Besides being able to parse a "standard" User-Agent using this library,
you are also able to parse a minimal DSL which allows you to select a User-Agent
out of a group of User-Agents.

```
windows firefox 90.. | windows chrome 95..101
```

The above is an example of the DSL. 

This selector specifies the _platform_ has to be "windows",
with the browser being either a firefox of v90.0.0 or higher or it
being a chrome browser of version 95.0.0 or higher, but lower than v101.0.0.

This does give some duplication (e.g. we had to specify windows twice).
It does however allow us to keep the DSL as simple as possible.

See [the "User Agent Selector" Chapter](#user-agent-parsing) for more information.

## User Agent Parsing

... TODO

## User Agent Selector

... TODO

### DSL History

Initially the selector DSL was a lot more complex and looked a lot more like a language
than how we allow it in this version. In one hand it limits our use cases more, but at the other hand
it allows certain features that were previously not possible. In fact it allows us to still
do the same features we required before already, but we can do it using simpler and faster code.
That and it also allows us to specify an "as-close-as-possible" match, which previously wasn't possible.

You can read ["the original DSL design"](./docs/design/old_dsl_design.md)
for some historical references or to satisfy your curiosity, should you want.
