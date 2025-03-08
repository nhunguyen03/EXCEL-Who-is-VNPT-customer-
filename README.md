# [EXCEL] Who is VNPT's customer?
## I. Introduction
### 1. About project
This project uses **hypothetical data from VNPT Express** from 2023 to the present (February 17, 2025) on goods imports (from international markets to Vietnam), focusing on analyzing consignees and answering the question: **"Who are VNPT Express's customers?"**

The project emphasizes data processing and visualization using Excel (a dynamic dashboard that allows customization based on relevant conditions).

**The analysis results provide insights into:**
- **Who the consignees** (importers) primarily are
- **Where they live**
- **The retention rate** of each customer segment
- **The types of goods** they frequently import
- **Who the shippers** (senders) are
- **Current service selection trends** — whether customers prioritize convenience or cost optimization

Link dataset here: 
[IMP 2023](https://docs.google.com/spreadsheets/d/1PRKKMJeQ_g72SO5WfC0ZVF-QPpAOHL6FfMJMdKbiSyc/edit?gid=2095503206#gid=2095503206)
[IMP 2024](https://docs.google.com/spreadsheets/d/1NdU2WDDOMpUwWqjwCzUMdlNj-F9twI-3MfOebMxV39Q/edit?gid=1804060942#gid=1804060942)

### 2. Explain dataset
- **Startclock Date**: ngày ghi nhận đơn hàng
- **Waybill Number** - **Primary Key**: mã đơn hàng
- **Product Group**: loại hàng hóa (DOX - document; WPX - package). DOX chỉ chứa document; WPX chứa cả document và goods
- **Billing Account Name**: tên đơn vị vận chuyển (VNPT EXPRESS LTD)
- **CODE**: mã code đơn hàng
- **Operation Service Codes**: mã dịch vụ vận hành, xác định các loại dịch vụ của 1 lô hàng

  [Tham khảo thêm các mã dịch vụ tại đây](https://docs.rocketshipit.com/rs/docs/dhl-addons.html?utm_source=chatgpt.com)

- **Contents**: các hàng hóa trong lô hàng
- **Shipper address**: địa chỉ người gửi hàng
- **Shipper City**: thành phố người gửi hàng
- **Shipper Company Name**: tên người gửi hàng
- **Destination Country/Territory Area Code**: mã vùng quốc gia đích đến
- **Consignee Company Name**: tên người nhận hàng
- **Origin Facility**: mã kho vận chuyển
- **Operations Shipper Reference**: số tham chiếu do người gửi cung cấp, để quản lý và theo dõi quá trình vận chuyển
- **Currency Selected Conversion Rate**: tỷ giá chuyển đổi tiền tệ được chọn. Ở đây là 1 nghĩa là đơn vị tiền được lựa chọn là USD
- **Billing Shipper Reference**: số tham chiếu liên kết với hóa đơn vận chuyển do người gửi (shipper) tạo ra
- **MAC Code**: mã sản phẩm
- **Consignee Address**: địa chỉ người nhận hàng
- **Consignee City**: thành phố người nhận hàng
- **Calculated Number of Pieces**: số lượng kiện hàng trong lô hàng
- **Count of Shipments**: số lần vận chuyển lô hàng
- **Billing Net Weight Charge**: phí tính theo trọng lượng lô hàng
- **Billed Weight**: trọng lượng lô hàng
 
## II. PROCESS DATA
[Link Process Data here](https://docs.google.com/spreadsheets/d/1M1qsbT4Tony5o0XI9C94FpjU1QE5w-9pA6D4HanNQVQ/edit?gid=2095503206#gid=2095503206) 

### 1. Clean data
Kiểm tra giá trị từng cột bằng các hàm đơn giản UNIQUE, QUERY, COUNTIFS, IF.... Phát hiện một số lỗi sai sau:
- Cột **Operations Shipper Reference**: có 4 giá trị không đúng format '30-'

![image](https://github.com/user-attachments/assets/9dfa5b92-5a6c-4762-87ef-5281b1198534)

- Cột **Shipper City**: các thành phố bị lặp lại bằng các tên khác nhau

![image](https://github.com/user-attachments/assets/44016255-dea7-4e90-9531-c462c84a030d)

- Cột **Destination Country/Territory Area Code**: có 4 giá trị sai - Consignee City không khớp với Destination Country. **Tất cả đơn hàng đều đến VN**

![image](https://github.com/user-attachments/assets/1f6d9d0e-7403-4fd4-92dd-0fd9e0867713)

- Cột **Consignee City**: có nhiều giá trị cho 1 thành phố

![image](https://github.com/user-attachments/assets/ffa7356f-f411-4ebd-90cf-b9aaabf8dd51)


Xử lý các lỗi sai trên bằng hàm XLOOKUP kết hợp với các hàm cơ bản IFS, IF, COUNTBLANK,.... Kết quả trả về dataset đã được làm sạch (**clean_data**)

### 2. Enrich data
Để thuận tiện cho việc visualization ở bước sau, ta cần thêm một số cột như sau:
- Cột **Fee (T/F), Extra charge (T/F), Surcharge (T/F)**: phân loại dịch vụ được bao gồm trong đơn hàng. **Fee** là phí vận chuyển cố định; **Extra charge** là phí dịch vụ (optional tùy vào khách hàng); **Surcharge** là phụ phí (phụ phí nhiên liệu, vận chuyển hàng hóa nguy hiểm, vận chuyển hàng hóa bị hạn chế bởi quy định quốc tế, vận chuyển vùng sâu vùng xa, điểm đến hạn chế,...)

![image](https://github.com/user-attachments/assets/aa035e47-d72e-4946-9e04-af307484d89d)

- Cột **Shipper Country**: quốc gia của người gửi hàng

![image](https://github.com/user-attachments/assets/70418a04-343d-4243-951a-6eb17447786a)

- Cột **Shipper Type**: segment khách hàng. Dựa trên cột **Shipper Company Name** để suy đoán loại khách hàng. Ví dụ khách hàng 'Embassy...' được phân loại là Government; khách hàng '... LTD' được phân loại là Business

![image](https://github.com/user-attachments/assets/e6e2676a-22c1-41a8-97b9-31e08d68f8e0)

- Cột **Consignee Type**: segment khách hàng. Dựa trên cột **Consignee Company Name** để suy đoán loại khách hàng. Ví dụ khách hàng 'Embassy...' được phân loại là Government; khách hàng '... LTD' được phân loại là Business 

![image](https://github.com/user-attachments/assets/d33ae898-cd50-44d7-ba47-79bbc368ef1c)

- Cột **Active/ Inactive/ New**: phân loại khách hàng
  + Active: có giao dịch nhiều hơn 1 năm _(VD: khách hàng phát sinh giao dịch đầu năm 2023, đến năm 2025 mới có giao dịch tiếp theo. Thì họ là active customer)_
  + Inactive: chỉ có giao dịch trong 1 năm, không có giao dịch ở những năm còn lại
  + New: có giao dịch ở năm hiện tại (2025), không có giao dịch vào những năm trước

![image](https://github.com/user-attachments/assets/4270cfe2-7763-4eed-8934-597628cdbbf2)

- Cột **Year, MonthYear, Date**: phục vụ cho mục đích visualize dữ liệu theo năm (YoY) hoặc tháng (MoM)

  ![image](https://github.com/user-attachments/assets/06e3bee4-ed6d-402c-ba3a-d512ad87dfdd)

## III. VISUALIZE DATA
[Link Visualize Data here](https://docs.google.com/spreadsheets/d/1t6nKFCS6BEpNehD_rQ-PWZYZhF4Os0plHUIz3Sg8mCY/edit?gid=1013104594#gid=1013104594)

### 1. Static dashboard
**CÁCH LÀM**: Dashboard tĩnh được visualize dựa trên lớp data tĩnh **InteractiveData**

Overview dashboard gồm 2 chỉ số quan trọng là **tổng khách hàng (_Num of consignee_)** và **tổng doanh thu (_Total revenue_)**.

Doanh thu được break down theo 2 dimensions: 
- **Loại hàng hóa (_product type_)** - để xác định loại hàng hóa chủ yếu mà VNPT Express vận chuyển >> từ đó, tạo thêm các dịch vụ phụ (Extra charge) phù hợp với loại hàng hóa để tăng doanh số
- **Nhóm khách hàng (_consignee type_)** - để xác định nhóm khách hàng chủ lực >> từ đó, có những chính sách giữ chân

Nhìn số liệu **revenue qua thời gian (MoM và YoY)** để thấy những điểm bất thường (Ví dụ doanh thu đột ngột giảm vào tháng 2-4/2025). Ngoài ra, các chỉ số **retention rate** và **AOV** (chi tiêu trung bình trên đơn hàng) cũng giúp end-user hiểu tổng quát về từng nhóm khách hàng

![image](https://github.com/user-attachments/assets/aba97e33-839f-457f-85ae-5aaa07f76d65)

### 2. Dynamic dashboard
**CÁCH LÀM**: Ý tưởng của dashboard động là tạo 1 lớp data (**DetailData**) có thể thay đổi được theo filter. Sau đó, visualize các chỉ số trên lớp data đó **_(concept tương tự như page-level filter trong PBI)_**. Dùng hàm QUERY kết hợp với các hàm nâng cao như REGEXECTRACT, TEXTJOIN, ... để tự động hóa việc dữ liệu thay đổi theo filter

Detail dashboard gồm các chỉ số quan trọng: **số lượng khách hàng (_Num of consignee_)**, **tổng doanh số (_Total revenue_)**, **tỷ lệ đóng góp doanh thu (_% Contribution_)**, **tỷ lệ giữ chân (_Retention rate_)** và **chi tiêu trung bình trên đơn hàng (_AOV_)**

Break down revenue theo 3 dimensions:
- **Nhóm khách hàng gửi (_Consignee type_)** - để biết đối tác gửi hàng chính của từng nhóm consignee là ai, % đóng góp doanh thu. List ra 10 quốc gia được nhập hàng nhiều nhất để xác định vị trí của đối tác
- **Loại hàng hóa (_Product type_)** - để biết loại hàng hóa mà nhóm consignee nhập chủ yếu. List ra 10 hàng hóa được vận chuyển nhiều nhất **(_Content_)**
- **Thành phố khách hàng nhận (_Consignee city_)** - để xác định các thành phố nhập hàng về nhiều nhất. Từ đó, có thể có những chính sách ưu tiên vận chuyển cho các thành phố này

Ngoài ra, tính % số lượng đơn hàng có dùng các **dịch vụ phụ (_Extra charge_)** hoặc **dịch vụ có phụ phí (_Surcharge_)** để biết xu hướng khách hàng ưu tiên việc tối ưu chi phí hay ưu tiên sự tiện lợi và an toàn. [Tham khảo các loại phí dịch vụ tại đây](https://docs.rocketshipit.com/rs/docs/dhl-addons.html?utm_source=chatgpt.com)

![image](https://github.com/user-attachments/assets/0f8cab48-a7ed-42f2-9e69-4948621636d0)

## IV. INSIGHTS AND RECOMMENDATIONS
Để biết thêm chi tiết về insights và recommendations, tham khảo tại đây:
[VNPT_Excel_Test.pdf](https://github.com/user-attachments/files/19043771/VNPT_Excel_Test.pdf)

**Trả lời cho câu hỏi "Khách hàng của VNPT Express là ai?"**
![11](https://github.com/user-attachments/assets/5e87cf1b-b6a4-45ed-b9b4-c36de5d59f37)
![12](https://github.com/user-attachments/assets/41272180-d050-48fb-b709-a142bd0fccb5)
![13](https://github.com/user-attachments/assets/921f23b1-57b6-47f3-aeca-ff1080341525)


