# 运行 NodeBB

> 本文章基于 NodeBB 官方文档  进行细节优化，更符合文档的排版。

在之前的章节中，我们已经掌握了 NodeBB 可执行文件 的基本指令的用法。也知道我们该使用：
* `./nodebb start` 启动 NodeBB
* `./nodebb stop` 停止 NodeBB
值得一提的是 : `npm start` 和 `npm stop` 其实是和上面的指令是等效的。

----------------------------
>[warning] 特殊的启动方式:
> `node app.js` 启动单一进程的 NodeBB (使用该指令时只启动一个 NodeBB 进程)
> `node loader.js` 启动 进程池 的 NodeBB （只有使用该指令时，NodeBB 的端口池配置才会生效）
> **虽然上面的方法也能正常启动 NodeBB ，但我们依旧推荐使用 可执行文件来操作NodeBB**

