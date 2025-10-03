# Phân tích hành vi đặt phòng khách sạn và các yếu tố ảnh hưởng đến việc huỷ đặt phòng
# 1 Mục tiêu dự án
Phân tích dữ liệu đặt phòng khách sạn để hiểu các yếu tố nào khiến khách huỷ phòng.
Khám phá mối quan hệ giữa các biến như thời gian đặt, loại khách, loại phòng, kênh đặt phòng, quốc tịch,… với hành vi huỷ
Hiểu rõ yếu tố nào làm tăng xác suất huỷ phòng
(Tuỳ chọn) Mô hình dự đoán hỗ trợ ra quyết định đặt cọc hoặc ưu tiên khách hàng
Biết được nguyên nhân hủy đặt phòng, qua đó giúp cho doanh nghiệp giảm thiểu tỉ lệ hủy đặt phòng.
# 2 Nguồn dữ liệu sử dụng
Link data set: https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand
Mô tả: Bộ dữ liệu này chứa thông tin đặt phòng cho khách sạn thành phố và khách sạn nghỉ dưỡng, bao gồm thông tin như thời điểm đặt phòng, thời gian lưu trú, số người lớn, trẻ em và trẻ sơ sinh, số chỗ đậu xe có sẵn, cùng nhiều thông tin khác.
# 3 Tổng quan dữ liệu 
## Giới thiệu bộ dữ liệu

Bộ dữ liệu được chia thành 4 nhóm biến chính, cung cấp đầy đủ thông tin để phân tích hành vi đặt phòng:

### A. Biến về Thời gian và Kỳ nghỉ
- **lead_time**: Số ngày từ khi đặt phòng đến ngày đến (ảnh hưởng lớn đến tỷ lệ hủy).  
- **arrival_date_year / month / day_of_month**: Thông tin chi tiết về ngày đến.  
- **stays_in_weekend_nights & stays_in_week_nights**: Số đêm lưu trú vào cuối tuần và ngày thường.  

### B. Biến về Khách hàng và Thông tin Booking
- **adults, children, babies**: Số lượng người lớn và trẻ em.  
- **is_repeated_guest**: Khách hàng có phải khách quen hay không.  
- **country**: Quốc gia của khách hàng (mã hóa theo chuẩn ISO).  
- **customer_type**: Loại hình khách hàng (ví dụ: Transient, Group, Contract).  

### C. Biến về Tài chính và Kênh Đặt phòng
- **market_segment**: Phân khúc thị trường (ví dụ: Online TA, Offline TA/TO, Corporate, Direct).  
- **distribution_channel**: Kênh phân phối đặt phòng.  
- **deposit_type**: Loại tiền đặt cọc (ví dụ: No Deposit, Non-Refund).  
- **adr (Average Daily Rate)**: Giá phòng trung bình hàng ngày.  
### D. Biến về Trạng thái và Yêu cầu
- **is_canceled**: Biến mục tiêu chính (0: không hủy, 1: hủy).  
- **reservation_status**: Trạng thái cuối cùng của đặt phòng (ví dụ: Check-Out, Canceled, No-Show).  
- **total_of_special_requests**: Tổng số yêu cầu đặc biệt của khách hàng.
- # 4 Công cụ và công nghệ áp dụng 
- **Ngôn ngữ**: Python
- **Xử lý & Phân tích**: pandas, numpy
- **Trực quan hóa**: matplotlib, seaborn
- **Mô hình hóa**:
- **Logistic Regression**: Mô hình hồi quy tuyến tính cho biến mục tiêu nhị phân.  
- **Random Forest**: Rừng cây ngẫu nhiên để tăng độ chính xác và giảm overfitting.  
- **XGBoost**: Mô hình boosted tree hiệu quả, tối ưu hóa dự đoán hủy phòng.
- **Môi trường**: Google Colab/Jupyter Notebook
- **Công cụ hỗ trợ**: CSV
# 5 Các insight chính và giả thuyết được đưa ra
## Insight và Giả thuyết

### 1. Insight chính từ EDA

**A. Thời gian và Kỳ nghỉ**
- Khách đặt trước **rất lâu** (lead_time dài) có xu hướng hủy cao hơn.  
- Lưu trú vào **cuối tuần** có tỷ lệ hủy thấp hơn ngày thường.

**B. Khách hàng và Thông tin Booking**
- Khách **quen** (is_repeated_guest) ít hủy hơn khách mới.  
- Booking dạng **Group** và **Contract** hủy ít hơn **Transient** (khách lẻ).  
- Một số quốc gia có tỷ lệ hủy cao hơn, có thể liên quan đến khoảng cách hoặc thói quen đặt phòng.

**C. Tài chính và Kênh đặt phòng**
- Khách có **deposit** ít hủy hơn; **No Deposit** → hủy cao nhất.  
- Giá phòng (ADR) quá cao hoặc quá thấp có thể liên quan đến hủy nhiều hơn.

**D. Trạng thái và Yêu cầu**
- Khách có nhiều **yêu cầu đặc biệt** ít hủy hơn, thể hiện mức độ cam kết cao.  
- Hầu hết hủy được ghi nhận trước ngày đến; một số **No-Show** vẫn giữ booking.

### 2. Giả thuyết tiềm năng để mô hình hóa

1. **Lead time dài → tăng khả năng hủy**  
2. **Booking không deposit → tăng khả năng hủy**  
3. **Khách lẻ/Transient → tăng khả năng hủy so với Group/Contract**  
4. **Khách mới → tăng khả năng hủy so với khách quen**  
5. **Nhiều yêu cầu đặc biệt → giảm khả năng hủy**  
6. **Lưu trú cuối tuần → giảm khả năng hủy**  
7. **ADR cực thấp hoặc cực cao → tăng khả năng hủy**
# 6 Mô hình 
- Xây dựng và so sánh các mô hình với nhau
- Lựa chọn mô hình XGBoost vì có kết quả tốt nhất (Accuracy ~79%, Precision 0.68)
# 7 Kết luận tổng thể
**Kết quả EDA** :
- Quốc gia: Bồ Đào Nha có số lượng hủy và tỷ lệ hủy cao nhất.
- Kênh đặt phòng: Online Travel Agents có tỷ lệ hủy cao nhất.
- Khách quay lại: có xu hướng ít hủy hơn khách mới.
- Yêu cầu đặc biệt: khách có nhiều yêu cầu đặc biệt thường ít hủy hơn.
- Giá phòng cao: tỷ lệ hủy cao hơn.

**Modeling**:
- Đã thử 3 mô hình: Logistic Regression, Random Forest, XGBoost.
- XGBoost đạt hiệu quả cao nhất (Accuracy 79%, Precision 0.68).
- Tuy nhiên, cả 3 mô hình đều bị lệch về dự đoán “không hủy”.
# 8 Khuyến nghị dựa trên kết quả phân tích
## Khuyến nghị
- Khuyến khích **khách đặt trước ngắn hạn** hoặc áp dụng **chính sách đặt cọc** để giảm tỷ lệ hủy.  
- Tập trung chăm sóc **khách quen và nhóm/booking hợp đồng**, vì họ ít hủy hơn.  
- Theo dõi các booking với **lead time dài**, **khách lẻ**, hoặc **khách có ADR cực thấp/cao**, để đưa ra nhắc nhở hoặc ưu đãi phù hợp.  
- Đáp ứng **yêu cầu đặc biệt của khách** để tăng mức độ cam kết và giảm hủy phòng.


