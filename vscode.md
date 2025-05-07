
## 如何在 VS Code 中粘贴图片到 Markdown
1. 配置路径：在 VS Code 的设置中，添加以下配置：
- Markdown › Copy Files: Destination
   ```json
   "${documentDirName}/images": "**/images/*.md"
   ```
