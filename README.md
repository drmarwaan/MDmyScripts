# MDmyScripts
### Generate a Markdown file with folder tree + contents (ideal for uploading multiple scripts to LLMs)

## Features
- Skips `.vs` and `.git` folders. 
- Outputs contents of `[FolderName].md` with directory tree and full file contents.
- No installs (standalone - portable).
- Structured for clarity for AI

## Usage
Download MDmyScripts.exe → Run in any folder → get a clean, ready-to-upload-to-ai file of all your scripts.

## Usage for sick ppl
save as `name.py` in any folder >> run with `./name.py` in terminal >> upload output file to ai for related questions
```
import os

def generate_tree_structure(start_path):
    tree_lines = []

    def walk(path, prefix=""):
        entries = sorted(os.listdir(path))
        for i, entry in enumerate(entries):
            full_path = os.path.join(path, entry)

            # Skip .vs and .git directories
            if os.path.isdir(full_path) and entry in [".vs", ".git"]:
                continue

            is_last = i == len(entries) - 1
            tree_lines.append(f"{prefix}{'└──' if is_last else '├──'} {entry}")

            if os.path.isdir(full_path):
                new_prefix = prefix + ("    " if is_last else "│   ")
                walk(full_path, new_prefix)

    walk(start_path)
    tree_lines = ["# tree", "```"] + tree_lines + ["```"]
    return "\n".join(tree_lines)

def write_file_content(path, relative_path):
    try:
        with open(path, 'r', encoding='utf-8') as f:
            content = f.read()
        return f"\n# {relative_path}\n```\n{content}\n```\n"
    except Exception as e:
        print(f"Error reading {path}: {e}")
        return ""

def main():
    current_dir = os.getcwd()
    current_dir_name = os.path.basename(current_dir)
    output_file = f"contents of {current_dir_name}.md"
    output_path = os.path.join(current_dir, output_file)

    # Remove existing output file if it exists
    if os.path.exists(output_path):
        os.remove(output_path)

    # Generate and write the directory tree
    tree_output = generate_tree_structure(current_dir)
    with open(output_path, 'w', encoding='utf-8') as out:
        out.write(tree_output)

    # Append file contents (excluding .vs directories)
    for root, dirs, files in os.walk(current_dir):
        # Skip .vs directories
        dirs[:] = [d for d in dirs if d != ".vs"]

        for file in files:
            if file == output_file:
                continue
            file_path = os.path.join(root, file)
            rel_path = os.path.relpath(file_path, current_dir)
            content = write_file_content(file_path, rel_path)
            if content:
                with open(output_path, 'a', encoding='utf-8') as out:
                    out.write(content)

if __name__ == "__main__":
    main()
```
