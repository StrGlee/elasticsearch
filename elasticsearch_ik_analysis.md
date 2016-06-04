# elasticsearch_ik_analysis
## Step 1.安装 IK Analysis
&nbsp;&nbsp;Elasticsearch 自带的分词器会粗暴地把每个汉字直接分开，没有根据词库来分词。为了处理中文搜索，还需要安装中文分词插件。我使用的是elasticsearch-analysis-ik，支持自定义词库。   
&nbsp;&nbsp;首先，下载与 Elasticsearch 匹配的 elasticsearch-analysis-ik 插件：
```
wget -c https://github.com/medcl/elasticsearch-analysis-ik/archive/v1.8.1.zip
unzip v1.9.0.zip
```
&nbsp;&nbsp;解压后，进入插件源码目录编译：
```
sudo apt-get install maven
cd elasticsearch-analysis-ik-1.9.0
mvn package
```
&nbsp;&nbsp;如果一切顺利，在 target/releases/ 目录下可以找到编好的文件。将其解压并拷到 ~/es_root 对应目录：
```
mkdir -p ~/es_root/plugins/ik/
unzip target/releases/elasticsearch-analysis-ik-1.9.0.zip -d ~/es_root/plugins/ik/
```
&nbsp;&nbsp;elasticsearch-analysis-ik 的配置文件在~/es_root/plugins/ik/config/ik/ 目录，很多都是词表，直接用文本编辑器打开就可以修改，改完记得保存为 utf-8 格式。   
&nbsp;&nbsp;现在再启动 Elasticsearch 服务，如果看到类似下面这样的信息，说明 IK Analysis 插件已经装好了：
```
plugins [analysis-ik]
```
## Step 2.配置同义词
&nbsp;&nbsp;Elasticsearch 自带一个名为 synonym 的同义词 filter。为了能让 IK 和 synonym 同时工作，我们需要定义新的 analyzer，用 IK 做 tokenizer，synonym 做 filter。听上去很复杂，实际上要做的只是加一段配置。
打开 ~/es_root/config/elasticsearch.yml 文件，加入以下配置：
```
index:
  analysis:
    analyzer:
      ik_syno:
          type: custom
          tokenizer: ik_max_word
          filter: [my_synonym_filter]
      ik_syno_smart:
          type: custom
          tokenizer: ik_smart
          filter: [my_synonym_filter]
    filter:
      my_synonym_filter:
          type: synonym
          synonyms_path: analysis/synonym.txt
```
&nbsp;&nbsp;以上配置定义了 ik_syno 和 ik_syno_smart 这两个新的 analyzer，分别对应 IK 的 ik_max_word 和 ik_smart 两种分词策略。根据 IK 的文档，二者区别如下：   
> ik_max_word：会将文本做最细粒度的拆分，例如「中华人民共和国国歌」会被拆分为「中华人民共和国、中华人民、中华、华人、人民共和国、人民、人、民、共和国、共和、和、国国、国歌」，会穷尽各种可能的组合；

> ik_smart：会将文本做最粗粒度的拆分，例如「中华人民共和国国歌」会被拆分为「中华人民共和国、国歌」；

&nbsp;&nbsp;ik_syno 和 ik_syno_smart 都会使用 synonym filter 实现同义词转换。为了方便后续测试，建议创建 ~/es_root/config/analysis/synonym.txt 文件，输入一些同义词并存为 utf-8 格式。例如：   
ua,user-agent,userAgent   
js,javascript   
internet explore=>ie   
