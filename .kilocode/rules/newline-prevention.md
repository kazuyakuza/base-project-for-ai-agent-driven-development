# Newline Prevention Rule

## Guidelines

- When using tools like [`write_to_file`](tools/write_to_file) or [`search_replace`](tools/search_replace) to create or edit files, AI agents must use **actual newline characters** in the content parameter.
- **Never** use literal `\\n` escape sequences to represent newlines in file content.
- File content must contain real line breaks so that when written to disk, the file has proper formatting.

## Why This Rule Exists

AI agents sometimes generate content with literal `\\n` strings (e.g., `\"line1\\nline2\"`), which results in files containing the visible text `\\n` instead of actual line breaks. This breaks file formatting and readability.

## Examples

### ❌ Incorrect (Literal \\n)
```json
{
  \"path\": \"example.txt\",
  \"content\": \"First line\\nSecond line\\nThird line\"
}
```
**Resulting file content:**
```
First line\nSecond line\nThird line
```

### ✅ Correct (Actual newlines)
```json
{
  \"path\": \"example.txt\",
  \"content\": \"First line\nSecond line\nThird line\"
}
```
**Resulting file content:**
```
First line
Second line
Third line
```

## Enforcement

- Before finalizing any file write operation, visually verify that multi-line content uses actual line breaks.
- If editing existing files with [`search_replace`](tools/search_replace), ensure replacement text uses proper newlines.
- This applies to ALL file content operations across all tools and modes.