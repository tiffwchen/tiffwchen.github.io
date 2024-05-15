---
layout: post
title: Working With Forks
excerpt: Common Commands Used While Working With Forks Because I Always Forget
---

## _Working With Forks_

A reference document for myself so I don't have to Google the same things every time. 

### Syncing Your Fork

```
# Add a new remote upstream repository
git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git

# Sync your fork
git fetch upstream
git checkout main
git merge upstream/main

# Sync your fork's remote repository
git push

```

Sources:
* [How to Sync Your Fork with the Original Git Repository](https://www.freecodecamp.org/news/how-to-sync-your-fork-with-the-original-git-repository/)

### Working With Somebody Else's Branch In Their Fork of the Same Repository

Assuming you already have cloned your own fork of the repository:
```
git remote add other https://github.com/OTHER_FORK_OWNER/OTHER_FORK_REPOSITORY.git

git fetch other

# Checkout new branch to work on
git checkout -b add-other-changes

# Pull their branch 
git pull other other-fork-branch
```

Once you've made changes and want to push to your remote:
```
git push origin HEAD
``` 

If you want to list the remotes you have added for the repository, run:
```
git remote -v
```

Sources:
* [How do you work with someone else's fork?](https://dev.to/davidmaceachern/how-do-you-work-with-someone-else-s-fork-5fd7#:~:text=How%20do%20you%20work%20with%20someone%20else%27s%20fork%3F,Make%20some%20changes%20then%20push%20to%20Github%20)

<iframe title="[@10]|Productivity Benchmark_Showcase" width="1140" height="541.25" src="https://app.powerbi.com/reportEmbed?reportId=f7e3b880-f310-47b7-bdf8-fbdfbd217e28&autoAuth=true&ctid=c9e8e6f7-ce8b-49ae-9eb4-d91e77472c23" frameborder="0" allowFullScreen="true"></iframe>