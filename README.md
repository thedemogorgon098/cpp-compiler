# MiniCPP

A from-scratch compiler for a small C++ subset. Programs are lexed, parsed, type-checked, lowered to three-address IR, optimized, compiled to a custom stack bytecode, and executed by a Python virtual machine. There is no GCC, Clang, LLVM, or Python transpilation.

Pipeline:

`source → lexer → parser → semantic analyzer → IR → optimizer → bytecode → VM → output`

## Requirements (Windows)

- Python 3.10 or later (standard library only)
- Node.js 18 or later (for the IDE)

Check versions in PowerShell:

```powershell
python --version
node --version
npm --version
```

## Run tests

From the project root (`compiler2`):

```powershell
cd C:\Users\anujk\compiler2
python -m unittest discover -s tests -v
```

## Start the compiler API

```powershell
cd C:\Users\anujk\compiler2
python server.py
```

The server binds to `http://127.0.0.1:8765`.

- `POST /compile` JSON `{ "source": "..." }`
- `POST /run` JSON `{ "source": "...", "stdin": "..." }`
- `GET /health`

Both POST endpoints return diagnostics, tokens, AST, symbol table, IR, bytecode, and (for `/run`) stdout, stderr, exit code, and status.

## Start the interactive IDE

In a **second** PowerShell window:

```powershell
cd C:\Users\anujk\compiler2\frontend
npm install
npm run dev
```

Open `http://localhost:5173`. Vite proxies `/compile` and `/run` to the Python server.

Optional production build (then only `python server.py` is needed after copying assets):

```powershell
cd C:\Users\anujk\compiler2\frontend
npm install
npm run build
cd ..
python server.py
```

Then open `http://127.0.0.1:8765`.

## Language subset

Supported:

- Types: `int`, `float`, `double`, `bool`, `char`, `string`, `void`
- Variables, assignment, arithmetic, comparisons, `&&` `||` `!`, `++` `--`
- `if` / `else`, `while`, `for`, `break`, `continue`
- Functions, parameters, return values, recursion
- One-dimensional arrays and indexing
- `cout <<`, `cin >>`, `endl`
- `int main()` and `return 0;`
- `#include <iostream>` and `using namespace std;`
- String concatenation (`+`) and comparisons

Rejected with a diagnostic: templates, pointers, classes, STL containers, `switch`, and other unsupported C++.

## Limits

- Source size, request body, and stdin size caps
- VM step limit, recursion depth, array size, and output size caps
- Malformed programs return JSON errors; the HTTP server does not crash

## Layout

- `compiler/` — lexer, parser, semantic analysis, IR, optimizer, codegen, VM
- `server.py` — localhost HTTP API
- `tests/` — stage tests and end-to-end programs
- `frontend/` — React + Vite + Monaco IDE
