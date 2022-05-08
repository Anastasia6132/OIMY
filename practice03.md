# Лабораторная работа 2. Переход между цветовыми пространствами. Линейный и нелинейный переход. Мера цветовой разницы. Функции преобразования яркости. Гамма, логарифмическое, экспоненциаяльное кодирование.
План работ:

1. Скачать любое цифровое изображение. Желательно многоцветное
    Исходное изображение:
    <img src="resources/image.jpg" width="500"/>
1. Отобразить изображение по каналам RGB (каждый канал представить как градации серого).
```
public void channels(BufferedImage img) throws IOException {
    int h = img.getHeight();
    int w = img.getWidth();
    BufferedImage chR = new BufferedImage(w, h, TYPE_INT_RGB);
    BufferedImage chG = new BufferedImage(w, h, TYPE_INT_RGB);
    BufferedImage chB = new BufferedImage(w, h, TYPE_INT_RGB);
    for (int y = 0; y < h; y++) {
        for (int x = 0; x < w; x++) {
            int rgb = img.getRGB(x, y);
            int red = red(rgb);
            int green = green(rgb);
            int blue = blue(rgb);
            chR.setRGB(x, y, rgb(red, red, red));
            chG.setRGB(x, y, rgb(green, green, green));
            chB.setRGB(x, y, rgb(blue, blue, blue));
        }
    }
    save(chR, "result/channels", "r", FORMAT);
    save(chG, "result/channels", "g", FORMAT);
    save(chB, "result/channels", "b", FORMAT);
}
```
Результат: 

- Red:
 <img src="resources/channels/r.jpg" width="500"/>
- Green:
 <img src="resources/channels/g.jpg" width="500"/>
- Blue:
 <img src="resources/channels/b.jpg" width="500"/>


1. Лианеризовать изображение обратным гамма преобразованием.

```
public BufferedImage gammaCorrection(BufferedImage img, double gamma) throws IOException {
    int h = img.getHeight();
    int w = img.getWidth();
    BufferedImage result = new BufferedImage(w, h, TYPE_INT_RGB);
    int[] gammaLUT = new int[256];
    for (int i = 0; i < gammaLUT.length; i++) {
        gammaLUT[i] = (int) (255 * (Math.pow(i / 255f, 1 / gamma)));
    }
    for (int y = 0; y < h; y++) {
        for (int x = 0; x < w; x++) {
            int rgb = img.getRGB(x, y);
            int red = gammaLUT[red(rgb)];
            int green = gammaLUT[green(rgb)];
            int blue = gammaLUT[blue(rgb)];
            result.setRGB(x, y, rgb(red, green, blue));
        }
    }
    save(result, "result/gammaCor", "result", FORMAT);
    return result;
}
```
 <img src="resources/gammaCor/result.jpg" width="500"/>

1. Отобразить по каналам RGB.
```
public void rgbChannels(BufferedImage img) throws IOException {
    int h = img.getHeight();
    int w = img.getWidth();
    BufferedImage chR = new BufferedImage(w, h, TYPE_INT_RGB);
    BufferedImage chG = new BufferedImage(w, h, TYPE_INT_RGB);
    BufferedImage chB = new BufferedImage(w, h, TYPE_INT_RGB);
    for (int y = 0; y < h; y++) {
        for (int x = 0; x < w; x++) {
            int rgb = img.getRGB(x, y);
            int red = red(rgb);
            int green = green(rgb);
            int blue = blue(rgb);
            chR.setRGB(x, y, rgb(red, 0, 0));
            chG.setRGB(x, y, rgb(0, green, 0));
            chB.setRGB(x, y, rgb(0, 0, blue));
        }
    }
    save(chR, "result/rgbChannels", "r", FORMAT);
    save(chG, "result/rgbChannels", "g", FORMAT);
    save(chB, "result/rgbChannels", "b", FORMAT);
}
```
 - R
 <img src="resources/rgbChannels/r.jpg" width="500"/>
 - G
 <img src="resources/rgbChannels/g.jpg" width="500"/>
 - B
 <img src="resources/rgbChannels/b.jpg" width="500"/>
 
