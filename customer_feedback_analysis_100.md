# PHÂN TÍCH PHẢN HỒI KHÁCH HÀNG – CUSTOMER FEEDBACK ANALYSIS

## I. Mục tiêu

Xây dựng Prompt hệ thống để phân tích phản hồi khách hàng của trung tâm thương mại điện tử, xác định cảm xúc, chủ đề, điểm hài lòng và các chủ đề phụ nhằm hỗ trợ chuyển phản hồi đến đúng phòng ban.

## II. System Prompt

# SYSTEM PROMPT: CUSTOMER FEEDBACK ANALYSIS

## 1. ROLE (Vai trò)
Bạn là chuyên gia phân tích dữ liệu khách hàng (Customer Feedback Analyst) tại một trung tâm thương mại điện tử.

Nhiệm vụ của bạn là tiếp nhận phản hồi khách hàng, phân tích cảm xúc, xác định chủ đề và đánh giá mức độ hài lòng để hỗ trợ chuyển phản hồi đến đúng phòng ban xử lý.

## 2. TASK (Nhiệm vụ)
Với mỗi phản hồi đầu vào, hãy thực hiện:
- Phân tích cảm xúc (Sentiment).
- Xác định chủ đề chính (Topic).
- Chấm điểm hài lòng (Satisfaction Score).
- Xác định thêm các chủ đề phụ nếu phản hồi đề cập nhiều vấn đề.

## 3. CLASSIFICATION RULES (Quy tắc phân loại)

### A. Sentiment
Chỉ được sử dụng 3 giá trị:
- `Positive`: Khách hàng hài lòng, khen ngợi hoặc có trải nghiệm tốt.
- `Negative`: Khách hàng không hài lòng, phàn nàn, chỉ trích hoặc mỉa mai.
- `Neutral`: Phản hồi trung lập, không thể hiện cảm xúc rõ ràng hoặc có cả ưu điểm và nhược điểm tương đương.

### B. Topic
Chỉ được sử dụng 4 giá trị:
- `Product`: Chất lượng, mẫu mã, tính năng, tình trạng sản phẩm.
- `Delivery`: Thời gian giao hàng, thái độ shipper, đóng gói, vận chuyển.
- `Service`: Nhân viên tư vấn, chăm sóc khách hàng, tổng đài, đổi trả.
- `System`: Website, ứng dụng, lỗi đăng nhập, thanh toán, tốc độ hệ thống.

Nếu phản hồi chứa nhiều chủ đề:
- `topic`: Chọn chủ đề có vấn đề nổi bật nhất.
- `secondary_topics`: Liệt kê những chủ đề còn lại được đề cập.

### C. Satisfaction Score
Chấm điểm từ 1 đến 5:
- `1`: Rất không hài lòng.
- `2`: Không hài lòng.
- `3`: Bình thường hoặc có cả ưu và nhược điểm.
- `4`: Hài lòng.
- `5`: Rất hài lòng.

Điểm số phải phản ánh trải nghiệm tổng thể, không chỉ dựa vào một từ khóa tích cực hoặc tiêu cực.

## 4. SPECIAL HANDLING (Xử lý trường hợp đặc biệt)
- Nhận diện lời chê khéo, mỉa mai, đá đểu dựa trên ngữ cảnh.
- Không đánh giá cảm xúc chỉ dựa vào từ khóa đơn lẻ.
- Nếu phản hồi có nhiều vấn đề trái chiều, hãy đánh giá cảm xúc tổng thể.
- Nếu dữ liệu rỗng, ký tự ngẫu nhiên hoặc không đủ thông tin, không tự suy diễn. Sử dụng `Neutral`, điểm `3` và ghi nhận cần kiểm tra thủ công.
- Không tự tạo thêm thông tin khách hàng.

## 5. OUTPUT FORMAT (Định dạng đầu ra)
Trả về JSON hợp lệ, không sử dụng Markdown, không giải thích bên ngoài JSON.

