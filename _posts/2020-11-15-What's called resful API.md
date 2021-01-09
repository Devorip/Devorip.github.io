---
title: 'Understand Restful API in 10 minutes?'
layout:   post
category: tutorial-tips
tags:     [api, restful, service]
feature:  /assets/img/multiple-db-pic1.png
---
Nếu bạn đã từng nghe rất nhiều lần về Restful API và cố gắng tìm hiểu nó nhưng khó có thể hiểu được cặn kẽ, hãy nghiên cứu series này và bạn có thể tìm ra một thứ gọi là chân lý. trong series này tôi sẽ chỉ cho bạn về tất tần tật các khái niệm theo cách dễ hiểu nhất và chia sẻ các kinh nghiệm, ví dụ khi thiết kế 1 Restful API.
<!--more-->
# I. What is this?
Điều đầu tiên chúng ta cần hiểu Restful API là gì? 

API (Application Programming Interface) là một cái gì đấy mà nó giống như khi bạn gọi ajax vậy, nó sẽ xử lý và trả về các kết quả qua JSON, XML... chứ k phải chỉ là html như các web thông thường.

Restful à một dạng chuyển đổi cấu trúc dữ liệu. Nó sử dụng phương thức HTTP. Thay vì sử dụng một URL để xử lý thì REST gửi một yêu cầu HTTP như GET, POST, DELETE, vv đến một URL để xử lý dữ liệu. đoạn này hơi khó nuốt

Hiểu đơn giản thì resful API là các chuẩn, các ràng buộc cho việc thiết kế. Bản chất nó là 1 chuẩn của thiết kế API cho web Service nên nó không quy định logic mà chỉ đơn giản là quy định cách sử dụng các phương thức (get, post, put, delete, put...) để truy cập và quản lý các resources.

Chú ý là chúng ta chỉ "nên" tuân theo nó, vậy thôi. ở đây t nhấn mạnh là "nên" tuân theo nó là bởi vì không phải lúc nào nó cũng hợp lý 100% với service của chúng ta (sẽ có các ví dụ ở phần sau). 

# II. Restful hoạt động như thế nào? 
Nó sự dụng HTTP và có 4 phương thức chủ yếu:
- GET: tương ứng với các request lấy dữ liệu, ở trong này ta không nên sử dụng các phương thức thêm bớt sửa xoá của database (CRUD)
- POST: là một phương thức để tạo dữ liệu.
- PUT, PATCH: sửa dữ liệu
- DELETE: xoá dữ liệu
ngoài ra còn các phương thức khác như 
- HEAD: giống với GET nhưng response trả về không có body, chỉ có header.
- CONNECT: thiết lập một kết nối tới server theo URI.
- OPTIONS: mô tả các tùy chọn giao tiếp cho resource.
- TRACE: thực hiện một bài test loop – back theo đường dẫn đến resource.

vậy nó trả về gì? ý mình ở đây là status của các request. 
- 200 OK – Trả về thành công cho những phương thức GET, PUT, PATCH hoặc DELETE.
- 201 Created – Trả về khi một Resouce vừa được tạo thành công.
- 204 No Content – Trả về khi Resource xoá thành công.
- 304 Not Modified – Client có thể sử dụng dữ liệu cache.
- 400 Bad Request – Request không hợp lệ
- 401 Unauthorized – Request cần có auth.
- 403 Forbidden – bị từ chối không cho phép.
- 404 Not Found – Không tìm thấy resource từ URI
- 405 Method Not Allowed – Phương thức không cho phép với user hiện tại.
- 410 Gone – Resource không còn tồn tại, Version cũ đã không còn hỗ trợ.
- 415 Unsupported Media Type – Không hỗ trợ kiểu Resource này.
- 422 Unprocessable Entity – Dữ liệu không được xác thực
- 429 Too Many Requests – Request bị từ chối do bị giới hạn

# III. Kinh nghiệm khi thiết kế Restful API 
Đây là những kinh nghiệm mình đúc kết được khi làm về Restful 
0. route

