---
title: "Apache commons-lang3 StringUtils详解"
date: 2024-10-17
---

```java
//str="幽默就像WiFi，看不见摸不着，但没有它，生活就断了网。"
//abbrevMarker="......"
//offset=8
//maxWidth=13
//abbrevMarkerLength=6
//strLen=28
//minAbbrevWidthOffset=13
public static String abbreviate(final String str, final String abbrevMarker, int offset, final int maxWidth) {
    //省略符号为空，字符串从0索引开始，到返回结果长度结束，返回截取的字符串
    if (isNotEmpty(str) && EMPTY.equals(abbrevMarker) && maxWidth > 0) {
        return substring(str, 0, maxWidth);
    }

    //字符串或省略符号为空，返回原字符串
    if (isAnyEmpty(str, abbrevMarker)) {
        return str;
    }

    //省略符号长度
    final int abbrevMarkerLength = abbrevMarker.length();
    //一个省略符号长度+1
    final int minAbbrevWidth = abbrevMarkerLength + 1;
    //两个省略符号长度的总和+1
    final int minAbbrevWidthOffset = abbrevMarkerLength + abbrevMarkerLength + 1;

    //结果内容不能全是省略符号，至少有一个字符，否则抛出异常
    if (maxWidth < minAbbrevWidth) {
        throw new IllegalArgumentException(String.format("Minimum abbreviation width is %d", minAbbrevWidth));
    }

    //字符串长度
    final int strLen = str.length();

    //字符串长度小于返回结果长度，返回原字符串
    if (strLen <= maxWidth) {
        return str;
    }

    //显示字符长度大于字符串长度，显示字符长度更改为字符串长度
    if (offset > strLen) {
        offset = strLen;
    }

    //字符串长度减去显示字符长度小于返回结果长度及减去省略符号长度，显示字符长度更改为长度减去(返回结果长度减去省略符号长度)
    if (strLen - offset < maxWidth - abbrevMarkerLength) {
        offset = strLen - (maxWidth - abbrevMarkerLength);
    }

    //显示字符长度小于等于省略字符长度加1，截取字符串，从索引值0至(返回结果长度减去省略符号长度)，追加省略符号字符串
    if (offset <= abbrevMarkerLength + 1) {
        return str.substring(0, maxWidth - abbrevMarkerLength) + abbrevMarker;
    }
    //返回结果长度小于(两个省略符号长度加1)
    if (maxWidth < minAbbrevWidthOffset) {
        throw new IllegalArgumentException(String.format("Minimum abbreviation width with offset is %d", minAbbrevWidthOffset));
    }
    //(显示字符长度加返回结果长度减去省略符号长度)小于字符串长度，返回，前置省略符号加调用重载方法
    if (offset + maxWidth - abbrevMarkerLength < strLen) {
        //递归入参：
            //"，看不见摸不着，但没有它，生活就断了网。"，
            //"......"，
            //7(13-6)
        return abbrevMarker + abbreviate(str.substring(offset), abbrevMarker, maxWidth - abbrevMarkerLength);
    }
    //返回，前置省略符号加截取字符串(字符串长度减去(返回结果长度减去省略符号长度))
    return abbrevMarker + str.substring(strLen - (maxWidth - abbrevMarkerLength));
}
```