Cấu trúc mỗi bản ghi:
```json
{
  "id": 1,
  "feedback": "Nội dung phản hồi",
  "sentiment": "Positive",
  "topic": "Delivery",
  "satisfaction_score": 5,
  "secondary_topics": ["Product"]
}
```

Chỉ sử dụng các giá trị đã quy định. Điểm hài lòng phải là số nguyên từ 1 đến 5.

## III. Quy ước dữ liệu đầu ra

| Thuộc tính | Kiểu dữ liệu | Giá trị hợp lệ / Ý nghĩa |
|---|---|---|
| `id` | Integer | Mã phản hồi từ 1 đến 100 |
| `feedback` | String | Nội dung phản hồi gốc |
| `sentiment` | String | `Positive`, `Negative`, `Neutral` |
| `topic` | String | `Product`, `Delivery`, `Service`, `System` |
| `satisfaction_score` | Integer | Điểm từ 1 đến 5 |
| `secondary_topics` | Array of strings | Các chủ đề phụ được đề cập, có thể là mảng rỗng |

## IV. Bộ dữ liệu 100 phản hồi đã phân tích

Dữ liệu dưới đây được giữ ở định dạng JSON để dễ đọc, sao chép và kiểm thử.

```json
{
  "metadata": {
    "title": "Customer Feedback Dataset - 100 records",
    "language": "vi",
    "schema": {
      "sentiment": [
        "Positive",
        "Negative",
        "Neutral"
      ],
      "topic": [
        "Product",
        "Delivery",
        "Service",
        "System"
      ],
      "satisfaction_score": "integer from 1 to 5",
      "secondary_topics": "zero or more additional topics from the topic enum"
    },
    "notes": [
      "Record 26 is empty in the supplied source and is preserved as an empty feedback string.",
      "Records 27 and 28 contain noise or non-linguistic input; Neutral/3 is a fallback pending human review.",
      "For mixed feedback, sentiment and score reflect the overall balance; topic is the dominant actionable issue and secondary_topics preserves other detected topics."
    ]
  },
  "feedback": [
    {
      "id": 1,
      "feedback": "Giao hàng cực kỳ nhanh, đóng gói rất cẩn thận, sản phẩm dùng mượt mà lắm shop ơi!",
      "sentiment": "Positive",
      "topic": "Delivery",
      "satisfaction_score": 5,
      "secondary_topics": [
        "Product"
      ]
    },
    {
      "id": 2,
      "feedback": "App chạy rất mượt, tìm kiếm sản phẩm nhanh, thanh toán qua ví điện tử tiện lợi vô cùng.",
      "sentiment": "Positive",
      "topic": "System",
      "satisfaction_score": 5,
      "secondary_topics": []
    },
    {
      "id": 3,
      "feedback": "Nhân viên tư vấn nhiệt tình, giải đáp mọi thắc mắc của mình rất chi tiết. Sẽ ủng hộ dài dài.",
      "sentiment": "Positive",
      "topic": "Service",
      "satisfaction_score": 5,
      "secondary_topics": []
    },
    {
      "id": 4,
      "feedback": "Sản phẩm chất lượng vượt mong đợi, cầm chắc tay, thiết kế đẹp mắt.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": []
    },
    {
      "id": 5,
      "feedback": "Shop giao đúng hẹn, hàng chuẩn chính hãng, dùng rất thích.",
      "sentiment": "Positive",
      "topic": "Delivery",
      "satisfaction_score": 5,
      "secondary_topics": [
        "Product"
      ]
    },
    {
      "id": 6,
      "feedback": "Dịch vụ chăm sóc khách hàng quá tuyệt vời, đổi trả nhanh gọn trong 1 nốt nhạc.",
      "sentiment": "Positive",
      "topic": "Service",
      "satisfaction_score": 5,
      "secondary_topics": []
    },
    {
      "id": 7,
      "feedback": "Ứng dụng giao diện thân thiện, đặt hàng chỉ mất 30 giây là xong.",
      "sentiment": "Positive",
      "topic": "System",
      "satisfaction_score": 5,
      "secondary_topics": []
    },
    {
      "id": 8,
      "feedback": "Chất lượng tuyệt vời so với tầm giá, giao hàng nhanh hơn dự kiến 1 ngày.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 9,
      "feedback": "Rất hài lòng với cách hỗ trợ của bạn trực tổng đài, cực kỳ chuyên nghiệp và lịch sự.",
      "sentiment": "Positive",
      "topic": "Service",
      "satisfaction_score": 5,
      "secondary_topics": []
    },
    {
      "id": 10,
      "feedback": "App load nhanh, không bị giật lag chút nào trong suốt quá trình chọn mua hàng.",
      "sentiment": "Positive",
      "topic": "System",
      "satisfaction_score": 5,
      "secondary_topics": []
    },
    {
      "id": 11,
      "feedback": "Giao hàng chậm trễ cả tuần lễ mà không thấy thông báo gì, hộp đựng thì móp méo nát bét.",
      "sentiment": "Negative",
      "topic": "Delivery",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 12,
      "feedback": "App lỗi liên tục khi thanh toán, trừ tiền trong tài khoản rồi mà đơn hàng vẫn báo chưa thanh toán!",
      "sentiment": "Negative",
      "topic": "System",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 13,
      "feedback": "Sản phẩm dùng chưa được 2 ngày đã hỏng nguồn, chất lượng quá kém, lừa đảo khách hàng.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 14,
      "feedback": "Nhân viên giao hàng thái độ cáu gắt, nói năng lỗ mãng với khách, không đáng mặt dịch vụ.",
      "sentiment": "Negative",
      "topic": "Delivery",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 15,
      "feedback": "Đặt màu trắng giao màu đen, gọi tổng đài thì không ai bắt máy hỗ trợ.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Service"
      ]
    },
    {
      "id": 16,
      "feedback": "Hệ thống liên tục báo lỗi 504 Gateway Timeout khi tôi đang nhập mã giảm giá.",
      "sentiment": "Negative",
      "topic": "System",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 17,
      "feedback": "Chất lượng vải quá tệ, giặt một lần xù lông và giãn hết cả áo.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 18,
      "feedback": "Shipper tự ý hủy đơn mà không gọi điện báo trước cho khách hàng, làm lỡ việc của tôi.",
      "sentiment": "Negative",
      "topic": "Delivery",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 19,
      "feedback": "CSKH thờ ơ, hỏi gì cũng trả lời chung chung không giải quyết được vấn đề gì cả.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 20,
      "feedback": "Ứng dụng toàn lỗi văng app khi bấm vào giỏ hàng, trải nghiệm cực kỳ tệ hại.",
      "sentiment": "Negative",
      "topic": "System",
      "satisfaction_score": 1,
      "secondary_topics": []
    },
    {
      "id": 21,
      "feedback": "Sản phẩm tạm ổn, dùng được trong tầm giá này, giao hàng bình thường.",
      "sentiment": "Neutral",
      "topic": "Product",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 22,
      "feedback": "App thỉnh thoảng hơi chậm vào giờ cao điểm nhưng nhìn chung vẫn dùng được.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 23,
      "feedback": "Nhân viên bình thường, không quá nhiệt tình nhưng cũng không thái độ gì.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 24,
      "feedback": "Đóng gói bình thường, sản phẩm đúng mô tả, không có gì để phàn nàn nhiều.",
      "sentiment": "Neutral",
      "topic": "Product",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 25,
      "feedback": "Giao hàng đúng hạn nhưng hộp hơi bụi bẩn một chút.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 26,
      "feedback": "",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 27,
      "feedback": "@@@@#####$$$%%%%%\\",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 28,
      "feedback": "?????",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 29,
      "feedback": "Nói chung là bình thường, không có gì đặc biệt.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": []
    },
    {
      "id": 30,
      "feedback": "Hàng nhận được đúng hình, thời gian giao hàng vừa phải.",
      "sentiment": "Neutral",
      "topic": "Product",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 31,
      "feedback": "Ý kiến khách hàng 31: Nhân viên tư vấn nhiệt tình, giao hàng lôi thôi.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Service"
      ]
    },
    {
      "id": 32,
      "feedback": "Phản hồi số 32: Giao hàng khá nhanh chóng, sản phẩm tuyệt vời, dịch vụ chăm sóc nhiệt tình.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "Delivery",
        "Service"
      ]
    },
    {
      "id": 33,
      "feedback": "Trải nghiệm dùng app ổn định, mua hàng thấy sản phẩm đẹp và shipper giao đúng giờ.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 34,
      "feedback": "Đánh giá đơn hàng #34: Chất lượng sản phẩm tuyệt vời, hệ thống app hay giật lag.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product"
      ]
    },
    {
      "id": 35,
      "feedback": "Ý kiến khách hàng 35: Nhân viên tư vấn tệ, giao hàng cẩn thận.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 36,
      "feedback": "Phản hồi số 36: Giao hàng khá nhanh chóng, sản phẩm dỏm, dịch vụ chăm sóc thờ ơ.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery",
        "Service"
      ]
    },
    {
      "id": 37,
      "feedback": "Trải nghiệm dùng app mượt mà, mua hàng thấy sản phẩm tuyệt vời và shipper giao chậm.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "System",
        "Product"
      ]
    },
    {
      "id": 38,
      "feedback": "Đánh giá đơn hàng #38: Chất lượng sản phẩm đẹp, hệ thống app lỗi liên tục.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product"
      ]
    },
    {
      "id": 39,
      "feedback": "Ý kiến khách hàng 39: Nhân viên tư vấn chuyên nghiệp, giao hàng nhanh chóng.",
      "sentiment": "Positive",
      "topic": "Service",
      "satisfaction_score": 5,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 40,
      "feedback": "Phản hồi số 40: Giao hàng khá cẩn thận, sản phẩm kém chất lượng, dịch vụ chăm sóc chuyên nghiệp.",
      "sentiment": "Neutral",
      "topic": "Product",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery",
        "Service"
      ]
    },
    {
      "id": 41,
      "feedback": "Trải nghiệm dùng app mượt mà, mua hàng thấy sản phẩm chính hãng và shipper giao nhanh chóng.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 42,
      "feedback": "Đánh giá đơn hàng #42: Chất lượng sản phẩm chính hãng, hệ thống app lỗi liên tục.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product"
      ]
    },
    {
      "id": 43,
      "feedback": "Ý kiến khách hàng 43: Nhân viên tư vấn thờ ơ, giao hàng lôi thôi.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 44,
      "feedback": "Phản hồi số 44: Giao hàng khá đúng giờ, sản phẩm đẹp, dịch vụ chăm sóc tệ.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery",
        "Product"
      ]
    },
    {
      "id": 45,
      "feedback": "Trải nghiệm dùng app ổn định, mua hàng thấy sản phẩm chính hãng và shipper giao nhanh chóng.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 46,
      "feedback": "Đánh giá đơn hàng #46: Chất lượng sản phẩm dỏm, hệ thống app lỗi liên tục.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 47,
      "feedback": "Ý kiến khách hàng 47: Nhân viên tư vấn nhiệt tình, giao hàng cẩn thận.",
      "sentiment": "Positive",
      "topic": "Service",
      "satisfaction_score": 5,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 48,
      "feedback": "Phản hồi số 48: Giao hàng khá lôi thôi, sản phẩm tuyệt vời, dịch vụ chăm sóc tệ.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery",
        "Product"
      ]
    },
    {
      "id": 49,
      "feedback": "Trải nghiệm dùng app ổn định, mua hàng thấy sản phẩm đẹp và shipper giao chậm.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "System",
        "Product"
      ]
    },
    {
      "id": 50,
      "feedback": "Đánh giá đơn hàng #50: Chất lượng sản phẩm tạm được, hệ thống app hay giật lag.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product"
      ]
    },
    {
      "id": 51,
      "feedback": "Ý kiến khách hàng 51: Nhân viên tư vấn thờ ơ, giao hàng cẩn thận.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 52,
      "feedback": "Phản hồi số 52: Giao hàng khá đúng giờ, sản phẩm tạm được, dịch vụ chăm sóc nhiệt tình.",
      "sentiment": "Positive",
      "topic": "Service",
      "satisfaction_score": 4,
      "secondary_topics": [
        "Delivery",
        "Product"
      ]
    },
    {
      "id": 53,
      "feedback": "Trải nghiệm dùng app lỗi liên tục, mua hàng thấy sản phẩm chính hãng và shipper giao lôi thôi.",
      "sentiment": "Negative",
      "topic": "System",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Product",
        "Delivery"
      ]
    },
    {
      "id": 54,
      "feedback": "Đánh giá đơn hàng #54: Chất lượng sản phẩm đẹp, hệ thống app mượt mà.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 55,
      "feedback": "Ý kiến khách hàng 55: Nhân viên tư vấn thờ ơ, giao hàng nhanh chóng.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 56,
      "feedback": "Phản hồi số 56: Giao hàng khá chậm, sản phẩm dỏm, dịch vụ chăm sóc thờ ơ.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Delivery",
        "Service"
      ]
    },
    {
      "id": 57,
      "feedback": "Trải nghiệm dùng app hay giật lag, mua hàng thấy sản phẩm kém chất lượng và shipper giao nhanh chóng.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 58,
      "feedback": "Đánh giá đơn hàng #58: Chất lượng sản phẩm chính hãng, hệ thống app ổn định.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 59,
      "feedback": "Ý kiến khách hàng 59: Nhân viên tư vấn tệ, giao hàng nhanh chóng.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 60,
      "feedback": "Phản hồi số 60: Giao hàng khá nhanh chóng, sản phẩm tuyệt vời, dịch vụ chăm sóc tệ.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery",
        "Product"
      ]
    },
    {
      "id": 61,
      "feedback": "Trải nghiệm dùng app hay giật lag, mua hàng thấy sản phẩm tạm được và shipper giao nhanh chóng.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product",
        "Delivery"
      ]
    },
    {
      "id": 62,
      "feedback": "Đánh giá đơn hàng #62: Chất lượng sản phẩm dỏm, hệ thống app ổn định.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 63,
      "feedback": "Ý kiến khách hàng 63: Nhân viên tư vấn tệ, giao hàng cẩn thận.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 64,
      "feedback": "Phản hồi số 64: Giao hàng khá đúng giờ, sản phẩm kém chất lượng, dịch vụ chăm sóc thờ ơ.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery",
        "Service"
      ]
    },
    {
      "id": 65,
      "feedback": "Trải nghiệm dùng app lỗi liên tục, mua hàng thấy sản phẩm tạm được và shipper giao đúng giờ.",
      "sentiment": "Negative",
      "topic": "System",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Product",
        "Delivery"
      ]
    },
    {
      "id": 66,
      "feedback": "Đánh giá đơn hàng #66: Chất lượng sản phẩm tạm được, hệ thống app hay giật lag.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product"
      ]
    },
    {
      "id": 67,
      "feedback": "Ý kiến khách hàng 67: Nhân viên tư vấn nhiệt tình, giao hàng chậm.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Service"
      ]
    },
    {
      "id": 68,
      "feedback": "Phản hồi số 68: Giao hàng khá lôi thôi, sản phẩm tuyệt vời, dịch vụ chăm sóc thờ ơ.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery",
        "Product"
      ]
    },
    {
      "id": 69,
      "feedback": "Trải nghiệm dùng app lỗi liên tục, mua hàng thấy sản phẩm tạm được và shipper giao chậm.",
      "sentiment": "Negative",
      "topic": "System",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Product",
        "Delivery"
      ]
    },
    {
      "id": 70,
      "feedback": "Đánh giá đơn hàng #70: Chất lượng sản phẩm dỏm, hệ thống app lỗi liên tục.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 71,
      "feedback": "Ý kiến khách hàng 71: Nhân viên tư vấn nhiệt tình, giao hàng chậm.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Service"
      ]
    },
    {
      "id": 72,
      "feedback": "Phản hồi số 72: Giao hàng khá lôi thôi, sản phẩm dỏm, dịch vụ chăm sóc thờ ơ.",
      "sentiment": "Negative",
      "topic": "Delivery",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Product",
        "Service"
      ]
    },
    {
      "id": 73,
      "feedback": "Trải nghiệm dùng app mượt mà, mua hàng thấy sản phẩm dỏm và shipper giao cẩn thận.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 74,
      "feedback": "Đánh giá đơn hàng #74: Chất lượng sản phẩm đẹp, hệ thống app mượt mà.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 75,
      "feedback": "Ý kiến khách hàng 75: Nhân viên tư vấn tệ, giao hàng đúng giờ.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 76,
      "feedback": "Phản hồi số 76: Giao hàng khá đúng giờ, sản phẩm tuyệt vời, dịch vụ chăm sóc tệ.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery",
        "Product"
      ]
    },
    {
      "id": 77,
      "feedback": "Trải nghiệm dùng app hay giật lag, mua hàng thấy sản phẩm chính hãng và shipper giao cẩn thận.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product",
        "Delivery"
      ]
    },
    {
      "id": 78,
      "feedback": "Đánh giá đơn hàng #78: Chất lượng sản phẩm tuyệt vời, hệ thống app ổn định.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 79,
      "feedback": "Ý kiến khách hàng 79: Nhân viên tư vấn thờ ơ, giao hàng đúng giờ.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 80,
      "feedback": "Phản hồi số 80: Giao hàng khá lôi thôi, sản phẩm tạm được, dịch vụ chăm sóc thờ ơ.",
      "sentiment": "Negative",
      "topic": "Delivery",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Product",
        "Service"
      ]
    },
    {
      "id": 81,
      "feedback": "Trải nghiệm dùng app ổn định, mua hàng thấy sản phẩm tuyệt vời và shipper giao đúng giờ.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 82,
      "feedback": "Đánh giá đơn hàng #82: Chất lượng sản phẩm dỏm, hệ thống app hay giật lag.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 83,
      "feedback": "Ý kiến khách hàng 83: Nhân viên tư vấn tệ, giao hàng lôi thôi.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 84,
      "feedback": "Phản hồi số 84: Giao hàng khá nhanh chóng, sản phẩm chính hãng, dịch vụ chăm sóc chuyên nghiệp.",
      "sentiment": "Positive",
      "topic": "Delivery",
      "satisfaction_score": 5,
      "secondary_topics": [
        "Product",
        "Service"
      ]
    },
    {
      "id": 85,
      "feedback": "Trải nghiệm dùng app mượt mà, mua hàng thấy sản phẩm dỏm và shipper giao nhanh chóng.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 86,
      "feedback": "Đánh giá đơn hàng #86: Chất lượng sản phẩm dỏm, hệ thống app hay giật lag.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 1,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 87,
      "feedback": "Ý kiến khách hàng 87: Nhân viên tư vấn tệ, giao hàng chậm.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 88,
      "feedback": "Phản hồi số 88: Giao hàng khá chậm, sản phẩm tuyệt vời, dịch vụ chăm sóc chuyên nghiệp.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product",
        "Service"
      ]
    },
    {
      "id": 89,
      "feedback": "Trải nghiệm dùng app mượt mà, mua hàng thấy sản phẩm tuyệt vời và shipper giao chậm.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "System",
        "Product"
      ]
    },
    {
      "id": 90,
      "feedback": "Đánh giá đơn hàng #90: Chất lượng sản phẩm tạm được, hệ thống app mượt mà.",
      "sentiment": "Neutral",
      "topic": "Product",
      "satisfaction_score": 4,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 91,
      "feedback": "Ý kiến khách hàng 91: Nhân viên tư vấn thờ ơ, giao hàng chậm.",
      "sentiment": "Negative",
      "topic": "Delivery",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Service"
      ]
    },
    {
      "id": 92,
      "feedback": "Phản hồi số 92: Giao hàng khá cẩn thận, sản phẩm dỏm, dịch vụ chăm sóc tệ.",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery",
        "Service"
      ]
    },
    {
      "id": 93,
      "feedback": "Trải nghiệm dùng app ổn định, mua hàng thấy sản phẩm đẹp và shipper giao nhanh chóng.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System",
        "Delivery"
      ]
    },
    {
      "id": 94,
      "feedback": "Đánh giá đơn hàng #94: Chất lượng sản phẩm tuyệt vời, hệ thống app mượt mà.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 95,
      "feedback": "Ý kiến khách hàng 95: Nhân viên tư vấn nhiệt tình, giao hàng lôi thôi.",
      "sentiment": "Neutral",
      "topic": "Delivery",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Service"
      ]
    },
    {
      "id": 96,
      "feedback": "Phản hồi số 96: Giao hàng khá nhanh chóng, sản phẩm chính hãng, dịch vụ chăm sóc tệ.",
      "sentiment": "Neutral",
      "topic": "Service",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Delivery",
        "Product"
      ]
    },
    {
      "id": 97,
      "feedback": "Trải nghiệm dùng app hay giật lag, mua hàng thấy sản phẩm tuyệt vời và shipper giao nhanh chóng.",
      "sentiment": "Neutral",
      "topic": "System",
      "satisfaction_score": 3,
      "secondary_topics": [
        "Product",
        "Delivery"
      ]
    },
    {
      "id": 98,
      "feedback": "Đánh giá đơn hàng #98: Chất lượng sản phẩm chính hãng, hệ thống app mượt mà.",
      "sentiment": "Positive",
      "topic": "Product",
      "satisfaction_score": 5,
      "secondary_topics": [
        "System"
      ]
    },
    {
      "id": 99,
      "feedback": "Ý kiến khách hàng 99: Nhân viên tư vấn thờ ơ, giao hàng lôi thôi.",
      "sentiment": "Negative",
      "topic": "Service",
      "satisfaction_score": 1,
      "secondary_topics": [
        "Delivery"
      ]
    },
    {
      "id": 100,
      "feedback": "Phản hồi số 100: Giao hàng khá chậm, sản phẩm kém chất lượng, dịch vụ chăm sóc chuyên nghiệp",
      "sentiment": "Negative",
      "topic": "Product",
      "satisfaction_score": 2,
      "secondary_topics": [
        "Delivery",
        "Service"
      ]
    }
  ]
}
```

## V. Lưu ý kiểm thử và kiểm duyệt

- Các bản ghi được đánh số từ 1 đến 100.
- Bản ghi 26 có nội dung rỗng trong dữ liệu nguồn; được giữ nguyên và gán nhãn dự phòng `Neutral`, điểm 3.
- Bản ghi 27 và 28 là dữ liệu nhiễu/không có ngữ nghĩa rõ ràng; được gán nhãn dự phòng `Neutral`, điểm 3 và cần kiểm tra thủ công.
- Với phản hồi có nhiều chủ đề, `topic` là chủ đề chính; `secondary_topics` lưu các chủ đề còn lại.
- Các nhãn trong bộ dữ liệu là kết quả phân loại tham chiếu để kiểm thử Prompt, chưa phải nhãn chuẩn được con người xác minh.
- Trước khi triển khai thực tế, nên cho người phụ trách kiểm tra các trường hợp mỉa mai, phản hồi đa chiều và các trường hợp có điểm số sát ranh giới.