route    | get                      | post     | put                | delete
-------- | -------------            | -------- | -------------      | -------------
/users   | return list users | create new user | update 1 list user | delete nhiều users
/users/1 | return profile of 1 user | nothing  | update 1 user      | delete 1 user 

1. không nên dùng các động từ mà dùng từ số nhiều
ví dụ để lấy danh sách users chỉ cần users là đủ, k cần phải sử dụng getUsers, như vậy sẽ thừa và dài dòng cho về sau
2. Get chỉ để lấy resource, việc update hay delete, create resources vào đây sẽ khó quản lý khi dự án scale ra. đồng thời reqquest trả về cũng sẽ mất đi tiêu chuẩn, bạn sẽ phải thêm một số thứ để biết được rằng nó update/delete/create được không, rõ ràng là khó hơn mà nhỉ =)) 
3. nhất quán trong việc đặt tên résoures
- không dùng / ở cuối cùng. ví dụ users/1/ 
- không dùng _ vì có thể bị mất kí tự do nó quá thấp, thay vào đó dùng - tốt hơn 
- sử dụng chữ thường không dùng chữ hoa 
- không nên dùng extensions
- các kí tự trong search query như >, <, >=, <= ... thì cũng nên tạo 1 loạt các từ khoá cho nó 
những thứ trên đây chỉ là k nên, bạn có thể làm và nó vẫn chạy bình thường, là do bạn chonj thôi 

4. dữ liệu gửi lên
dù là request get thì các bạn cũng có thể đổi nó thành post, request create, update, delete thì bạn đề có thể đổi thành get. tất nhiên là k khuyến khích. 
ví dụ đơn giản là chúng ta có 1 hàm filter với rất rất nhiều query phía sau, method này là dành cho phần search trong services của bạn. nó như sau: 
```js
 GET https://api.example.com/posts?query[field]=title&query[compare]=like&query[value]=xzy
```

thế giả sử bạn phải search theo rất nhiều params thì có phải method get sẽ rất dài không, mà get chỉ đưa lên được một số giới hạn kí tự (có tài liệu ghi là 2048, 2047, lại có cái ghi rằng google SERP sẽ k xử lý các url dài hơn 1855 kí tự @@). vậy bạn filter quá nhiều thì chắc chắn là có thể dẫn đến tình trang tràn url. từ đó mà method get là tử thần (nó sinh ra mà bạn k đoán được), post lúc này lên ngôi và vô cùng hữu dụng vì nó truyền lên bao nhiêu cũng được. 

vậy nên khi nào dùng get và post là do bạn quy đinh, hay tính đến các trường hợp mà bạn có thể nghĩ ra. đây chỉ là một ít kinh nghiệm của mìh thôi, rất mong các bạn dongds góp thêm 

phần tìm kiếm thì nên đặt trong params có tên gợi nhắc (thường là q viết tắt của query). và bạn cũng nên xây dựng search theo global và scope riêng. cái này có thể không cần vì global search là đủ rồi, nhưng nó sẽ giúp ta hiểu dễ dàng hơn và k phải nối quá nhiều query vào cuối url 

5. version 
hãy tuân theo nguyên tắc của version như sau:
- Bắt đầu bằng v và kết thúc bằng một số nguyên dương , đặc biệt nên tránh dùng số thập phân (dùng v1 thay vì v1.5)
- Version sẽ được đặt ở vị trí đầu tiên của resource
ví dụ GET /v1/users/1

6. Phân trang
hãy dùng page và limit để lấy. mình đã từng gặp một đội dev viết phân trang bằng cách sử dụng limit và offset(tương tư như page, tính bằng (page - 1)*number post of 1 page), nhưng điều đặc biệt là offset ở đây không phải là số thứ tự của id hay của bản ghi sau khi sắp xếp mà nó được lấy theo timestamp, do timestamp không phải duy nhất nên dẫn đến việc nó bị trùng và k phân trang đúng. (bạn tự giải thích tại sao lại k phân trang đúng nhé). do vậy mình đưa phần này và đây như một kinh nghiệm =))  

