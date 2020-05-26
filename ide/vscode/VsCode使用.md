# VsCode使用

## 常用快捷键
| 快捷键           | 说明                |
| -----           | -----:              |
| Ctrl + Enter    | 当前行下插入一行     |
| Ctrl + /        | 行注释              |
| Shift + Alt + A | 区块注释            |
| Ctrl + P        | 查找并打开文件       |
|Shift + Alt + 左键| 列编辑       |
|||

## 修改快捷键
1. Manager --> Keyboard Shortcuts --> Open Keyboard Shortcuts Json，添加如下内容

```JSON

[
    { "key": "alt+/",  "command": "editor.action.triggerSuggest","when": "editorTextFocus" },
    { "key": "ctrl+d", "command": "editor.action.cutLines","when": "editorTextFocus" },
    { "key": "ctrl+alt+down","command": "editor.action.copyLinesDownAction", "when": "editorTextFocus" },
    { "key": "ctrl+alt+up", "command": "editor.action.copyLinesUpAction", "when": "editorTextFocus" },
    { "key": "ctrl+d","command": "editor.action.deleteLines","when": "editorTextFocus" },
    { "key": "f3","command": "editor.action.addSelectionToNextFindMatch","when": "editorFocus"},
    {
        "key": "alt+u",
        "command": "editor.action.transformToUppercase"
    },
    {
        "key": "alt+l",
        "command": "editor.action.transformToLowercase"
    },
    {
        "key": "alt+m",
        "command": "workbench.action.maximizeEditor"
    },
    {
        "key": "ctrl+k",
        "command": "workbench.action.terminal.clear",
        "when": "terminalFocus"
    }
]
```

## 自定义配置
1. Manager --> Settings --> Open Settings Json，添加如下内容
```JSON
{
    "update.channel": "none",
    "update.enableWindowsBackgroundUpdates": false,
    "update.showReleaseNotes": false,
    "files.autoSave": "onFocusChange",
    "editor.minimap.enabled": false,
    "workbench.startupEditor": "newUntitledFile"
}
```
## 制作workspace文件
1. 新建xxx.code-workspace文件
2. 填充以下内容
```JSON
{
	"folders": [
		{
			"path": "D:\\git_boco\\NFV\\ct5gc"
		},
        {
			"path": "D:\\git_boco\\NFV\\ct5gc_web"
		}
	]
}
```
3. File --> Open Workspace，选择xxx.code-workspace

## 调试NodeJs
1. Debug --> Open lanuch.json，添加或修改以下内容
```JSON
{
    "version": "0.2.0",
    "configurations": [    
        {
            "type": "node",
            "request": "attach",
            "name": "Attach to Process",
            "processId": "${command:PickProcess}"
        }
    ]
}
```
2. npm run dev 启动工程
3. 按F5快捷键，搜索 agent_worker，注意路径，选择，进入调试模型
4. 在代码中添加断点，进行调试
5. 目前只支持一个工程进行调试

## Markdown转PDF
1. 安装Markdown PDF插件
2. 在setting.json中追加配置项
```
"markdown-pdf.executablePath": "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe"
```
3. 打开md文件，右击，export pdf