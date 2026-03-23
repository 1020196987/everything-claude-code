# Visa Document Translator

## Visa Document Translator
## 签证文件翻译器

Automatically translate visa application documents from images to professional English PDFs.
自动将签证申请文件从图片翻译成专业英文 PDF。

## Features

## Features
## 功能

- Automatic OCR: Tries multiple OCR methods (macOS Vision, EasyOCR, Tesseract)
  - 自动 OCR：尝试多种 OCR 方法（macOS Vision、EasyOCR、Tesseract）
- Bilingual PDF: Original image + professional English translation
  - 双语 PDF：原图 + 专业英文翻译
- Multi-language: Supports Chinese, and other languages
  - 多语言：支持中文和其他语言
- Professional Format: Suitable for official visa applications
  - 专业格式：适合官方签证申请
- Fully Automated: No manual intervention required
  - 全自动化：无需手动干预

## Supported Documents

## Supported Documents
## 支持的文件类型

- Bank deposit certificates (存款证明)
- Employment certificates (在职证明)
- Retirement certificates (退休证明)
- Income certificates (收入证明)
- Property certificates (房产证明)
- Business licenses (营业执照)
- ID cards and passports

## Usage

## Usage
## 使用方法

```bash
/visa-doc-translate <image-file>
```

### Examples

### Examples
### 示例

```bash
/visa-doc-translate RetirementCertificate.PNG
/visa-doc-translate BankStatement.HEIC
/visa-doc-translate EmploymentLetter.jpg
```

## Output

## Output
## 输出

Creates `<filename>_Translated.pdf` with:
创建 `<filename>_Translated.pdf`，包含：

- **Page 1**: Original document image (centered, A4 size)
  - **第 1 页**：原始文件图片（居中，A4 大小）
- **Page 2**: Professional English translation
  - **第 2 页**：专业英文翻译

## Requirements

## Requirements
## 需求

### Python Libraries

### Python Libraries
### Python 库

```bash
pip install pillow reportlab
```

### OCR (one of the following)

### OCR (one of the following)
### OCR（任选其一）

**macOS (recommended):**
**macOS（推荐）：**

```bash
pip install pyobjc-framework-Vision pyobjc-framework-Quartz
```

**Cross-platform:**
**跨平台：**

```bash
pip install easyocr
```

**Tesseract:**
**Tesseract：**

```bash
brew install tesseract tesseract-lang
pip install pytesseract
```

## How It Works

## How It Works
## 工作原理

1. Converts HEIC to PNG if needed
   1. 如需要，将 HEIC 转换为 PNG
2. Checks and applies EXIF rotation
   2. 检查并应用 EXIF 旋转
3. Extracts text using available OCR method
   3. 使用可用的 OCR 方法提取文本
4. Translates to professional English
   4. 翻译成专业英文
5. Generates bilingual PDF
   5. 生成双语 PDF

## Perfect For

## Perfect For
## 适用于

- Australia visa applications
  - 澳大利亚签证申请
- USA visa applications
  - 美国签证申请
- Canada visa applications
  - 加拿大签证申请
- UK visa applications
  - 英国签证申请
- EU visa applications
  - 欧盟签证申请

## License

## License
## 许可证

MIT
