---
layout: post
title: Java文件变更通知
category: 技术
tags: Nginx
description: JDK1.7 文件变更通知
---

### JDK1.7文件更改事件通知

> `java.nio.file`包提供了文件变更通知的API(WatchService)
> WatchService允许监听一个或多个目录的变化, 同时可以选择性的接收事件，其中包括：创建，删除，修改。



#### 代码
```
package com.pux.test;

import java.io.IOException;
import java.nio.file.*;

/**
 * Created by xiaojs on 18/9/16.
 */
public class MyWatchService {

    public static void main(String[] args) {

        try {
            WatchService watchService = FileSystems.getDefault().newWatchService();

            // 监听该目录
            Path path = Paths.get("/Users/Jason");
            // 注册指定事件
            path.register(watchService, StandardWatchEventKinds.ENTRY_CREATE);

            // 此处可以放到其他的线程中
            while(true){
                WatchKey key = null;
                try {
                    // 等待信号
                    key = watchService.take();
                }catch (InterruptedException e){}

                for(WatchEvent<?> event : key.pollEvents()){
                    WatchEvent.Kind<?> kind = event.kind();
                    // 文件可能丢失或遗弃
                    if(kind == StandardWatchEventKinds.OVERFLOW){
                        continue;
                    }

                    // 文件名在Event上下文中
                    WatchEvent<Path> ev = (WatchEvent<Path>)event;

                    // 也有可能是目录
                    // 如果是目录可以通过 filename.resolve()来解析, 然后再次Paths.get(xxx).register(...)
                    Path filename = ev.context();

                    System.out.format("file %s%n", filename);
                    // 后续逻辑...
                }

                key.reset();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}

```


