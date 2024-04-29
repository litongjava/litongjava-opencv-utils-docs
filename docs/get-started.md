# 快速入门

## 概述

`litongjava-opencv-utils` 是一个基于 OpenCV 的 Java 工具库，旨在提供一系列便利的功能，帮助开发者在 Java 应用程序中轻松实现图像处理和计算机视觉任务。此库封装了多种图像操作的方法，包括图像的读取、保存、形状识别、颜色提取和更多复杂的图像分析功能。

## 添加库

litongjava-opencv-utils 已经推送到 maven center,在项目中直接导入即可使用

### maven

```xml
<dependencies>
    <!-- litongjava-opencv-utils dependency -->
    <dependency>
        <groupId>com.litongjava</groupId>
        <artifactId>litongjava-opencv-utils</artifactId>
        <version>1.0</version>
        <exclusions>
            <!-- Excluding logback-classic from the dependency -->
            <exclusion>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-classic</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>

```

### gradle

```
implementation("com.litongjava:litongjava-opencv-utils:1.0")
```

你可以可以排除 logback-classic

```
implementation("com.litongjava:litongjava-opencv-utils:1.0") {
  exclude group: 'ch.qos.logback', module: "logback-classic"
}
```

## 使用

### ColorDivisionUtils

#### 简介

`ColorDivisionUtils` 类提供了颜色分割的功能，主要用于在 HSV 色彩空间中基于颜色范围进行图像分割。该工具类可以识别并分割指定的颜色区域，特别适用于图像处理和计算机视觉项目中的颜色检测。

#### 使用示例

以下是如何使用 `ColorDivisionUtils` 类进行颜色分割的示例：

```java
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.core.Scalar;
import org.opencv.imgcodecs.Imgcodecs;
import com.litongjava.opencv.utils.ColorDivisionUtils;

public class Example {
    static {
        System.loadLibrary(Core.NATIVE_LIBRARY_NAME);
    }

    public static void main(String[] args) {
        Mat srcImage = Imgcodecs.imread("path/to/image.jpg");
        Mat hsvImage = new Mat();
        Imgproc.cvtColor(srcImage, hsvImage, Imgproc.COLOR_BGR2HSV);

        Scalar lowerBound = new Scalar(0, 70, 50);
        Scalar upperBound = new Scalar(10, 255, 255);
        Mat mask = ColorDivisionUtils.colorDivision(hsvImage, lowerBound, upperBound);

        Imgcodecs.imwrite("path/to/output.jpg", mask);
    }
}
```

#### 方法说明

##### `public static Mat colorDivision(Mat hsv, Scalar lowerb, Scalar upperb)`

对 HSV 图像进行颜色分割。

###### 参数

- `Mat hsv`: 输入的 HSV 格式图像。
- `Scalar lowerb`: 颜色分割的下界。
- `Scalar upperb`: 颜色分割的上界。

###### 返回值

- `Mat`: 表示分割后的二值化图像。

### ContoursUtils

#### 简介

`ContoursUtils` 类提供了一系列方法，用于处理和分析图像中的轮廓。它能够从图像中识别和提取特定形状的轮廓，例如圆形或接近圆形的区域。这些功能在图像处理和计算机视觉项目中非常有用，尤其是在进行形状检测和分类时。

#### 使用示例

以下是如何使用 `ContoursUtils` 类的两个主要功能的示例：

```java
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.core.MatOfPoint;
import org.opencv.imgcodecs.Imgcodecs;
import org.opencv.imgproc.Imgproc;
import java.util.ArrayList;
import java.util.List;
import com.litongjava.opencv.utils.ContoursUtils;

public class Example {
    static {
        System.loadLibrary(Core.NATIVE_LIBRARY_NAME);
    }

    public static void main(String[] args) {
        Mat srcImage = Imgcodecs.imread("path/to/image.jpg");
        Mat grayImage = new Mat();
        Imgproc.cvtColor(srcImage, grayImage, Imgproc.COLOR_BGR2GRAY);
        Mat blurredImage = new Mat();
        Imgproc.GaussianBlur(grayImage, blurredImage, new org.opencv.core.Size(5, 5), 0);
        Mat edgedImage = new Mat();
        Imgproc.Canny(blurredImage, edgedImage, 100, 200);
        List<MatOfPoint> contours = new ArrayList<>();
        Mat hierarchy = new Mat();
        Imgproc.findContours(edgedImage, contours, hierarchy, Imgproc.RETR_EXTERNAL, Imgproc.CHAIN_APPROX_SIMPLE);

        Mat resultImage = ContoursUtils.extraCircleArea(srcImage, contours);
        Imgcodecs.imwrite("path/to/output.jpg", resultImage);
    }
}
```

