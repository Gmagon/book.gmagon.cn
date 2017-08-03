---
index: true
type: api
---

## 获取文件名称，不带文件扩展名
```
const char* get_fileanme_no_ext(const char*filename)
{
    const char* _ext = get_filename_ext(filename);
    const char* _fileanme = get_filename(filename);

    if(strcmp(_ext, "") == 0) return _fileanme;

    int len = strlen(_fileanme) - strlen(_ext) -1;

    char* buffer = (char *)malloc(len);


    if(len > 0) {
    memcpy(buffer, _fileanme, len);
    }

    buffer[len] = 0;

    return buffer;
}
```