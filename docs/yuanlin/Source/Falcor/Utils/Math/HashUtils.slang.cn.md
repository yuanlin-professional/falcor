# HashUtils.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/HashUtils.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供 GPU 端的哈希函数和伪随机数生成工具，用于随机采样种子初始化等场景。

## 函数
| 函数签名 | 说明 |
|----------|------|
| `uint jenkinsHash(uint a)` | Robert Jenkins 的 32 位完美哈希函数（无碰撞） |
| `uint2 blockCipherTEA(uint v0, uint v1, uint iterations = 16)` | 基于 TEA（Tiny Encryption Algorithm）的 64 位分组密码，生成伪随机数对 |

## 依赖关系
### import
- 无外部依赖

## 实现细节

- Jenkins 哈希是完美哈希（无碰撞），适用于整数键的快速散列。
- TEA 分组密码使用 128 位固定密钥，默认 16 轮迭代，可用于为其他伪随机数生成器提供种子。
