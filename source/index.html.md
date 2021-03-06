---
title: Tài liệu kỹ thuật CyStack API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python

toc_footers:
  - <a href='https://cystack.io/apikeys'>Tạo API Key</a>
  - <a href='/'>English</a>

includes:
  - errors

search: true
---

# Giới thiệu

Dưới đây là tài liệu hướng dẫn sử dụng CyStack API. Bạn có thể sử dụng các API của chúng tôi để thực hiện các chức năng quản lý trên CyStack Platform. Bằng cách này, bạn có thể tích hợp CyStack Platform vào hệ thống của bạn hoặc tự phát triển một Scanner trên hạ tầng sẵn có của chúng tôi. 

Các chức năng mà bạn được phép/không được phép sử dụng thông qua CyStack APIClient

Chức năng | Truy cập qua API
-- | -- 
Tạo target | Không
Liệt kê và xem thông tin target | Có
Tạo và xem thông tin scan | Có
Xem thông tin lỗ hổng | Có
Xem thông tin và cấu hình Monitoring | Có
Quản lý thông tin người dùng | Không
Quản lý danh sách nhận tin (notifications) | Không
Quản lý API Keys | Không

Chúng tôi có các đoạn code mẫu được viết bằng Shell và Python. Bạn có thể tham khảo ở cột bên phải, ứng với các chức năng được cung cấp. Việc truy cập API bằng các ngôn ngữ lập trình khác hoàn toàn tương tự.

<aside class="notice">
Lưu ý: Các API không thuộc phạm vi tài liệu này được dành cho những mục đích khác của CyStack Team. Các developer <b>không nên sử dụng</b> chúng nếu không được phép.
</aside>

# Xác thực

CyStack Platform sử dụng hình thức xác thực thông qua API key (Bearer Token) cho tất cả các request từ Client gửi đến API Server. Bạn có thể tạo mới một API key từ trang [Quản lý API key](https://cystack.io/apikeys).

Sau khi đã có được API key, bạn phải thêm vào header của tất cả các request key/value sau:

`Authorization: Bearer cystackapiexample`

<aside class="notice">
Bạn phải thay <code>cystackapiexample</code> bằng API key thật của bạn.
</aside>

# Target

## Liệt kê danh sách target

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def list_target():
    endpoint = "%s/targets" % ROOT_URL
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)

print list_target()	
```

```shell
curl "https://api.cystack.io/v1/targets"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{  
   "count":1,
   "next":null,
   "previous":null,
   "results":[  
      {  
         "verification":0,
         "href":"/v1/targets/599fc347-3e46-4163-8a1d-444bd1928a85",
         "id":"599fc347-3e46-4163-8a1d-444bd1928a85",
         "address":"https://cystack.net"
      }
   ]
}
```

Endpoint này liệt kê tất cả các target hiện có của user (ứng với API key)

### HTTP Request

`GET https://api.cystack.io/v1/targets`

### Các tham số theo URL

Tham số | Mô tả
--------- | -----------
page | Mặc định, kết quả trả về sẽ được phân trang với 10 kết quả/trang. Tham số page nhận vào một số tự nhiên đại diện cho trang cần lấy
q | Từ khóa dùng để lọc target
ordering | Giá trị được ưu tiên sắp xếp. Giá trị tham số hợp lệ `?ordering=target`, `?ordering=verification`, `?ordering=target,verification`. Nếu muốn sắp xếp theo chiều ngược lại, thêm dấu `-` vào trước giá trị cần sắp xếp chẳng hạn `?ordering=-target`

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
count | Số lượng kết quả lấy được
next | URL trang kết quả tiếp theo, nếu trang tiếp theo không có dữ liệu thì trả về `null`
previous | URL trang kết quả phía trước, nếu trang trước không có dữ liệu thì trả về `null`
results | Một mảng các đối tượng target, trong đó các thành phần được mô tả dưới đây
verification | Tình trạng xác thực chủ sở hữu, là 0/1 đại diện cho `đã xác thực chủ sở hữu` / `chưa xác thực chủ sở hữu`
href | Đường dẫn đến endpoint xem thông tin chi tiết target. Địa chỉ tuyệt đối = `https://api.cystack.io` + `đường dẫn`
id | ID của target
address | Địa chỉ truy cập target


