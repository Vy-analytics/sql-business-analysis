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

## SQL 

-- 1. Lấy ra những khách hàng nam có tuổi từ 50 trở lên và hiển thị thêm cột tuổi
SELECT *, DATEDIFF(YEAR, Birthday, GETDATE()) AS Age
FROM Customers
WHERE Gender = 'Male'
    AND DATEDIFF(YEAR, Birthday, GETDATE()) >= 50

-- 2. Lấy ra những khách hàng có tên bắt đầu bằng L và ở Italy
SELECT *
FROM Customers
WHERE Name LIKE 'L%'
    AND Country = 'Italy'

-- 3. Lấy ra những khách hàng ở những khu vực có state code là TX hoặc SA là nữ hoặc khách hàng ở khu vực đó có độ tuổi từ 20 đến 40 
SELECT *
FROM Customers
WHERE
    State_Code IN ('TX', 'SA')
    AND 
    (Gender = 'Female' 
        OR DATEDIFF(YEAR, Birthday, GETDATE()) BETWEEN 20 AND 40)

-- 4. Hãy cho biết những khách hàng đến từ những quốc gia nào, sắp xếp theo thứ tự tăng dần?
SELECT DISTINCT Country
FROM Customers
ORDER BY 1

-- 5. Hãy cho biết doanh nghiệp đang bán những loại sản phẩm nào (category) thuộc brand nào, sắp xếp theo thứ tự giảm dần brand và tăng dần category?
SELECT DISTINCT Brand, Category
FROM Products
ORDER BY
    Brand DESC 
    , Category
    
-- 6. Hãy cho biết brand nào cung cấp Audio (category) đang có số lượng sản phẩm (productkey) nhiều hơn 30 sản phẩm ?
SELECT 
    Brand
    , COUNT(ProductKey) AS QTY_PRODUCT
FROM Products
WHERE Category = 'Audio'
GROUP BY Brand
HAVING COUNT(ProductKey) > 30

-- 7. Hãy tính doanh thu (USD) theo từng ngày
SELECT 
    Order_Date
    , SUM(Quantity * Unit_Price_USD) AS Rev
FROM Sales S 
    INNER JOIN Products P ON S.ProductKey = P.ProductKey
GROUP BY Order_Date

-- 8. Hãy cho biết tên sản phẩm có doanh thu (USD) thấp hơn doanh thu trung bình của từng cửa hàng (store)
;
WITH 
RAW_ AS 
(
    SELECT  
        StoreKey
        , Product_Name
        , SUM(Quantity * Unit_Price_USD) AS REV
    FROM Sales S 
        INNER JOIN Products P ON S.ProductKey = P.ProductKey
    GROUP BY 
        StoreKey
        , Product_Name
)
SELECT *
FROM RAW_ R 
    INNER JOIN 
    (
        SELECT 
            StoreKey
            , AVG(REV) AS AVG_REV
        FROM RAW_
        GROUP BY StoreKey
    ) A ON R.StoreKey = A.StoreKey
            AND R.REV < A.AVG_REV

-- 9. Hãy cho biết tên khách hàng nữ nào có số lượng đơn hàng ít nhất ở khu vực có state_code là WA
;
WITH 
RAW_ AS
(
    SELECT 
        C.CustomerKey
        , C.Name
        , COUNT(DISTINCT Order_Number) AS Qty_Orders
    FROM Sales S 
        INNER JOIN 
        (
            SELECT *
            FROM Customers
            WHERE Gender = 'Female'
                AND State_Code = 'WA'
        ) C ON S.CustomerKey = C.CustomerKey
    GROUP BY 
        C.CustomerKey
        , C.Name
)
SELECT *
FROM RAW_
WHERE Qty_Orders = (SELECT MIN(Qty_Orders) FROM RAW_)

## Insight chính
- Cơ cấu khách hàng khác nhau theo khu vực
- Một số thương hiệu chiếm ưu thế trong từng danh mục
- Doanh thu có sự chênh lệch giữa các cửa hàng
