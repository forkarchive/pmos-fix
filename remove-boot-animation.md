根据你的输出，`mkinitfs` 在生成 `initramfs` 时失败，原因是它尝试加载 **`/usr/share/pbsplash/pmos-logo-text.svg`** 文件，而该文件已经被删除。这表明 `pbsplash` 的文件路径仍然被引用于 **`mkinitfs` 配置文件中**。

要解决这个问题，你需要从 `mkinitfs` 的配置中移除对这些文件的引用。

---

### **解决方法：移除 `pbsplash` 配置引用**

#### **1. 修改 `mkinitfs` 配置**
`mkinitfs` 使用文件列表来决定哪些资源被包含在 `initramfs` 中。我们需要移除 `/usr/share/pbsplash/pmos-logo-text.svg` 的引用。

1. **找到并编辑配置文件**
   - 打开 `/usr/share/mkinitfs/files/30-postmarketos-bootsplash.files`：
     ```bash
     sudo nano /usr/share/mkinitfs/files/30-postmarketos-bootsplash.files
     ```

2. **注释掉或删除与 `pbsplash` 相关的行**
   - 找到类似以下的行：
     ```plaintext
     /usr/share/pbsplash/pmos-logo-text.svg
     /usr/share/pbsplash/pmos-logo-text-epaper.svg
     /usr/share/pbsplash/OpenSans-Regular.svg
     ```
   - 将这些行注释掉（在行前加 `#`）或删除：
     ```plaintext
     # /usr/share/pbsplash/pmos-logo-text.svg
     # /usr/share/pbsplash/pmos-logo-text-epaper.svg
     # /usr/share/pbsplash/OpenSans-Regular.svg
     ```

3. **保存并退出**
   - 按 `Ctrl+O` 保存文件，按 `Ctrl+X` 退出编辑器。

---

#### **2. 重新生成 `initramfs`**
现在重新运行 `mkinitfs`，应该能够成功生成：
```bash
sudo mkinitfs
```

---

#### **3. 验证和重启设备**
1. 确保 `mkinitfs` 成功完成。
2. 重启设备：
   ```bash
   sudo reboot
   ```

---

### **进一步检查**

如果还有其他文件引用问题，可以通过以下方式检查所有与 `pbsplash` 相关的配置：

1. **全局查找 `pbsplash` 的引用**
   - 使用 `grep` 搜索配置目录中所有引用：
     ```bash
     grep -r "pbsplash" /usr/share/mkinitfs /etc/mkinitfs
     ```
   - 将搜索到的文件逐一检查并移除相关引用。

2. **检查 `pbsplash` 的残留配置**
   - 查看是否有其他与 `pbsplash` 相关的文件：
     ```bash
     sudo find / -name "*pbsplash*"
     ```
   - 删除不需要的文件。

---

### **总结**

通过移除 `mkinitfs` 配置中的 `pbsplash` 文件引用，你可以成功生成新的 `initramfs`，并彻底禁用启动动画。完成这些操作后，设备启动过程将显示完整的内核日志和 OpenRC 输出。如果还有其他问题，请提供进一步的输出信息，我会帮助你解决！ 😄
