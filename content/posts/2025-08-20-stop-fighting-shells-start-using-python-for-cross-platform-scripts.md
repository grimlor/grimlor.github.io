---
title: "Stop Fighting Shells, Start Using Python for Cross-Platform Scripts"
date: 2025-08-20
type: post
---

# Stop Fighting Shells, Start Using Python for Cross-Platform Scripts

# *How a simple coverage report task led me to discover the universal scripting solution hiding in plain sight*

## The Problem: Platform-Specific Shell Hell

Like many developers working across different environments, I found myself maintaining platform-specific shell commands that worked great... until they didn't. 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7HXmgAFqz6uk3xAHYGQict9Aaz2psfgniB6Ob_A229bQbdMvI1u4ReMhcCwFlDAnKO1HRTagiPJR_mDZyHgkKtucqolxcfuP_sVWfBbDkBg24BejDYa8iAscaQVVIdUHqakZ7hnKTa5rEVXGpSaSjWahNTcvj4t7tGgfhvYkKFOeExN65aYMR-6RPdAwv/s320/This%20Is%20Fine%20GIF.gif)[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7HXmgAFqz6uk3xAHYGQict9Aaz2psfgniB6Ob_A229bQbdMvI1u4ReMhcCwFlDAnKO1HRTagiPJR_mDZyHgkKtucqolxcfuP_sVWfBbDkBg24BejDYa8iAscaQVVIdUHqakZ7hnKTa5rEVXGpSaSjWahNTcvj4t7tGgfhvYkKFOeExN65aYMR-6RPdAwv/s480/This%20Is%20Fine%20GIF.gif)

My .zshrc was littered with WSL-specific hacks:

```
# Give me back "open" (WSL-only)
alias open=explorer.exe

# Make opening files with Edge easier (also WSL-only)
alias edge='cmd.exe /c start msedge'

# For opening files in browser (WSL-specific paths)
function open-local() {
    cmd.exe /c start "$(wslpath -w "$1")"
}

```

These worked perfectly in my WSL environment, but they were fragile, non-portable, and would break the moment I worked on a different system.

## The Catalyst: A Simple Task

While setting up a Python project with `taskipy` for Gradle-style task management, I wanted to add a command that would generate a coverage report and automatically open it in the browser:

```
[tool.taskipy.tasks]
coverage-html = "coverage html && ..."  # What goes here?

```

My first instinct was to append my WSL-specific command:

```
coverage-html = "coverage html && cmd.exe /c start \"$(wslpath -w htmlcov/index.html)\""

```

But then I realized: this would only work on WSL. Anyone else trying to use my project would get cryptic errors.

## The Epiphany: Python is Everywhere
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjiVcOOSmqM1T9xdpVd1-Oq7Y0B60C1ET40dtGOZF3LODHmhEkRVzaXlWSaN7eN6qQS2NtquWjdcXKodTwIpyEUxep_u8szx0w8OAkt9dIVJZ4Ktz6FTz1mXP07lyLqt1nj1yloy39kMh-Cw1lWMVb5GEnEP7dgry17bgipvAfvRX5zIBPzfbXhE3CQzaqL/s320/I%20Know%20Yes%20GIF%20by%20VeeFriends.gif)[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjiVcOOSmqM1T9xdpVd1-Oq7Y0B60C1ET40dtGOZF3LODHmhEkRVzaXlWSaN7eN6qQS2NtquWjdcXKodTwIpyEUxep_u8szx0w8OAkt9dIVJZ4Ktz6FTz1mXP07lyLqt1nj1yloy39kMh-Cw1lWMVb5GEnEP7dgry17bgipvAfvRX5zIBPzfbXhE3CQzaqL/s480/I%20Know%20Yes%20GIF%20by%20VeeFriends.gif)  

That's when it hit me - **Python is the truly cross-platform scripting tool**. Every system where I'd want to run this project already has Python installed. And Python's standard library includes `webbrowser`, which handles platform differences for me:

```
[tool.taskipy.tasks]
coverage-html = "coverage html && python -c \"import webbrowser; webbrowser.open('htmlcov/index.html')\""

```

This works identically on:

