---
title: 使用rclone备份知识库
description: 使用rclone备份知识库
date: 2024-03-10
updated: 2024-03-10
taxonomies:
  tags:
    - 技术
    - Obsidian
    - rclone
extra:
  emoji: 💾
---
一直使用 `Obsidian` 管理个人的知识库，然后配合 `syncthing` 进行多台设置之间的同步。近期同步的时候发现整个知识库的文件大小已经达到 2GB，对于如此核心的资产，貌似容灾做的很不够。

现在的核心数据是存放在 N1 小 NAS 上挂载的移动硬盘上，然后通过 Syncthing 同步到家里的电脑和公司的电脑上，暂且可以认为有 3 个备份。这里有两个痛点，首先服务器上的数据是在移动硬盘上的，感觉不是很保险，如果什么时候突然出现故障，估计数据要找回是个很大的问题。然后就是两台电脑上的数据只是为了写作用，不会时刻跟服务器上保持同步，一般是写完一些内容提交下，可能会有 1 到 2 天的延迟。

在考虑好要做备份后，我首先想到的就是同步到云盘，毕竟本地虽然方便，但是要自己做好数据保护，放到云上就可以让对应的服务商帮你搞定这一切。云盘第一个想到的就是自己用的比较多的阿里云盘，另外是国内能正常访问的 `onedrive`。定好存储后就是如何同步上去，网上搜了下后大致确认使用 `rclone`。

初步写了个脚本实现定时同步。

```bash
#!/bin/sh

# Define the source and destination
SOURCE="/backup"
DESTINATION=":/Backup"

# Define the list of flags
providers="aliyun onedrive"

# Infinite loop
while true; do
	for provider in $providers; do
		# Record the start time of the synchronization
		echo "[$provider] Sync started at $(date)"

		# Run rclone command
		rclone -v --config /data/rclone.conf sync "$SOURCE" "$provider$DESTINATION" --exclude-from exclude-file.txt

		# Record the end time of the synchronization
		echo "[$provider] Sync completed at $(date)"
	done

	# Sleep for 10 minutes
	sleep 600
done
```

不过在跑了一个晚上后发现一个很大的问题，阿里云盘竟然每次都跑的时候都会同步。然后去翻 `rclone` 的官方文档，发现是 `Webdav` 协议本身不支持将文件的元数据同步到服务端。不过 `rclone` 做了扩展，可以通过 `X-OC-Mtime` 头来支持，但是需要 `Webdav` 服务商能识别该 header。然后我去 `Alist` 的 GitHub 找 issues，没想到还真兼容，但是我在修改 `Webdav` 服务器和 `rclone` 的 vender 之后，发现还是一样的问题。然后再去确认，发现阿里云盘竟然又[不支持](https://github.com/alist-org/alist/issues/4938)了😂。

没办法放弃阿里云盘，但是觉得一家不保险，就考虑加个 Google Drive 吧，考虑到 Google Drive 需要魔法上网，把脚本改成可以配置的方式。同时觉得一直傻轮询的方式很蠢，网上找了个 rust 的 watchexec 来监控文件变化，有变更的时候再执行脚本，基本实现了自己的需求。

```bash
#!/bin/sh

# Proxy 配置
PROXY="http://xx:xx@127.0.0.1:5678" # 请替换成你的代理服务器和端口

# provider 配置，格式为："provider_name=true|false"
PROVIDERS="onedrive=false
gdrive=true"

log() {
        echo "$(date '+%Y/%m/%d %H:%M:%S') INFO  : $1"
}

setup_proxy() {
        export http_proxy=$PROXY
        export https_proxy=$PROXY
        log "Enable Proxy for $1."
}

unset_proxy() {
        unset http_proxy
        unset https_proxy
        log "Disable Proxy for $1."
}

rclone_command() {
        provider=$1
        # 这里应该是你要执行的rclone命令
        log "Start rclone for provider: $provider"
        # 此处添加你的rclone命令，例如：
        rclone -v --config /data/rclone.conf sync "/backup" "$provider:/Backup" --exclude-from /data/exclude-file.txt
        log "Finish rclone for provider: $provider"
}

# 读取并处理每一个provider的配置
echo "$PROVIDERS" | while IFS='=' read -r provider proxy_enabled; do
        if [ "$proxy_enabled" = "true" ]; then
                setup_proxy "$provider"
                rclone_command "$provider"
                unset_proxy "$provider"
        else
                rclone_command "$provider"
        fi
        log "---------------------------------------"
done
```

以上的脚本基本实现了想要的功能，不过在了解 watchexec 之后，我发现 watchexec 原来可以将变更文件输出，这样其实配合 rclone 的 `--filter-from` 参数其实是可以实现客户端的增量同步的，也就能解决 webdav 的每次都同步问题。不过这个脚本得配合着一些改动，况且感觉现在也够用了，后续有新的需求的时候再考虑修改下。