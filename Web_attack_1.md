(https://)Tiếp Theo là phần Phân Tích về Detecting Web Attacks

1. **Pratice 1 :** 	

    *SOC165 - Possible SQL Injection Payload Detected*

Đây là thông tin chi tiết về Rule

![image](https://hackmd.io/_uploads/HyTOhldc1x.png)

    *The action was ALLOWED*

Sau đó tạo playbook và làm theo yêu cầu có sẵn:


Ownership of the IP addresses and devices.

If the traffic is coming from outside (Internet);

Ownership of IP address (Static or Pool Address? Who owns it? Is it web hosting?)

Reputation of IP Address (Search in VirusTotal, AbuseIPDB, Cisco Talos)

If the traffic is coming from company network;

Hostname of the device

Who owns the device (username)


ok sau các bước trên mình sẽ bắt đầu phân tích. Đầu tiên như thông tin ban đầu đây là cuộc tấn công SQLi 

IP : 
167.99.169.17 độc tuy nhiên AV không giải thích nhiều nên mình sử dụng Abuseipdb

![image](https://hackmd.io/_uploads/HyAIx-_5yl.png)

![image](https://hackmd.io/_uploads/rkgE--_cke.png)
![image](https://hackmd.io/_uploads/S1zU--dqJe.png)
Có rất nhiều báo cáo xoay quanh về IP này có nguồn gốc từ HOA KÌ
Requested URL :
https://172.16.17.18/search/?q=%22%20OR%201%20%3D%201%20--%20-

decode: https://172.16.17.18/search/?q=" OR 1 = 1 -- -

Kiểm tra Log Management , chúng ta có thể thấy được attacker đã truy cập vào máy chủ thành công có phản hồi 200
![image](https://hackmd.io/_uploads/SJIVMbdc1g.png)

Tuy nhiên attacker không thể tiếp tục gửi phản hồi đến máy chủ 
trạng thái 500 đã chứng mình điều đó
![image](https://hackmd.io/_uploads/Hyj7fZu91g.png)

Họ đã gặp lỗi trên máy chủ.
bây giờ sẽ kiểm tra các EDR liên quan và hạn chế phát sinh và ngăn ngừa thiệt hại

Chúng ta thấy địa chỉ IP address, 167.99.169.17, là độc hại và nằm ngoài mạng của công ty vì nó không nằm trong EDR. IP nguồn được gửi đến IP đích, 172.16.17.18 là WebServer1001 trên cổng 443.

![image](https://hackmd.io/_uploads/rk2rX-dqJe.png)

Cuối cùng là kết thúc playbook và đóng vé

![image](https://hackmd.io/_uploads/ryExV-O5ye.png)
Có chọn malicious (Theo như AV )
![image](https://hackmd.io/_uploads/SJDeVb_ckx.png)
SQLi (decode URL có payload SQLi)
![image](https://hackmd.io/_uploads/BJje4Wuc1x.png)
not planned ( không tìm thấy các kế hoạch tấn công vào máy chủ)
![image](https://hackmd.io/_uploads/SkkbN-u51e.png)
Internet -> To company 
![image](https://hackmd.io/_uploads/SkbbNbu5Jx.png)
No (máy chủ phản hồi 500 sau khi threat actor dùng hàm search)
![image](https://hackmd.io/_uploads/H1V0Vb_9yl.png)
No (cuộc tấn công không thành công nên không cần)



2. **Pratice 2**

    SOC166 - Javascript Code Detected in Requested URL
    
Như thường lệ mình vẫn mở Rule và xem thông tin chi tiết 

![image](https://hackmd.io/_uploads/S16RcbuqJe.png)
Device Action : Allowed
Ip address :  112.85.42.13

Mình sẽ kiểm tra ip có malicous không
![image](https://hackmd.io/_uploads/S1D33Wucke.png)
Mình sẽ kiểm tra trên abuseipdb để kiếm thêm thông tin 
abuseipdb 
![image](https://hackmd.io/_uploads/r1hS1M_c1g.png)
Ip này xuất phát từ China với domain chinaunicom.cn
Và khi kiểm tra báo cáo thì nó độc hại ![image](https://hackmd.io/_uploads/HkIO1MOc1g.png)


Cùng kiểm tra thêm về URL: 

https://172.16.17.17/search/?q=<$script>javascript:$alert(1)<$/script>

attacker đã chèn payload kiểm tra hệ thống có dễ bị XSS hay không. NẾu thành công khi truy cập URL hệ trình duyệt sẽ hiển thị thông báo "1".

Tiếp theo mình sẽ đi kiểm tra Raw log : 
![image](https://hackmd.io/_uploads/SyyZQzO9kx.png)
![image](https://hackmd.io/_uploads/SkNzQfuc1g.png)
Dễ thấy được attacker đã được truy cập vào máy chủ
![image](https://hackmd.io/_uploads/Hk1UXzOqkx.png)
Tuy nhiên khi chèn các payload XSS 
bị trả về status 302 chứng tỏ cuộc tấn công đã bị chuyển hướng nên không thành công.

Vào EDR xem các thiết bị nào liên quan đến cuộc tấn công này:

![image](https://hackmd.io/_uploads/SkLtNzOc1x.png)

Ở đây chúng ta có nắm được thông tin như sau : 

địa chỉ 112.85.42.13, là IP độc hại và nằm ngoài mạng của công ty (không có trong danh sach EDR công ty) IP nguồn được gửi đến IP đích, 172.16.17.17 là WebServer1002 thông qua cổng 443.

 Trả lời các câu hỏi trong report nào !!! 
![image](https://hackmd.io/_uploads/HkCvHzO51g.png)
malicious
![image](https://hackmd.io/_uploads/HkvKrGdckl.png)
XSS
![image](https://hackmd.io/_uploads/SybcrGO51g.png)
Not planned đây không phải là cuộc tấn công lên kế hoạch sẵn vì kiểm tra IP/nguồn.đích.tên máy chủ /URL không có gì 
![image](https://hackmd.io/_uploads/rkyyUzOcJe.png)
internet -> company 
Vì ip 112.85.42.13 bắt nguồn từ Trung quốc và chèn payload độc hại vào web sever 1002 172.16.17.12 ip đích
![image](https://hackmd.io/_uploads/HkMwLfucyg.png)
No, cuộc tấn công bị chuyển hướng ( 302 status) 
![image](https://hackmd.io/_uploads/ByZfvGd9yg.png)
No, cuộc tấn công không thành công
![image](https://hackmd.io/_uploads/Hy1vPG_c1g.png)
Đóng ticker !!


3. **PRATICE 3 **

 
     *SOC167 - LS Command Detected in Requested URL*
  
 Thông tin chi tiết về Rule
![image](https://hackmd.io/_uploads/H18gif_9kg.png)
Device Action : Allowed
Nhận thấy Ip nguồn :  188.114.96.15

Kiểm tra ip trên Av ![image](https://hackmd.io/_uploads/HkMFjMOc1e.png)

Tiếp tục kiểm tra bên AbuseIPDB
![image](https://hackmd.io/_uploads/H1rssGO91e.png)

Kiểm tra các báo cáo ![image](https://hackmd.io/_uploads/SJWRjMOcJx.png)

Tiếp theo kiểm tra trên URL : 

https://letsdefend.io/blog/?s=skills

ở đây ta có thể thấy cảnh báo được xuất hiện lý do chứa ls 
và vô tình skills có chứa "ls".
Oke để rõ ràng hơn ta cần phân tích Raw log 

![image](https://hackmd.io/_uploads/ry2H6fucye.png)

Ở đây ta có thể thấy ip nguồn là 172.16.17.46 tức là ip từ công ty được gửi ra ngoài đến cổng 443 của ip đích 188.114.96.15 domain ở bên Đức (cloudflare) 

![image](https://hackmd.io/_uploads/Sk-cCz_qkg.png)

Ở phần raw log chúng ta có thể thấy phuong thức http GET yêu cầu tài nguyên từ máy chủ .

![image](https://hackmd.io/_uploads/BJxfR0Gdqke.png)
và được kết nối với EDR username : EloitPRD
Kiểm tra browser history và network
![image](https://hackmd.io/_uploads/SyRgkQuqye.png)
Trong browser history chúng ta có thể thấy thời gian thực hiện các hành động ngắn nhưng các URL hoàn toàn vô hại.
Và bên cạnh đó khi kiểm tra trên các AV hoặc AbuseIPDB không thấy malicious. và domain name là cloudflare.com

![image](https://hackmd.io/_uploads/rkAzy7_9Jl.png)

Hoàn toàn trùng khớp 

Sau tất cả các phân tích thì chắc chắn đây là lưu lượng an toàn.

![image](https://hackmd.io/_uploads/Bkod0NOqyx.png)
Non-Malicious
![image](https://hackmd.io/_uploads/ryKDJHdckg.png)
Yes 
![image](https://hackmd.io/_uploads/rk35JB_q1g.png)
Nó là cảnh báo False Positive.

4. **Practive 4 **
    
    	SOC168 - Whoami Command Detected in Request Body

 Tiếp tục đến với 1 Rule khác 
![image](https://hackmd.io/_uploads/r1OfxHu9yl.png)

Device Action : Allowed
Ip nguồn : 61.177.172.07
ip đích : 172.16.17.16

Check ip trên AV 
![image](https://hackmd.io/_uploads/rJwSWHOqkg.png)
cảnh báo được đưa ra là Ip độc

Đối với AbuseIPDB ta thấy được nguồn ip đến từ China và domain name ở chinatelecom.cn
![image](https://hackmd.io/_uploads/H1e9-HO9Jx.png)


Kiểm tra Raw log ở Log Management có 5 alerts ở đây:

![image](https://hackmd.io/_uploads/Hka6bBOc1g.png)

Chúng ta có thể thấy được thông tin Ip nguồn 61.177.172.87 từ China gửi đến ip đích 172.16.17.16 một EDR trên Letdefend mang tên : Webserver1004 Thông qua cổng 443

![image](https://hackmd.io/_uploads/HkpxQrO9kx.png)


![image](https://hackmd.io/_uploads/B1B5MBu5yx.png)

Dựa vào phương thức http POST attacker đã chèn các commandline và gửi chúng đến máy chủ để xử lý trong phần request body thông qua tham số ?c=.
?c=cat /etc/shadow hoặc ?c=cat /etc/passwd
Máy chủ đã chấp nhận thực thi các lệnh này (xác định ở status 200)
Tác hại làm lộ  thông tin mật khẩu được mã hóa của người dùng trên hệ thống Linux (/etc/shadow)
hoặc thông tin tài khoản người dùng(/etc/passwd)

Kiểm tra xem các EDR nào liên quan để cách ly và ngăn chặn kịp thời. 

![image](https://hackmd.io/_uploads/HylCBrucJe.png)
Việc attacker yêu cầu máy chủ xử lý các lệnh  hoàn toàn trùng khớp tại Terminal History 
![image](https://hackmd.io/_uploads/HJm1ISd5ye.png)

Tiếp theo trả lời câu hỏi và đóng ticker 

![image](https://hackmd.io/_uploads/BkG2FB_c1x.png)
Malicious 
![image](https://hackmd.io/_uploads/r1YpKH_5kg.png)
Yes
![image](https://hackmd.io/_uploads/rkOAKSO51l.png)
Malicious
![image](https://hackmd.io/_uploads/ByDJcBdqyg.png)
Command injection 
![image](https://hackmd.io/_uploads/Byux9rdcye.png)
Not Planned
![image](https://hackmd.io/_uploads/B1t-qBOqyg.png)
Internet -> company 
![image](https://hackmd.io/_uploads/HkqQqSOcJx.png)
Yes
![image](https://hackmd.io/_uploads/rJYTqrdqJx.png)
Yes
![image](https://hackmd.io/_uploads/Hyz1iHOcye.png)
true positive
