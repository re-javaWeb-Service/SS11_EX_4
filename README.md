Phần 1 - Phân tích logic
1. Tại sao dự án có Line Coverage cao (90%) nhưng vẫn tiềm ẩn nhiều bug logic?
   Line Coverage (độ phủ dòng lệnh) chỉ đo lường xem một dòng code có được chạy qua ít nhất một lần trong quá trình test hay không. Tuy nhiên, nó không kiểm tra trạng thái của dữ liệu (data state), không kiểm tra các sự kết hợp khác nhau của đầu vào, và đặc biệt là không đảm bảo tất cả các luồng rẽ nhánh đã được thực thi.

Một test case sơ sài mang tính "happy path" (kịch bản hoàn hảo) có thể dễ dàng đi qua từ trên xuống dưới hàm, chạm vào hầu hết các dòng code và mang lại ảo giác an toàn với con số 90%. Nhưng thực tế, hệ thống vẫn sẽ lỗi khi người dùng thao tác ở những luồng ngoại lệ (Unhappy path) mà test case chưa bao giờ rẽ vào.

2. Phân biệt Line Coverage và Branch Coverage
   Line Coverage (Độ phủ dòng): Tỷ lệ phần trăm các dòng mã nguồn đã được thực thi bởi các bài kiểm thử.

Công thức: (Số dòng code được test chạy qua / Tổng số dòng code) * 100.

Branch Coverage (Độ phủ nhánh): Tỷ lệ phần trăm các nhánh logic (các luồng điều kiện như true/false của if-else, các case trong switch, hoặc các vòng lặp) đã được thực thi.

Công thức: (Số nhánh logic được test rẽ vào / Tổng số nhánh logic có trong code) * 100.

3. Tại sao theo dõi Branch Coverage lại quan trọng hơn?
   Trong hệ thống Quản lý Đơn hàng, logic nghiệp vụ thường rất phức tạp với nhiều điều kiện ràng buộc (ví dụ: kho còn hàng không? khách có mã giảm giá không? tài khoản có bị khóa không?).
   Branch Coverage ép buộc người viết test phải giả lập cả điều kiện Đúng và Sai cho mọi mệnh đề. Điều này giúp phát hiện sớm các lỗ hổng như:

Bỏ quên xử lý trường hợp null hoặc danh sách rỗng.

Tính toán sai khi đi vào nhánh else (ví dụ: khách hàng không có mã giảm giá bị tính sai tiền).

Các lỗi vòng lặp (vòng lặp không chạy lần nào hoặc chạy vô hạn).

Vì vậy, Branch Coverage phản ánh mức độ kiểm thử toàn diện tốt hơn nhiều so với Line Coverage để đạt được mục tiêu "ít hơn 2 lỗi nghiêm trọng mỗi tháng".

4. Hai ví dụ minh họa (Line Coverage cao nhưng Branch Coverage thấp)
   Ví dụ 1: Ẩn lỗi chia cho 0 (Missing Else branch)

Java
public double calculateAveragePrice(double totalPrice, int itemCount) {
if (itemCount == 0) {
itemCount = 1; // Gán tạm bằng 1 để tránh lỗi crash chia cho 0
}
return totalPrice / itemCount;
}
Test case: Chạy calculateAveragePrice(100.0, 0)

Line Coverage: 100% (Tất cả 5 dòng code đều được chạy qua vì điều kiện if đúng).

Branch Coverage: 50% (Chỉ nhánh itemCount == 0 mang giá trị true được chạy).

Phân tích lỗi: QA nhìn vào báo cáo thấy 100% Line Coverage sẽ nghĩ hàm này an toàn. Tuy nhiên, Branch Coverage chỉ ra rằng chúng ta chưa từng test trường hợp itemCount != 0 (nhánh false). Nếu sau này có ai đó sửa logic hoặc thêm logic vào luồng false, hệ thống sẽ không có test case nào bảo vệ.

Ví dụ 2: Lỗi thiếu kiểm tra ràng buộc logic (Single If without Else)

Java
public void processRefund(Order order) {
double refundAmount = order.getTotalPrice();

    if (order.isVipCustomer()) {
        refundAmount = refundAmount + 50.0; // VIP được đền bù thêm 50k
    }
    
    bankService.transfer(order.getCustomerId(), refundAmount);
}
Test case: Chạy processRefund(vipOrder) (Truyền vào một khách hàng VIP).

Line Coverage: 100% (Vì là khách VIP nên mã chạy thẳng vào trong khối if và chạy hết đến cuối hàm).

Branch Coverage: 50% (Chưa từng test kịch bản isVipCustomer() == false).

Phân tích lỗi: Dù Line Coverage là 100%, nhưng nếu ta không bao giờ test kịch bản khách hàng bình thường (Branch Coverage), ta có thể không nhận ra một rủi ro nghiệp vụ: Liệu khách bình thường có bị trừ phí hay gặp lỗi khi gọi hàm bankService không? Branch Coverage thấp nhắc nhở ta phải viết thêm 1 test case cho khách Non-VIP."# SS11_EX_4" 