#### 方法说明

##### `public static Mat extraCircleArea(Mat src, List<MatOfPoint> contours)`

从图像中提取接近圆形的区域。

###### 参数

- `Mat src`: 输入的原始图像。
- `List<MatOfPoint> contours`: 图像中检测到的轮廓列表。

###### 返回值

- `Mat`: 提取出的圆形区域的掩码图像，用于进一步处理或显示。

##### `public static List<MatOfPoint> extraCircleContours(Mat src, List<MatOfPoint> contours)`

从给定的轮廓列表中筛选出接近圆形的轮廓。

###### 参数

- `Mat src`: 输入的原始图像。
- `List<MatOfPoint> contours`: 图像中检测到的轮廓列表。

###### 返回值

- `List<MatOfPoint>`: 筛选后的接近圆形的轮廓列表。

##### `public static boolean isCircle(List<MatOfPoint> contours)`

判断给定的轮廓列表中是否存在接近圆形的轮廓。

###### 参数

- `List<MatOfPoint> contours`: 图像中检测到的轮廓列表。

###### 返回值

- `boolean`: 如果存在接近圆形的轮廓，则返回 `true`；否则返回 `false`。

### FileUploadUtils

#### 简介

`FileUploadUtils` 类提供了一个简便的方法来上传文件到服务器。它支持通过 HTTP POST 方法上传文件，并处理文件的多部分格式编码。这个工具类非常适合需要在客户端和服务器之间传输文件的应用程序。

#### 使用示例

以下是如何使用 `FileUploadUtils` 类上传文件到服务器的示例：

```java
import java.io.File;
import com.litongjava.opencv.utils.FileUploadUtils;

public class Example {
    public static void main(String[] args) {
        File file = new File("path/to/your/file.jpg");
        String requestURL = "http://yourserver.com/upload";
        String response = FileUploadUtils.uploadFile(file, requestURL);
        System.out.println("Server response: " + response);
    }
}
```

#### 方法说明

##### `public static String uploadFile(File file, String requestURL)`

上传文件到服务器。

###### 参数

- `File file`: 需要上传的文件。
- `String requestURL`: 服务器接受上传的 URL。

###### 返回值

- `String`: 服务器响应的内容，如果上传成功，通常是服务器对上传文件的处理结果。

##### `public static String upload(InputStream inputStream, String requestURL, String name, String filename)`

使用输入流上传文件到服务器，允许更细粒度的文件处理。

###### 参数

- `InputStream inputStream`: 文件内容的输入流。
- `String requestURL`: 服务器接受上传的 URL。
- `String name`: 表单中文件参数的名称。
- `String filename`: 上传的文件名（包括扩展名）。

###### 返回值

- `String`: 服务器响应的内容。

##### `public static String upload(byte[] bytes, String requestURL, String name, String filename)`

使用字节数组上传文件到服务器，适用于文件内容已在内存中的情况。

###### 参数

- `byte[] bytes`: 文件内容的字节数组。
- `String requestURL`: 服务器接受上传的 URL。
- `String name`: 表单中文件参数的名称。
- `String filename`: 上传的文件名（包括扩展名）。

###### 返回值

- `String`: 服务器响应的内容。

### ImgprocUtils

#### 简介

`ImgprocUtils` 类提供了一系列的图像处理功能，主要用于查找和处理图像中的轮廓。这个工具类适用于需要在图像中识别形状、执行图像分割和图像增强的应用程序。

#### 使用示例

以下是如何使用 `ImgprocUtils` 类进行图像中轮廓的查找和处理的示例：

