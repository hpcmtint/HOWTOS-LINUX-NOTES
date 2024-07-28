Zsh function to generate SSH keys for different encryption types and save them in the `.ssh` directory with the type in the filename without any human interaction. Here's a script to achieve this:

```zsh
generate_ssh_keys() {
  local ssh_dir="$HOME/.ssh"
  local passphrase=""

  mkdir -p "$ssh_dir"
  cd "$ssh_dir"

  local key_types=("rsa" "dsa" "ecdsa" "ed25519")

  for key_type in "${key_types[@]}"; do
    local file_name="id_${key_type}"
    ssh-keygen -t $key_type -f "$ssh_dir/$file_name" -N "$passphrase" -C "${key_type}_key"
  done

  echo "SSH keys generated for the following types: ${key_types[@]}"
}

generate_ssh_keys
```

### Explanation:
1. **`generate_ssh_keys` function**:
    - This function encapsulates the logic for generating SSH keys.
  
2. **`local ssh_dir="$HOME/.ssh"`**:
    - Sets the SSH directory to `$HOME/.ssh`.
  
3. **`local passphrase=""`**:
    - Sets an empty passphrase for non-interactive key generation. You can modify this if you need a passphrase.

4. **`mkdir -p "$ssh_dir"`**:
    - Ensures that the `.ssh` directory exists.

5. **`cd "$ssh_dir"`**:
    - Changes the current directory to the `.ssh` directory.

6. **`local key_types=("rsa" "dsa" "ecdsa" "ed25519")`**:
    - Array of key types to generate.

7. **Loop through each key type and generate keys**:
    - For each key type, it runs `ssh-keygen` with the appropriate type and saves the key in the `.ssh` directory with a filename that includes the key type.
  
8. **`-N "$passphrase"`**:
    - Sets the passphrase for the key (empty in this case).

9. **`-C "${key_type}_key"`**:
    - Adds a comment to the key.

10. **`echo "SSH keys generated for the following types: ${key_types[@]}"`**:
    - Prints a message indicating which keys have been generated.

### Usage:
1. Save the function in your `.zshrc` file or another Zsh script file.
2. Source the file or restart your terminal.
3. Run the function by typing `generate_ssh_keys` in your terminal.

This will generate SSH keys of types `rsa`, `dsa`, `ecdsa`, and `ed25519` in the `.ssh` directory without requiring any user interaction.
