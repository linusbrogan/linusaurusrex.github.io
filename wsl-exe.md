# Convenient `.exe` invocation in WSL

In Command Prompt and PowerShell, I can invoke executables without the file extensions (e.g., `notepad note.txt` vs `notepad.exe note.txt`).
To reproduce this behavior in WSL, my first thought was just to `alias` every `.exe` I might want to use, but that is inconvenient.

I did a little digging into the behavior when a command is not found, and I discovered a convenient solution.
Bash runs the function `command_not_found_handle` when a command is not found, so I set a custom `command_not_found_handle` in my `.bashrc` to retry the command with the `.exe` extension added.

```bash
CNF_PATH=/usr/lib/command-not-found
command_not_found_handle() {
	# Check for .exe extension
	if grep -Eiq '\.exe$' <<< "$1"; then
		$CNF_PATH -- "$1"
	else
		# Retry with .exe extension
		"$1.exe" "${@:2}"
	fi
	return $?
}