- ✅ WSL (opens in Windows' default browser)
- ✅ Native Linux (opens in default browser)
- ✅ macOS (opens in default browser)
- ✅ Windows (opens in default browser)
![](https://blogger.googleusercontent.com/img/a/AVvXsEjCtRIzHodihkiulmrc8q2KlagAbyljze4PSJX-yIDSO9SW5NdbcGAvhIYQQ3HrWg06UM8Rf8Phwp0SC2iMxqBd0u611sA2b8v4snh3_K_oNd1hejT8y3s3tsXb8jex2-t0dgY7Jd6tg-2_fU4qXh5Kz7yc0ElWbNzckcJ_ILS8uZmYhwPEiVlOZM8ckeW5)[](https://blogger.googleusercontent.com/img/a/AVvXsEjCtRIzHodihkiulmrc8q2KlagAbyljze4PSJX-yIDSO9SW5NdbcGAvhIYQQ3HrWg06UM8Rf8Phwp0SC2iMxqBd0u611sA2b8v4snh3_K_oNd1hejT8y3s3tsXb8jex2-t0dgY7Jd6tg-2_fU4qXh5Kz7yc0ElWbNzckcJ_ILS8uZmYhwPEiVlOZM8ckeW5)  
  

## The Transformation: From Hacks to Universal Solutions

Inspired by this success, I rewrote my shell functions using the same principle:

### Before (WSL-specific):

```
alias open=explorer.exe
alias edge='cmd.exe /c start msedge'
function open-local() {
    cmd.exe /c start "$(wslpath -w "$1")"
}

```

### After (universal):

```
function open() {
    if [ -z "$1" ]; then
        echo "Usage: open <file_or_url>"
        return 1
    fi
    
    # Handle URLs vs local files
    if [[ "$1" =~ ^https?:// ]]; then
        python3 -c "import webbrowser, sys; webbrowser.open(sys.argv[1])" "$1"
    else
        python3 -c "import webbrowser, sys, os; webbrowser.open('file://' + os.path.abspath(sys.argv[1]))" "$1"
    fi
}

```

![](https://blogger.googleusercontent.com/img/a/AVvXsEj-Aj3LRkzZi3aQ3Hd6AU-eSjRCmudgmJundhKsVLjkN5Bx7l5ztwlgXVGfhFWkUtSalWUcrHYimrnRnsBPfciGhyKZk0l-Z3-_JhPHwUNCSdZPOaCGpOVt-N3LM2jfcItuCDFDQyLBZqwPe-Qc8Reacb9PGni6vYLeXj8YvmTyDOFBSINW53KOU8llJ8hg)[](https://blogger.googleusercontent.com/img/a/AVvXsEj-Aj3LRkzZi3aQ3Hd6AU-eSjRCmudgmJundhKsVLjkN5Bx7l5ztwlgXVGfhFWkUtSalWUcrHYimrnRnsBPfciGhyKZk0l-Z3-_JhPHwUNCSdZPOaCGpOVt-N3LM2jfcItuCDFDQyLBZqwPe-Qc8Reacb9PGni6vYLeXj8YvmTyDOFBSINW53KOU8llJ8hg)  
I went from three platform-specific commands to one universal function that:

- Works on any platform where Python exists (everywhere)
- Handles both files and URLs intelligently
- Respects the user's default browser preferences
- Provides helpful error messages

## Why Python Wins for Cross-Platform Scripting

### Shell scripting limitations:

- **bash** ≠ **zsh** ≠ **cmd** ≠ **PowerShell**
- Platform-specific utilities (`wslpath`, `explorer.exe`, etc.)
- Different syntax for the same operations
- Inconsistent error handling

### PowerShell's promise vs. reality:

- Technically cross-platform, but Windows-centric in practice
- Unfamiliar to non-Microsoft ecosystem developers
- Still requires platform-specific knowledge for many tasks

### Python's advantages:

- **Truly universal**: Same code, same behavior, everywhere
- **Rich standard library**: Modules like `webbrowser`, `os`, `pathlib` handle platform differences internally
- **Ubiquitous**: Available on virtually every development environment
- **Consistent APIs**: No guessing about platform-specific flags or behaviors

## Beyond File Opening: The Broader Pattern

This insight extends far beyond opening files:

```
# File operations
python -c "from pathlib import Path; print(Path.home() / 'documents')"

# Network requests  
python -c "import urllib.request; print(urllib.request.urlopen('https://api.github.com').read())"

# JSON processing
python -c "import json, sys; print(json.dumps(json.load(sys.stdin), indent=2))"

# Process management
python -c "import subprocess; subprocess.run(['git', 'status'])"

```

Each of these works identically across platforms, while shell equivalents require different syntax and tools.

## The Takeaway

**Stop fighting shell differences. Start using Python as your universal runtime.**

When you need cross-platform functionality in your shell scripts, task runners, or development workflows, ask yourself: "Could Python handle this?" The answer is usually yes, and the solution will be more portable, maintainable, and reliable than platform-specific shell commands.

Your future self (and your collaborators) will thank you when your scripts work everywhere, without modification.

---

*What platform-specific scripts are cluttering your dotfiles? Share your Python-powered solutions in the comments!*

---

How's that? It captures your journey from the WSL hacks to the universal solution, while making the broader point about Python as the cross-platform scripting lingua franca.![](https://blogger.googleusercontent.com/img/a/AVvXsEi4EeQJCRFViUgQ0TzbLYfkdWZ20-1nxAtSUhTqB1d9SEcLZ9kzpQTFSWhs_aVl_WOwQpdQU1YFRvRvMiBkMbnjJ1ULiZYPo7DexlhCQwNvgoEZKsU0dTjpsjFwIosN4coEFC5uJQCy4gyZQtK4EOPvTn_oiI2R-eptZBnxGBZ9fz3F0ptxDWFOFQBJ9_PS)[](https://blogger.googleusercontent.com/img/a/AVvXsEi4EeQJCRFViUgQ0TzbLYfkdWZ20-1nxAtSUhTqB1d9SEcLZ9kzpQTFSWhs_aVl_WOwQpdQU1YFRvRvMiBkMbnjJ1ULiZYPo7DexlhCQwNvgoEZKsU0dTjpsjFwIosN4coEFC5uJQCy4gyZQtK4EOPvTn_oiI2R-eptZBnxGBZ9fz3F0ptxDWFOFQBJ9_PS)
