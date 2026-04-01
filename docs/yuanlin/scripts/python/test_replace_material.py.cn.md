# test_replace_material.py 脚本文档

> 路径: `scripts/python/test_replace_material.py`
> 类型: Python 脚本
> 模块: scripts/python

## 功能概述
该脚本演示了如何在运行时动态替换场景中的材质。它加载一个 Cornell Box 场景，然后将两个材质分别替换为 PBRT 漫反射材质（带纹理）和神经材质（Neural Material）。这展示了 Falcor 的材质替换 API 和对多种材质类型的支持。

## 主要功能/类

### `setup_renderpass(testbed)`
| 参数 | 类型 | 说明 |
|------|------|------|
| testbed | `falcor.Testbed` | 测试平台实例 |

**返回值**: 无

**功能细节**:
创建标准路径追踪渲染图（VBufferRT → PathTracer → AccumulatePass → 输出）。

### `main()`
主函数，执行以下步骤：
1. 设置日志级别为 Info
2. 创建 D3D12 设备（GPU 0）和带窗口的测试平台（1920x1080）
3. 加载 `cornell_box.pyscene` 场景
4. 创建替换材质：
   - `PBRTDiffuseMaterial`：带棋盘格纹理的漫反射材质
   - `NeuralMaterial`：从 JSON 文件加载的神经材质
5. 替换场景中索引 0 和 1 的材质
6. 运行渲染循环

## 使用示例
```bash
python test_replace_material.py
# 打开窗口显示替换材质后的 Cornell Box 场景
```

## 依赖关系
### 导入的模块
- `falcor` - Falcor 渲染框架

## 实现细节
- 使用 `falcor.Device(type=falcor.DeviceType.D3D12)` 显式指定 D3D12 后端
- `PBRTDiffuseMaterial` 通过 `load_texture` 加载棋盘格基础颜色纹理
- `NeuralMaterial` 从 JSON 配置文件加载（`neural/material_4.json`）
- 材质替换通过 `scene.replace_material(index, material)` 实现
- 替换材质后渲染管线自动适配新材质类型
- 使用 `testbed.run()` 进入交互式渲染循环