<aside class="notice">
Lưu ý — Request phải gửi kèm API Key
</aside>

# Scan

## Khái niệm
Một scan tương ứng với một lần quét lỗ hổng, trên một target xác định

## Tạo scan

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def create_scan():
    endpoint = "%s/scans" % ROOT_URL
    post_data = {
        "target": "395a2f59-ad32-4e1c-b9b0-4063a55cf4e2",
        "profile": 5,
        "notification": [
            34
        ],
        "description": "string",
        "advances": {
            "headers": {"ping": "123"},
            "authentication": {
                "authentication_method": "cookie",
                "username": "",
                "password": "",
                "log_out_url": "",
                "cookie": "cookieeeeeeeeeeeeeeeeee",
                "username_field": "",
                "password_field": "",
                "auth_url": "",
                "check_url": "",
                "check_string": ""
            }
        }
    }

    r = requests.post(endpoint, headers=AUTHENTICATION_HEADER, json=post_data)
    if r.status_code == 201:
        return r.headers['Location']
    return None

print create_scan()
```

```shell
curl -i -s -k  -X $'POST' \
    -H $'Authorization: Bearer cystackapiexample' -H $'content-type: application/json' \
    --data-binary $'{\"target\": \"395a2f59-ad32-4e1c-b9b0-4063a55cf4e2\",\"profile\": 5,\"notification\": [34],\"description\": \"string\",\"advances\": {\"headers\": {\"ping\": \"123\"},\"authentication\": {\"authentication_method\": \"cookie\",\"username\": \"\",\"password\": \"\",\"log_out_url\": \"\",\"cookie\": \"cookieeeeeeeeeeeeeeeeee\",\"username_field\": \"\",\"password_field\": \"\",\"auth_url\": \"\",\"check_url\": \"\",\"check_string\": \"\"}}}' \
    $'https://api.cystack.io/v1/scans'
```

> Nếu thành công, HTTP Response sẽ có Status code 201 và trong header chứa location tham chiếu đến đối tượng mới được tạo

```json
{"Location": "/v1/scans/65fa9150-fbca-49ce-bac6-586e58cd36f5"}
```

Endpoint này cho phép tạo mới 1 scan

### HTTP Request

`POST https://api.cystack.io/v1/scans`

### Các tham số trong JSON Body

Tham số | Mô tả
--------- | -----------
target | ID của target cần Scan (String)
profile | Profile Scan (Integer)
notification | Danh sách người nhận thông báo (Integer List)
description | Thông tin mô tả về scan (String)
advances | Các thông tin nâng cao khác, tùy chọn (Object)
headers | Danh sách các header muốn thêm vào trong request (Dictionary)
authentication | Thông tin xác thực (Object)
authentication_method | Kiểu xác thực, các giá trị hợp lệ bao gồm `form`, `cookie`, `basic`
username | Tên đăng nhập (String)
password | Password (String)
log_out_url | URL logout, dùng để tránh crawl địa chỉ này (String)
cookie | Giá trị cookie (String)
username_field | Tên biến ứng với username trong form authentication (String)
password_field | Tên biến ứng với password trong form authentication (String)
auth_url | URL login (String)
check_url | URL bất kỳ chứa `check_string`, đây là URL chỉ nhìn thấy được sau khi xác thực thành công (String)
check_string | Từ khóa, chuỗi muốn kiểm tra có tồn tại hay không (String)

## Liệt kê scan

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def list_scan():
    endpoint = "%s/scans" % ROOT_URL
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)
```

```shell
curl "https://api.cystack.io/v1/scans"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{  
   "count":1,
   "next":null,
   "previous":null,
   "results":[  
      {  
         "status":"Stopped",
         "profile":"Full Audit",
         "vulnerabilities":0,
         "start_time":"13:58:01 20/11/2017",
         "href":"/v1/scans/07c13583-a14d-43b7-814d-a8f6db64253d",
         "target":{  
            "ip":"42.112.22.134",
            "os":"Unknown",
            "id":"03c0e5ad-767e-4c86-8adb-85509965fd54",
            "address":"https://cystack.net"
         }
      }
   ]
}
```

Endpoint này liệt kê tất cả các scan hiện có của user (ứng với API key)

### HTTP Request

`GET https://api.cystack.io/v1/scans`

