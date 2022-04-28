## :maple_leaf: 工具

- #### git pr
  ```bash
  # 提pr的流程
  git remote add upstream

  git fetch upstream

  git checkout main

  git rebase upstream/main
  
  git push origin main
  ```
- ####  git rebase

  ```bash
  ！！！main分支禁止rebase

  # 将当前分支上相对于远程新增的提交，全部rebase成一个commit,保持分支干净
  git rebase -i head~N
  
  pick：保留该commit（缩写:p）
  reword：保留该commit，但我需要修改该commit的注释（缩写:r）
  edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
  squash：将该commit和前一个commit合并（缩写:s）
  fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
  exec：执行shell命令（缩写:x）
  drop：我要丢弃该commit（缩写:d）
  
  # 将远端的[target]分支下载到本地，并在本地新建一个[local]分支
  git fetch origin [target]:[local]

  git checkout [local]
  git rebase [target]
    # if conflict
    git add .
    git rebase --continue
    # 在任何时候，你可以用--abort参数来终止rebase的行动（git rebase --abort），并且target分支会回到rebase开始前的状态
    git rebase --abort
  git checkout [target]
  git merge [local]
  git push origin [target]
  ```
  ```bash
  # 如果你想本地[target]更新到当前[branch]
  git checkout [target]
  git pull origin [target]
  git checkout [local]
  # 将当前[branch]改变变基到[target]
  git rebase [target]

  # 如果你想将远端[target]更新到当前[branch]
  git fetch origin [target]
  git rebase origin/[target]
  git push
  ```
          
