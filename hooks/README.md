# Automatically add task type to your commit messages

The following guid will show you how to add the task type to your commit messages, automatically, using git hooks.

Git hooks are, basically, just scripts that Git executes before or after events such as: `commit`, `push`, and `receive`.

Now that you know what you need, just create a new file under `.git/hooks` folder. Name it **prepare-commit-msg**.
Copy the code below and paste it into your newly created file.

```bash
#!/bin/bash

# List the branch prefixes that apply, bellow
if [ -z "$BRANCHES_TO_APPLY" ]; then
	BRANCHES_TO_APPLY=(chore fix feat)
fi

# Pick the current branch name
BRANCH_NAME=$(git symbolic-ref --short HEAD)

# Remove the unnecessary parts
TRIMMED=$(echo $BRANCH_NAME | grep -Eo '^(\w+)' | sed -e 'y/ABCDEFGHIJKLMNOPQRSTUVWXYZ/abcdefghijklmnopqrstuvwxyz/')

# Check if it matches
APPLY_TO_BRANCH=$(printf "%s\n" "${BRANCHES_TO_APPLY[@]}" | grep -c "^$TRIMMED$")

# If it matches, prepend the part that interests us to the given commit message
if [ -n "$BRANCH_NAME" ] && [[ $APPLY_TO_BRANCH -eq 1 ]]; then
	sed -i.bak -e "1s/^/$TRIMMED: /" $1
fi
```

After the creation of the file, you'll need to give it its proper permissions. This can be achieved by executing `chmod +x prepare-commit-msg`.
If you want to see it in action just make a new commit and type any message you want. You should see now, given the branch name above, something like:

```bash
commit 976f4354779a824e5edfd851857b26c9bcfd3e14 (feat/GH-1234)
Author: Rafael <email@example.com>
Date:   Tue Jul 23 17:35:25 2019 +0100

    feat: This is a testing message
```

# Apply your changes globally

In order to apply the behavior described above to every new repository, 
you need to create a folder that's going to contain all of your hooks. You can, for instance, clone this repo that already contains the necessary code. After having your own folder you execute the following command:

```bash
git config --global init.templatedir '/path/to/your/own/folder'
```
Within you should have another folder named `hooks`, this is where we're going to place our git hooks.
Now that the folder structure is correct you should give execution permissions to the script. The command to execute after is:

```bash
chmod a+x /path/to/your/own/folder/hooks/prepare-commit-msg
```

Now you have everything set up. Every time that you create or clone new repositories your commit messages will be appended with the task type based on the branch that you're working on.