### Các tham số theo URL

Tham số | Mô tả
--------- | -----------
page | Mặc định, kết quả trả về sẽ được phân trang với 10 kết quả/trang. Tham số page nhận vào một số tự nhiên đại diện cho trang cần lấy
q | Từ khóa dùng để lọc target
ordering | Giá trị được ưu tiên sắp xếp. Giá trị tham số hợp lệ `?ordering=target`, `?ordering=start_time`, `?ordering=vulnerabilities`, `?ordering=target,start_time,vulnerabilities`. Nếu muốn sắp xếp theo chiều ngược lại, thêm dấu `-` vào trước giá trị cần sắp xếp chẳng hạn `?ordering=-target`
status | Lọc theo trạng thái của scan, các giá trị hợp lệ bao gồm `Stopped`, `Running`, `Stopping`, `Queued`

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
count | Số lượng kết quả lấy được
next | URL trang kết quả tiếp theo, nếu trang tiếp theo không có dữ liệu thì trả về `null`
previous | URL trang kết quả phía trước, nếu trang trước không có dữ liệu thì trả về `null`
results | Một mảng các đối tượng scan, trong đó các thành phần được mô tả dưới đây
profile | Tên profile scan được sử dụng trong scan này
vulnerabilities | Số lượng lỗ hổng tìm thấy
start_time | Thời gian bắt đầu scan
href | Đường dẫn đến endpoint xem thông tin chi tiết scan. Địa chỉ tuyệt đối = `https://api.cystack.io` + `đường dẫn`
target | Một dictionary chứa các thông tin của target
ip | IP của target
os | Hệ điều hành mà target đang sử dụng, trong trường hợp không xác định được thì trả về `Unknown`
id | ID của target
address | Địa chỉ của target

## Dừng một scan

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def stop_scan(scan_id):
    endpoint = "%s/scans/%s/stop" % (ROOT_URL, scan_id)
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)
    
print stop_scan("9ba42a5f-f31d-4f18-9812-c7f18eca09e8")
```

```shell
curl "https://api.cystack.io/v1/scans/9ba42a5f-f31d-4f18-9812-c7f18eca09e8/stop"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau (trong đó trường `status = Stopped`):

```json
{
  "status": "Stopped",
  "profile": "Full Audit",
  "description": "None",
  "noti_href": [
    "/v1/notifications/29"
  ],
  "start_time": 1511957637,
  "stop_time": 1511958637,
  "vuln_href": [
    "/v1/vulnerabilities/4b2dad22-3fc7-4719-a67c-2bcd0317b632",
    "/v1/vulnerabilities/5c067b1e-ffc6-41fc-9678-352cd391cc3d",
  ],
  "target": {
    "ip": "42.112.22.134",
    "os": "Unknown",
    "id": "395a2f59-ad32-4e1c-b9b0-4063a55cf4e2",
    "address": "https://cystack.net"
  }
}
```

Endpoint này cho phép dừng 1 scan đang chạy

### HTTP Request

`GET https://api.cystack.io/v1/scans/<scan_id>/stop`

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
status | Trạng thái của Scan
profile | Tên profile scan được sử dụng trong scan này
description | Mô tả về scan
start_time | Thời gian bắt đầu scan
start_time | Thời gian kết thúc scan
noti_href | Một danh sách tham chiếu đến những người được notify
vuln_href | Một danh sách tham chiếu đến các lỗ hổng tìm thấy
target | Một dictionary chứa các thông tin của target
ip | IP của target
os | Hệ điều hành mà target đang sử dụng, trong trường hợp không xác định được thì trả về `Unknown`
id | ID của target
address | Địa chỉ của target

## Xem chi tiết một scan

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def detail_scan(scan_id):
    endpoint = "%s/scans/%s" % (ROOT_URL, scan_id)
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)
    
