### Github拉取代码报错

`Failed to connect to github.com port 443: Timed out`

这是由于电脑里开启了代理，例如开启了翻墙软件等，就会造成这个原因

解决方法：1.取消全局代理。2.找到并打开代理软件

 `git config --global --unset http.proxy  
 `git config --global --unset https.proxy

### Git回退版本

当commit的内容有错误，需要回撤到之前的某个版本时，可以用`get reset`命令来回退版本。

- `git --hard Head^`：重置位置的同时，直接将 working Tree工作目录、 index 暂存区及 repository 都重置成目标Reset节点的內容,所以效果看起来等同于清空暂存区和工作区。
    
- `git --soft Head^`：
    
- 重置位置的同时，保留working Tree工作目录和index暂存区的内容，只让repository中的内容和 reset 目标节点保持一致，因此原节点和reset节点之间的【差异变更集】会放入index暂存区中(Staged files)。所以效果看起来就是工作目录的内容不变，暂存区原有的内容也不变，只是原节点和Reset节点之间的所有差异都会放到暂存区中。
    
- `git --mixed Head^`：等价于`git Head^`，
    
- 重置位置的同时，只保留Working Tree工作目录的內容，但会将 Index暂存区 和 Repository 中的內容更改和reset目标节点一致，因此原节点和Reset节点之间的【差异变更集】会放入Working Tree工作目录中。所以效果看起来就是原节点和Reset节点之间的所有差异都会放到工作目录中。