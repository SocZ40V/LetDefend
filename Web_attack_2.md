**Pracetive 5**
    
  	SOC169 - Possible IDOR Attack Detected  
    
Đây là một số thông tin về Rule 
![image](https://hackmd.io/_uploads/BJ0aJU_5kx.png)
Device Action :Allowed

Ip nguồn : 134.209.118.137
Ip đích : 172.16.17.15
Request URL: https://172.16.17.15/get_user_info/

Check Ip nguồn bằng AV và AbuseIPDB
![image](https://hackmd.io/_uploads/ByEdeIOqyl.png)

![image](https://hackmd.io/_uploads/HJN5lIu9Jx.png)

Ip nguồn được AV cảnh báo là Ip độc địa chỉ đến từ Mỹ domain IP là : digitalocean.com

tiếp theo đọc raw log từ log management : 
Gồm 5 alert từ Ip độc gửi đến Webserver thông qua cổng 443 

![image](https://hackmd.io/_uploads/Bkc7ZUOqJx.png)
Thông qua raw log
Ta có thể thấy Attacker gửi yêu cầu xử lý dữ liệu qua phương thức Http POST thông qua tham số ?user_id=
và các yêu cầu được server chấp nhận xử lý (status 200)
![image](https://hackmd.io/_uploads/Sk9vZL_9kx.png)

Kiểm tra EDR nào bị ảnh hưởng : Webserver1005

![image](https://hackmd.io/_uploads/Sk4PG8_5ke.png)

Kết Luận : Attacker ip độc 134.209.118.137 thông qua phương thức http POST gửi các request đến https://172.16.17.15/get_user_info/?use_id=1...5 dựa vào tham số ?used_id= yêu cầu Webserver1005 xử lý , gây hậu quả rò rỉ dữ liệu người dụng nặng hơn là gián đoạn dịch vụ, làm chậm hoặc sập máy chủ.

NGắt kết nối và hoàn thành report thôi

**Practive 6**

    SOC170 - Passwd Found in Requested URL - Possible LFI Attack
    
Đây là một số thông tin về RULE
![image](https://hackmd.io/_uploads/SJ8jjId9yl.png)

Ip nguồn :  106.55.45.162
Ip đích : 172.16.17.13
request URL : https://172.16.17.13/?file=../../../../etc/passwd

Check ip nguồn trên AV : ![image](https://hackmd.io/_uploads/r1wKh8d9yx.png)

không tìm thấy kết quả 
Mình quyết định kiểm tra bên IPAbuse
![image](https://hackmd.io/_uploads/H1VUyD_c1g.png)
Kiểm tra phần báo cáo cho rằng Ip độc
Nên mình sẽ check raw log ở log management:

![image](https://hackmd.io/_uploads/Hy5i38_cye.png)

Ok!! nhìn vào ảnh trên ta có một số thông tin sau 
Ip 106.55.45.162 gửi một phương thức http GET để lấy dữ liệu server 172.16.17.13 thông qua cổng 443. bằng URL :https://172.16.17.13/?file=../../../../etc/passwd 
tuy nhiên đã bị server không phản hồi bằng status 500.
 điều này có nghĩa attacker không tấn công thành công.
 
 kiểm tra EDR với ip 172.16.17.13 có tên là Webserver1006
 
 ![image](https://hackmd.io/_uploads/rk-kRIuqJx.png)

![image](https://hackmd.io/_uploads/SkjeALucJe.png)

khi kiểm tra Webserver1006 không có gì khác thường vì vậy mình chắc chắn đây là alert false positive.

Kết Luận : Ip độc 106.55.45.162 gửi một phương thức http GET thông qua request URL:https://172.16.17.13/?file=../../../../etc/passwd  để lấy dữ liệu từ /etc/passwd Webserver1006 nhưng không thành công.

oke thế là xong mình sẽ hoàn thành report và đóng cảnh báo 