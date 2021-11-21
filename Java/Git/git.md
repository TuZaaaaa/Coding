# Git
## add .gitignore file
先将文件取消 track，然后再 commit
```
    git rm -r --cached .
    git add .
    git commit -m 'update .gitignore'
```

## 关联离线项目到 github
```
    unzip <repo>.zip
    cd <repo>
    git init
    git add .
    git remote add origin https://github.com/<user>/<repo>.git
    git remote update
    git checkout master
```