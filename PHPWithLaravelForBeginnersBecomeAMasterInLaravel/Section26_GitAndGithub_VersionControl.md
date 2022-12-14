# Section 26: Git and Github - Version Control

### Git Intro

### Git Installation

### Git Commands
```bash
git init
```
### Git Crash Lecture
```bash
git status
touch index.html
git add .
git commit -m "message"
git log
git reset --hard sha
```

### Git Workflow
```bash
ls -la
rm -rf .git
rm -rf index.html
git init
touch index.html
git status
git add .
git commit -m "Added file"
git status
git log
#add <h1>Feature 1 added</h1> to index.html
git add .
git commit -m "Added feature 1"
git log
#add <h1>Feature 2 added</h1> to index.html
git add .
git commit -m "Added feature 2"
git log
$ git reset --hard 9dcb1b82229bdb156a33494a1dfa3df11cfea22e
git log
#add <h1>Feature 2 added</h1> to index.html
git add .
git commit -m "Added feature 2"
git log
git reset 9dcb1b82229bdb156a33494a1dfa3df11cfea22e
git log
git status
git add .
git commit -m "Added feature 2"
git log
```

### Branches
```bash
git branch example
git branch
git checkout example
git branch
git branch -d example
git checkout -b newcategory
touch category.php
#add <?php echo "TEAM B" ?> to category.php
git checkout master
git merge newcategory
git branch -d newcategory
```

### Creating Repositories on Github
create gitcourse repository in github
```bash

```

### Git Settings
```bash
git config --global user.name "Username"
git config --global user.email "Email"

```

### Cloning and Pushing to Github
```bash
git clone https://github.com/edmundmagnojr-nabepero/gitcourse.git
cd gitcourse
touch index.html
git add .
git commit -m "created a new index.html"
git push origin main
```

### Pull Request and Pulling Changes
```bash
git pull
```

### Contributing to the Project
