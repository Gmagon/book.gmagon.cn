---
index: true
type: api
---

## 判断是否为目录
```
int is_directory(const char* path)
{
    struct stat path_stat;
    if (stat(path, &path_stat) != 0) return 0;
    return S_ISDIR(path_stat.st_mode);
}
```