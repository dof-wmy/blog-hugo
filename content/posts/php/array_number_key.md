---
title: "纯数字字符串 作为 PHP 索引数组的键"
summary: 纯数字字符串（数字为整型且不超过整型最大值）作为索引数组的键时，会自动被转为整型
date: 2021-10-19T10:55:49+08:00
draft: true
---

纯数字字符串（数字为整型且不超过整型最大值）作为索引数组的键时，会自动被转为整型

通过查看 Laravel 源码发现

多态关联查询问题（int string 被 转为 int，导致数据库索引无效引起慢查询）

Illuminate\Database\Eloquent\Relations\MorphTo@buildDictionary 写入数据
Illuminate\Database\Eloquent\Relations\MorphTo@gatherKeysByType array_keys 取出的数据被转换

重写数据库模型 newMorphTo 方法

```PHP
/**
 * Instantiate a new MorphTo relationship.
 *
 * @param  \Illuminate\Database\Eloquent\Builder  $query
 * @param  \Illuminate\Database\Eloquent\Model  $parent
 * @param  string  $foreignKey
 * @param  string  $ownerKey
 * @param  string  $type
 * @param  string  $relation
 * @return MorphToModel
 */
protected function newMorphTo(Builder $query, Model $parent, $foreignKey, $ownerKey, $type, $relation)
{
    return new MorphToModel($query, $parent, $foreignKey, $ownerKey, $type, $relation);
}
```

```PHP
<?php

namespace App\Model;

use App\Model\Finance\ProviderWithdraw;
use App\Model\Order\Order;
use Illuminate\Database\Eloquent\Relations\MorphTo;

class MorphToModel extends MorphTo
{
    protected function gatherKeysByType($type)
    {
        $values = array_keys($this->dictionary[$type]);
        if (in_array($type, [
            Order::class,
            ProviderWithdraw::class,
        ])) {
            $values = array_map('strval', $values);
        }

        return $values;
    }
}
```