```java
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.imgcodecs.Imgcodecs;
import com.litongjava.opencv.utils.ImgprocUtils;
import com.litongjava.opencv.model.DebugInfo;

public class Example {
    static {
        System.loadLibrary(Core.NATIVE_LIBRARY_NAME);
    }

    public static void main(String[] args) {
        Mat srcImage = Imgcodecs.imread("path/to/image.jpg");
        DebugInfo debugInfo = new DebugInfo("baseName", "tempPath", true, true, "http://uploadServer.com", "jpg");

        try {
            List<MatOfPoint> contours = ImgprocUtils.findContours(srcImage, debugInfo);
            Mat resultImage = ImgprocUtils.findContoursAndDraw(srcImage, debugInfo);
            Imgcodecs.imwrite("path/to/resultImage.jpg", resultImage);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 方法说明

##### `public static List<MatOfPoint> findContours(Mat src, DebugInfo debugInfo) throws IOException`

查找图像中的轮廓。

###### 参数

- `Mat src`: 输入的原始图像。
- `DebugInfo debugInfo`: 包含调试信息的对象，例如保存路径和是否保存中间步骤的图像。

###### 返回值

- `List<MatOfPoint>`: 图像中检测到的轮廓列表。

##### `public static Mat findContoursAndDraw(Mat src, DebugInfo debugInfo) throws IOException`

查找图像中的轮廓并在原图上绘制这些轮廓。

###### 参数

- `Mat src`: 输入的原始图像。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `Mat`: 绘制了轮廓的图像。

##### `public static MaxAreaBo getMaxArea(Mat src, DebugInfo debugInfo) throws IOException`

查找图像中面积最大的轮廓。

###### 参数

- `Mat src`: 输入的原始图像。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `MaxAreaBo`: 包含最大面积和对应矩形区域的对象。

### KmeansUtils

#### 简介

`KmeansUtils` 类提供了一个方法来使用 k-means 聚类算法识别和修改图像的背景色。该方法将图像背景色设置为白色，是一种常用的技术，特别适用于需要突出前景对象或进行进一步图像分析的情况。

#### 使用示例

以下是如何使用 `KmeansUtils` 类将图像背景色设置为白色的示例：

```java
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.imgcodecs.Imgcodecs;
import com.litongjava.opencv.utils.KmeansUtils;

public class Example {
    static {
        System.loadLibrary(Core.NATIVE_LIBRARY_NAME);
    }

    public static void main(String[] args) {
        Mat srcImage = Imgcodecs.imread("path/to/image.jpg");
        Mat resultImage = KmeansUtils.backgrand2White(srcImage);
        Imgcodecs.imwrite("path/to/resultImage.jpg", resultImage);
    }
}
```

#### 方法说明

##### `public static Mat backgrand2White(Mat src)`

将图像的背景色设置为白色，使用 k-means 聚类来识别背景。

###### 参数

- `Mat src`: 输入的原始图像。

###### 返回值

- `Mat`: 背景色被修改为白色的图像。

### ListUtils

#### 简介

`ListUtils` 类提供了一个通用的工具方法，用于将多个列表合并成一个单一的列表。这个方法非常有用于处理多个数据集合，需要将它们合并进行统一处理的情形。

#### 使用示例

以下是如何使用 `ListUtils` 类合并多个列表的示例：

```java
import com.litongjava.opencv.utils.ListUtils;
import java.util.Arrays;
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<Integer> list1 = Arrays.asList(1, 2, 3);
        List<Integer> list2 = Arrays.asList(4, 5, 6);
        List<Integer> list3 = Arrays.asList(7, 8, 9);

        List<Integer> combinedList = ListUtils.toList(list1, list2, list3);
        System.out.println("Combined List: " + combinedList);
    }
}
```

#### 方法说明

##### `public static <T> List<T> toList(List<T>... lists)`

合并多个列表成一个单一的列表。

###### 参数

- `List<T>... lists`: 一个或多个列表。

###### 返回值

- `List<T>`: 包含所有输入列表中所有元素的单一列表。

### MathUtils

#### 简介

`MathUtils` 类提供了一组数学计算工具，用于执行常见的几何和代数运算，如计算两点间的距离、从三点计算角度，以及找出几个数中的最大值。这些方法对于图像处理、物理模拟或任何需要进行基本数学运算的应用程序都非常有用。

#### 使用示例

以下是如何使用 `MathUtils` 类进行基本的数学计算的示例：

```java
import com.litongjava.opencv.utils.MathUtils;

