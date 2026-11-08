玻璃表面缺陷检测数据集
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9b6f80b914ff4596be63f2326f39c1a1.png)


类别：3类 'point_defect'点状缺陷,'liner_defect'划痕缺陷,'planar_defect'擦伤缺陷
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f98fa26f7ce940398c3b94257cac8eb0.png)

图片数量：8000张

train:val:test按照8:1:1比例划分

yolo格式数据集

 换yaml文件路径可直接训练
 ![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/93c4ccd10e6a4f978bc41c203f469d64.png)
、使用YOLOv8训练玻璃表面缺陷检测数据集，、步骤：安装依赖、准备数据集、配置YOLOv8、模型训练和评估、以及构建，GUI应用程序来展示检测结果。

以下是详细的指南和代码示例。

### 1. 安装依赖

首先确保你的环境中已经安装了必要的库。这里我们主要需要PyTorch和Ultralytics的YOLOv8实现。

```bash
pip install torch torchvision torchaudio  # 根据需要选择适合的版本
pip install ultralytics opencv-python
```

### 2. 准备数据集

假设你已经下载并解压了玻璃表面缺陷检测数据集，并且它包含了YOLO格式的标注文件。确保数据集结构如下：

```
glass_defects/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
└── labels/
    ├── train/
    ├── val/
    └── test/
```

创建一个`data.yaml`文件来定义数据集路径和类别信息。

```yaml
train: ./glass_defects/images/train/
val: ./glass_defects/images/val/

nc: 3  # 类别数量
names: ['point_defect', 'liner_defect', 'planar_defect']
```

### 3. 配置YOLOv8

#### 3.1 加载预训练模型或自定义模型

你可以选择加载YOLOv8的预训练模型开始训练，或者根据自己的需求定义模型架构。

```python
from ultralytics import YOLO

# 加载预训练的YOLOv8n模型
model = YOLO('yolov8n.yaml')  # 或者 'yolov8n.pt' 如果你想从预训练权重开始
```

### 4. 训练模型

接下来，使用定义好的`data.yaml`文件开始训练。

```python
# 开始训练
results = model.train(data='path/to/data.yaml', epochs=100, imgsz=640, batch=16)
```

- `epochs`: 设置训练轮数。
- `imgsz`: 输入图像的尺寸。
- `batch`: 每批次图像的数量。

### 5. 评估模型

训练完成后，可以在验证集上评估模型性能。

```python
# 在验证集上评估模型性能
metrics = model.val()
print(f"Validation mAP: {metrics.box.map}")
```

### 6. 构建GUI应用程序（可选）

提供一个简单的PyQt5 GUI示例，用于展示YOLOv8的检测结果。

首先，确保安装了PyQt5。

```bash
pip install PyQt5
```

然后，编写以下代码来构建GUI。

```python
import sys
from PyQt5.QtWidgets import QApplication, QLabel, QVBoxLayout, QWidget, QPushButton, QFileDialog
from PyQt5.QtGui import QPixmap, QImage
import cv2
import numpy as np
from ultralytics import YOLO

class AppDemo(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle('YOLOv8 Glass Defect Detection')
        self.setGeometry(100, 100, 800, 600)

        self.image_label = QLabel(self)
        self.button = QPushButton("Load Image", self)
        self.button.clicked.connect(self.load_image)

        vbox = QVBoxLayout()
        vbox.addWidget(self.image_label)
        vbox.addWidget(self.button)
        self.setLayout(vbox)

        self.model = YOLO('path/to/your/best.pt')  # 使用训练好的模型

    def load_image(self):
        fname, _ = QFileDialog.getOpenFileName(self, 'Open file', 'c:\\', "Image files (*.jpg *.png)")
        if fname:
            self.show_image(fname)

    def show_image(self, image_path):
        results = self.model.predict(source=image_path)
        img = cv2.imread(image_path)
        for r in results:
            for box in r.boxes.xyxy:
                x1, y1, x2, y2 = map(int, box)
                cv2.rectangle(img, (x1, y1), (x2, y2), (0, 255, 0), 2)

        height, width, channel = img.shape
        bytes_per_line = 3 * width
        q_img = QImage(img.data, width, height, bytes_per_line, QImage.Format_RGB888).rgbSwapped()
        pixmap = QPixmap.fromImage(q_img)
        self.image_label.setPixmap(pixmap)

if __name__ == '__main__':
    app = QApplication(sys.argv)
    demo = AppDemo()
    demo.show()
    sys.exit(app.exec_())
```

### 总结

上述步骤涵盖了如何使用YOLOv8在玻璃表面缺陷检测数据集上进行目标检测的整个过程，包括安装依赖项、准备数据集、配置YOLOv8、训练和评估模型以及构建一个简单的GUI应用程序展示检测结果。请根据实际需求调整参数和代码细节。如果有任何问题或需要进一步的帮助，请随时提问！

### 运行代码

确保所有路径都是正确的，并根据需要调整参数。以下是完整的运行步骤：

1. **安装依赖**：
   ```bash
   pip install torch torchvision torchaudio
   pip install ultralytics opencv-python
   pip install PyQt5
   ```

2. **准备数据集**：
   - 确保数据集结构正确。
   - 创建`data.yaml`文件。

3. **配置YOLOv8**：
   ```python
   from ultralytics import YOLO

   model = YOLO('yolov8n.yaml')  # 或者 'yolov8n.pt'
   ```

4. **训练模型**：
   ```python
   results = model.train(data='path/to/data.yaml', epochs=100, imgsz=640, batch=16)
   ```

5. **评估模型**：
   ```python
   metrics = model.val()
   print(f"Validation mAP: {metrics.box.map}")
   ```

6. **构建GUI应用程序**：
   ```python
   # 上述提供的GUI代码
   ```

希望这些步骤和代码能帮助你顺利完成项目！如果有任何问题，请随时提问。
