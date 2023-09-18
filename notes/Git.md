# Git

## 删除本地和远程 tag

### 删除本地 tag

```shell
$ git push --delete <remote> <tag name>
```

### 删除远程 tag

```shell
$ git tag -d <tag name>
```

## 从仓库中永久删除某个文件

```shell
$ git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch <path/to/file>' \
  --prune-empty --tag-name-filter cat -- --all
```

## 从 Github 上拉取 Pull Request 到本地

```shell
$ git fetch origin pull/<ID>/head:<BRANCH_NAME>
$ git checkout <BRANCH_NAME>
```

## 移动子模块路径

```sh
$ git mv <original path> <new path>
```