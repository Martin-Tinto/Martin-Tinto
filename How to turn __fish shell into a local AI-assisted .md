How to turn **fish shell into a local AI-assisted terminal**.
Everything runs **locally**, no cloud APIs required. 🐟🤖

This guide uses **Ollama** to run models on your machine.

---

# 🧠 Fish AI Terminal Guide (Local LLM)

What you’ll get:

✔ Ask questions from the prompt
✔ AI command suggestions
✔ Auto-explain failed commands
✔ Fix typos / unknown commands
✔ Summarize man pages
✔ Explain logs/output via pipes
✔ AI command completion
✔ Built-in help command

Works great on Linux systems using **fish shell**.

---

# Step 1 — Install Ollama

Install the local LLM runtime:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Start the service if needed:

```bash
ollama serve
```

---

# Step 2 — Download a model

A good default model:

```bash
ollama pull llama3
```

Other good options:

```
mistral
deepseek-coder
```

---

# Step 3 — Create the main AI function

Create the file:

```
~/.config/fish/functions/ai.fish
```

Paste:

```fish
function ai
    set model llama3

    if test "$argv[1]" = "show"
        if test "$argv[2]" = "commands"
            echo ""
            echo "🤖 AI Terminal Commands"
            echo "────────────────────────────"
            echo "ai <question>        Ask the AI anything"
            echo "suggest <task>       Generate shell command"
            echo "manai <command>      Summarize a man page"
            echo "explain              Explain piped output"
            echo ""
            echo "Examples:"
            echo "ai how do I use fdisk"
            echo "suggest find files larger than 1GB"
            echo "manai rsync"
            echo "journalctl -xe | explain"
            echo ""
            return
        end
    end

    ollama run $model "$argv"
end
```

Reload fish:

```bash
exec fish
```

Test it:

```bash
ai how do I use fdisk
```

---

# Step 4 — AI command generator

Create:

```
~/.config/fish/functions/suggest.fish
```

```fish
function suggest
    ai "Return ONLY a Linux command that accomplishes this task:

$argv"
end
```

Example:

```bash
suggest find files larger than 1GB
```

---

# Step 5 — Explain terminal output

Create:

```
~/.config/fish/functions/explain.fish
```

```fish
function explain
    cat | ai "Explain this terminal output and suggest actions:"
end
```

Example:

```bash
journalctl -xe | explain
```

---

# Step 6 — AI man-page summarizer

Create:

```
~/.config/fish/functions/manai.fish
```

```fish
function manai
    set page (man $argv | col -b)

    echo $page | ai "Summarize this Linux man page. Explain key options with examples."
end
```

Example:

```bash
manai rsync
```

---

# Step 7 — Auto-explain failed commands

Create:

```
~/.config/fish/functions/fish_postexec.fish
```

```fish
function fish_postexec --on-event fish_postexec
    if test $status -ne 0
        echo ""
        echo "🤖 Command failed. Asking AI..."

        ai "The following Linux command failed.

Command:
$argv

Exit code:
$status

Explain the likely reason and suggest a fix."
    end
end
```

Now failed commands automatically trigger explanations.

---

# Step 8 — Fix unknown commands

Create:

```
~/.config/fish/functions/fish_command_not_found.fish
```

```fish
function fish_command_not_found
    echo "❌ Command not found: $argv"
    echo "🤖 Searching for alternatives..."

    ai "User typed this command but it doesn't exist:

$argv

Suggest the correct Linux command."
end
```

Example:

```
gti status
```

AI suggests:

```
git status
```

---

# Step 9 — AI command completion

Create:

```
~/.config/fish/functions/aicomplete.fish
```

```fish
function aicomplete
    set cmd (commandline -cp)

    ai "Complete this Linux command. Return only the completed command.

$cmd"
end
```

Bind it to a key.

Edit:

```
~/.config/fish/config.fish
```

Add:

```fish
bind \cg aicomplete
```

Now press:

```
CTRL + G
```

to get an AI completion.

---

# Step 10 — Show available commands

Run:

```
ai show commands
```

Example output:

```
🤖 AI Terminal Commands
────────────────────────────
ai <question>        Ask the AI anything
suggest <task>       Generate shell command
manai <command>      Summarize a man page
explain              Explain piped output
```

---

# Example workflow

Ask questions:

```bash
ai how do I resize a btrfs filesystem
```

Generate commands:

```bash
suggest find files older than 30 days
```

Explain logs:

```bash
dmesg | explain
```

Summarize documentation:

```bash
manai fdisk
```

---

# Result

Your fish shell now has a **local AI assistant** capable of:

🐟 answering Linux questions
🔧 generating commands
📚 summarizing documentation
🧠 explaining errors automatically
📜 analyzing logs

All running locally via **Ollama**.

---

If you'd like, I can also provide a **single installer script (~40 lines)** that automatically installs **all of this in one command** so your friends can just run:

```bash
curl ... | fish
```

and instantly get the full AI-enabled fish terminal. 🚀
