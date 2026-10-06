# Turbo C++ 3.0 in the browser

Students who were taught on Borland Turbo C++ 3.0 (1992) get the same IDE in the
browser: `index.html` is the
full-screen IDE. `js/turbo.js` is self-contained — no
dependencies, no build step.

What is emulated:

- The 80x25 EGA text screen — blue desktop, pull-down menu bar, double-line
  window frames, scroll bars, drop shadows, blinking cursor, line:column
  indicator, and the grey status line.
- The original keyboard map: `F1` help, `F2` save, `F3` open, `F5` zoom,
  `F6` next window, `F9` make, `Alt+F9` compile, `Ctrl+F9` run, `Alt+F5` user
  screen, `Alt+F3` close, `F10` menu, `Alt+X` quit to DOS, plus the WordStar
  editor commands (`Ctrl+Y` delete line, `Ctrl+Q Y`, `Ctrl+K B/K/Y`, …).
- The **Compiling** status box, the **Message** window with Borland-style
  diagnostics (`Error HELLO.CPP 7: Statement missing ; in function main()`) that
  move the editor cursor to the offending line, and the black **user screen**
  showing program output.
- File | Open / Save against a small `C:` drive kept in the browser's
  `localStorage`, plus a toy `DOS shell` (`DIR`, `TYPE`, `CLS`, `VER`, `MEM`,
  `EXIT`).

What is *not* emulated: the debugger (`F7`/`F8`, watches, breakpoints), projects,
`Options` pages, and BGI graphics — those menu items answer with a polite dialog.
There is no x86 emulation: code is compiled by the same Wandbox service the rest
of the app uses.

### Turbo dialect support

Turbo-era source is accepted as written:

```cpp
#include <iostream.h>
#include <conio.h>

void main()
{
    clrscr();
    cout << "Hello, world!" << endl;
    getch();
}
```

Before compiling, `TurboIDE.modernize()` prepends a compatibility prologue
(modern headers, `using namespace std;`, no-op `clrscr()`/`getch()`/`gotoxy()`
shims), blanks the classic `.h` includes and rewrites `void main()` to
`int main()`. The prologue ends with a `#line 1` directive, so **error line
numbers still match what the student sees in the editor**. Source that is
already ISO C++ is passed through untouched.

### Mounting it in your own page

```html
<link rel="stylesheet" href="css/turbo.css">
<script src="js/turbo.js"></script>
<div id="ide"></div>
<script>
  const ide = TurboIDE.mount("#ide", {
    code: TurboIDE.DEFAULT_SOURCE,
    fileName: "HELLO.CPP",
    rows: 25,                       // shorter screens are allowed when embedded
    runner: TurboIDE.wandboxRunner({ compiler: "gcc-head" })
  });
  ide.getCode();                    // current source
</script>
```

`runner(code, stdin)` is the only thing the IDE needs from the outside: return
`{ compileError, output, runtimeError }` and the emulation does the rest.
`TurboIDE.wandboxRunner()` is the ready-made one used here; `onRunCheck` replaces
what `Ctrl+F9` does when the host page wants to grade instead of just run.

## Run it

Open `index.html` in a browser, or serve the folder with any static host (e.g. GitHub Pages). Compiling needs internet access to the public Wandbox API.