print detail_scan("9ba42a5f-f31d-4f18-9812-c7f18eca09e8")
```

```shell
curl "https://api.cystack.io/v1/scans/9ba42a5f-f31d-4f18-9812-c7f18eca09e8"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{
  "status": "Stopped",
  "profile": "Full Audit",
  "description": "None",
  "noti_href": [
    "/v1/notifications/29"
  ],
  "start_time": 1511957637,
  "stop_time": 1511958637,
  "vuln_href": [
    "/v1/vulnerabilities/4b2dad22-3fc7-4719-a67c-2bcd0317b632",
    "/v1/vulnerabilities/5c067b1e-ffc6-41fc-9678-352cd391cc3d",
  ],
  "target": {
    "ip": "42.112.22.134",
    "os": "Unknown",
    "id": "395a2f59-ad32-4e1c-b9b0-4063a55cf4e2",
    "address": "https://cystack.net"
  }
}
```

Endpoint này trả về thông tin chi tiết của 1 scan

### HTTP Request

`GET https://api.cystack.io/v1/scans/<scan_id>`

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
status | Trạng thái của Scan
profile | Tên profile scan được sử dụng trong scan này
description | Mô tả về scan
start_time | Thời gian bắt đầu scan
start_time | Thời gian kết thúc scan
noti_href | Một danh sách tham chiếu đến những người được notify
vuln_href | Một danh sách tham chiếu đến các lỗ hổng tìm thấy
target | Một dictionary chứa các thông tin của target
ip | IP của target
os | Hệ điều hành mà target đang sử dụng, trong trường hợp không xác định được thì trả về `Unknown`
id | ID của target
address | Địa chỉ của target

## Xóa một scan

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def delete_scan(scan_id):
    endpoint = "%s/scans/%s" % (ROOT_URL, scan_id)
    r = requests.delete(endpoint, headers=AUTHENTICATION_HEADER)
    if r.status_code == 204:
        return True
    return False
    
print delete_scan("9ba42a5f-f31d-4f18-9812-c7f18eca09e8")
```

```shell
curl -i -s -k  -X $'DELETE' \
    -H $'Authorization: Bearer cystackapiexample' -H $'content-type: application/json' \
    $'https://api.cystack.io/v1/scans/098d3ca7-d461-4f15-ad17-909158a41966'
```

> Nếu thành công, HTTP Response sẽ có Status code `204` No Content và không có Response Body

Endpoint này cho phép xóa 1 scan

### HTTP Request

`DELETE https://api.cystack.io/v1/scans/<scan_id>`


# Vulnerability

## Khái niệm
Một Vulnerability tương ứng với một lỗ hổng tìm được sau khi scan

## Liệt kê lỗ hổng

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def list_vulnerability():
    endpoint = "%s/vulnerabilities" % ROOT_URL
    print endpoint
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)


print list_vulnerability()
```

```shell
curl "https://api.cystack.io/v1/vulnerabilities"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{
  "count": 2,
  "next": "https://cystack.io/v1/vulnerabilities?page=2",
  "previous": null,
  "results": [
    {
      "false_positive": false,
      "severity": "Medium",
      "name": "Click-Jacking vulnerability",
      "href": "/v1/vulnerabilities/060d674c-fb48-4182-83db-85e0323b8fd1",
      "id": "060d674c-fb48-4182-83db-85e0323b8fd1",
      "target": "https://cystack.net"
    }
  ]
}
```

Endpoint này liệt kê tất cả các lỗ hổng hiện có của user (ứng với API key)

### HTTP Request

`GET https://api.cystack.io/v1/vulnerabilities`

### Các tham số theo URL

Tham số | Mô tả
--------- | -----------
page | Mặc định, kết quả trả về sẽ được phân trang với 10 kết quả/trang. Tham số page nhận vào một số tự nhiên đại diện cho trang cần lấy
q | Từ khóa dùng để lọc vulnerability và target
ordering | Giá trị được ưu tiên sắp xếp. Giá trị tham số hợp lệ `?ordering=severity`, `?ordering=target`, `ordering=false_positive`, `ordering=name`, `?ordering=severity,false_positive,name,target`. Nếu muốn sắp xếp theo chiều ngược lại, thêm dấu `-` vào trước giá trị cần sắp xếp chẳng hạn `?ordering=-target`
sev | Lọc theo mức độ nguy hiểm của lỗi, các giá trị hợp lệ bao gồm `High`, `Medium`, `Low`, `Information`

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
count | Số lượng kết quả lấy được
next | URL trang kết quả tiếp theo, nếu trang tiếp theo không có dữ liệu thì trả về `null`
previous | URL trang kết quả phía trước, nếu trang trước không có dữ liệu thì trả về `null`
results | Một mảng các đối tượng vulnerabilities, trong đó các thành phần được mô tả dưới đây
false_positive | Lỗi có phải false positive hay không
severity | Mức độ nguy hiểm của lỗi
name | Tên của lỗi
href | Đường dẫn đến endpoint xem thông tin chi tiết vulnerability
id | ID của vulnerability
target | Địa chỉ của target

