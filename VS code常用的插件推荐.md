名称: Material Icon Theme

名称: Markdown Preview

名称: indent-rainbow

名称: Bracket Pair Colorizer 2

名称: TODO HIGHLIGHT

* 在插件->找到TODO HIGHLIGHT, 设置->扩展设置->TODO HIGHLIGHT Keywords->在setting.json中编辑,如下：

      {
      "editor.fontSize": 15,
      "window.zoomLevel": 0,
      "todo-tree.tree.showScanModeButton": false,
      "todohighlight.keywords": [
          {
              "text": "你的关键字:",
              "color": "#0000ff",
              "backgroundColor": "#ff0000",
          }
      ]
      }

* 在json文件中编辑,模板已经有了,只需添加{ }中的内容(这儿以关键字,字体颜色,背景色为例),保存即可。