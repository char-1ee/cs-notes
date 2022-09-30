---
description: A better workflow than add-commit-push convention.
---

# Better GitHub Workflow

```bash
# start a new feature 
git clone <https...>
git checkout -b my-feature

# after make new changes
git diff

# after check changes, push to remote my-feature
git add <file_name...>
git commit 
git push origin my-feature

# assume before push to remote, there are new updates in remote main
git checkout main

# synchronize new updates from remote to local main branch
git pull origin main

# after synchronized local main with remote main
# we merge that updates from local main into local my-feature branch
git checkout my-feature

# rebase on local main(sychronized), need manually solve rebase conflicts
git rebase main
 
# after rebase, we have successfully update remote main on our local my-feature
# then push updated my-feature to remote by force
git push -f origin my-feature

# then pull request (squash and merge) and delete remote my-feature. 
# then we need delete the local my-feature too
git branch -D my-feature

# dont forget to update local main
git pull origin main
```

Reference&#x20;

{% embed url="https://www.bilibili.com/video/BV19e4y1q7JJ?share_source=copy_web&vd_source=34b4be2c46ac1377b18609e471038eec" %}
bilibili
{% endembed %}

