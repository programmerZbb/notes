curl 这个工具最大的好处就是能脱离浏览器复现浏览器发送请求的全部场景，所以在前后端联调的场景广泛使用。

如下一个标准的curl:
```
curl 'https://mcs.snssdk.com/list' \
  -H 'authority: mcs.snssdk.com' \
  -H 'accept: */*' \
  -H 'accept-language: zh-CN,zh;q=0.9' \
  -H 'content-type: application/json; charset=UTF-8' \
  -H 'origin: https://juejin.cn' \
  -H 'referer: https://juejin.cn/' \
  -H 'sec-ch-ua: "Chromium";v="112", "Google Chrome";v="112", "Not:A-Brand";v="99"' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'sec-ch-ua-platform: "macOS"' \
  -H 'sec-fetch-dest: empty' \
  -H 'sec-fetch-mode: cors' \
  -H 'sec-fetch-site: cross-site' \
  -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36' \
  --data-raw '[{"events":[{"event":"applog_trace","params":"{\"count\":3,\"state\":\"net\",\"key\":\"log\",\"params_for_special\":\"applog_trace\",\"aid\":2608,\"platform\":\"web\",\"_staging_flag\":1,\"sdk_version\":\"4.2.9\",\"event_index\":1683340852943}","local_time_ms":1683340110329}],"user":{"user_unique_id":"7044094845685745160","user_id":"1706872719552526","user_is_login":true,"web_id":"7044094845685745160"},"header":{"app_id":2608,"os_name":"mac","os_version":"10_15_7","device_model":"Macintosh","ab_sdk_version":"90083871,90081172","language":"zh-CN","platform":"Web","sdk_version":"4.2.9","sdk_lib":"js","timezone":8,"tz_offset":-28800,"resolution":"1920x1080","browser":"Chrome","browser_version":"112.0.0.0","referrer":"https://juejin.cn/search?query=curl","referrer_host":"juejin.cn","width":1920,"height":1080,"screen_width":1920,"screen_height":1080,"utm_source":"gold_browser_extension","custom":"{\"student_verify_status\":\"not_student\",\"user_level\":0,\"profile_id\":\"1706872719552526\"}"},"local_time":1683340110}]' \
  --compressed
```

## 几个常用的参数

* 参考：https://juejin.cn/post/7210320199690305596

* -o 输入内容到某个文件（output）

  ```shell
  curl 'http://127.0.0.1:8090/static/test.txt' -o test.txt
  ```

  

### -d 携带post数据

```kotlin
curl -X POST -d "name=andrew&age=22" -F "nm=da" https://www.baidu.com
# 将data编码
curl -X POST --data-urlencode "name=andrew age=22" https://www.baidu.com
```

注意：`-d`是发送`post`参数，`-F`是发送`form-data`数据，两者不能同时使用，否则会报错：

```sql
curl -X POST -d "name=andrew" -F "age=22" https://www.baidu.com
Warning: You can only select one HTTP request method! You asked for both POST
Warning: (-d, --data) and multipart formpost (-F, --form).
```

### 携带 get 请求数据

* 直接提现在querystring 中



### -H 设置header

