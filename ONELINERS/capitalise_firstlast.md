A one-liner incorporating the capitalization function, using `sed` for text manipulation:

```bash
echo "john.doe" | sed -E 's/(\w)(\w*)\.(\w)(\w*)/\U\1\E\2.\U\3\E\4/'
```
**Explanation:**
1. `echo "john.doe"`: Provides the input name.
2. `sed -E`: Invokes `sed` with extended regular expression support (`-E`).
3. `s/(\w)(\w*)\.(\w)(\w*)/\U\1\E\2.\U\3\E\4/`:
    - `s/`: Indicates the substitution command.
    - `(\w)(\w*)`: Matches and captures the first letter (`\w`) of the first name and any remaining letters (`\w*`).
    - `\.`: Matches the dot.
    - `(\w)(\w*)`: Matches and captures the first letter and remaining letters of the last name.
    - `\U\1\E\2.\U\3\E\4`: Replaces the matched text with:
        - `\U\1\E`: The first letter of the first name capitalized.
        - `\2`: The remaining letters of the first name unchanged.
        - `.`: The dot.
        - `\U\3\E`: The first letter of the last name capitalized.
        - `\4`: The remaining letters of the last name unchanged. 

**Example Use:**

```bash
echo "jane.smith" | sed -E 's/(\w)(\w*)\.(\w)(\w*)/\U\1\E\2.\U\3\E\4/'  # Output: Jane.Smith
```
