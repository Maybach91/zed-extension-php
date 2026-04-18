# PHP Extension for Zed

A native [Zed](https://zed.dev) extension that brings full PHP language support to the editor — including intelligent code completion, diagnostics, refactoring, syntax highlighting, PHPDoc support, and interactive debugging via Xdebug.

- **Repository:** [github.com/zed-extensions/php](https://github.com/zed-extensions/php)
- **Official docs:** [zed.dev/docs/languages/php](https://zed.dev/docs/languages/php)
- **License:** Apache 2.0

---

## Capabilities

### Language Intelligence (via LSP)

The extension supports three PHP language servers. Only one is active per workspace at a time:

| Server | Type | Highlights |
|---|---|---|
| **Phpactor** | Open-source (default) | Heavy-lifting refactoring, introspection, go-to-definition |
| **Intelephense** | Freemium (npm) | Comprehensive IntelliSense, code actions, fast indexing |
| **PHP Tools** | Freemium (npm) | Full-stack PHP IDE features, Laravel/framework awareness |

All three servers provide: code completion, go-to-definition, hover documentation, diagnostics, symbol search, and workspace-wide indexing.

### Syntax Highlighting & Parsing

- **Tree-sitter grammar** ([tree-sitter/tree-sitter-php](https://github.com/tree-sitter/tree-sitter-php)) for fast, local, offline parsing
- **PHPDoc syntax highlighting** ([claytonrcarter/tree-sitter-phpdoc](https://github.com/claytonrcarter/tree-sitter-phpdoc)) inside doc comment blocks
- Language injection: HTML, CSS, JavaScript, and SQL embedded in PHP files are highlighted in their respective grammars
- Smart bracket matching & auto-close for `()`, `[]`, `{}`, `''`, and `""`
- Correct indentation rules for PHP's block structure

### Editor Integration

- **Code folding** for functions, classes, and blocks
- **Outline panel** showing classes, methods, and functions
- **Symbol indexing** for workspace-wide navigation
- **Test detection** — automatically detects PHPUnit test methods for run/debug from the editor
- **Text objects** for selecting PHP constructs (functions, classes, arguments)
- **Prettier formatting** via `@prettier/plugin-php` (when Prettier is installed)
- **Auto-completion trigger on `$`** — variables are suggested immediately on `$` input

### Debugging (via Xdebug)

Interactive step-through debugging using the Debug Adapter Protocol (DAP):

- Set and hit breakpoints
- Step over / into / out of code
- Inspect variables and evaluate expressions at runtime
- Navigate the call stack
- Run specific PHPUnit tests with debug attached

The adapter is powered by [xdebug/vscode-php-debug](https://github.com/xdebug/vscode-php-debug) and is downloaded automatically on first use.

### Tailwind CSS Integration

When the Tailwind CSS language server is installed, the extension can be configured to provide Tailwind completions inside PHP and Blade files.

---

## Limitations

- **Xdebug must be installed separately** in your PHP environment — the extension only provides the DAP bridge.
- **Attach mode is not supported** — only `launch` requests are accepted by the Xdebug adapter.
- **Phpactor on Windows** requires PHP to be in `PATH` (`.phar` files are not directly executable on Windows).
- **Intelephense and PHP Tools premium features require a paid license** for advanced functionality.
- **PHP Tools may show upsell prompts** in its free tier — disable it explicitly if you don't want it active alongside another server.
- **No built-in formatter** — code formatting requires Prettier with `@prettier/plugin-php` installed in your project.
- **PHPDoc is a hidden embedded language** — it does not appear as a standalone selectable language and is only active inside PHP doc comment blocks.
- **Tree-sitter grammars are downloaded at build time** — first-time setup requires internet access.

---

## Prerequisites

### PHP

PHP must be installed and available in your `PATH`:

```bash
# macOS (Homebrew)
brew install php

# Debian / Ubuntu
sudo apt-get install php-cli

# CentOS 8+ / RHEL
sudo dnf install php-cli

# Arch Linux
sudo pacman -S php

# Verify
which php        # macOS / Linux
where php        # Windows
```

### Xdebug (for debugging only)

Install Xdebug for your PHP version and configure it in `php.ini`:

```ini
[xdebug]
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_port=9003
xdebug.client_host=127.0.0.1
```

---

## Installation

Install via Zed's built-in extension marketplace:

1. Open the command palette (`Cmd+Shift+P` / `Ctrl+Shift+P`)
2. Run **Extensions: Install Extension**
3. Search for **PHP** and click **Install**

The extension is published under the identifier `php` and is maintained by the Zed team.

---

## Configuration

### Choosing a Language Server

Edit your Zed `settings.json` (`Cmd+,` / `Ctrl+,`) to set which language server is active. Use `!` to explicitly disable others.

**Phpactor (default, open-source):**
```json
{
  "languages": {
    "PHP": {
      "language_servers": ["phpactor", "!intelephense", "!phptools", "..."]
    }
  }
}
```

**Intelephense (freemium):**
```json
{
  "languages": {
    "PHP": {
      "language_servers": ["intelephense", "!phpactor", "!phptools", "..."]
    }
  }
}
```

**PHP Tools (freemium):**
```json
{
  "languages": {
    "PHP": {
      "language_servers": ["phptools", "!intelephense", "!phpactor", "..."]
    }
  }
}
```

### Intelephense License

Place your license file at `~/intelephense/licence.txt` (macOS/Linux) or `%USERPROFILE%\intelephense\licence.txt` (Windows), or pass it via settings:

```json
{
  "lsp": {
    "intelephense": {
      "initialization_options": {
        "licenceKey": "/path/to/licence.txt"
      }
    }
  }
}
```

### PHP Tools License

```json
{
  "lsp": {
    "phptools": {
      "initialization_options": {
        "0": "YOUR_LICENSE_KEY"
      }
    }
  }
}
```

Or set the environment variable `DEVSENSE_PHP_LS_LICENSE` in your project's `.env` file.

For additional PHP Tools settings (stubs, include paths, etc.):

```json
{
  "lsp": {
    "phptools": {
      "initialization_options": {
        "php.stubs": ["*", "zip", "zlib", "pcntl", "composer", "wordpress"],
        "php.workspace.includePath": "file:///path/to/stubs"
      }
    }
  }
}
```

### Intelephense Advanced Settings

```json
{
  "lsp": {
    "intelephense": {
      "initialization_options": {
        "completion": { "snippetSupport": true },
        "format": { "enable": true },
        "codeAction": { "enable": true },
        "diagnostics": {
          "enable": true,
          "undefinedProperties": true
        }
      }
    }
  }
}
```

### Tailwind CSS in PHP Files

```json
{
  "lsp": {
    "tailwindcss-language-server": {
      "settings": {
        "includeLanguages": {
          "php": "html"
        },
        "experimental": {
          "classRegex": [
            "class=\"([^\"]*)\"",
            "class='([^']*)'",
            "class=\\\"([^\\\"]*)\\\""
          ]
        }
      }
    }
  }
}
```

### Tailwind CSS in Laravel / Blade Files

```json
{
  "lsp": {
    "tailwindcss-language-server": {
      "settings": {
        "includeLanguages": {
          "php": "html",
          "blade": "html"
        },
        "experimental": {
          "classRegex": [
            "class=\"([^\"]*)\"",
            "class='([^']*)'",
            "class=\\\"([^\\\"]*)\\\"",
            "@class\\(\\[([^\\]]*)\\]\\)"
          ]
        }
      }
    }
  }
}
```

---

## Debugging

Add launch configurations to your `.zed/tasks.json` (or the Zed debug panel):

**Listen for Xdebug (web/CLI):**
```json
[
  {
    "label": "PHP: Listen to Xdebug",
    "adapter": "Xdebug",
    "request": "launch",
    "program": "${file}",
    "port": 9003
  }
]
```

**Debug a specific PHPUnit test:**
```json
[
  {
    "label": "PHP: Debug this test",
    "adapter": "Xdebug",
    "request": "launch",
    "program": "vendor/bin/phpunit",
    "args": ["--filter", "$ZED_SYMBOL"]
  }
]
```

### Xdebug Troubleshooting

- Confirm Xdebug is installed for the active PHP version: `php -v` should show `with Xdebug`
- Confirm `xdebug.mode=debug` is set in `php.ini`
- Confirm Xdebug is initiating a session (use `xdebug_info()` in a page or `XDEBUG_SESSION=1` query param)
- Make sure the port in your launch config matches `xdebug.client_port` (default: `9003`)
- Check Zed's language server logs via the command palette → **Language Server Logs**

---

## Contributing

### Requirements

- Rust (Edition 2021) with the `wasm32-wasi` target:
  ```bash
  rustup target add wasm32-wasi
  ```
- Zed editor (for local testing)

### Build

```bash
cargo build --target wasm32-wasi
```

### Local Development

Test without publishing by running Zed in dev-extension mode:

```bash
zed --dev-extension /path/to/zed-extensions/php
```

Open a PHP file to trigger extension activation. Use `eprintln!()` in Rust code to write to Zed's log.

### Code Quality

Before submitting a pull request:

```bash
cargo fmt          # format code
cargo clippy       # lint
cargo check        # type-check without full compile
```

### Adding Features

| Area | Location | Reference |
|---|---|---|
| New language server | `src/language_servers/` | Follow the Intelephense or Phpactor module pattern |
| Debug adapter changes | `src/xdebug.rs` | Implements the DAP adapter lifecycle |
| Syntax highlighting | `languages/php/highlights.scm` | Tree-sitter query syntax |
| Language injection | `languages/php/injections.scm` | Embed HTML/JS/CSS/SQL in PHP |
| Test detection | `languages/php/runnables.scm` | Match PHPUnit test patterns |
| Config/indentation | `languages/php/config.toml` | Editor behavior settings |
| Debug schema | `debug_adapter_schemas/Xdebug.json` | Launch config JSON schema |

### File Structure

```
zed-extensions/php/
├── extension.toml                  # Extension manifest
├── Cargo.toml                      # Rust build config (cdylib → wasm)
├── src/
│   ├── php.rs                      # Entry point, PhpExtension struct
│   ├── language_servers/           # Intelephense, PhpTools, Phpactor managers
│   └── xdebug.rs                   # Xdebug DAP adapter
├── languages/
│   ├── php/
│   │   ├── config.toml             # Bracket matching, auto-close, comment style
│   │   ├── highlights.scm          # Syntax highlighting rules
│   │   ├── injections.scm          # Embedded language detection
│   │   ├── indents.scm             # Indentation rules
│   │   ├── outline.scm             # Code structure (classes, methods)
│   │   ├── runnables.scm           # PHPUnit test detection
│   │   ├── tags.scm                # Symbol indexing
│   │   └── textobjects.scm         # Smart selection patterns
│   └── phpdoc/
│       └── config.toml             # PHPDoc embedded language config
└── debug_adapter_schemas/
    └── Xdebug.json                 # Xdebug launch configuration schema
```

### Licensing

All contributions are accepted under the **Apache 2.0** license. By submitting a pull request, you agree to license your contribution under these terms.

---

## Useful Links

- [Zed PHP Docs](https://zed.dev/docs/languages/php)
- [Phpactor](https://github.com/phpactor/phpactor)
- [Intelephense](https://intelephense.com)
- [PHP Tools for Zed](https://docs.devsense.com/other/zed/)
- [Xdebug](https://xdebug.org)
- [vscode-php-debug](https://github.com/xdebug/vscode-php-debug)
- [Zed Extension Development Docs](https://zed.dev/docs/extensions/developing-extensions)
