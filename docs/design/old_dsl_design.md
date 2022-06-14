# Old User Agent Selector

> NOTE: this is an old DSL design document,
> which no longer is used as the design for anything in this crate.
>
> This document is kept for historical reference value only.

What is we wish to not only select a specific browser, but allow a range of possible browsers.
What if for example we wish to allow firefox or chrome, of a range of versions
and that on the windows platform. This is possible by using a User Agent Selector string
rather than just a plain old "standard" selector. Example:

```
windows && [[ firefox >= 90 || [[ chrome >= 95 && chrome < 101 ]] ]]
```

This selector specifies the _platform_ has to be "windows",
with the browser being either a firefox of v90.0.0 or higher or it
being a chrome browser of version 95.0.0 or higher, but lower than v101.0.0.

The language for the user agent specifier can be described by following (case insensitive) _BNF_:

```bnf
<letter>               ::= 'a' | 'b' | ... | 'z'
<positive-digit>       ::= '1' | ... | '9'
<digit>                ::= '0' | <positive-digit>
<opt-digit>            ::= '' | <digit><opt-digit>
<number>               ::= <positive-digit> | <opt-digit>
<version-prefix>       ::= '' | 'v'<opt-whitespace>
<opt-whitespace>       ::= '' | ' '<opt-whitespace>
<identifier>           ::= '' | <letter><identifier>

<opt-version-part>     ::= '' | .<number>
<version>              ::= <version-prefix><number><opt-version-part><opt-version-part>

<compare>              ::= '==' | '<' | '<=' | '>' | '>=' | '!=' | '<>' | '==' | '~='
<opt-compare>          ::= '' | <compare>

<qualifier-identifier> ::= <identifier> | <identifier><opt-compare><version>

<unary-operator>       ::= '!'
<unary-expression>     ::= <unary-operator><opt-whitespace><expression>
 
<binary-operator>      ::= '&&' | '||'
<binary-expression>    ::= <expression><opt-whitespace><binary-operator><opt-whitespace><expression>

<group-start>          ::= '[[' 
<group-end>            ::= ']]'
<group>                ::= <group-start><opt-whitespace><expression><opt-whitespace><group-end>

<expression>           ::= <qualitifed-identifier> | <group> | <unary-expression> | <binary-expression>
```

A User-Agent selector is an `<expression>`. And a browser is identified by an identifier (e.g. `chrome`),
and the same goes for a platform (e.g. `windows`).

A standard User-Agent translates to a User-Agent selector of the following format:

```
<identifier> <= <version> && <identifier> <= <version>
```

with the first identifier identifying the _browser_ and the second one identifying the _platform_.
For example the User-Agent given at the start of this chapter translated to:

```
safari <= v15.3 && macos <= v10.15.7
```

The above _BNF_ defines the UA Selector language in its purest form,
in practice however, parsers for the UA Selector can also be as lenient as required, as such an identifier can in fact be
an entire User-Agent. The only requirement is that ambiguity is forbidden.
Meaning the identifier cannot only be in the `A-Z` range, but also contain `(), _-.`,
and in fact anything else as long as it doesn't contain a `<compare>`, `<group-start>`, `unary-operator`,
or `binary-operator` expression.

This also allows for example to select one of two standard User-Agent strings, just to give an example.

Finally, a User-Agent selector parser can, as a fallback, treat the entire input string as one big wacky identifier,
from which to pull information about the desired (single) identifier (pair).
