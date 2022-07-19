# Git 分布式应用

## remotes

通过 Git 进行分布式协作开发。

### clone. 

- clone 是 repository 的一份完整的拷贝，它包含了原 repository 的所有对象。（what is clone）
- 有了 clone 就可以在本地进行开发。(why do we need a clone)
- clone 和其他 repostiories 建立连接以交换数据。（introduce remotes)

### remotes

Git 在本地 repository 中通过 remotes 和其他 repositories 建立连接。

- 一个 remote 就是一个 repository 的引用，它指向了一个本地 repository 之外的 repository。可以用来在两个 repo 之间交换数据。
- remote 这个词有点迷惑，因为 remote repository 物理上不一定在远端，它也可能在本地磁盘上。
- remote-tracking branches & local-tracking branches.

## Repository

- bare repository
- development repository



## remote management

git clone https://github.com/hanning0511/git.git

git remote add nextkernel ssh://root@syzbot.sh.intel.com/ag

git remote add local file:///home/ninghan/tasks/git/ddt





## Agenda

- clone
  - 什么是 clone。
  - clone 的意义，作用。
  - 引出 remotes
- remotes
  - 从 local 到 remote, 交换数据
  - 
- 
