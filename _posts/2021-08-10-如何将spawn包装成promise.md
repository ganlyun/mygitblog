# 如何将spawn包装成promise

原问题链接：https://stackoverflow.com/questions/58570325/how-to-turn-child-process-spawns-promise-syntax-to-async-await-syntax



## 题主给出的方法

主要通过判断`exit`事件返回的`code`值决定`resolve`还是`reject`

```js
function callToolsPromise(req) {
    return new Promise((resolve, reject) => {
        let pipshell = 'pipenv';
        let args = ['run', 'tools'];
        req.forEach(arg => {
            args.push(arg)
        });
        tool = spawn(pipshell, args);
        tool.on('exit', (code) => {
            if (code !== 0) {
                tool.stderr.on('data', (data) => {
                    reject(data);
                });
            } else {
                tool.stdout.on ('data', (data) => {
                    resolve(JSON.parse(data)):
                });
            }
        });
    })
}
```

关于这种方法，我有一个疑问，退出时才会执行回调的话，这时候绑定的data事件会不会大部分信息应该已经丢失了？



## 高赞答案方法

使用`async/await`写法

使用了`for async (chunk of stream)`读取数据流，并使用局部变量缓存数据流

使用`close`事件返回的`code`码决定是否抛出错误

```js
async function spawnChild() {
    const { spawn } = require('child_process');
    const child = spawn('node', ["child.js"]);

    let data = "";
    for await (const chunk of child.stdout) {
        console.log('stdout chunk: '+chunk);
        data += chunk;
    }
    let error = "";
    for await (const chunk of child.stderr) {
        console.error('stderr chunk: '+chunk);
        error += chunk;
    }
    const exitCode = await new Promise( (resolve, reject) => {
        child.on('close', resolve);
    });

    if( exitCode) {
        throw new Error( `subprocess error exit ${exitCode}, ${error}`);
    }
    return data;
}
```



看完答案，不禁又引出一个问题，exit和close究竟有什么不同



## 关于exit和close事件的异同

原文 https://stackoverflow.com/questions/37522010/difference-between-childprocess-close-exit-events



先说**结论**：如果你更关注程序什么时候结束，使用exit，如果你更关注程序的输入输出，使用close



按照node文档对close事件的描述，由于可能有进程共享读写流的情况，所以close事件只会在读写流关闭后触发。

这句可能比较难以理解，换种说法就是，exit事件触发后，close事件可能会延迟触发甚至不触发——因为共享的读写流没有关闭



详细效果可以查看原答案中的例子
