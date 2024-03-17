# electron 桌面程序

## vite.electron.dev.ts

在启动 vite 项目时 直接启动 electron 不用开启两个 shell

```ts
// 开发环境插件electron
import type { Plugin } from "vite";
import type {AddressInfo} from 'net';
// 返回进程信息
import {spawn} from 'child_process';
// 热更新
import fs from 'node:fs';

const buildBackground = () => {]
    require('esbuild').buildSync({
        entryPoints: ['./electron.ts'],
        bundle: true,
        outfile: 'dist/electron.js',
        platform: 'node',
        target: 'node14',
        external: ['electron']
    })
}
export const ElectronDevPlugin = (): Plugin => {
    // 必须有name属性
    return {
        name: 'electron-dev',
        configureServer(server) {
            buildBackground();
            server?.httpServer?.once('listening', () => {
                // 读取vite服务信息
                const addressInfo = server.httpServer?.address() as AddressInfo;
                const IP = `http://localhost:${addressInfo.port}`;
                /**
                 * 进程传参法第一个参数是electron的入口文件
                 * require('electron')返回一个路径
                 * 第二个参数是将background.ts的路径传递给electron
                 * electron不认识ts，需要将ts编译为js
                 */

                let ElectronProcess = spawn(require('electron'), ['dist/background.js', IP])
                // 文件发生变化，重新编译
                fs.watch('./electron.ts', () => {
                    // 杀死进程
                    ElectronProcess.kill();
                    buildBackground();
                    ElectronProcess = spawn(require('electron'), ['dist/background.js', IP])
                }
                // 监听日志
                ElectronProcess.stderr.on('data', (data) => {
                    console.log(data.toString());
                })

            }
        }
    }
}
```

## 注册插件

tsconfig.node.json

```json
"include": [
    // 其他配置
    "plugins/**/*.ts",
]
```

引入文件 vite.config.ts

```ts
import { ElectronDevPlugin } from "./plugins/electron-dev";
// 注册打包
import { ElectronBuildPlugin } from './plugins/electron-build'
export default defineConfig({
    plugins: [
        ElectronDevPlugin(),
        ElectronBuildPlugin()
    ],
    base: './' //默认绝对路径  改成相对路径，不然会白屏

}
```

## electron

background.ts

```ts
import { app, BrowserWindow } from "electron";

app.whenReady().then(() => {
    const win = new BrowserWindow({
        width: 800,
        height: 600,
        webPreferences: {
            // 可以在渲染进程调用node api
            nodeIntegration: true,
            // 关闭渲染进程的沙箱
            contextIsolation: false,
            // 关闭跨域检测
            webSecurity: false
        }
    }

    if (process.argv[2]) {
        // 使用进程传参
        win.loadURL(process.argv[2])
    }else {
        win.loadFile('index.html')
    }
}
```

## electron 打包

vite.electron.build.ts

```ts
// 生产环境插件Electron
import type { Plugin } from "vite";
import fs from "node:fs";
import path from "node:path";
import * as electronBuilder from "electron-builder";

const buildBackground = () => {]
    require('esbuild').buildSync({
        entryPoints: ['./electron.ts'],
        bundle: true,
        outfile: 'dist/electron.js',
        platform: 'node',
        target: 'node14',
        external: ['electron']
    })
}
// 打包 需要等Vite打包完成之后的index.html文件 再执行electron-build打包
export const ElectronBuildPlugin: Plugin = {
    return {
        name: 'electron-build-plugin',
        closeBundle () {
            buildBackground();
            // electron-builder 需要指定package.json main
            const json = JSON.parse(fs.readFileSync('package.json', 'utf-8'));
            json.main = 'background.js';
            fs.writeFileSync('package.json', JSON.stringify(json, null, 2));
            // bug electron-builder 会下载垃圾文件
            // 解决方法：指定假文件作为锚点
            fs.mkdirSync('dist/node_modules', {recursive: true})
            // 打包
            electronBuilder.build({
                config: {
                    directories: {
                        output: path.resolve(process.cwd(), 'release'),
                        app: path.resolve(process.cwd(), 'dist'),
                        asar: true, // 压缩包
                        productName: 'electron-vite',
                        nsis: {
                            // 取消一键安装
                            onClick: false,
                            allowToChangeInstallationDirectory: true, //指定安装目录
                        }
                    }
                }
            })
        }
    }
}
```
