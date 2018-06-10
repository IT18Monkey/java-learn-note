# Basic IO

1. 字节流（Byte Streams）

   `FileInputStream`，`FileOutputStream`

2. 字符流（Character Streams）

   `FileReader`,`FileWriter`, `InputStreamReader `,`  OutputStreamWriter`

3. 缓冲流(Buffered Streams)

到目前为止，我们看到的大多数示例都使用unbuffered I/O。这意味着每个读或写请求都由底层操作系统直接处理。这可以使程序的效率大大降低，因为每个这样的请求通常会触发磁盘访问、网络活动或其他相对昂贵的操作。

 `BufferedInputStream` , `BufferedOutputStream` ,`BufferedReader`, `BufferedWriter` 