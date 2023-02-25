---
title: "XML：无效字符报错"
created: 2023-02-24 19:44
tags:
- xml
---

如果出现如下报错：

```
PCDATA invalid Char {value}
```

说明你的文件中出现无效字符，是因为文件中包含控制字符[^1]（非打印字符），`value` 就是对应的控制字符的十进制表示。

如果只是想替换掉[^2]，执行下面命令：

```bash
sed 's/[\x00-\x08\x0b-\x0c\x0e-\x1f]//g' yourfile
```

如果想要知道替换是的哪个控制字符：

```bash
sed -E 's/\x00/[NUL]/g;s/\x01/[SOH]/g;s/\x02/[STX]/g;s/\x03/[ETX]/g;s/\x04/[EOT]/g;s/\x05/[ENQ]/g;s/\x06/[ACK]/g;s/\x07/[BEL]/g;s/\x08/[BS]/g ;s/\x0A/[LF]/g;s/\x0B/[VT]/g;s/\x0C/[FF]/g;s/\x0D/[CR]/g;s/\x0E/[SO]/g;s/\x0F/[SI]/g;s/\x10/[DLE]/g; s/\x11/[DC1]/g;s/\x12/[DC2]/g;s/\x13/[DC3]/g;s/\x14/[DC4]/g;s/\x15/[NAK]/g;s/\x16/[SYN]/g;s/\x17/[ETB]/g;s/\x18/[CAN]/g;s/\x19/[EM]/g; s/\x1A/[SUB]/g; s/\x1B/[ESC]/g;s/\x1C/[FS]/g;s/\x1D/[GS]/g;s/\x1E/[RS]/g;s/\x1F/[US]/g;s/\x7F/[DEL]/g' yourfile
```

[^1]: [控制字符 - 维基百科，自由的百科全书](https://zh.wikipedia.org/zh-hans/%E6%8E%A7%E5%88%B6%E5%AD%97%E7%AC%A6)
[^2]: [XML 解析中，如何排除控制字符_coreyhsu2020的博客-CSDN博客](https://blog.csdn.net/jayxujia123/article/details/5990213)