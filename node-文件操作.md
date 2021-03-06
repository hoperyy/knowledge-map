# Node 文件操作

+   utime

    ```javascript
    function utime(filepath) {
        const fs = require('fs');
        
        const newTime = ((Date.now() - (10 * 1000))) / 1000;
        fs.utimesSync(filepath, newTime, newTime);
    }
    ```

+   writeFileSync

    ```javascript
    function writeFileSync(filepath, content) {
        const fs = require('fs');

        const fd = fs.openSync(filepath, 'w+');
        fs.writeFileSync(filepath, content);
        fs.closeSync(fd);
    }
    ```

+   为 `body` 添加 `<script>` 标签、为 `<head>` 添加 `<link>` 标签

    ```javascript
    function shiftScriptInBody(content, scriptUrl) {
        let bodyIndex = content.indexOf('<body');

        if (bodyIndex === -1) {
            return content;
        }

        let htmlPart1Str = content.substring(0, bodyIndex);
        let bodyStr = content.substring(bodyIndex);

        const scriptIndex = bodyStr.indexOf('<script');

        let splitStr = '<script';

        if (scriptIndex === -1) {
            splitStr = '</body>';
        }

        const tempIndex = bodyStr.indexOf(splitStr);

        if (tempIndex !== -1) {
            bodyStr = `${bodyStr.substring(0, tempIndex)}${scriptUrl}\n${bodyStr.substring(tempIndex)}`;
        }

        return htmlPart1Str + bodyStr;
    }

    function pushScriptInBody(content, scriptUrl) {
        let bodyIndex = content.indexOf('</body>');

        if (bodyIndex === -1) {
            return content;
        }

        const resultStr = content.replace('</body>', `${scriptUrl}</body>`)

        return resultStr;
    }

    function pushCssLinkInHead(content, cssUrl) {
        let bodyIndex = content.indexOf('</head>');

        if (bodyIndex === -1) {
            return content;
        }

        const resultStr = content.replace('</head>', `${cssUrl}</head>`)

        return resultStr;
    }
    ```

+   删除 html 中的注释

    ```javascript
    function removeComment(content) {
        const commentReg = /\<\!\-\-[\s\S]+?\-\-\>/i;
        return content.split(commentReg).join('');
    }
    ```

+   删除 html 中无用的 script 标签

    ```javascript
    function removeUselessDotScript(content) {
        let result = content;

        const scriptReg = /\<script[\s\S]+?\<\/script\>/g;

        const arr = result.match(scriptReg);

        if (arr) {
            arr.forEach((item) => {
                // 删除 jquery
                if (/\<script[\s\S]+?src\=[\s\S]+?jquery[\s\S]+?\<\/script\>/.test(item)) {
                    result = result.split(item).join('');
                }

                // 删除 zepto
                if (/\<script[\s\S]+?src\=[\s\S]+?zepto[\s\S]+?\<\/script\>/.test(item)) {
                    result = result.split(item).join('');
                }
            });
        }

        return result;
    }
    ```

+   文件监听与操作

    ```javascript
    const chokidar = require('chokidar');

    function watchFiles(fileChangeCallback) {
        // 处理监听
        const chokidarWatcher = chokidar.watch('.', {
            persistent: true,
            alwaysStat: true,
            interval: 300,
            ignored: /(\.git)|(\.ds_store)|(node_modules)/i,
        });

        // listeners
        chokidarWatcher.on('ready', () => {
            chokidarWatcher.on('add', (file) => {
                console.log('add: ', file);
                fileChangeCallback(file);
            });
            chokidarWatcher.on('change', (file) => {
                console.log('change: ', file);
                fileChangeCallback(file);
            });
            chokidarWatcher.on('unlink', (file) => {
                console.log('unlink: ', file);
                fileChangeCallback(file);
            });
            chokidarWatcher.on('unlinkDir', (file) => {
                console.log('unlinkDir: ', file);
                fileChangeCallback(file);
            });
        });

        return chokidarWatcher;
    }

    const writeMarkdown = (filepath) => {
        // if (!/\.md$/.test(filepath)) {
        //    return;
        // }
        console.log(filepath);
    };

    watchFiles(writeMarkdown);
    ```

+   目录压缩

    ```bash
    npm i zip-dir
    ```