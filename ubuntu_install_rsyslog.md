# ubuntu install rsyslog
## 1.install rsyslog
>  ###### 1.Enter the following command:
```
sudo add-apt-repository ppa:adiscon/v8-stable
```
> ###### 2. Then update your apt cache:  
```
sudo apt-get update
```
> ###### 3. Finally install the new rsyslog version:  
```
sudo apt-get install rsyslog
```

## 2. install rsyslog-elasticsearch:
```
sudo apt-get install rsyslog-elasticsearch
```
## 3. config

```
module(load="omelasticsearch")
template(name="testTemplate"
         type="list"
         option.json="on") {
           constant(value="{")
             constant(value="\"timestamp\":\"")      property(name="timereported" dateFormat="rfc3339")
             constant(value="\",\"message\":\"")     property(name="msg")
             constant(value="\",\"host\":\"")        property(name="hostname")
             constant(value="\",\"severity\":\"")    property(name="syslogseverity-text")
             constant(value="\",\"facility\":\"")    property(name="syslogfacility-text")
             constant(value="\",\"syslogtag\":\"")   property(name="syslogtag")
           constant(value="\"}")
         }
action(type="omelasticsearch"
       server="myserver.local"
       serverport="9200"
       template="testTemplate"
       searchIndex="test-index"
       searchType="test-type"
       bulkmode="on"
       queue.type="linkedlist"
       queue.size="5000"
       queue.dequeuebatchsize="300"
       action.resumeretrycount="-1")
```
```
module(load="imuxsock")             # for listening to /dev/log
module(load="omelasticsearch") # for outputting to Elasticsearch
# this is for index names to be like: logstash-YYYY.MM.DD
template(name="logstash-index"
  type="list") {
    constant(value="logstash-")
    property(name="timereported" dateFormat="rfc3339" position.from="1" position.to="4")
    constant(value=".")
    property(name="timereported" dateFormat="rfc3339" position.from="6" position.to="7")
    constant(value=".")
    property(name="timereported" dateFormat="rfc3339" position.from="9" position.to="10")
}

# this is for formatting our syslog in JSON with @timestamp
template(name="plain-syslog"
  type="list") {
    constant(value="{")
      constant(value=""@timestamp":"")     property(name="timereported" dateFormat="rfc3339")
      constant(value="","host":"")        property(name="hostname")
      constant(value="","severity":"")    property(name="syslogseverity-text")
      constant(value="","facility":"")    property(name="syslogfacility-text")
      constant(value="","tag":"")   property(name="syslogtag" format="json")
      constant(value="","message":"")    property(name="msg" format="json")
    constant(value=""}")
}
# this is where we actually send the logs to Elasticsearch (localhost:9200 by default)
action(type="omelasticsearch"
    template="plain-syslog"
    searchIndex="logstash-index"
    dynSearchIndex="on")
```
