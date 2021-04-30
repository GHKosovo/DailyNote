# Java IO 精讲

主要就是字符流字节流的输入输出，其他流大都是这两种的分支，子类等

怎么区分输入输出流呢？要以Java程序为主体，客体为其他，比如文件（字符串等），文件为客体，那么输入流就是从文件中输入Java程序中，而输出流则是从java程序中输出到文件中

## 字符流

分Reader和Writer，它们俩都是抽象类，故，都是使用他们的实现类

常见的有FileReader,FileWriter

```java
//新建字符流于try/catch之外
        FileReader fileReader = null;
        FileWriter fileWriter = null;
        try {
            fileReader = new FileReader("F:\\download\\test.txt");
            fileWriter = new FileWriter("F:\\download\\test2.txt");
            char[] chars = new char[255];
            int read = 0;
            while ((read = fileReader.read(chars)) != -1) {
                fileWriter.write(chars);
            }
            //刷入输出流
            fileWriter.flush();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                //关闭字符流
                if (fileReader != null)
                    fileReader.close();
                if (fileWriter!=null)
                    fileWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```

一般地，IO流都会有异常捕获，所以，一般都采取上面的写法，先定义字符流后，再try/catch中新建字符流并赋值

最后，记得关闭流

## 字节流

一般地，就是InputStream和OutputStream，他们也是抽象类，

跟字符流的写法差不多

## 缓冲流

分字符缓冲流和字节缓冲流，

字符缓冲流，一般就是BufferedRead和BufferedWriter；而字节缓冲流就是BufferedOutputStream和BufferedInputStream

> 缓冲流会比原生的字节/字符流块很多，它的效率比较高，节省时间

## 其他

### 合并流

就是可以把字节流或字符流等多个合并为一个的一种流形式

有两种构造函数

- SequenceInputStream(Enumeration<? extends InputStream> e)
- SequenceInputStream(InputStream s1, InputStream s2)

可以看到，如果是两个，可以直接合成，而如果是多于两个，需要用到枚举类，这个枚举类可以用Vector来生成。

