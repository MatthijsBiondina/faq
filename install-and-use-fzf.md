## Instruction Manual for FZF on Ubuntu

### Prerequisites

- A machine running Ubuntu.
- Access to a terminal.
- Git installed on your machine. If not installed, you can install it using `sudo apt install git`.
- Basic familiarity with using the command line.

### Step 1: Install `fzf`

#### Cloning the Repository and Installing

1. **Open your terminal**.

2. **Clone the `fzf` repository** into the `.fzf` directory in your home folder:

    ```bash
    git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
    ```

    This command clones the repository with a history truncated to the last commit (which speeds up the cloning process).

3. **Run the installation script**:

    ```bash
    ~/.fzf/install
    ```

    This script will perform several actions:
    - It offers to create key bindings for `fzf`:
      - `CTRL-T` - Paste the selected files and directories onto the command line.
      - `CTRL-R` - Paste the selected command from history onto the command line.
      - `ALT-C` - cd into the selected directory.
    - It asks if you want to update your shell configuration files. Saying yes (`y`) will modify files like `.bashrc`, `.zshrc`, or `.fish.config`, depending on your shell.

#### Installation Script Prompts

During the installation, you will encounter the following prompts:

- **"Do you want to enable fuzzy auto-completion?"** (Y/n) - Typing `Y` and hitting Enter will set up auto-completion for your shell commands using `fzf`.

- **"Do you want to enable key bindings?"** (Y/n) - Typing `Y` and hitting Enter will enable useful key bindings that enhance the shell usability with `fzf`.

- **"Do you want to update your shell configuration files?"** (Y/n) - Typing `Y` will update your shell configuration files to source `fzf` automatically.

### Step 2: Use `fzf`

After installing, `fzf` can be utilized directly in the command line or through its key bindings, if enabled. Here’s how to use it:

#### Basic Commands

- **Fuzzy search for a file and open using an editor:**
  
  ```bash
  vim $(fzf)
  ```

- **Search through command history:**
  
  ```bash
  CTRL-R
  ```
  
  After pressing `CTRL-R`, start typing parts of any previous command, and `fzf` will show a fuzzy-searchable list of commands that match the input.

- **Fuzzy search and change into a directory:**
  
  ```bash
  cd $(find . -type d | fzf)
  ```

#### Advanced Configuration

- **Set default options** for `fzf` by adding the following to your shell configuration file (e.g., `.bashrc`):

  ```bash
  export FZF_DEFAULT_OPTS="--height 40% --layout=reverse --border"
  ```

  This sets the height of the `fzf` command line interface to 40% of the screen, reverses the layout, and adds a border.

- **Customize `fzf` to change directories**:

  ```bash
  function fcd() {
    local dir
    dir=$(find ${1:-.} -path '*/\.*' -prune -o -type d -print 2> /dev/null | fzf +m)
    cd "$dir"
  }
  ```

  Add this function to your shell configuration file to enable directory changes using `fzf`. Use it by typing `fcd` in the terminal.

### Conclusion

`fzf` is a powerful tool that enhances your command-line efficiency with fuzzy searching capabilities for files, command history, and directories. By following this manual, you should have `fzf` installed and configured for basic use on your Ubuntu system. Experiment with `fzf`’s options and integrate them into your workflow for improved productivity.

Feel free to explore more complex configurations and combine `fzf` with other command-line tools to fully leverage its capabilities. This installation and basic usage guide provides a foundation that you can build upon as you become more accustomed to working with `fzf`.
