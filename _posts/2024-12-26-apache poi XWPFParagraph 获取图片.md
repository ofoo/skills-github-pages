---
title: "apache poi XWPFParagraph 获取图片"
date: 2024-12-26
---



在 Apache POI 中，`XWPFParagraph` 对象代表 Word 文档中的一个段落，但它本身并不直接包含图片。图片在 Word 文档中通常作为独立的对象嵌入，或者在表格单元格、页眉/页脚等位置中，而不是直接位于段落内。

然而，你可以遍历文档中的所有关系来找到嵌入的图片，即使它们不是直接作为段落内容的一部分。图片通常作为 `XWPFPicture` 对象存储在 `XWPFDocument` 的图片列表中，或者作为段落或表格单元格中的内联对象。

以下是一个更详细的示例，说明如何遍历 Word 文档中的段落和表格，并查找内联图片以及文档级别的图片：

```java
import org.apache.poi.openxml4j.opc.OPCPackage;
import org.apache.poi.xwpf.usermodel.*;
import org.apache.poi.util.Units;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.List;

public class WordImageExtractor {

    public static void main(String[] args) {
        String filePath = "path/to/your/document.docx";
        try (FileInputStream fis = new FileInputStream(filePath);
             XWPFDocument document = new XWPFDocument(OPCPackage.open(fis))) {

            // 提取文档级别的图片
            List<XWPFPictureData> allPictures = document.getAllPictures();
            for (int i = 0; i < allPictures.size(); i++) {
                XWPFPictureData picture = allPictures.get(i);
                byte[] data = picture.getData();
                String extension = getPictureExtension(picture.getPictureType());
                try (FileOutputStream fos = new FileOutputStream("document_level_image_" + (i + 1) + "." + extension)) {
                    fos.write(data);
                }
            }

            // 遍历段落和表格以查找内联图片
            for (XWPFParagraph paragraph : document.getParagraphs()) {
                List<XWPFRun> runs = paragraph.getRuns();
                for (XWPFRun run : runs) {
                    List<XWPFPicture> pictures = run.getEmbeddedPictures();
                    for (XWPFPicture picture : pictures) {
                        XWPFPictureData pictureData = picture.getPictureData();
                        byte[] data = pictureData.getData();
                        String extension = getPictureExtension(pictureData.getPictureType());
                        try (FileOutputStream fos = new FileOutputStream("inline_image_" + (allPictures.size() + pictures.indexOf(picture) + 1) + "." + extension)) {
                            fos.write(data);
                        }
                    }
                }
            }

            // 遍历表格中的图片（如果存在）
            for (XWPFTable table : document.getTables()) {
                for (XWPFTableRow row : table.getRows()) {
                    for (XWPFTableCell cell : row.getTableCells()) {
                        for (XWPFParagraph p : cell.getParagraphs()) {
                            List<XWPFRun> runs = p.getRuns();
                            for (XWPFRun run : runs) {
                                List<XWPFPicture> pictures = run.getEmbeddedPictures();
                                for (XWPFPicture picture : pictures) {
                                    XWPFPictureData pictureData = picture.getPictureData();
                                    byte[] data = pictureData.getData();
                                    String extension = getPictureExtension(pictureData.getPictureType());
                                    try (FileOutputStream fos = new FileOutputStream("table_inline_image_" + (allPictures.size() + pictures.indexOf(picture) + 1) + "." + extension)) {
                                        fos.write(data);
                                    }
                                }
                            }
                        }
                    }
                }
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static String getPictureExtension(int pictureType) {
        // 根据图片类型获取文件扩展名，与之前的示例相同
        // ...（省略了 switch-case 语句）
        return "bin"; // 默认情况
    }
}
```

在这个示例中，我们首先提取了文档级别的所有图片，然后遍历了文档中的每个段落和表格单元格，查找内联图片。注意，内联图片是通过 `XWPFRun` 对象的 `getEmbeddedPictures` 方法获取的。我们还为每个提取的图片生成了一个唯一的文件名，并将其保存到文件系统中。

请注意，这个示例中的文件路径和异常处理应该根据你的实际需求进行调整。此外，`getPictureExtension` 方法根据图片类型返回相应的文件扩展名，这与之前的示例相同。