## Xem chi tiết lỗ hổng

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def detail_vulnerability(vuln_id):
    endpoint = "%s/vulnerabilities/%s" % (ROOT_URL, vuln_id)
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)


print detail_vulnerability('060d674c-fb48-4182-83db-85e0323b8fd1')
```

```shell
curl "https://api.cystack.io/v1/vulnerabilities/060d674c-fb48-4182-83db-85e0323b8fd1"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{
  "traffic_hrefs": [
    "/v1/vulnerabilities/060d674c-fb48-4182-83db-85e0323b8fd1/traffics/5a1b8babcca1e02c146c4afa"
  ],
  "description": "The whole target has no protection (X-Frame-Options header) against Click-Jacking attacksClickjacking (User Interface redress attack, UI redress attack, UI redressing) is a malicious technique of tricking a Web user into clicking on something different from what the user perceives they are clicking on, thus potentially revealing confidential information or taking control of their computer while clicking on seemingly innocuous web pages.",
  "long_description": "The server didn't return an `X-Frame-Options` header which means that this website could be at risk of a clickjacking attack. The `X-Frame-Options` HTTP response header can be used to indicate whether or not a browser should be allowed to render a page inside a frame or iframe. Sites can use this to avoid clickjacking attacks, by ensuring that their content is not embedded into other sites.",
  "scan": {
    "target": {
      "id": "395a2f59-ad32-4e1c-b9b0-4063a55cf4e2",
      "address": "https://cystack.net"
    },
    "id": "06b804ed-2775-4e35-90d2-bd5a047762c3"
  },
  "severity": "Medium",
  "solution": "Configure your web server to include an X-Frame-Options header.",
  "plugin_name": "click_jacking",
  "false_positive": false,
  "references": [
    {
      "url": "http://tools.ietf.org/html/rfc7034",
      "title": "RFC-7034"
    },
    {
      "url": "https://developer.mozilla.org/en-US/docs/Web/HTTP/X-Frame-Options",
      "title": "Mozilla developer network"
    },
    {
      "url": "https://www.owasp.org/index.php/Clickjacking",
      "title": "OWASP Clickjacking document"
    },
    {
      "url": "http://vulndb.github.io/vuln/53",
      "title": "Vulndb-53"
    }
  ],
  "urls": [
    "None"
  ],
  "var": "",
  "attributes": {},
  "id": "060d674c-fb48-4182-83db-85e0323b8fd1",
  "name": "Click-Jacking vulnerability"
}
```

Endpoint này trả về thông tin chi tiết của 1 vulnerability

### HTTP Request

`GET https://api.cystack.io/v1/vulnerabilities/<vuln_id>`

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
traffic_hrefs | Danh sách href các traffic của vulnerability
description | Mô tả về lỗi
long_description | Mô tả thêm về loại lỗi
severity | Mức độ nguy hiểm của vulnerability
solution | Cách xử lý vulnerability
plugin_name | Tên plugin phát hiện ra lỗi
false_positive | Lỗi có phải false positive hay không
scan | Thông tin về đối tượng scan, trong đó bao gồm scan_id và target
references | Danh sách các tài liệu tham chiếu đến lỗi
urls | Danh sách các URL bị lỗi
var | Tham số chứa lỗi
attributes | Các thuộc tính của lỗi (nâng cao)
id | ID của lỗi
name | Tên của lỗi

## Cập nhật lỗ hổng

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def update_vulnerability(vuln_id):
    endpoint = "%s/vulnerabilities/%s" % (ROOT_URL, vuln_id)
    data = {"false_positive": True}
    r = requests.put(endpoint, headers=AUTHENTICATION_HEADER, json=data)
    return json.loads(r.text)


