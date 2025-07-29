# 使用remote-ssh连接离线服务器

使用vscode连接远程服务器时，会在服务器上安装两个东西，一个cli，一个server

> commit-id可以在vscode的版本信息中看到

它们的下载地址如以下格式
https://vscode.download.prss.microsoft.com/dbazure/download/stable/<commit-id>/vscode_cli_alpine_arm64_cli.tar.gz
https://vscode.download.prss.microsoft.com/dbazure/download/stable/<commit_id>/vscode-server-linux-arm64.tar.gz

> 注意这里的文件名跟处理器架构有关

cli中的code文件解压为
~/.vscode-server/code-<commit-id>
server解压到
~/.vscode-server/cli/servers/Stage-<commit-id>/server

完成以上操作就不会在离线服务器执行下载动作了，而是直接使用已经部署好的server，如果没有生效，可能需要查看.vscode-server文件夹的权限，或者通过`touch 0`命令标记为已安装
