---
---

# CLI Patterns - Click Best Practices

## Version Note

These patterns are largely version-agnostic, but **type syntax MUST match your target runtime for
the code you are editing** (not necessarily your dev tooling version).

- If the code must run on Python 3.6-3.9, follow `versions/python-3.6.md`.
- If the code is Python 3.10+, follow `versions/python-3.10.md` (or newer).

Most examples here use `click` (third-party). If your project does not depend on `click`, see the
stdlib-only `argparse` runnable demo at the end of this document.

## Core Rules

1. **Use `click.echo()` for output, NEVER `print()`**
2. **Exit with `raise SystemExit(1)` for CLI errors**
3. **Error boundaries at command level**
4. **Use `err=True` for error output**
5. **Flush stderr before `click.confirm()`** (prevents buffering hangs)

## Basic Click Patterns

```python
import click
from pathlib import Path

# ✅ CORRECT: Use click.echo for output
@click.command()
@click.argument("name")
def greet(name: str) -> None:
    """Greet the user."""
    click.echo(f"Hello, {name}!")

# ❌ WRONG: Using print()
@click.command()
def greet(name: str) -> None:
    print(f"Hello, {name}!")  # NEVER use print in CLI
```

## Error Handling in CLI

```python
# ✅ CORRECT: CLI command error boundary
@click.command("create")
@click.argument("name")
def create(name: str) -> None:
    """Create a resource."""
    try:
        create_resource(name)
    except subprocess.CalledProcessError as e:
        click.echo(f"Error: Command failed: {e.stderr}", err=True)
        raise SystemExit(1)
    except ValueError as e:
        click.echo(f"Error: {e}", err=True)
        raise SystemExit(1)
```

## Output Patterns

```python
# Regular output to stdout
click.echo("Processing complete")

# Error output to stderr
click.echo("Error: Operation failed", err=True)

# Colored output
click.echo(click.style("Success!", fg="green"))
click.echo(click.style("Warning!", fg="yellow", bold=True))

# Progress indication
with click.progressbar(items) as bar:
    for item in bar:
        process(item)
```

## Command Structure

```python
@click.group()
@click.pass_context
def cli(ctx: click.Context) -> None:
    """Main CLI entry point."""
    ctx.ensure_object(dict)
    ctx.obj["config"] = load_config()

@cli.command()
@click.option("--dry-run", is_flag=True, help="Perform dry run")
@click.argument("path", type=click.Path(exists=True))
@click.pass_obj
def sync(obj: dict, path: str, dry_run: bool) -> None:
    """Sync the repository."""
    config = obj["config"]

    if dry_run:
        click.echo("DRY RUN: Would sync...")
    else:
        perform_sync(Path(path), config)
        click.echo("✓ Sync complete")
```

## User Interaction

```python
import sys

# ✅ CORRECT: Flush stderr before confirmation prompts
# This prevents buffering hangs when mixing stderr output with stdin prompts
click.echo("Warning: This operation is destructive!", err=True)
sys.stderr.flush()  # Flush before prompting
if click.confirm("Are you sure?"):
    perform_dangerous_operation()

# ❌ WRONG: click.confirm() after stderr output without flush
# This can hang because stderr isn't flushed before the prompt
click.echo("Warning: This operation is destructive!", err=True)
if click.confirm("Are you sure?"):  # BAD: potential buffering hang
    perform_dangerous_operation()

# User input
name = click.prompt("Enter your name", default="User")

# Password input
password = click.prompt("Password", hide_input=True)

# Choice selection
choice = click.prompt(
    "Select option",
    type=click.Choice(["option1", "option2"]),
    default="option1"
)
```

## Path Handling

```python
@click.command()
@click.argument(
    "input_file",
    type=click.Path(exists=True, file_okay=True, dir_okay=False)
)
@click.argument(
    "output_dir",
    type=click.Path(exists=False, file_okay=False, dir_okay=True)
)
def process(input_file: str, output_dir: str) -> None:
    """Process input file to output directory."""
    input_path = Path(input_file)
    output_path = Path(output_dir)

    if not output_path.exists():
        output_path.mkdir(parents=True)

    click.echo(f"Processing {input_path} → {output_path}")
```

## Key Takeaways

1. **Always click.echo()**: Never use print() in CLI code
2. **Error to stderr**: Use `err=True` for error messages
3. **Exit cleanly**: Use `raise SystemExit(1)` for errors
4. **User-friendly**: Provide clear messages and confirmations
5. **Type paths**: Use `click.Path()` for path arguments

---

## Copy-Paste Runnable Demo (stdlib `argparse`, Python 3.6+)

```python
import argparse
import sys
from pathlib import Path
from typing import List, Optional

def main(argv: Optional[List[str]] = None) -> int:
    parser = argparse.ArgumentParser(prog="myapp")
    parser.add_argument("path", help="Path to process")
    parser.add_argument("--dry-run", action="store_true")
    args = parser.parse_args(argv)

    path = Path(args.path)
    if not path.exists():
        sys.stderr.write("Error: path does not exist: %s\n" % (path,))
        return 1

    if args.dry_run:
        sys.stdout.write("DRY RUN: would process %s\n" % (path,))
        return 0

    sys.stdout.write("Processed %s\n" % (path,))
    return 0

if __name__ == "__main__":
    raise SystemExit(main())
```
