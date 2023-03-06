# subcommands

> **This is a modified fork of [google/subcommands](https://github.com/google/subcommands) for my personal projects**

Subcommands is a Go package that implements a simple way for a single command to
have many subcommands, each of which takes arguments and so forth.


## Usage ##

Set up a 'print' subcommand:

```go
import (
  "context"
  "fmt"
  "os"
  "strings"

  "github.com/lucasepe/subcommands"
)

type printCmd struct {
  capitalize bool
}

func (*printCmd) Name() string     { return "print" }
func (*printCmd) Synopsis() string { return "Print args to stdout." }
func (*printCmd) Usage() string {
  return `print [-capitalize] <some text>:
  Print args to stdout.
`
}

func (p *printCmd) SetFlags(f *pflag.FlagSet) {
  f.BoolVar(&p.capitalize, "capitalize", false, "capitalize output")
}

func (p *printCmd) Execute(_ context.Context, f *pflag.FlagSet, _ ...interface{}) subcommands.ExitStatus {
  for _, arg := range f.Args() {
    if p.capitalize {
      arg = strings.ToUpper(arg)
    }
    fmt.Printf("%s ", arg)
  }
  fmt.Println()
  return subcommands.ExitSuccess
}
```

Register using the default Commander, also use some built in subcommands,
finally run Execute using ExitStatus as the exit code:

```go
func main() {
  subcommands.Register(subcommands.HelpCommand(), "")
  subcommands.Register(subcommands.FlagsCommand(), "")
  subcommands.Register(subcommands.CommandsCommand(), "")
  subcommands.Register(&printCmd{}, "")

  flag.Parse()
  ctx := context.Background()
  os.Exit(int(subcommands.Execute(ctx)))
}
```