1. Отобразить поканально разницу между исходным изображением и линеаризованным.
2. 
```
private void difference(BufferedImage img, BufferedImage gCor) throws IOException {
    int h = img.getHeight();
    int w = img.getWidth();
    BufferedImage chR = new BufferedImage(w, h, TYPE_INT_RGB);
    BufferedImage chG = new BufferedImage(w, h, TYPE_INT_RGB);
    BufferedImage chB = new BufferedImage(w, h, TYPE_INT_RGB);
    for (int y = 0; y < h; y++) {
        for (int x = 0; x < w; x++) {
            int orig = img.getRGB(x, y);
            int corr = gCor.getRGB(x, y);
            int red = red(orig) - red(corr);
            int green = green(orig) - green(corr);
            int blue = blue(orig) - blue(corr);
            chR.setRGB(x, y, rgb(red, 0, 0));
            chG.setRGB(x, y, rgb(0, green, 0));
            chB.setRGB(x, y, rgb(0, 0, blue));
        }
    }
    save(chR, "result/difference", "r", FORMAT);
    save(chG, "result/difference", "g", FORMAT);
    save(chB, "result/difference", "b", FORMAT);
}
```
- R
<img src="resources/difference/r.jpg" width="500"/>
- G
<img src="resources/difference/g.jpg" width="500"/>
- B
<img src="resources/difference/b.jpg" width="500"/>

1. Написать функцию перевода цветов из линейного RGB в XYZ с использованием матрицы. Найти подходящую библиотечную функцию. Сравнить результаты через построение разностного изоборажения.

```
    public BufferedImage RGBtoXYZ(BufferedImage img) throws IOException {
        //opencv
        Mat xyzMat = new Mat();
        Imgproc.cvtColor(img2Mat(img), xyzMat, Imgproc.COLOR_BGR2XYZ);
        BufferedImage resultL = (BufferedImage) HighGui.toBufferedImage(xyzMat);

        int h = img.getHeight();
        int w = img.getWidth();
        BufferedImage result = new BufferedImage(w, h, TYPE_INT_RGB);
        for (int i = 0; i < h; i++) {
            for (int j = 0; j < w; j++) {
                int rgb = img.getRGB(j, i);
                int[] xyz = RGBtoXYZ(ch1(rgb), ch2(rgb), ch3(rgb));
                result.setRGB(j, i, color(xyz[2], xyz[1], xyz[0]));
            }
        }
        save(result, "result/RGBtoXYZ", "result", FORMAT);
        save(resultL, "result/RGBtoXYZ", "resultLib", FORMAT);
        save(diff(result, resultL), "result/RGBtoXYZ", "diff", FORMAT);
        return result;
    }
        private static int[] RGBtoXYZ(double r, double g, double b) {
        return new int[]{
                (int) Math.round(r * 0.412453 + g * 0.357580 + b * 0.180423),
                (int) Math.round(r * 0.212671 + g * 0.715160 + b * 0.072169),
                (int) Math.round(r * 0.019334 + g * 0.119193 + b * 0.950227)
        };
    }
```
3. Написать функцию перевода цветов из XYZ в RGB (построить обратную матрицу XYZ в RGB). Преобразовать изображение XYZ в линейный RGB. Применить гамма преобразование. Сравнить результаты через построение разностного изоборажения.
4. Построить проекцию цветов исходного изображения на цветовой локус (плоскость xy).
5. Написать функцию перевода цветов из линейного RGB в HSV и обратно. Найти подходящую библиотечную функцию. Сравнить результаты через построение разностного изоборажения.
6. Используя библиотечные функции цветовой разности сравнить результаты, полученные в пунктах 6, 7, 9 (для каждой функции).
