---
title: Carbon
summary: Carbon 库使用须知
date: 2021-10-13T03:27:32.000Z
draft: false
---

## Carbon::parse()

>`Carbon::parse()` 第一个入参为*数值*类型时会**强制**使用 UTC 时区，*无论`date_default_timezone_set`结果*
>
>>解决方案
>>
>>`Carbon::createFromTimestamp($timestamp)`会使用`date_default_timezone_get()`的时区

[An Issue](https://github.com/briannesbitt/Carbon/issues/1905)

[Source Code](https://github.com/briannesbitt/Carbon/blob/master/src/Carbon/Traits/Creator.php)

```PHP
public function __construct($time = null, $tz = null)
{
    // ...
    if (is_numeric($time) && (!\is_string($time) || !preg_match('/^\d{1,14}$/', $time))) {
        $time = static::createFromTimestampUTC($time)->format('Y-m-d\TH:i:s.uP');
    }
    // ...
}
```
