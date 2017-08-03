---
index: true
type: api
---

## 获取文件名称，带扩展名
```
const char *get_filename_ext(const char *filename) {
    const char *dot = strrchr(filename, '.');
    if (!dot || dot == filename) return "";
    return dot + 1;
}



int main()
{
    // 以下输出扩展名tiff
    printf("%s\n", get_filename_ext("/c/c/c/c/c.test.tiff"));
    printf("%s\n", get_filename_ext("test.tiff"));
    printf("%s\n", get_filename_ext("test.blah.tiff"));


    // 以下不输出
    printf("%s\n", get_filename_ext(".tiff"));
    printf("%s\n", get_filename_ext("test."));
    printf("%s\n", get_filename_ext("test"));
    printf("%s\n", get_filename_ext("..."));

    return 0;
}
```