---
title: "The Chromium Chronicle #24: StrongAlias, IdType, and TokenType"
date: 2023-01-12T20:50:33+08:00
draft: false
description: "使用StrongAlias定义类型，避免基础数据类型相同而意义不同的参数赋值时导致的错误"
tags: ["The Chromium Chronicle"]
---

|   标题   | The Chromium Chronicle #24: StrongAlias, IdType, and TokenType |
| :------: | ------------------------------------------------------------ |
|   作者   | Łukasz Anforowicz in Bellevue, WA                            |
| 发表时间 | August, 2021                                                 |
|   地址   | https://developer.chrome.com/blog/chromium-chronicle-24/     |



你能发现下面代码中的错误吗？当你在Chroumium上进行代码审查的时候，你还能发现这个错误吗？比如[这里](https://chromium.googlesource.com/chromium/src/+/94fe07eb3484daee0f7b091aaee3f8511c7a34fa)和[这里](https://chromium.googlesource.com/chromium/src/+/7761de92d068807a1ea2632c90506fdc3b8a2c9d)。

```c++
Token CreateToken(int command_data, int buffer_id);
...
auto token = CreateToken(GetCommandBufferId(), GetCommandData());
```

有时候相同的数据类型可能代表着完全不兼容的数据，特别是像integers或者strings等一些基础数据类型。上面的例子生动的说明了这种错误是怎么发生的。还好的是，Chromium的`//base`引入了一种方法可以创建明确清晰的不同类型：

```c++
#include "base/types/strong_alias.h"

// The first template argument of StrongAlias is a "tag" type.
// The "tag" type is used to distinguish between different
// StrongAlias types.
using CommandData = base::StrongAlias<class CommandDataTag, int>;
using CommandBufferId = base::StrongAlias<class CommandBufferIdTag, int>;

Token CreateToken(CommandData command_data, CommandBufferId buffer_id);
```

Separate types improve readability. Additionally, `StrongAlias` catches type mix-ups at compile time:

独立的类型明显地提高了可读性。另外`StrongAlias`也可以在编译器捕获不同的类型混用的错误：

```shell
test.cc:456:16: error: no matching function for call to 'CreateToken'
  auto token = CreateToken(GetCommandBufferId(), GetCommandData());
               ^~~~~~~~~~~
test.cc:123:7: note: candidate function not viable: no known conversion from
'StrongAlias<class CommandBufferIdTag, [...]>' to
'StrongAlias<class CommandDataTag, [...]>' for 1st argument
Token CreateToken(CommandData command_data, CommandBufferId buffer_id);
      ^
```

由于他们有不同的`tag`类型，所以编译器会将这两种类型当做是不同的不兼容的类型。`StrongAlias`可以将任意的类型当作为`tag`类型，甚至是使用不存在实际定义的原位前向声明类都可以。上面的例子中的`class CommandDataTag`和`class CommandBufferIdTag`实际上并没有定义。

在以后的编程中，考虑使用下面的替代类型而不是原始的基础数据类型（比如bool，int，string等）：

* 使用`base::IdType32<TagType>`而不是`int32_t`
* 使用`base::TokenType<TagType>`而不是`base::UnguessableToken`
* 使用`enum class`而不是`bool`(比如，`kForReload`，`kNotForReload`而不是`true`和`false`)
* 使用`base::StrongAlias<TagType, SomeWrappedType>`替换其他的基础数据类型