print update_vulnerability('060d674c-fb48-4182-83db-85e0323b8fd1')
```

```shell
curl -i -s -k  -X 'PUT' "https://api.cystack.io/v1/vulnerabilities/060d674c-fb48-4182-83db-85e0323b8fd1" \
    -H "Authorization: Bearer cystackapiexample" \
    --data-binary $'{\"false_positive\":true}' 
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{
  "traffic_hrefs": [
    "/v1/vulnerabilities/060d674c-fb48-4182-83db-85e0323b8fd1/traffics/5a1b8babcca1e02c146c4afa"
  ],
  "description": "The whole target has no protection (X-Frame-Options header) against Click-Jacking attacksClickjacking (User Interface redress attack, UI redress attack, UI redressing) is a malicious technique of tricking a Web user into clicking on something different from what the user perceives they are clicking on, thus potentially revealing confidential information or taking control of their computer while clicking on seemingly innocuous web pages.",
  "long_description": "The server didn't return an `X-Frame-Options` header which means that this website could be at risk of a clickjacking attack. The `X-Frame-Options` HTTP response header can be used to indicate whether or not a browser should be allowed to render a page inside a frame or iframe. Sites can use this to avoid clickjacking attacks, by ensuring that their content is not embedded into other sites.",
  "scan": {
    "target": {
      "id": "395a2f59-ad32-4e1c-b9b0-4063a55cf4e2",
      "address": "https://cystack.net"
    },
    "id": "06b804ed-2775-4e35-90d2-bd5a047762c3"
  },
  "severity": "Medium",
  "solution": "Configure your web server to include an X-Frame-Options header.",
  "plugin_name": "click_jacking",
  "false_positive": true,
  "references": [
    {
      "url": "http://tools.ietf.org/html/rfc7034",
      "title": "RFC-7034"
    },
    {
      "url": "https://developer.mozilla.org/en-US/docs/Web/HTTP/X-Frame-Options",
      "title": "Mozilla developer network"
    },
    {
      "url": "https://www.owasp.org/index.php/Clickjacking",
      "title": "OWASP Clickjacking document"
    },
    {
      "url": "http://vulndb.github.io/vuln/53",
      "title": "Vulndb-53"
    }
  ],
  "urls": [
    "None"
  ],
  "var": "",
  "attributes": {},
  "id": "060d674c-fb48-4182-83db-85e0323b8fd1",
  "name": "Click-Jacking vulnerability"
}
```

Endpoint này giúp cập nhật trạng thái `false_positive` của một lỗi

### HTTP Request

`PUT https://api.cystack.io/v1/vulnerabilities/<vuln_id>`

### Các tham số trong json body

Tham số | Mô tả
--------- | -----------
false_positive | Cho biết lỗi này có bị false positive hay không. Các giá trị hợp lệ là `true` và `false`

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
traffic_hrefs | Danh sách href các traffic của vulnerability
description | Mô tả về lỗi
long_description | Mô tả thêm về loại lỗi
severity | Mức độ nguy hiểm của vulnerability
solution | Cách xử lý vulnerability
plugin_name | Tên plugin phát hiện ra lỗi
false_positive | Lỗi có phải false positive hay không
scan | Thông tin về đối tượng scan, trong đó bao gồm scan_id và target
references | Danh sách các tài liệu tham chiếu đến lỗi
urls | Danh sách các URL bị lỗi
var | Tham số chứa lỗi
attributes | Các thuộc tính của lỗi (nâng cao)
id | ID của lỗi
name | Tên của lỗi

# Monitoring

## Khái niệm
Monitoring là chức năng giám sát tình trạng hoạt động của website, gồm tình trạng up/down và tình trạng an ninh

## Lấy thông tin về trạng thái up/down

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def up_down(target_id):
    endpoint = "%s/targets/%s/monitor_result" % (ROOT_URL, target_id)
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)


print up_down("de3b1674-a507-4293-a360-50f8ffe3a921")
```

```shell
curl "https://api.cystack.io/v1/targets/de3b1674-a507-4293-a360-50f8ffe3a921/monitor_result"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
[
  {
    "url": "https://cystack.net",
    "status_code": 200,
    "start_time": 1514969894,
    "target_id": "de3b1674-a507-4293-a360-50f8ffe3a921",
    "is_up": true,
    "timeout": 30,
    "id": "5a4c9b2608b54160f50f4e64",
    "response_time": 0.5933599472045898,
    "notify": false
  }
]
```

Endpoint này cho phép truy vấn tình trạng Up/Down của 1 target. Số lượng phần tử trong mảng trả về tương ứng với số thời điểm kiểm tra, trong khoảng thời gian từ `now - interval_time * 30` đến `now` (`now` là thời điểm hiện tại)

### HTTP Request

`GET https://api.cystack.io/v1/targets/<target_id>/monitor_result`

