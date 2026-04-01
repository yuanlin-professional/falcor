# RGLFile 源码文档
> 路径: `Source/Falcor/Scene/Material/RGLFile.h` + `RGLFile.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Material

## 功能概述
实现了 RGL BRDF 数据库文件的读写功能。RGL 文件采用 "tensor_file" 格式，包含多个命名字段（theta_i、phi_i、sigma、ndf、vndf、rgb、luminance 等），用于描述基于自适应参数化的测量 BRDF 数据。

## 类与接口

### `RGLFile`
- **继承**: 无
- **职责**: 解析和验证 RGL BRDF 文件格式

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `RGLFile(ifstream& in)` | 从输入流加载 RGL 文件 |
| `void saveFile(ofstream& out) const` | 保存 RGL 文件 |
| `const MeasurementData& data() const` | 获取测量数据 |
| `void addField(const string& name, FieldType type, const vector<uint32_t>& shape, const void* data)` | 添加字段 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mMeasurement` | `MeasurementData` | 渲染所需的测量数据集合 |

### `MeasurementData` 结构体
包含 thetaI、phiI、sigma、ndf、vndf、rgb、luminance 等字段指针，以及各向同性标志和描述文本。

## 依赖关系
### 被以下文件引用
- `RGLMaterial.cpp` - 加载 RGL BRDF 数据

## 实现细节
- 文件头为 "tensor_file"，版本号 1.0
- 支持 UInt8、UInt32、Float32 三种字段类型
- `validate()` 方法检查所有必需字段的类型、维度和形状一致性
- 数据块按 8 字节边界对齐
- 各向同性判定：phi_i 维度 <= 2 时为各向同性
