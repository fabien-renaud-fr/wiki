# Useful commands

## Amend

### Amend author

Reference: https://stackoverflow.com/a/3042512

```
# Edit git config
git config --local user.name "Fabien RENAUD"
git config --local user.email "frd.frenaud@gmail.com"

# Start rebase
git rebase -i --root                        # Replace pick by edit

# Repeat until "Successfully rebased and updated"
git commit --amend --reset-author --no-edit
git rebase --continue

# Overwrite commits
git push -f
```