### Các tham số theo URL

Tham số | Mô tả
--------- | -----------
target_id | ID của target

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
url | Địa chỉ target (String)
status_code | HTTP Code trả về khi request đến địa chỉ trên (Integer)
start_time | Thời điểm request kiểm tra (Unix Time Stamp)
target_id | ID của target (String)
is_up | Trạng thái up/down (Boolean)
timeout | Thời gian timeout khi request (Second)
id | ID của lượt request (String)
response_time | Thời gian phản hồi của target (Float, second)
notify | Gửi cảnh báo đến người dùng hay không (Boolean)

## Lấy thông tin cấu hình kiểm tra up/down

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def get_settings(target_id):
    endpoint = "%s/targets/%s/monitoring" % (ROOT_URL, target_id)
    r = requests.get(endpoint, headers=AUTHENTICATION_HEADER)
    return json.loads(r.text)


print get_settings("de3b1674-a507-4293-a360-50f8ffe3a921")
```

```shell
curl "https://api.cystack.io/v1/targets/de3b1674-a507-4293-a360-50f8ffe3a921/monitoring"
  -H "Authorization: Bearer cystackapiexample"
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{
  "interval": 5,
  "is_activated": true,
  "notifications": [
    20
  ]
}
```

Endpoint này cho phép truy vấn các cấu hình được thiết lập để kiểm tra tình trạng up/down của target

### HTTP Request

`GET https://api.cystack.io/v1/targets/<target_id>/monitoring`

### Các tham số theo URL

Tham số | Mô tả
--------- | -----------
target_id | ID của target

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
interval | Khoảng thời gian giữa 2 lần kiểm tra gần nhất (Integer, Minute)
is_activated | Kích hoạt hay không việc kiểm tra up/down cho target (Boolean)
notifications | Danh sách ID của những người nhận email thông báo (Array Integer)

## Cập nhật cấu hình kiểm tra up/down

```python
import requests
import json

ROOT_URL = 'https://api.cystack.io/v1'
API_KEY = 'cystackapiexample'
AUTHENTICATION_HEADER = {'Authorization': 'Bearer %s' % API_KEY}


def update_settings(data):
    endpoint = "%s/targets/%s/monitoring" % (ROOT_URL, data['target_id'])
    payload = {"interval": data["interval"], "is_activated": data["is_activated"],
               "notifications": data["notifications"]}
    r = requests.put(endpoint, headers=AUTHENTICATION_HEADER, json=payload)
    return json.loads(r.text)


print update_settings(data = {"target_id": "de3b1674-a507-4293-a360-50f8ffe3a921", "interval": 10, "is_activated": 0, "notifications": [20]})
```

```shell
curl -i -s -k  -X $'PUT' \
    -H $'Authorization: Bearer cystackapiexample' -H $'content-type: application/json' \
    --data-binary $'{\"interval\":10,\"is_activated\":false,\"notifications\":[20]}' \
    $'https://api.cystack.io/v1/targets/de3b1674-a507-4293-a360-50f8ffe3a921/monitoring'
```

> Nếu thành công, kết quả nhận được sẽ là JSON như sau:

```json
{
  "interval": 5,
  "is_activated": true,
  "notifications": [
    20
  ]
}
```

Endpoint này cho phép thiết lập lại các cấu hình kiểm tra tình trạng up/down của target

### HTTP Request

`PUT https://api.cystack.io/v1/targets/<target_id>/monitoring`

### Các tham số theo URL

Tham số | Mô tả
--------- | -----------
target_id | ID của target

### Ý nghĩa kết quả trả về

Key | Mô tả
--------- | -----------
interval | Khoảng thời gian giữa 2 lần kiểm tra gần nhất (Integer, Minute)
is_activated | Kích hoạt hay không việc kiểm tra up/down cho target (Boolean)
notifications | Danh sách ID của những người nhận email thông báo (Array Integer)