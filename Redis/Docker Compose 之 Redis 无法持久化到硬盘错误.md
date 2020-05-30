## Docker Compose 之 Redis 无法持久化到硬盘错误

---

> (error) MISCONF Redis is configured to save RDB snapshots, but it is currently not able to persist on disk. Commands that may modify the data set are disabled, because this instance is configured to report errors during writes if RDB snapshotting fails (stop-writes-on-bgsave-error option). Please check the Redis logs for details about the RDB error.

翻译（谷歌）：

> （错误）MISCONF Redis配置为保存RDB快照，但是当前无法持久保存在磁盘上。禁用了可能修改数据集的命令，因为此实例配置为在RDB快照失败时在写入过程中报告错误（stop-writes-on-bgsave-error选项）。请检查Redis日志以获取有关RDB错误的详细信息。

### 问题：

- 启动 Redis 的用户没有写入权限



### 解决方案：

使用 root 用户启动 Redis

```yml
version: '3.1'
services:
  master:
    image: redis
    container_name: redis
    # 修改镜像用户为 root
    user: root
    ports:
      - 6379:6379
```



