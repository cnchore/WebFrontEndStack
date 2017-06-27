# 找回因为账号，用户名和git上不一致，而失去的contributions

1. git clone bare 项目

user替换成你的github账户名，repo.git替换成你的repo的名字

```
git clone --bare https://github.com/user/repo.git
cd repo.git
```

2. 在repo.git目录下，建立一个.sh的shell脚本文件，name为test.sh，名字可以自己自由命名

3. 在test.sh文件内添加如下脚本

```

#!/bin/sh

git filter-branch --env-filter '

OLD_NAME="项目提交时和git不一致的用户名"
OLD_EMAIL="项目提交时和git不一致的邮箱"
CORRECT_NAME="正确的git用户名"
CORRECT_EMAIL="正确的git邮箱"

if [ "$GIT_COMMITTER_NAME" = "$OLD_NAME" ]
then
   export GIT_COMMITTER_NAME="$CORRECT_NAME"
   export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_NAME" = "$OLD_NAME" ]
then
   export GIT_AUTHOR_NAME="$CORRECT_NAME"
   export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags

```
4. 执行shell脚本

```
./test.sh
```
> 如果提示permission denied，执行如下命令后，再次执行shell脚本
```
chmod +x  *.sh
```
5. shell脚本执行完成后，执行如下命令

```
git push --force --tags origin 'refs/heads/*
```

6. 最后把这个临时down下来的repo目录删掉就行，一切大功告成

>再去看的你的github首页，丢失的绿格子都回来了。
