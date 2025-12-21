# Single-Section Boolean Conditions Rule

## Guidelines

- Boolean conditions in statements like `if`, `while`, etc., should be kept to a single section.
- If a condition requires more than one section, it should be extracted into a separate method with a descriptive name.
- The method call should then replace the complex condition in the original statement.

### Examples

#### Incorrect
```
if (some_bool_var && some_num_var >= some_constant_num) { 
    // ... 
}
```

#### Correct
```
if (some_meaning_name(...params...)) { 
    // ... 
}