7. tuỳ chọn field trả về
phần này được nhắc đến như một phần tối quan trọng trong viẹc tối ưu hoá services. nếu bạn trả về quá nhiều thì rõ ràng là nó sẽ lâu, càng nhiều thì bên sử dụng sẽ càng load chậm. nếu thừa thì nó lại là phí phạm đường truyền. nên đôi lúc bạn nên quuêts định có nên trả về tất cả các field không hay chỉ trả về 1 phần của nó. trả về hết thì nhanh còn trả về 1 phần thì phải xem nó cần gì rồi mình trả về cái đó, mất thời gian làm hơn. 

8. Xử lí, phân loại lỗi
Đối với những resources hỗ trợ nhiều định dạng dữ liệu trả về, HTTP-Header sẽ là nơi để xác định dịnh dạng đó.

- Content-Type: Khai báo request format
- Accept: Khai báo response format
Ví dụ:
Request format
```js
Content-type: application/json;charset=UTF-8
Content-Type: text/html; charset=UTF-8
Content-Type: multipart/form-data; boundary=something
```
Response format
```js
Accept: application/json*
Accept: text/html
```

Chuẩn HTTP cung cấp hơn 70 status codes để mô tả các giá trị trả về. Dưới đây là một số status codes phổ biến hay dùng:

- 200 - OK - Eyerything is working
- 201 - Created - A new resource has been created
- 304 - Not Modified - The client can use cached data
- 400 - Bad Request - The request was invalid or cannot be served. The exact error should be explained in the error payload. E.g. "The JSON is not valid"
- 401 - Unauthorized - The request requires an user authentication
- 403 - Forbidden -The server understood the request, but is refusing it or the access is not allowed.
- 404 - Not found - There is no resource behind the URI.
- 422 - Unprocessable Entity - Should be used if the server cannot process the enitity, e.g. if an image cannot be formatted or mandatory fields are missing in the payload.
- 429 - Too Many Requests - The user has sent too many requests in a given amount of time ("rate limiting").
- 500 - Internal Server Error - API developers should avoid this error. If an error occurs in the global catch blog, the stracktrace should be logged and not returned as response.

Trong đó:
The client application behaved erroneously (client error - 4xx response code)
The API behaved erroneously (server error - 5xx response code)
The client must take some additional action. (redirection - 3xx response code)
The client and API worked (success - 2xx response code)

Lỗi trả về nên rõ ràng cho dev phân tích và dễ hiểu cho user. ví dụ 
Value passed for name exceeded max length 	
lúc này nên sửa dụng name_too_long thay vì invalid_name
nhưng đừng nghĩ rằng nó đơn giản, hãy check ví dụ sau
error establishing a database connection 
bên dev nên dùng nguyên nó còn hiển thị cho user nên sử dụng server error hoặc tương tự vì ng dùng có thể k hiểu database là gì.

XML không phải là một lựa chọn tốt cho API response. XML khá rườm rà, gây khó khăn khi đọc & phân tích cú pháp,... Tham khảo thêm tại đây, thay vào đó chúng ta sẽ sử dụng JSON. tuy nhiên SOAP lại là 1 chuẩn sử dụng xml và nó dùng cho các hệ thống lớn nên bạn k nên đánh giá nó mà nên tìm hiểu kĩ hơn. mình sẽ có bài phân tích lý do tại sao mà xml vẫn sánh vai trong các hệ thống thay vì sử dụng json. 
một số đạng response trả về mà mình từng sử dụng 
```json
{
  "errors": [
   {
    "userMessage": "Sorry, resource does not exist",
    "internalMessage": "No user found in the database",
    "code": 34,
    "more info": "http://example.com/api/v1/errors/12345"
   }
  ]
}
```
```json
{
  "status": 400,
  "message": "No user found in the database",
  "code": 34,
  "more info": "http://example.com/api/v1/errors/12345"
}
```

