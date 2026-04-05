# Frequently Asked Questions

```{contents}
:depth: 2
:local: true
```

## General

### Shell Variable Expansion On Windows

I have a simple Click app :

```
import click

@click.command()
@click.argument('message')
def main(message: str):
    click.echo(message)

if __name__ == '__main__':
    main()

```

When you pass an environment variable in the argument, it expands it:

```{code-block} powershell
> Desktop python foo.py '$M0/.viola/2025-01-25-17-20-23-307878'
> M:/home/ramrachum/.viola/2025-01-25-17-20-23-307878
>
```
Note that I used single quotes above, so my shell is not expanding the environment variable, Click does. How do I get Click to not expand it?

#### Answer

If you don't want Click to emulate (as best it can) unix expansion on Windows, pass windows_expand_args=False when calling the CLI.
Windows command line doesn't do any *, ~, or $ENV expansion. It also doesn't distinguish between double quotes and single quotes (where the later means "don't expand here"). Click emulates the expansion so that the app behaves similarly on both platforms, but doesn't receive information about what quotes were used.

### Why Do `Path` Completions Show Shortened Entries, But My Custom Completer Doesn't?

When Click completes a `Path` parameter, it tells the shell that the completion item represents a
file or directory. The shell then decides how to display those completions, which often means
showing a shortened suffix instead of the full path prefix.

For custom completions, Click can only send the completion values you return. If those values are
not marked as `file` or `dir`, the shell will usually display them exactly as provided.

#### Answer

If your custom completion is really a path, return `CompletionItem` objects with `type="file"` or
`type="dir"` so the shell can handle them like built-in path completions. If the values are not
actual filesystem paths, Click can't ask the shell to apply the same path-specific display logic,
so the full completion value is expected. See {doc}`shell-completion` for more about completion item
metadata.