public class Example {
    public static void main(String[] args) {
        double[] p0 = {1.0, 1.0};
        double[] p1 = {4.0, 5.0};
        double[] p2 = {7.0, 8.0};

        double distance = MathUtils.calculating_distance(p0, p1);
        double angle = MathUtils.calculating_angle(p0, p1, p2);
        double maxAngle = MathUtils.max(30.0, 45.0, 60.0, 75.0);

        System.out.println("Distance between points: " + distance);
        System.out.println("Angle between points: " + angle);
        System.out.println("Maximum angle: " + maxAngle);
    }
}
```

#### 方法说明

##### `public static double calculating_angle(double[] p1, double[] p2, double[] p0)`

从三个坐标点中计算角度。

###### 参数

- `double[] p1`: 第一个点的坐标。
- `double[] p2`: 第二个点的坐标。
- `double[] p0`: 交点（即角度的顶点）的坐标。

###### 返回值

- `double`: 以度为单位的角度。

##### `public static double calculating_distance(double[] p0, double[] p1)`

从已知的两个点计算两点之间的距离。

###### 参数

- `double[] p0`: 第一个点的坐标。
- `double[] p1`: 第二个点的坐标。

###### 返回值

- `double`: 两点之间的距离。

##### `public static double max(double angle_a, double angle_b, double angle_c, double angle_d)`

返回四个值中最大的一个。

###### 参数

- `double angle_a`: 第一个值。
- `double angle_b`: 第二个值。
- `double angle_c`: 第三个值。
- `double angle_d`: 第四个值。

###### 返回值

- `double`: 这四个数中的最大值。

### MatUtils

#### 简介

`MatUtils` 类提供了一系列用于处理 OpenCV 的 `Mat` 对象的实用方法，包括图像的读取、写入、转换及调试输出。此类大大简化了图像文件与 `Mat` 对象之间的转换操作，同时提供了将图像数据上传到服务器的功能。

#### 使用示例

以下是如何使用 `MatUtils` 类进行图像读取、写入和转换的示例：

```java
import org.opencv.core.Mat;
import com.litongjava.opencv.utils.MatUtils;
import java.awt.image.BufferedImage;

