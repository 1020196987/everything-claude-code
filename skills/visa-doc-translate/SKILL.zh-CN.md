---
name: visa-doc-translate
description: Translate visa application documents (images) to English and create a bilingual PDF with original and translation
description zh-CN: 将签证申请文件（图像）翻译成英文，并创建包含原文和译文的双语 PDF
---

You are helping translate visa application documents for visa applications.

你正在帮助翻译签证申请的签证申请文件。

## Instructions

## Instructions
## 说明

When the user provides an image file path, AUTOMATICALLY execute the following steps WITHOUT asking for confirmation:

当用户提供图像文件路径时，自动执行以下步骤，无需询问确认：

1. **Image Conversion**: If the file is HEIC, convert it to PNG using `sips -s format png <input> --out <output>`
   - **图像转换**：如果文件是 HEIC，使用 `sips -s format png <input> --out <output>` 转换为 PNG

2. **Image Rotation**:
   - **图像旋转**：
   - Check EXIF orientation data
     - 检查 EXIF 方向数据
   - Automatically rotate the image based on EXIF data
     - 根据 EXIF 数据自动旋转图像
   - If EXIF orientation is 6, rotate 90 degrees counterclockwise
     - 如果 EXIF 方向为 6，逆时针旋转 90 度
   - Apply additional rotation as needed (test 180 degrees if document appears upside down)
     - 根据需要应用额外旋转（如果文档颠倒则测试 180 度）

3. **OCR Text Extraction**:
   - **OCR 文本提取**：
   - Try multiple OCR methods automatically:
     - 自动尝试多种 OCR 方法：
     - macOS Vision framework (preferred for macOS)
       - macOS Vision 框架（macOS 首选）
     - EasyOCR (cross-platform, no tesseract required)
       - EasyOCR（跨平台，无需 tesseract）
     - Tesseract OCR (if available)
       - Tesseract OCR（如可用）
   - Extract all text information from the document
     - 从文档中提取所有文本信息
   - Identify document type (deposit certificate, employment certificate, retirement certificate, etc.)
     - 识别文档类型（存款证明、在职证明、退休证明等）

4. **Translation**:
   - **翻译**：
   - Translate all text content to English professionally
     - 专业地将所有文本内容翻译成英文
   - Maintain the original document structure and format
     - 保持原始文档结构和格式
   - Use professional terminology appropriate for visa applications
     - 使用适合签证申请的专业术语
   - Keep proper names in original language with English in parentheses
     - 将专有名词保留原语言并在括号中注明英文
   - For Chinese names, use pinyin format (e.g., WU Zhengye)
     - 对于中文姓名，使用拼音格式（如 WU Zhengye）
   - Preserve all numbers, dates, and amounts accurately
     - 准确保留所有数字、日期和金额

5. **PDF Generation**:
   - **PDF 生成**：
   - Create a Python script using PIL and reportlab libraries
     - 使用 PIL 和 reportlab 库创建 Python 脚本
   - Page 1: Display the rotated original image, centered and scaled to fit A4 page
     - 第 1 页：显示旋转后的原始图像，居中并缩放至 A4 页面
   - Page 2: Display the English translation with proper formatting:
     - 第 2 页：以适当格式显示英文翻译：
     - Title centered and bold
       - 标题居中加粗
     - Content left-aligned with appropriate spacing
       - 内容左对齐并适当间距
     - Professional layout suitable for official documents
       - 适合官方文件的职业布局
   - Add a note at the bottom: "This is a certified English translation of the original document"
     - 在底部添加注释："This is a certified English translation of the original document"
   - Execute the script to generate the PDF
     - 执行脚本生成 PDF

6. **Output**: Create a PDF file named `<original_filename>_Translated.pdf` in the same directory
   - **输出**：在与原文件相同的目录中创建名为 `<original_filename>_Translated.pdf` 的 PDF 文件

## Supported Documents

## Supported Documents
## 支持的文档

- Bank deposit certificates (存款证明)
- Income certificates (收入证明)
- Employment certificates (在职证明)
- Retirement certificates (退休证明)
- Property certificates (房产证明)
- Business licenses (营业执照)
- ID cards and passports
- Other official documents

## Technical Implementation

## Technical Implementation
## 技术实现

### OCR Methods (tried in order)

### OCR Methods (tried in order)
### OCR 方法（按顺序尝试）

1. **macOS Vision Framework** (macOS only):
   ```python
   import Vision
   from Foundation import NSURL
   ```

2. **EasyOCR** (cross-platform):
   ```bash
   pip install easyocr
   ```

3. **Tesseract OCR** (if available):
   ```bash
   brew install tesseract tesseract-lang
   pip install pytesseract
   ```

### Required Python Libraries

### Required Python Libraries
### 所需 Python 库

```bash
pip install pillow reportlab
```

For macOS Vision framework:
```bash
pip install pyobjc-framework-Vision pyobjc-framework-Quartz
```

## Important Guidelines

## Important Guidelines
## 重要指南

- DO NOT ask for user confirmation at each step
  - 不要在每个步骤询问用户确认
- Automatically determine the best rotation angle
  - 自动确定最佳旋转角度
- Try multiple OCR methods if one fails
  - 如果一种方法失败，尝试多种 OCR 方法
- Ensure all numbers, dates, and amounts are accurately translated
  - 确保所有数字、日期和金额准确翻译
- Use clean, professional formatting
  - 使用简洁、专业的格式
- Complete the entire process and report the final PDF location
  - 完成整个过程并报告最终 PDF 位置

## Example Usage

## Example Usage
## 示例用法

```bash
/visa-doc-translate RetirementCertificate.PNG
/visa-doc-translate BankStatement.HEIC
/visa-doc-translate EmploymentLetter.jpg
```

## Output Example

## Output Example
## 输出示例

The skill will:
该技能将：

1. Extract text using available OCR method
   - 使用可用的 OCR 方法提取文本
2. Translate to professional English
   - 翻译成专业英文
3. Generate `<filename>_Translated.pdf` with:
   - 生成 `<filename>_Translated.pdf`，包含：
   - Page 1: Original document image
     - 第 1 页：原始文档图像
   - Page 2: Professional English translation
     - 第 2 页：专业英文翻译

Perfect for visa applications to Australia, USA, Canada, UK, and other countries requiring translated documents.

非常适合澳大利亚、美国、加拿大、英国和其他需要翻译文件的国家/地区的签证申请。
