Mục tiêu:
1.	Tạo role mới so với role có sẵn
2.	Cấp quyền cụ thể cho role này trong từng project service
Giới thiệu
Mỗi một Openstack service Identity, compute, network...đều  một chính sách truy nhập dựa trên role. Chúng xác định cách thức mà mỗi người dùng có thể truy cập đến từng project với những thao tác cụ thể như nào, và  được định nghĩa trong file policy.json
Bất cứ khi nào một API call đến một OpenStack service được thực hiện, service’s policy engine sử dụng các định nghĩa chính sách thích hợp để xác định xem call có thể được chấp nhận hay không. Bất kỳ thay đổi nào đối với policy.json đều có hiệu lực ngay lập tức, cho phép triển khai các chính sách mới trong khi dịch vụ đang chạy.
Syntax:
Một policy file là một file text định dạng JSON. Bao gồm các policy và 
1.	Mỗi policy được định nghĩa bởi một dòng  theo định 
	       "<alias>"  : "<define alias>"
         "<target>" : "<rule>"
•	Alias: đại diện cho 1 tập role
•	Target: Hay mục tiêu của chính sách còn có thể coi như 1 hành động ("action") đại diện cho lời gọi API như: tạo máy ảo, tạo router....
•	Rule: Quy định, chỉ ra quyền role nào được phép gán
•	Ví dụ: có role "test" thuộc user "test". Định nghĩa xem role đó trong file policy.json có 2 cách như sau
Cách 1:
    "test1" : "role: test"
    "identity:create_user" : "rule: test1"
Cách 2:
   "identity:create_user" : "role: test"    	

=> Có thể định nghĩa 1 hành động này thông qua alias hoặc định nghĩa trực tiếp. Việc định nghĩa thông qua alias sẽ giúp ta định nghĩa ra 1 rule bao gồm nhiều role trong đó
2. Rule
Rule  xác định xem API call có được phép hay không, rule có thể là:
•	Always true. Các action luôn được phép . Điều này có thể được viết bằng "" (empty string), [], or "@". Trong version Mitaka "" (empty string) được hiểu sử dụng giá trị default 
•	Always false. Các action không bao giờ được phép. Được viết  "!".
•	a special check:
o	<role>:<role name>, Kiểm tra xem liệu các API credentials có bao gồm role này hay không.
o	<rule>:<rule name>, định nghĩa của một alias
•	Acomparison of two values  "value1 : value2", values có thể là:
o	Constants: Strings, numbers, true, false.
o	API attributes can be project_id, user_id or domain_id.
o	Target object attributes: là các trường từ các mô tả đối tượng trong database. 
Ví dụ trong trường hợp của API "compute:start", đối tượng là instance được start. Policy cho việc start instance có thể sử dụng thuộc tính %(project_id)s, project sở hữu instance. 
o	the flag is_admin: chỉ ra rằng đặc quyền quản trị được cấp thông qua cơ chế admin token. Admin token cho phép khởi tạo cơ sở dữ liệu identity trước khi admin role tồn tại
