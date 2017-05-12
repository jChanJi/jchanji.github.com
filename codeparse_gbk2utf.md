# [Go to home page] [1]
<center>2017-5-12</center><br>
## 使用java将文件夹下的文件批量的从gbk编码转化成utf-8编码
### 一、建立java项目，导入[commons-io-*.jar][2]
### 二、新建class，文件名随便起，我的是Codeparse,包名为exchangecode
```markdown
package exchangecode;

import java.io.File;
import java.io.IOException;
import java.util.Collection;

import org.apache.commons.io.FileUtils;

public class Codeparse {

    public static void main(String[] args) throws IOException {
        //GBK编码格式源码路径,根据自己的文件路径写 
        String srcDirPath = "F:\\test"; 
        //转为UTF-8编码格式源码路径，根据自己的文件路径写 
        String utf8DirPath ="F:\\out"; 
                
        //获取所有txt文件,如果是其他类型的文件，将{“txt”}中的txt换为其他文件的后缀名
        @SuppressWarnings("unchecked")
        Collection<File> javaGbkFileCol =  FileUtils.listFiles(new File(srcDirPath), new String[]{"txt"}, true); 
                
        for (File javaGbkFile : javaGbkFileCol) { 
              //UTF8格式文件路径 
              String utf8FilePath = utf8DirPath+javaGbkFile.getAbsolutePath().substring(srcDirPath.length()); 
              
              //使用GBK读取数据，然后用UTF-8写入数据 
              FileUtils.writeLines(new File(utf8FilePath), "UTF-8", FileUtils.readLines(javaGbkFile, "GBK"));        
        }
        System.out.println("success!");
    }

}
```
### 三、运行







[1]: https://jchanji.github.io "主页"
[2]: http://mirror.bit.edu.cn/apache//commons/io/binaries/commons-io-2.5-bin.zip "commons-io-*.jar"