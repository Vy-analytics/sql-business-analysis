# sql-business-analysis
Phân tích dữ liệu bán hàng và khách hàng bằng SQL
# Phân tích dữ liệu bán hàng và khách hàng bằng SQL

## Mục tiêu
Sử dụng SQL để phân tích dữ liệu bán hàng và khách hàng, từ đó trả lời các câu hỏi kinh doanh liên quan đến hành vi khách hàng, sản phẩm và doanh thu.

## Dataset
Dữ liệu bao gồm các bảng:
- Customers
- Products
- Sales
- Stores
- Exchange_Rates

Các bảng được liên kết với nhau thông qua:
- ProductKey
- CustomerKey
- StoreKey

## Nội dung phân tích
- Phân tích đặc điểm khách hàng theo độ tuổi, giới tính và khu vực
- Phân tích sản phẩm theo danh mục và thương hiệu
- Phân tích doanh thu theo thời gian và cửa hàng

## Kỹ thuật SQL sử dụng
- WHERE, LIKE, IN, BETWEEN
- JOIN
- GROUP BY, HAVING
- CTE (WITH)
- Subquery

## Insight chính
- Cơ cấu khách hàng khác nhau theo khu vực
- Một số thương hiệu chiếm ưu thế trong từng danh mục
- Doanh thu có sự chênh lệch giữa các cửa hàng