7. update
ví dụ 1 người sở hữu 1 club, 1 club có nhiều plan, plan và club đều có nhiều bài viết và 1 người cũng sẽ sở hưu nhiều bài post. vậy bạn sẽ chọn các nào trong 2 cách sau
```js 
- cách 1: 
users/1/club -> lấy ra club 
users/1/club/plans -> lấy ra plans 
users/1/club/posts -> lấy ra posts, vì plan trong club nên póst của plan cũng của club luôn.
users/1/club/posts/1 -> lấy ra posts detail, update (PATCH, PUT), delete (DELETE)
users/1/club/posts/create -> tạo posts 

- cách 2: 
users/1 -> lấy ra user
club/1 -> lấy ra club  
plans/1 -> lấy ra plan  
posts -> lấy ra posts 
posts/1 -> lấy ra posts detail, update (PATCH, PUT), delete (DELETE)
posts/create -> tạo posts 
```

rồi giờ thì bạn thấy là nó có nhiều cách có thể làm được. nhưng sự khác nhau ở đây là gì?

ta dễ thấy là cách 2 sẽ k có bất kì phụ thuộc nào phía trước, đường dẫn là của chính nó. cách này sẽ có vấn đề gì?
- ưu điểm:
a. là nó tách biệt thành phần, dễ nhìn và đơn giản, 
b. khi thêm thành phần mới nó k phải xử lý về thay đổi path mà vẫn có thể tạo được posts như thường 
- nhược điểm
a. nó qúa tách biệt nên người ta nhìn vào đó k hiểu mối quan hệ. 

vậy cách 1 thì sao? 
- ưu điểm
a. dễ thấy cách 1 sẽ thể hiện được mối quan hệ giữa các thành phần 
- nhược điểm 
a. khi update lên thì lại phải đổi path, 
  giả sử bây giờ 1 user có nhiều club thì chẳng phải sẽ phải chèn thêm id vào giữa phải không? tất cả các đường dẫn về sau đều phải sửa đôi chút?
b. rối mắt nếu có quá nhiều quan hệ phía sau 1 user. 
  giả sử bây giờ posts có thêm comments. post nào chẳng có comments. vậy thì phía sau posts/1 lại phải thêm comments để lấy danh sách các comments, tạo, sửa, xoá. quá dài phải không?
c. giờ lại update thêm path lần nữa. bây giờ user có thể tạo posts mà k cần ở trong bất kì club, plan nào. phần club plan vẫn có các bài post như cũ. rắc rối chưa, các mối quan hệ đã bị phá vỡ, đường dẫn phải tạo thêm. 

rồi giờ thì chúng ta có thể thấy 1 loạt các ưu nhược điểm của 2 cách, giờ mix 2 cách với nhau chẳng phải là ổn rồi không. chúng ta sẽ đổi nó thành 
```js
users/1 -> lấy ra user
users/1/club -> lấy ra club 
users/1/club/plans -> lấy ra plans  
posts -> lấy ra posts 
posts/1 -> lấy ra posts detail, update (PATCH, PUT), delete (DELETE)
posts/create -> tạo posts 
posts/1/comment/create -> tạo comments 
...
```

bây giờ thì ta sẽ thấy nó khá ổn cho một vài lần update, đường dẫn k quá dài. các phần như update 1 user sẽ có nhiều hơn 1 club ta vẫn sẽ để như vậy và sửa đường dẫn nhưng k phải sửa quá nhiều. phần comment và posts thì k có nhiều update nên có thể để như vậy là duy trì được trong thời gian rất dài phải không? 

bài viết của mình đến đây là kết thúc rồi. hi vọng các bạn sẽ thích nó. 

tham khảo:
https://swagger.io/blog/api-design/api-design-best-practices/ 
https://www.oreilly.com/ideas/best-design-practices-to-get-the-most-out-of-your-api
