# Origin
最早的 encoding 源自相同 OS 的不同系统之间的拨号交互(dialup communication)，例如 UNIX 的 uuencode，BinHex 的 TRS-80 (后来修改后作为 Macintosh 的交互规则)

# ASCII
8 个二进制位可以组合出 256种 状态，形成一个字节（byte），从 00000000 到 11111111。ASCII 码共规定了 128 个符号，使用后面的 7 位，第 1 位统一规定是 0。
美国规定的英语使用 128 个编码能够满足需求，后来欧洲决定利用闲置最高位表达各自语言的一些字符。亚洲很多国家的字符数远超过这个容量，于是渐渐出现了后来的 unicode。

# Unicode
Unicode 是一个囊括了世界上所有字符的字符集，其中每一个字符都对应有唯一的编码值（code point），然而它并不是编码格式，仅仅是字符集。
```python
>>> a = u"你好"
>>> a.encode('gbk')
b'\xc4\xe3\xba\xc3'
>>> a.encode('utf8')
b'\xe4\xbd\xa0\xe5\xa5\xbd'
```

# MIME
全称是 Multipurpose Internet Mail Extensions，主要为 SMTP 服务，后续也为 HTTP 服务，MIME 存在于 web transmission 的 header 中，header 包括 MIME-Version, Content-Type, Content-Disposition, Content-Transfer-Encoding 等。
Encoding 的方法包括 7bit, quoted-printable, base64, 8bit, binary 等。
## Base64
### basic
* Base64 源自 MIME content transfer encoding
* 名字中的 64 代表转换基数（radix-64，例如 decimal 的 radix 是 10，0-9），不同版本的 base64 都包含 A-Z, a-z, 0-9 这 62 个基数，另两个会变化，RFC(Request for Comments) 2045 另两个是 "+" 和 "/"，RFC 3501 另两个是 "+" 和 ","
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190123124353.png)
* 每个 digit 代表 6-bits data，2 ^ 6 = 64 

> 基数要求既存在于大多数 encodings，又能够被打印，这样在信息传输时不会被修改。例如 A-Z, a-z, 0-9 这 62 个基数通常出现在 encodings 中。

Base64 能在所有支持 Base64 encoding 的系统间安全传输信息，一般用于 binary data encoding (例如图片)，也可以用于 ASCII, unicode 等的 encoding。

### rule
"Man" 经过 base64 encode 后变成 "TWFu"。每个 ASCII 是 8-bit binary values，3 个 ASCII 共 24-bit，每 6 个一组，转换为 base64 的一个 code，共 4 个。

![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190123125654.png)
当位数不足的时候，base64 用 "=" 来补全，例如 "Ma" 经过 base64 encode 后变成 "TWE="，"M" 经过 base64 encode 后变成 "TQ=="。
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190123130119.png)
![](https://raw.githubusercontent.com/Sisyphus235/markdownPic/master/20190123130500.png)

结论：
* 无 "=" 出现，说明被转换字符结尾是 3 个一组的
* 单个 "=" 出现，说明被转换字符结尾是 2 个一组的
* 双 "==" 出现，说明被转换字符结尾是 1 个一组的
* 相同字符在被转换字符不同位置时 encoding 可能不一致（变相实现加密功能）
* Base64 编码后的数据比原始数据略长，长度约为原来的 4/3
* 因为编码后的 +/= 字符，标准的 Base64 并不适合直接放在 URL 里传输，有一些 Base64 的变种，它们将 +/ 等符号转换为其他符号（如 _- ），这样才能安全的在 URL 中传输（Url Safe）

# 延伸阅读
* [Base64](https://en.wikipedia.org/wiki/Base64#MIME)
* [MIME](https://en.wikipedia.org/wiki/MIME#Content-Transfer-Encoding)
* [MIME笔记](http://www.ruanyifeng.com/blog/2008/06/mime.html)
* [The MIME Information Page](http://www.hunnysoft.com/mime/index.htm)
* [unicode 与 utf8](https://foofish.net/unicode_utf-8.html)
* [ASCII，Unicode 和 UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)