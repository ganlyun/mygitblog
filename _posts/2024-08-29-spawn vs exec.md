# spawn vs exec

根据ai给出的重点

- `exec`: 用于执行 shell 命令，并返回命令的输出结果。它适用于需要获取命令输出的场景。
- `spawn`: 用于启动一个进程并获取它的标准输出和标准错误流。它适用于需要实时获取输出或长时间运行的进程。



具体使用时，两者也是有所差异

```javascript
// 使用exec执行命令
const command = 'ls -l'
exec(command, (error, stdout, stderr) => {
  if (error) {
    // 如果命令执行出错，则打印错误信息
    console.error(`exec error: ${error}`);
    return;
  }
  // 打印命令的stdout（标准输出）结果
  console.log(`stdout: ${stdout}`);
  // 如果存在stderr（标准错误），打印错误信息
  if (stderr) {
    console.error(`stderr: ${stderr}`);
  }
}, options);
```

作为对比

```javascript
// 定义要执行的命令和参数
const command = 'ls'; // 这个命令在Unix-like系统中列出目录内容，在Windows上使用 'dir' 命令
const args = ['-l', '-a']; // 命令参数

// 使用spawn创建子进程
const child = spawn(command, args, options);

// 监听子进程的输出流
child.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

// 监听子进程的错误输出流
child.stderr.on('data', (data) => {
  console.error(`stderr: ${data}`);
});

// 监听子进程的退出事件
child.on('close', (code) => {
  console.log(`child process exited with code ${code}`);
});

// 监听子进程的错误事件
child.on('error', (error) => {
  console.error(`Failed to start child process: ${error}`);
});
```



## 一个小案例

最近在搞的项目需要启动很多服务，就想做一个小工具一键启动所有服务

结合ai的解释，spawn明显是更适合做这个工作的

我们工具的核心代码就是这一句

```js
spawn('npm', ['run', 'start'], {cwd: '当前工作目录'})
```



结果第一步就碰到了问题

我在项目目录下执行这个小脚本的时候，报错

npm ENOENT

ENOENT是Error No Entry的意思，就是没有找到npm命令的意思。很奇怪，环境的nodejs和npm肯定是配置好的，执行

```js
spawn('node', ['-v'])
```

是可以输出node版本的，所以，环境没有问题

简单google了一下，stackoverflow上给出了答案：由于操作系统的原因，在linux下可以执行npm，而在win下就需要执行npm.cmd

本以为修改以后问题迎刃而解

结果

[ERR spawn EINVAL](https://stackoverflow.com/questions/78420489/error-when-running-npm-script-with-yarn-err-spawn-einval)

> It is important to note that there has been a breaking change for Windows users who utilize `child_process.spawn` and `child_process.spawnSync`. Node.js will now error with `EINVAL` if a `.bat` or `.cmd` file is passed to `child_process.spawn` and `child_process.spawnSync` without the `shell` option set. If the input to `spawn/spawnSync` is sanitized, users can now pass `{ shell: true }` as an option to prevent the occurrence of EINVALs errors.

简单翻译一下，同样是操作系统的原因，在win下如果想使用.cmd命令，必须要添加 { shell: true } 参数

因为——[在 Windows 上未启用 shell 选项的情况下，通过 child_process.spawn 的 args 参数进行命令注入 （CVE-2024-27980） - （高危）](https://nodejs.org/en/blog/vulnerability/april-2024-security-releases-2#command-injection-via-args-parameter-of-child_processspawn-without-shell-option-enabled-on-windows-cve-2024-27980---high)

经过修改，我们的命令变成了

```js
spawn('npm.cmd', ['run', 'start'], {cwd: '当前工作目录', shell: true})
```

运行符合预期