public class Example {
    public static void main(String[] args) {
        try {
            // 读取图像
            Mat image = MatUtils.imread("path/to/image.jpg");

            // 将 Mat 对象写入到文件
            MatUtils.imwrite("path/to/output.jpg", image);

            // 将 Mat 转换为 BufferedImage
            BufferedImage bufferedImage = MatUtils.mat2BufferImage(image);

            // 调试输出
            MatUtils.debugToFile(image, "path/to", "image.jpg", "output.jpg");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### 方法说明

##### `public static Mat imread(String imagePath) throws IOException`

读取图像文件到 `Mat` 对象。

###### 参数

- `String imagePath`: 图像文件的路径。

###### 返回值

- `Mat`: 读取的图像数据。

##### `public static void imwrite(String dstPath, Mat dst) throws IOException`

将 `Mat` 对象写入到图像文件。

###### 参数

- `String dstPath`: 目标文件的路径。
- `Mat dst`: 要写入的 `Mat` 对象。

###### 返回值

- 无。

##### `public static BufferedImage mat2BufferImage(Mat mat)`

将 `Mat` 对象转换为 `BufferedImage`。

###### 参数

- `Mat mat`: 要转换的 `Mat` 对象。

###### 返回值

- `BufferedImage`: 转换后的图像。

##### `public static void debugToFile(Mat mat, String imagePath, String oldName, String newName) throws IOException`

在调试过程中将 `Mat` 对象的图像保存到新文件，帮助跟踪处理过程。

###### 参数

- `Mat mat`: 要保存的 `Mat` 对象。
- `String imagePath`: 原始图像路径。
- `String oldName`: 原始文件名。
- `String newName`: 新文件名。

###### 返回值

- 无。

### OpenCVLibraryUtils

#### 简介

`OpenCVLibraryUtils` 类提供了一个方法来初始化 OpenCV 库。它确保所需的本地库目录存在，并且将其路径添加到 Java 的库路径中，然后加载 OpenCV 的核心库。这个类对于在 Java 应用程序中正确设置和使用 OpenCV 是必要的，尤其是在涉及本地依赖管理的场合。

#### 使用示例

以下是如何使用 `OpenCVLibraryUtils` 类初始化 OpenCV 库的示例：

```java
import com.litongjava.opencv.utils.OpenCVLibraryUtils;

public class Example {
    public static void main(String[] args) {
        OpenCVLibraryUtils.init();  // 初始化 OpenCV 库
        // 之后可以进行其他 OpenCV 操作
    }
}
```

#### 方法说明

##### `public static void init()`

初始化 OpenCV 库，确保本地依赖正确加载。

###### 参数

- 无。

###### 返回值

- 无。

##### `private static void initLibary()`

创建库目录（如果不存在）并加载 OpenCV 的核心库。

###### 参数

- 无。

###### 返回值

- 无。

##### `public static void addLibDir(String s) throws IOException`

将指定的目录添加到 Java 库路径中。

###### 参数

- `String s`: 要添加到 Java 库路径的目录。

###### 返回值

- 无。

### RecognitionPlateUtils

#### 简介

`RecognitionPlateUtils` 类提供了车牌识别过程中的预处理功能。该类通过灰度化和二值化处理来优化车牌区域的图像，从而为后续的识别步骤准备数据。这种预处理是车牌识别系统中关键的步骤，有助于提高识别的准确性。

#### 使用示例

以下是如何使用 `RecognitionPlateUtils` 类进行车牌图像的预处理的示例：

```java
import org.opencv.core.Mat;
import com.litongjava.opencv.utils.RecognitionPlateUtils;
import com.litongjava.opencv.model.DebugInfo;

public class Example {
    public static void main(String[] args) {
        Mat srcImage = // 从某处获取的原始车牌图像
        DebugInfo debugInfo = new DebugInfo("baseName", "tempPath", true, true, "uploadHost", "jpg");

        try {
            Mat preprocessedImage = RecognitionPlateUtils.pre(srcImage, debugInfo);
            // 使用预处理过的图像进行车牌识别
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 方法说明

##### `public static Mat pre(Mat src, DebugInfo debugInfo) throws IOException`

对车牌图像进行预处理，包括灰度化和二值化。

###### 参数

- `Mat src`: 原始车牌图像。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `Mat`: 预处理后的图像。

##### `private static Mat v1(Mat src, DebugInfo debugInfo) throws IOException`

为预处理操作提供的内部版本方法，主要用于提取车牌区域并进行初步的图像处理。

###### 参数

- `Mat src`: 原始车牌图像。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `Mat`: 经过提取车牌区域后的图像。

##### `private static Mat threshold(Mat src, DebugInfo debugInfo) throws IOException`

对车牌图像进行灰度化和二值化处理。

###### 参数

- `Mat src`: 灰度化后的车牌图像。
- `DebugInfo debugInfo`: 包含调试信息的对象，用于保存和上传处理后的图像。

###### 返回值

- `Mat`: 二值化处理后的车牌图像。

### RecognitionShapeUtils

#### 简介

`RecognitionShapeUtils` 类提供了识别图像中不同形状的功能。此类利用图像处理技术如灰度化、二值化以及形态学操作来提取图像中的各种形状，包括但不限于常见的几何形状和颜色过滤识别。它是用于高级图像分析和处理，在诸如图像识别、机器视觉等领域有广泛应用。

#### 使用示例

以下是如何使用 `RecognitionShapeUtils` 类进行图像中形状识别的示例：

```java
import com.litongjava.opencv.utils.RecognitionShapeUtils;
import com.litongjava.opencv.model.DebugInfo;
import java.io.IOException;

public class Example {
    public static void main(String[] args) {
        byte[] imageBytes = // 从文件或网络获取图像的字节数据
        DebugInfo debugInfo = new DebugInfo("baseName", "tempPath", true, true, "uploadHost", "jpg");

        try {
            List<Shape> shapes = RecognitionShapeUtils.index(imageBytes, debugInfo);
            for (Shape shape : shapes) {
                System.out.println("Detected shape: " + shape.getType());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 方法说明

##### `public static List<Shape> index(byte[] imageBytes, DebugInfo debugInfo) throws FileNotFoundException, IOException`

对输入的图像字节数据进行形状识别。

###### 参数

- `byte[] imageBytes`: 图像的字节数据。
- `DebugInfo debugInfo`: 包含调试信息的对象，用于调试过程中的文件保存和上传。

###### 返回值

- `List<Shape>`: 识别出的形状列表。

##### `public static List<Shape> recgnizeV1(byte[] imageBytes, DebugInfo debugInfo) throws FileNotFoundException, IOException`

图像的识别处理的第一个版本，实现了从图像提取特定区域并进行形状识别。

###### 参数

- `byte[] imageBytes`: 图像的字节数据。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `List<Shape>`: 从图像中识别出的形状列表。

##### `public static List<Shape> recognizeShape(Mat src, DebugInfo debugInfo) throws IOException`

从提取的图像区域中识别形状。

###### 参数

- `Mat src`: 要进行形状识别的图像。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `List<Shape>`: 识别出的形状列表。

##### `public static List<Shape> colorDivisionAndFindShape(Mat mat, String color, String colorSuffix, Scalar lower, Scalar upper, DebugInfo debugInfo) throws IOException`

从图像中分离出特定颜色并识别形状。

###### 参数

- `Mat mat`: 输入的图像。
- `String color`: 形状的颜色描述。
- `String colorSuffix`: 颜色的后缀，用于命名。
- `Scalar lower`: 颜色范围的下限。
- `Scalar upper`: 颜色范围的上限。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `List<Shape>`: 识别出的形状列表。

### RecognitionUtils

#### 简介

`RecognitionUtils` 类专注于图像识别过程中的预处理步骤，主要用于裁剪图像中的最大面积区域，这通常是为了更准确地识别和分析图像中的主要对象。此类的方法能够通过多次裁剪逐步缩小关注区域，确保关键内容得到突出。

#### 使用示例

以下是如何使用 `RecognitionUtils` 类进行图像的区域提取的示例：

```java
import org.opencv.core.Mat;
import com.litongjava.opencv.utils.RecognitionUtils;
import com.litongjava.opencv.model.DebugInfo;

public class Example {
    public static void main(String[] args) {
        Mat srcImage = // 获取图像Mat对象
        DebugInfo debugInfo = new DebugInfo("baseName", "tempPath", true, true, "uploadHost", "jpg");

        try {
            Mat focusedArea = RecognitionUtils.extraRecogArea(srcImage, debugInfo);
            // 使用focusedArea进行进一步的图像处理或识别
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 方法说明

##### `public static Mat extraRecogArea(Mat src, DebugInfo debugInfo) throws IOException`

从源图像中提取包含最大面积的图像区域。这通常是图像识别前的一个重要步骤，目的是减少背景噪声并集中于主要内容。

###### 参数

- `Mat src`: 输入的源图像。
- `DebugInfo debugInfo`: 包含调试和路径信息的对象。

###### 返回值

- `Mat`: 经过裁剪后包含主要识别区域的图像。

###### 异常

- `IOException`: 当读写文件或处理图像时发生错误。

通过多次递归裁剪，这个方法可以逐步细化图像区域，直到满足特定的面积比例条件，从而确保图像的主要部分被有效地提取出来。这个过程特别适用于需要高精度图像识别和分析的应用场景，如自动化检测、机器视觉等。

### ShapeUtils

#### 简介

`ShapeUtils` 类提供了从图像中识别和提取形状的工具，专门用于处理和分析经过形态学变换的二值化图像。此类能够从图像中找到轮廓，将其转换为多边形曲线，并识别最外部的轮廓，用于图形分析和计算机视觉应用。

#### 使用示例

以下是如何使用 `ShapeUtils` 类进行图像中形状识别的示例：

```java
import org.opencv.core.Mat;
import com.litongjava.opencv.utils.ShapeUtils;
import com.litongjava.opencv.model.DebugInfo;

public class Example {
    public static void main(String[] args) {
        Mat srcImage = // 获取图像Mat对象
        DebugInfo debugInfo = new DebugInfo("baseName", "tempPath", true, true, "uploadHost", "jpg");

        try {
            List<Mat> shapeContours = ShapeUtils.getShapeContours(srcImage, debugInfo);
            for (Mat contourImage : shapeContours) {
                // 使用contourImage进行进一步处理或显示
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 方法说明

##### `public static List<Mat> getShapeContours(Mat morphology, DebugInfo debugInfo) throws IOException`

从经过形态变换的二值化矩阵中提取图像轮廓。

###### 参数

- `Mat morphology`: 经过形态变换的二值化图像矩阵。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `List<Mat>`: 含有图像轮廓的列表。

##### `public static MatOfPoint2f getShape(MatOfPoint contour)`

从给定的轮廓中计算并返回多边形曲线。

###### 参数

- `MatOfPoint contour`: 输入的图像轮廓。

###### 返回值

- `MatOfPoint2f`: 近似多边形曲线。

##### `public static List<MatOfPoint> findExternalContours(Mat src, DebugInfo debugInfo)`

从图像中仅查找最外部的轮廓。

###### 参数

- `Mat src`: 输入的图像。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `List<MatOfPoint>`: 图像中最外部的轮廓列表。

##### `public static double getWhiteRatio(Mat src)`

计算图像中白色区域占总图像的比率。

###### 参数

- `Mat src`: 输入的图像。

###### 返回值

- `double`: 白色区域在图像中的占比。

### TrafficLightUtils

#### 简介

`TrafficLightUtils` 类提供了一个用于识别和处理交通灯图像的工具集，主要包括交通灯颜色的识别（红色、绿色、黄色）以及基于颜色的图像处理。这个类能够通过色彩分割和形状识别技术，从图像中准确地识别出交通灯的状态。

#### 使用示例

以下是如何使用 `TrafficLightUtils` 类进行交通灯图像识别的示例：

```java
import com.litongjava.opencv.utils.TrafficLightUtils;
import com.litongjava.opencv.model.DebugInfo;
import java.io.IOException;

public class Example {
    public static void main(String[] args) {
        byte[] imageBytes = // 从文件或网络获取图像的字节数据
        DebugInfo debugInfo = new DebugInfo("baseName", "tempPath", true, true, "uploadHost", "jpg");

        try {
            TrafficLight trafficLight = TrafficLightUtils.index(imageBytes, debugInfo);
            System.out.println("Detected traffic light state: " + trafficLight.getState());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 方法说明

##### `public static TrafficLight index(byte[] imageBytes, DebugInfo debugInfo) throws IOException`

主要入口方法，用于开始交通灯识别流程。

###### 参数

- `byte[] imageBytes`: 图像的字节数据。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `TrafficLight`: 识别出的交通灯状态对象。

##### `private static TrafficLight v1(byte[] imageBytes, DebugInfo debugInfo) throws IOException`

第一版本的实现方法，包含基本的图像读取和初步的颜色识别。

###### 参数

- `byte[] imageBytes`: 图像的字节数据。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `TrafficLight`: 识别出的交通灯状态对象。

##### `private static TrafficLight v2(byte[] imageBytes, DebugInfo debugInfo) throws IOException`

第二版本的实现方法，增加了背景颜色替换以及更精细的颜色提取。

###### 参数

- `byte[] imageBytes`: 图像的字节数据。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `TrafficLight`: 识别出的交通灯状态对象。

##### `private static TrafficLight v3(byte[] imageBytes, DebugInfo debugInfo) throws IOException`

第三版本的实现方法，包括了全面的颜色提取、轮廓识别和图像增强。

###### 参数

- `byte[] imageBytes`: 图像的字节数据。
- `DebugInfo debugInfo`: 包含调试信息的对象。

###### 返回值

- `TrafficLight`: 识别出的交通灯状态对象。

这个工具类通过详细的图像处理步骤，从简单的颜色识别到复杂的形态分析，提供了全面的交通灯状态检测功能，适用于交通监控、自动驾驶系统等领域。
