# colorterm
Simple library to abstract away CSI console escape sequences

# Usage

```python
# Simple formatting

from colorterm import Formatter as C

print(C(C.BOLD, C.RED, "bold red"))
print(C(C.BOLD, C.RED, C.WHT_BG, "bold red on white"))
print(C(C.B_GRN, C.BLU_BG, "bright green on black"))

C.disable()
print(C(C.BOLD, C.RED, "this text is unformatted"))
```

```python
# Proper string length handling

import colorterm
from colorterm import Formatter as C

text = C(C.GRN, "some green text")
print(len(text)) # prints 24
print(colorterm.str_length(text)) # prints 15 as expected
```

# Manual

Basic usage (as above) doesn't require reading the `console_codes(4)` manpage. However, I highly suggest you familiarize yourself with what's supported by skimming through the manpage on your system:
```bash
man console_codes
```

# Colors

These are the standard colors. Note that this library treats "default color" as a color and not an attribute (see Caveats below).

| Code | Color |
| ---- | ----- |
| `BLK` | Black |
| `RED` | Red |
| `GRN` | Green |
| `BRN` | Brown (or yellow on some systems) |
| `BLU` | Blue |
| `MAG` | Magenta |
| `CYN` | Cyan |
| `WHT` | White |
| `DFLT` | Default color (reset foreground/background) |

# Attributes

These are the standard attributes. You can use more than one in a single format call: `C(C.BOLD, C.ITALIC, C.RED, "text")` gives bold, italic, and red text.

| Code | Attribute |
| ---- | --------- |
| `BOLD` | Makes text bold |
| `HALF` | Makes text half-bright |
| `ITALIC` | Makes text italic (not supported everywhere) |
| `UNDERLINE` | Makes text underlined |
| `REVERSE` | "Reverse video" mapping (see manpage) |

# Color Modifiers

You can append one of `_FG`, `_BG`, `_B`, `_BFG`, and `_BBG` to any of the colors, including color aliases (see below). These modifiers have the following effect:

| Modifier | Action |
| -------- | ------ |
| `<color>_FG` | Identical to `<color>` (provided for completeness) |
| `<color>_BG` | Use the background code for `<color>` |
| `<color>_B` | Use the bright foreground code for `<color>` |
| `<color>_BFG` | Identical to `<color>_B` (provided for completeness) |
| `<color>_BBG` | Use the bright background code for `<color>` (not supported everywhere) |

where `<color>` can be one of the normal colors above or one of the color aliases below.

# Attribute Aliases

For convience, you can use one of these aliases instead of typing the full attribute name.

| Alias | Value |
| ----- | ----- |
| `ITAL` | `ITALIC` |
| `UND` | `UNDERLINE` |
| `UNDER` | `UNDERLINE` |
| `DEF` | `DFLT` |
| `B` | `BOLD` |
| `H` | `HALF` |
| `I` | `ITALIC` |
| `U` | `UNDERLINE` |
| `R` | `REVERSE` |

# Color Aliases

Like attribute aliases, these values work exactly like normal colors. Modifiers work exactly like normal colors; you can do things like `YEL_B`, `YEL_BG`, `GREEN_BG`, and so on.

| Alias | Value |
| ----- | ----- |
| `BLACK` | `BLK` |
| `GREEN` | `GRN` |
| `BROWN` | `BRN` |
| `YEL` | `BRN` |
| `YELLOW` | `BRN` |
| `BLUE` | `BLU` |
| `MAGENTA` | `MAG` |
| `CYAN` | `CYN` |
| `WHITE` | `WHT` |
| `DEF` | `DFLT` |
| `DEFAULT` | `DFLT` |

# Extension

The `ColorFormatterClass` defines a few methods allowing for adding extra attributes. Note that these must be usable with the CSI escape sequence `\x1c[ ... m`.

```python

from colorterm import Formatter as C
C.add_attr("BLINK", 5)
C(C.BLINK, "This text blinks")
```

## 8-bit and 24-bit Color

Note that `C.add_attr` doesn't require the second argument be an integer; strings are allowed. Code `38` defines an 8-bit color while code `48` defines a 24-bit color. Their values have the following format:

| Format | Description |
| ------ | ----------- |
| `38;5;x` | `x` is a value from `0` to `255` inclusive |
| `48;2;r;g;b` | `r`, `g`, and `b` are values from `0` to `255` inclusive |

```python
C.add_attr("PUR8", "38;5;201")
C(C.PUR8, "this text has a nice purple color in my terminal")
```

```python
C.add_attr("REDMAX", "48;2;255;0;0")
C(C.REDMAX, "This text is a truly vibrant red")

```

To enumerate the 8-bit colors your palette supplies, run the following Python:
```python
color = lambda n: "\033[38;5;{}m{:-3}\033[0m".format(n, n)
grid = [list(range(16*i, 16*i+16)) for i in range(16)]
print("\n".join(" ".join(clolor(n) for n in row) for row in grid))
```

# Caveats

Note that applying modifiers to `DFLT`, while possible, doesn't actually do anything. This is because I treat `DFLT` as a color instead of as an attribute.

