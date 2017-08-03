---
index: true
type: api
---

## 判断是否为文件
```
int is_regular_file(const char* path)
{
    struct stat path_stat;
    if (stat(path, &path_stat) != 0) return 0;
    return S_ISREG(path_stat.st_mode);
}
```