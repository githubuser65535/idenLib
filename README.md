惡意程式都把程式碼靜態編譯,在IDA/x64dbg分析的時候感覺很不方便，比方說sprintf_s, scanf_s

此工具可以自動掃瞄該段程式碼跟你系統中的函數庫去比對程式碼哪一段吻合,就可以將靜態程式碼反推得知是哪一個函數靜態被編譯器link上去

至於掃描的手段就是AOB搜尋法,把opcode給記錄下來,遇到data或是offset就跳過不當特徵處理

# idenLib - Library Function Identification

When analyzing malware or 3rd party software, it's challenging to identify statically linked libraries and to understand what a function from the library is doing.

[`idenLib.exe`](https://github.com/secrary/idenLib) is a tool for generating library signatures from `.lib`/`.obj` files.

[`idenLib.dp32`/`idenLib.dp64`](https://github.com/secrary/idenLibX) is a [`x32dbg`/`x64dbg`](https://x64dbg.com) plugin to identify library functions.

[`idenLib.py`](https://github.com/secrary/IDA-scripts/tree/master/idenLib) is an [`IDA Pro`](https://www.hex-rays.com/products/ida/index.shtml) plugin to identify library functions.


##### Any feedback is greatly appreciated: [@_qaz_qaz](https://twitter.com/_qaz_qaz)

## How does idenLib.exe generate signatures?

1. Parses input file(`.lib`/`.obj` file) to get a list of function addresses and function names.
2. Gets the last opcode from each instruction

![sig](https://user-images.githubusercontent.com/16405698/52433535-35442500-2b05-11e9-92a2-7ed0dfb319ab.png)

3. Compresses the signature with [zstd](https://github.com/facebook/zstd)

4. Saves the signature under the `SymEx` directory, if the input filename is `zlib.lib`, the output will be `zlib.lib.sig` or `zlib.lib.sig64`,
if `zlib.lib.sig(64)` already exists under the `SymEx` directory from a previous execution or from the previous version of the library, the next execution will append different signatures.
If you execute `idenLib.exe` several times with different version of the `.lib` file, the `.sig`/`sig64` file will include all unique function signatures.

Inside of a signature (it's compressed):
![signature](https://user-images.githubusercontent.com/16405698/52490971-e9a18200-2bbd-11e9-8d29-e85a71826c8f.png)

## Generating library signatures

![lib](https://user-images.githubusercontent.com/16405698/52433541-35dcbb80-2b05-11e9-918a-6d39afc5de91.gif)

## [`x32dbg`/`x64dbg`](https://x64dbg.com), [`IDA Pro`](https://www.hex-rays.com/products/ida/index.shtml) plugin usage:

1. Copy `SymEx` directory under `x32dbg`/`x64dbg`/`IDA Pro`'s main directory
2. Apply signatures:

[`x32dbg`/`x64dbg`](https://github.com/secrary/idenLibX):

![xdb](https://user-images.githubusercontent.com/16405698/52433536-35442500-2b05-11e9-990e-8d4889bfe1c6.gif)

[`IDA Pro`](https://github.com/secrary/IDA-scripts/tree/master/idenLib):

![ida_boost_2](https://user-images.githubusercontent.com/16405698/52433540-35dcbb80-2b05-11e9-9dd3-9bb44d678ea5.gif)

## NOTE:
Supports [`x86`](https://en.wikipedia.org/wiki/X86) and [`AMD64/x86-64`](https://en.wikipedia.org/wiki/X86-64) architectures.

## Useful links:
- Detailed information about [`C Run-Time Libraries (CRT)`](https://docs.microsoft.com/en-us/cpp/c-runtime-library/crt-library-features);

## Credits
- Disassembly by [Zydis](https://zydis.re)
- Compression by [zstd](https://github.com/facebook/zstd)
- Icon by [freepik](https://www.flaticon.com/authors/freepik)
