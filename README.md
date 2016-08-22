cli
===

[![Build Status](https://travis-ci.org/urfave/cli.svg?branch=master)](https://travis-ci.org/urfave/cli)
[![Windows Build Status](https://ci.appveyor.com/api/projects/status/rtgk5xufi932pb2v?svg=true)](https://ci.appveyor.com/project/urfave/cli)
[![GoDoc](https://godoc.org/github.com/urfave/cli?status.svg)](https://godoc.org/github.com/urfave/cli)
[![codebeat](https://codebeat.co/badges/0a8f30aa-f975-404b-b878-5fab3ae1cc5f)](https://codebeat.co/projects/github-com-urfave-cli)
[![Go Report Card](https://goreportcard.com/badge/urfave/cli)](https://goreportcard.com/report/urfave/cli)
[![top level coverage](https://gocover.io/_badge/github.com/urfave/cli?0 "top level coverage")](http://gocover.io/github.com/urfave/cli) /
[![altsrc coverage](https://gocover.io/_badge/github.com/urfave/cli/altsrc?0 "altsrc coverage")](http://gocover.io/github.com/urfave/cli/altsrc)

**Notice:** This is the library formerly known as
`github.com/codegangsta/cli` -- Github will automatically redirect requests
to this repository, but we recommend updating your references for clarity.

cli is a simple, fast, and fun package for building command line apps in Go. The
goal is to enable developers to write fast and distributable command line
applications in an expressive way.

<!-- toc -->

- [Overview](#overview)
- [Installation](#installation)
  * [Supported platforms](#supported-platforms)
  * [Using the `v2` branch](#using-the-v2-branch)
  * [Pinning to the `v1` branch](#pinning-to-the-v1-branch)
- [Getting Started](#getting-started)
- [Examples](#examples)
  * [Arguments](#arguments)
  * [Flags](#flags)
    + [Placeholder Values](#placeholder-values)
    + [Alternate Names](#alternate-names)
    + [Values from the Environment](#values-from-the-environment)
    + [Values from alternate input sources (YAML, TOML, and others)](#values-from-alternate-input-sources-yaml-toml-and-others)
  * [Subcommands](#subcommands)
  * [Subcommands categories](#subcommands-categories)
  * [Exit code](#exit-code)
  * [Bash Completion](#bash-completion)
    + [Enabling](#enabling)
    + [Distribution](#distribution)
    + [Customization](#customization)
  * [Generated Help Text](#generated-help-text)
    + [Customization](#customization-1)
  * [Version Flag](#version-flag)
    + [Customization](#customization-2)
  * [Full API Example](#full-api-example)
- [Contribution Guidelines](#contribution-guidelines)

<!-- tocstop -->

## Overview

Command line apps are usually so tiny that there is absolutely no reason why
your code should *not* be self-documenting. Things like generating help text and
parsing command flags/options should not hinder productivity when writing a
command line app.

**This is where cli comes into play.** cli makes command line programming fun,
organized, and expressive!

## Installation

Make sure you have a working Go environment.  Go version 1.2+ is supported.  [See
the install instructions for Go](http://golang.org/doc/install.html).

To install cli, simply run:
```
$ go get github.com/urfave/cli
```

Make sure your `PATH` includes the `$GOPATH/bin` directory so your commands can
be easily used:
```
export PATH=$PATH:$GOPATH/bin
```

### Supported platforms

cli is tested against multiple versions of Go on Linux, and against the latest
released version of Go on OS X and Windows.  For full details, see
[`./.travis.yml`](./.travis.yml) and [`./appveyor.yml`](./appveyor.yml).

### Using the `v2` branch

**Warning**: The `v2` branch is currently unreleased and considered unstable.

There is currently a long-lived branch named `v2` that is intended to land as
the new `master` branch once development there has settled down.  The current
`master` branch (mirrored as `v1`) is being manually merged into `v2` on
an irregular human-based schedule, but generally if one wants to "upgrade" to
`v2` *now* and accept the volatility (read: "awesomeness") that comes along with
that, please use whatever version pinning of your preference, such as via
`gopkg.in`:

```
$ go get gopkg.in/urfave/cli.v2
```

``` go
...
import (
  "gopkg.in/urfave/cli.v2" // imports as package "cli"
)
...
```

**NOTE**: There is a [migrator (python) script](./cli-v1-to-v2) available to aid
with the transition from the v1 to v2 API.

### Pinning to the `v1` branch

Similarly to the section above describing use of the `v2` branch, if one wants
to avoid any unexpected compatibility pains once `v2` becomes `master`, then
pinning to the `v1` branch is an acceptable option, e.g.:

```
$ go get gopkg.in/urfave/cli.v1
```

``` go
...
import (
  "gopkg.in/urfave/cli.v1" // imports as package "cli"
)
...
```

## Getting Started

One of the philosophies behind cli is that an API should be playful and full of
discovery. So a cli app can be as little as one line of code in `main()`.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "A new cli application"
} -->
``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  (&cli.App{}).Run(os.Args)
}
```

This app will run and show help text, but is not very useful. Let's give an
action to execute and some help documentation:

<!-- {
  "output": "boom! I say!"
} -->
``` go
package main

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Name: "boom",
    Usage: "make an explosive entrance",
    Action: func(c *cli.Context) error {
      fmt.Println("boom! I say!")
      return nil
    },
  }

  app.Run(os.Args)
}
```

Running this already gives you a ton of functionality, plus support for things
like subcommands and flags, which are covered below.

## Examples

Being a programmer can be a lonely job. Thankfully by the power of automation
that is not the case! Let's create a greeter app to fend off our demons of
loneliness!

Start by creating a directory named `greet`, and within it, add a file,
`greet.go` with the following code in it:

<!-- {
  "output": "Hello friend!"
} -->
``` go
package main

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Name: "greet",
    Usage: "fight the loneliness!",
    Action: func(c *cli.Context) error {
      fmt.Println("Hello friend!")
      return nil
    },
  }

  app.Run(os.Args)
}
```

Install our command to the `$GOPATH/bin` directory:

```
$ go install
```

Finally run our new command:

```
$ greet
Hello friend!
```

cli also generates neat help text:

```
$ greet help
NAME:
    greet - fight the loneliness!

USAGE:
    greet [global options] command [command options] [arguments...]

VERSION:
    0.0.0

COMMANDS:
    help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS
    --version Shows version information
```

### Arguments

You can lookup arguments by calling the `Args` function on `cli.Context`, e.g.:

<!-- {
  "output": "Hello \""
} -->
``` go
package main

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Action: func(c *cli.Context) error {
      fmt.Printf("Hello %q", c.Args().Get(0))
      return nil
    },
  }

  app.Run(os.Args)
}
```

### Flags

Setting and querying flags is simple.

<!-- {
  "output": "Hello Nefertiti"
} -->
``` go
package main

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Flags: []cli.Flag {
      &cli.StringFlag{
        Name: "lang",
        Value: "english",
        Usage: "language for the greeting",
      },
    },
    Action: func(c *cli.Context) error {
      name := "Nefertiti"
      if c.NArg() > 0 {
        name = c.Args().Get(0)
      }
      if c.String("lang") == "spanish" {
        fmt.Println("Hola", name)
      } else {
        fmt.Println("Hello", name)
      }
      return nil
    },
  }

  app.Run(os.Args)
}
```

You can also set a destination variable for a flag, to which the content will be
scanned.

<!-- {
  "output": "Hello someone"
} -->
``` go
package main

import (
  "os"
  "fmt"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  var language string

  app := &cli.App{
    Flags: []cli.Flag {
      &cli.StringFlag{
        Name:        "lang",
        Value:       "english",
        Usage:       "language for the greeting",
        Destination: &language,
      },
    },
    Action: func(c *cli.Context) error {
      name := "someone"
      if c.NArg() > 0 {
        name = c.Args().Get(0)
      }
      if language == "spanish" {
        fmt.Println("Hola", name)
      } else {
        fmt.Println("Hello", name)
      }
      return nil
    },
  }

  app.Run(os.Args)
}
```

See full list of flags at http://godoc.org/github.com/urfave/cli

#### Placeholder Values

Sometimes it's useful to specify a flag's value within the usage string itself.
Such placeholders are indicated with back quotes.

For example this:

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "&#45;&#45;config FILE, &#45;c FILE"
} -->
```go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Flags: []cli.Flag{
      &cli.StringFlag{
        Name:    "config",
        Aliases: []string{"c"},
        Usage:   "Load configuration from `FILE`",
      },
    },
  }

  app.Run(os.Args)
}
```

Will result in help output like:

```
--config FILE, -c FILE   Load configuration from FILE
```

Note that only the first placeholder is used. Subsequent back-quoted words will
be left as-is.

#### Alternate Names

You can set alternate (or short) names for flags by providing a comma-delimited
list for the `Name`. e.g.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "&#45;&#45;lang value, &#45;l value.*language for the greeting.*default: \"english\""
} -->
``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Flags: []cli.Flag {
      &cli.StringFlag{
        Name:    "lang",
        Aliases: []string{"l"},
        Value:   "english",
        Usage:   "language for the greeting",
      },
    },
  }

  app.Run(os.Args)
}
```

That flag can then be set with `--lang spanish` or `-l spanish`. Note that
giving two different forms of the same flag in the same command invocation is an
error.

#### Values from the Environment

You can also have the default value set from the environment via `EnvVars`.  e.g.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "language for the greeting.*APP_LANG"
} -->
``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Flags: []cli.Flag {
      &cli.StringFlag{
        Name:    "lang",
        Aliases: []string{"l"},
        Value:   "english",
        Usage:   "language for the greeting",
        EnvVars: []string{"APP_LANG"},
      },
    },
  }

  app.Run(os.Args)
}
```

If `EnvVars` contains more than one string, the first environment variable that
resolves is used as the default.

<!-- {
  "args": ["&#45;&#45;help"],
  "output": "language for the greeting.*LEGACY_COMPAT_LANG.*APP_LANG.*LANG"
} -->
``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Flags: []cli.Flag{
      &cli.StringFlag{
        Name:    "lang",
        Aliases: []string{"l"},
        Value:   "english",
        Usage:   "language for the greeting",
        EnvVars: []string{"LEGACY_COMPAT_LANG", "APP_LANG", "LANG"},
      },
    },
  }

  app.Run(os.Args)
}
```

#### Values from alternate input sources (YAML, TOML, and others)

There is a separate package altsrc that adds support for getting flag values
from other file input sources.

Currently supported input source formats:
* YAML
* TOML

In order to get values for a flag from an alternate input source the following
code would be added to wrap an existing cli.Flag like below:

``` go
  altsrc.NewIntFlag(&cli.IntFlag{Name: "test"})
```

Initialization must also occur for these flags. Below is an example initializing
getting data from a yaml file below.

``` go
  command.Before = altsrc.InitInputSourceWithContext(command.Flags, NewYamlSourceFromFlagFunc("load"))
```

The code above will use the "load" string as a flag name to get the file name of
a yaml file from the cli.Context.  It will then use that file name to initialize
the yaml input source for any flags that are defined on that command.  As a note
the "load" flag used would also have to be defined on the command flags in order
for this code snipped to work.

Currently only the aboved specified formats are supported but developers can
add support for other input sources by implementing the
altsrc.InputSourceContext for their given sources.

Here is a more complete sample of a command using YAML support:

<!-- {
  "args": ["test-cmd", "&#45;&#45;help"],
  "output": "&#45&#45;test value.*default: 0"
} -->
``` go
package notmain

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
  "gopkg.in/urfave/cli.v2/altsrc"
)

func main() {
  flags := []cli.Flag{
    altsrc.NewIntFlag(&cli.IntFlag{Name: "test"}),
    &cli.StringFlag{Name: "load"},
  }

  app := &cli.App{
    Action: func(c *cli.Context) error {
      fmt.Println("yaml ist rad")
      return nil
    },
    Before: altsrc.InitInputSourceWithContext(flags, altsrc.NewYamlSourceFromFlagFunc("load")),
    Flags: flags,
  }

  app.Run(os.Args)
}
```

### Subcommands

Subcommands can be defined for a more git-like command line app.

<!-- {
  "args": ["template", "add"],
  "output": "new task template: .+"
} -->
```go
package main

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Commands: []*cli.Command{
      {
        Name:    "add",
        Aliases: []string{"a"},
        Usage:   "add a task to the list",
        Action:  func(c *cli.Context) error {
          fmt.Println("added task: ", c.Args().First())
          return nil
        },
      },
      {
        Name:    "complete",
        Aliases: []string{"c"},
        Usage:   "complete a task on the list",
        Action:  func(c *cli.Context) error {
          fmt.Println("completed task: ", c.Args().First())
          return nil
        },
      },
      {
        Name:        "template",
        Aliases:     []string{"t"},
        Usage:       "options for task templates",
        Subcommands: []*cli.Command{
          {
            Name:  "add",
            Usage: "add a new template",
            Action: func(c *cli.Context) error {
              fmt.Println("new task template: ", c.Args().First())
              return nil
            },
          },
          {
            Name:  "remove",
            Usage: "remove an existing template",
            Action: func(c *cli.Context) error {
              fmt.Println("removed task template: ", c.Args().First())
              return nil
            },
          },
        },
      },
    },
  }

  app.Run(os.Args)
}
```

### Subcommands categories

For additional organization in apps that have many subcommands, you can
associate a category for each command to group them together in the help
output.

E.g.

```go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Commands: []*cli.Command{
      {
        Name: "noop",
      },
      {
        Name:     "add",
        Category: "template",
      },
      {
        Name:     "remove",
        Category: "template",
      },
    },
  }

  app.Run(os.Args)
}
```

Will include:

```
COMMANDS:
    noop

  Template actions:
    add
    remove
```

### Exit code

Calling `App.Run` will not automatically call `os.Exit`, which means that by
default the exit code will "fall through" to being `0`.  An explicit exit code
may be set by returning a non-nil error that fulfills `cli.ExitCoder`, *or* a
`cli.MultiError` that includes an error that fulfills `cli.ExitCoder`, e.g.:

``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  app := &cli.App{
    Flags: []cli.Flag{
      &cli.BoolFlag{
        Name:  "ginger-crouton",
        Value: true,
        Usage: "is it in the soup?",
      },
    },
    Action: func(ctx *cli.Context) error {
      if !ctx.Bool("ginger-crouton") {
        return cli.Exit("it is not in the soup", 86)
      }
      return nil
    },
  }

  app.Run(os.Args)
}
```

### Bash Completion

You can enable completion commands by setting the `EnableBashCompletion`
flag on the `App` object.  By default, this setting will only auto-complete to
show an app's subcommands, but you can write your own completion methods for
the App or its subcommands.

<!-- {
  "args": ["complete", "&#45;&#45;generate&#45;bash&#45;completion"],
  "output": "laundry"
} -->
``` go
package main

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  tasks := []string{"cook", "clean", "laundry", "eat", "sleep", "code"}

  app := &cli.App{
    EnableBashCompletion: true,
    Commands: []*cli.Command{
      {
        Name:    "complete",
        Aliases: []string{"c"},
        Usage:   "complete a task on the list",
        Action: func(c *cli.Context) error {
           fmt.Println("completed task: ", c.Args().First())
           return nil
        },
        BashComplete: func(c *cli.Context) {
          // This will complete if no args are passed
          if c.NArg() > 0 {
            return
          }
          for _, t := range tasks {
            fmt.Println(t)
          }
        },
      },
    },
  }

  app.Run(os.Args)
}
```

#### Enabling

Source the `autocomplete/bash_autocomplete` file in your `.bashrc` file while
setting the `PROG` variable to the name of your program:

`PROG=myprogram source /.../cli/autocomplete/bash_autocomplete`

#### Distribution

Copy `autocomplete/bash_autocomplete` into `/etc/bash_completion.d/` and rename
it to the name of the program you wish to add autocomplete support for (or
automatically install it there if you are distributing a package). Don't forget
to source the file to make it active in the current shell.

```
sudo cp src/bash_autocomplete /etc/bash_completion.d/<myprogram>
source /etc/bash_completion.d/<myprogram>
```

Alternatively, you can just document that users should source the generic
`autocomplete/bash_autocomplete` in their bash configuration with `$PROG` set
to the name of their program (as above).

#### Customization

The default bash completion flag (`--generate-bash-completion`) is defined as
`cli.BashCompletionFlag`, and may be redefined if desired, e.g.:

<!-- {
  "args": ["&#45;&#45;compgen"],
  "output": "wat\nhelp\nh"
} -->
``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  cli.BashCompletionFlag = &cli.BoolFlag{
    Name:   "compgen",
    Hidden: true,
  }

  app := &cli.App{
    EnableBashCompletion: true,
    Commands: []*cli.Command{
      {
        Name: "wat",
      },
    },
  }
  app.Run(os.Args)
}
```

### Generated Help Text

The default help flag (`-h/--help`) is defined as `cli.HelpFlag` and is checked
by the cli internals in order to print generated help text for the app, command,
or subcommand, and break execution.

#### Customization

All of the help text generation may be customized, and at multiple levels.  The
templates are exposed as variables `AppHelpTemplate`, `CommandHelpTemplate`, and
`SubcommandHelpTemplate` which may be reassigned or augmented, and full override
is possible by assigning a compatible func to the `cli.HelpPrinter` variable,
e.g.:

<!-- {
  "output": "Ha HA.  I pwnd the help!!1"
} -->
``` go
package main

import (
  "fmt"
  "io"
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  // EXAMPLE: Append to an existing template
  cli.AppHelpTemplate = fmt.Sprintf(`%s

WEBSITE: http://awesometown.example.com

SUPPORT: support@awesometown.example.com

`, cli.AppHelpTemplate)

  // EXAMPLE: Override a template
  cli.AppHelpTemplate = `NAME:
   {{.Name}} - {{.Usage}}
USAGE:
   {{.HelpName}} {{if .VisibleFlags}}[global options]{{end}}{{if .Commands}} command
[command options]{{end}} {{if
.ArgsUsage}}{{.ArgsUsage}}{{else}}[arguments...]{{end}}
   {{if len .Authors}}
AUTHOR(S):
   {{range .Authors}}{{ . }}{{end}}
   {{end}}{{if .Commands}}
COMMANDS:
{{range .Commands}}{{if not .HideHelp}}   {{join .Names ", "}}{{ "\t"
}}{{.Usage}}{{ "\n" }}{{end}}{{end}}{{end}}{{if .VisibleFlags}}
GLOBAL OPTIONS:
   {{range .VisibleFlags}}{{.}}
   {{end}}{{end}}{{if .Copyright }}
COPYRIGHT:
   {{.Copyright}}
   {{end}}{{if .Version}}
VERSION:
   {{.Version}}
   {{end}}
`

  // EXAMPLE: Replace the `HelpPrinter` func
  cli.HelpPrinter = func(w io.Writer, templ string, data interface{}) {
    fmt.Println("Ha HA.  I pwnd the help!!1")
  }

  (&cli.App{}).Run(os.Args)
}
```

The default flag may be customized to something other than `-h/--help` by
setting `cli.HelpFlag`, e.g.:

<!-- {
  "args": ["&#45;&#45halp"],
  "output": "haaaaalp.*HALP"
} -->
``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  cli.HelpFlag = &cli.BoolFlag{
    Name: "haaaaalp", Aliases: []string{"halp"},
    Usage: "HALP",
    EnvVars: []string{"SHOW_HALP", "HALPPLZ"},
  }

  (&cli.App{}).Run(os.Args)
}
```

### Version Flag

The default version flag (`-v/--version`) is defined as `cli.VersionFlag`, which
is checked by the cli internals in order to print the `App.Version` via
`cli.VersionPrinter` and break execution.

#### Customization

The default flag may be cusomized to something other than `-v/--version` by
setting `cli.VersionFlag`, e.g.:

<!-- {
  "args": ["&#45;&#45print-version"],
  "output": "partay version v19\\.99\\.0"
} -->
``` go
package main

import (
  "os"

  "gopkg.in/urfave/cli.v2"
)

func main() {
  cli.VersionFlag = &cli.BoolFlag{
    Name: "print-version", Aliases: []string{"V"},
    Usage: "print only the version",
  }

  app := &cli.App{
    Name: "partay",
    Version: "v19.99.0",
  }
  app.Run(os.Args)
}
```

Alternatively, the version printer at `cli.VersionPrinter` may be overridden, e.g.:

<!-- {
  "args": ["&#45;&#45version"],
  "output": "version=v19\\.99\\.0 revision=fafafaf"
} -->
``` go
package main

import (
  "fmt"
  "os"

  "gopkg.in/urfave/cli.v2"
)

var (
  Revision = "fafafaf"
)

func main() {
  cli.VersionPrinter = func(c *cli.Context) {
    fmt.Printf("version=%s revision=%s\n", c.App.Version, Revision)
  }

  app := &cli.App{
    Name: "partay",
    Version: "v19.99.0",
  }
  app.Run(os.Args)
}
```

### Full API Example

**Notice**: This is a contrived (functioning) example meant strictly for API
demonstration purposes.  Use of one's imagination is encouraged.

<!-- {
  "output": "made it!\nPhew!"
} -->
``` go
package main

import (
  "errors"
  "flag"
  "fmt"
  "io"
  "io/ioutil"
  "os"
  "time"

  "gopkg.in/urfave/cli.v2"
)

func init() {
  cli.AppHelpTemplate += "\nCUSTOMIZED: you bet ur muffins\n"
  cli.CommandHelpTemplate += "\nYMMV\n"
  cli.SubcommandHelpTemplate += "\nor something\n"

  cli.HelpFlag = &cli.BoolFlag{Name: "halp"}
  cli.BashCompletionFlag = &cli.BoolFlag{Name: "compgen", Hidden: true}
  cli.VersionFlag = &cli.BoolFlag{Name: "print-version", Aliases: []string{"V"}}

  cli.HelpPrinter = func(w io.Writer, templ string, data interface{}) {
    fmt.Fprintf(w, "best of luck to you\n")
  }
  cli.VersionPrinter = func(c *cli.Context) {
    fmt.Fprintf(c.App.Writer, "version=%s\n", c.App.Version)
  }
  cli.OsExiter = func(c int) {
    fmt.Fprintf(cli.ErrWriter, "refusing to exit %d\n", c)
  }
  cli.ErrWriter = ioutil.Discard
  cli.FlagStringer = func(fl cli.Flag) string {
    return fmt.Sprintf("\t\t%s", fl.Names()[0])
  }
}

type hexWriter struct{}

func (w *hexWriter) Write(p []byte) (int, error) {
  for _, b := range p {
    fmt.Printf("%x", b)
  }
  fmt.Printf("\n")

  return len(p), nil
}

type genericType struct {
  s string
}

func (g *genericType) Set(value string) error {
  g.s = value
  return nil
}

func (g *genericType) String() string {
  return g.s
}

func main() {
  app := cli.App{
    Name: "kənˈtrīv",
    Version: "v19.99.0",
    Compiled: time.Now(),
    Authors: []*cli.Author{
      &cli.Author{
        Name:  "Example Human",
        Email: "human@example.com",
      },
    },
    Copyright: "(c) 1999 Serious Enterprise",
    HelpName: "contrive",
    Usage: "demonstrate available API",
    UsageText: "contrive - demonstrating the available API",
    ArgsUsage: "[args and such]",
    Commands: []*cli.Command{
      &cli.Command{
        Name:        "doo",
        Aliases:     []string{"do"},
        Category:    "motion",
        Usage:       "do the doo",
        UsageText:   "doo - does the dooing",
        Description: "no really, there is a lot of dooing to be done",
        ArgsUsage:   "[arrgh]",
        Flags: []cli.Flag{
          &cli.BoolFlag{Name: "forever", Aliases: []string{"forevvarr"}},
        },
        Subcommands: []*cli.Command{
          &cli.Command{
            Name:   "wop",
            Action: wopAction,
          },
        },
        SkipFlagParsing: false,
        HideHelp:        false,
        Hidden:          false,
        HelpName:        "doo!",
        BashComplete: func(c *cli.Context) {
          fmt.Fprintf(c.App.Writer, "--better\n")
        },
        Before: func(c *cli.Context) error {
          fmt.Fprintf(c.App.Writer, "brace for impact\n")
          return nil
        },
        After: func(c *cli.Context) error {
          fmt.Fprintf(c.App.Writer, "did we lose anyone?\n")
          return nil
        },
        Action: func(c *cli.Context) error {
          c.Command.FullName()
          c.Command.HasName("wop")
          c.Command.Names()
          c.Command.VisibleFlags()
          fmt.Fprintf(c.App.Writer, "dodododododoodododddooooododododooo\n")
          if c.Bool("forever") {
            c.Command.Run(c)
          }
          return nil
        },
        OnUsageError: func(c *cli.Context, err error, isSubcommand bool) error {
          fmt.Fprintf(c.App.Writer, "for shame\n")
          return err
        },
      },
    },
    Flags: []cli.Flag{
      &cli.BoolFlag{Name: "fancy"},
      &cli.BoolFlag{Value: true, Name: "fancier"},
      &cli.DurationFlag{Name: "howlong", Aliases: []string{"H"}, Value: time.Second * 3},
      &cli.Float64Flag{Name: "howmuch"},
      &cli.GenericFlag{Name: "wat", Value: &genericType{}},
      &cli.Int64Flag{Name: "longdistance"},
      &cli.Int64SliceFlag{Name: "intervals"},
      &cli.IntFlag{Name: "distance"},
      &cli.IntSliceFlag{Name: "times"},
      &cli.StringFlag{Name: "dance-move", Aliases: []string{"d"}},
      &cli.StringSliceFlag{Name: "names", Aliases: []string{"N"}},
      &cli.UintFlag{Name: "age"},
      &cli.Uint64Flag{Name: "bigage"},
    },
    EnableBashCompletion: true,
    HideHelp: false,
    HideVersion: false,
    BashComplete: func(c *cli.Context) {
      fmt.Fprintf(c.App.Writer, "lipstick\nkiss\nme\nlipstick\nringo\n")
    },
    Before: func(c *cli.Context) error {
      fmt.Fprintf(c.App.Writer, "HEEEERE GOES\n")
      return nil
    },
    After: func(c *cli.Context) error {
      fmt.Fprintf(c.App.Writer, "Phew!\n")
      return nil
    },
    CommandNotFound: func(c *cli.Context, command string) {
      fmt.Fprintf(c.App.Writer, "Thar be no %q here.\n", command)
    },
    OnUsageError: func(c *cli.Context, err error, isSubcommand bool) error {
      if isSubcommand {
        return err
      }

      fmt.Fprintf(c.App.Writer, "WRONG: %#v\n", err)
      return nil
    },
    Action: func(c *cli.Context) error {
      cli.DefaultAppComplete(c)
      cli.HandleExitCoder(errors.New("not an exit coder, though"))
      cli.ShowAppHelp(c)
      cli.ShowCommandCompletions(c, "nope")
      cli.ShowCommandHelp(c, "also-nope")
      cli.ShowCompletions(c)
      cli.ShowSubcommandHelp(c)
      cli.ShowVersion(c)

      categories := c.App.Categories
      categories.AddCommand("sounds", &cli.Command{
        Name: "bloop",
      })

      for _, category := range c.App.Categories.Categories() {
        fmt.Fprintf(c.App.Writer, "%s\n", category.Name)
        fmt.Fprintf(c.App.Writer, "%#v\n", category.VisibleCommands())
        fmt.Fprintf(c.App.Writer, "%#v\n", category.VisibleCommands())
      }

      fmt.Printf("%#v\n", c.App.Command("doo"))
      if c.Bool("infinite") {
        c.App.Run([]string{"app", "doo", "wop"})
      }

      if c.Bool("forevar") {
        c.App.RunAsSubcommand(c)
      }
      c.App.Setup()
      fmt.Printf("%#v\n", c.App.VisibleCategories())
      fmt.Printf("%#v\n", c.App.VisibleCommands())
      fmt.Printf("%#v\n", c.App.VisibleFlags())

      fmt.Printf("%#v\n", c.Args().First())
      if c.Args().Len() > 0 {
        fmt.Printf("%#v\n", c.Args().Get(1))
      }
      fmt.Printf("%#v\n", c.Args().Present())
      fmt.Printf("%#v\n", c.Args().Tail())

      set := flag.NewFlagSet("contrive", 0)
      nc := cli.NewContext(c.App, set, c)

      fmt.Printf("%#v\n", nc.Args())
      fmt.Printf("%#v\n", nc.Bool("nope"))
      fmt.Printf("%#v\n", !nc.Bool("nerp"))
      fmt.Printf("%#v\n", nc.Duration("howlong"))
      fmt.Printf("%#v\n", nc.Float64("hay"))
      fmt.Printf("%#v\n", nc.Generic("bloop"))
      fmt.Printf("%#v\n", nc.Int64("bonk"))
      fmt.Printf("%#v\n", nc.Int64Slice("burnks"))
      fmt.Printf("%#v\n", nc.Int("bips"))
      fmt.Printf("%#v\n", nc.IntSlice("blups"))
      fmt.Printf("%#v\n", nc.String("snurt"))
      fmt.Printf("%#v\n", nc.StringSlice("snurkles"))
      fmt.Printf("%#v\n", nc.Uint("flub"))
      fmt.Printf("%#v\n", nc.Uint64("florb"))

      fmt.Printf("%#v\n", nc.FlagNames())
      fmt.Printf("%#v\n", nc.IsSet("wat"))
      fmt.Printf("%#v\n", nc.Set("wat", "nope"))
      fmt.Printf("%#v\n", nc.NArg())
      fmt.Printf("%#v\n", nc.NumFlags())
      fmt.Printf("%#v\n", nc.Lineage()[1])

      nc.Set("wat", "also-nope")

      ec := cli.Exit("ohwell", 86)
      fmt.Fprintf(c.App.Writer, "%d", ec.ExitCode())
      fmt.Printf("made it!\n")
      return ec
    },
    Metadata: map[string]interface{}{
      "layers":          "many",
      "explicable":      false,
      "whatever-values": 19.99,
    },
  }

  if os.Getenv("HEXY") != "" {
    app.Writer = &hexWriter{}
    app.ErrWriter = &hexWriter{}
  }

  app.Run(os.Args)
}

func wopAction(c *cli.Context) error {
  fmt.Fprintf(c.App.Writer, ":wave: over here, eh\n")
  return nil
}
```

## Contribution Guidelines

Feel free to put up a pull request to fix a bug or maybe add a feature. I will
give it a code review and make sure that it does not break backwards
compatibility. If I or any other collaborators agree that it is in line with
the vision of the project, we will work with you to get the code into
a mergeable state and merge it into the master branch.

If you have contributed something significant to the project, we will most
likely add you as a collaborator. As a collaborator you are given the ability
to merge others pull requests. It is very important that new code does not
break existing code, so be careful about what code you do choose to merge.

If you feel like you have contributed to the project but have not yet been
added as a collaborator, we probably forgot to add you, please open an issue.
