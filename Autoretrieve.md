#autoretrieve#
#  一、简介  #
    autoretrieve是一个独立网关，提供了数据的发送服务，将file coin到数据发送客户端之间进行一个连接
# 二、代码理解 #
## Cid ##
1. 简介
    1. CID是一个用来描述的内容定址的标识符，采用hash将内容进行加密，结果即为地址。通过multiformats（默认的代码规范，协议），完成对格式的要求。
1. 内容:CIDV1(通用的版本)
`<cidv1> ::= <multibase-prefix><multicodec-cidv1><multicodec-content-type><multihash-content-address>`
    1. `<multibase-prefix>`：占1或2字节的multibase(编码规则)，可以将cids编译成各种基本类型。
    2. `<multicodec-cidv1>`：是一个编码器，这里表示CID的版本,用作升级需要
    3. `<multicodec-content-type>`：这是一个编码器，用来表示被转换为地址的内容的类型或者格式。
    4. `<multihash-content-address>`：这是一个哈希值，用来表示，内容经过哈希加密之后的哈希值。

1. 特征：
    1. 便捷：CIDs在通常情况下为尽可能小的二进制码，所以CIDs可以作为长路径的一个标志符，或者作为一个URl路径。
    2. 传输方便：CIDs在编码时采用的是multibase去选择最好的方便传输的编码方式。
    3. 多样：CIDs能够支持多种多样的任何格式的内容进行任意一种哈希加密
    4. 避免锁定：CIDs会避免去锁定过时，或者将要过时的操作
    5. 可升级：CIDs在编码时会编译出一个版本号，确保了CID格式的编码能够进行升级操作。

1. 易读的CID编码格式：`<hr-cid> ::= <hr-mbc> "-" <hr-cid-mc> "-" <hr-mc> "-" <hr-mh>`
1. 解码规则：
    1. 是否为字符串（ASCII/UTF-8）：
        1. 如果是46给字符长度大小，并且开头为Qm，这是CIDV0版本，将其按照 base58btc进行转换，跳转第2
        2. 否则，则将其转换为二进制
            1. 如果第一个字节为`0x12`，返回错误。CIDV0版本不会遵守multibase进行编码，机器会避免将CIDV18于CIDv0s版本进行混淆，所以返回错误
            2. 否则，得到一个二进制数据，进行2

    1. 为一个二进制的CID数据
        1. 如果长度为34字节并且开头为`0x12,0x20...`则此版本为CIDV0
        2. 否则将版本号加入到，cid编码的首位，CID的编码规则放在第二位，剩下的内容即为hash值

