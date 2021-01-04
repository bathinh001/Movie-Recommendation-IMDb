# Đồ án cuối kì môn Khoa học dữ liệu và ứng dụng
Đồ án gồm 3 phần:
- Crawl data
- EDA
- Prediction model

# Crawl data
Folder: [crawler](https://github.com/bathinh001/1712168/tree/main/crawler), [data](https://github.com/bathinh001/1712168/tree/main/data).

## Movie
File thực hiện: [movies_crawler.ipynb](https://github.com/bathinh001/1712168/blob/main/crawler/movies_crawler.ipynb).

File kết quả: [movie.csv](https://github.com/bathinh001/1712168/blob/main/data/movie.csv), [credit.csv](https://github.com/bathinh001/1712168/blob/main/data/credit.csv).

Ở phần này, mình sẽ kết hợp cả 2 phương pháp crawl data:

- Parse HTML: đầu tiên lấy ra danh sách các genre (gồm 21 genres) ở trang top chart, từ mỗi genre lấy ra top movie có từ 10000 votes trở lên. Những bước và điều kiện như vậy sẽ giúp mình lấy ra những movie nổi tiếng, từ đó sẽ dễ khai thác user cũng như những thông số rating mỗi phim sẽ đáng tin cậy hơn nhờ vào lượt vote lớn. Tuy nhiên ở bước này chỉ lấy ra id của các movie, vì IMDb đã hỗ trợ mình API, chỉ cần dựa vào id để khai thác.
- API: sử dụng thư viện imdbpy, từ set id đã có ở bước trên qua API functions gọi các thuộc tính của movie.

Sau phần này sẽ có 2 tập data:
- movie.csv: những thông tin cơ bản của phim, không liên quan đến yếu tố con người.
- credit.csv: những thông tin liên quan đến diễn viên, đạo diễn, nhà sản xuất,... của mỗi movie.

## Rating
File thực hiện: [users_crawler.ipynb](https://github.com/bathinh001/1712168/blob/main/crawler/users_crawler.ipynb).

File kết quả: [rating.csv](https://github.com/bathinh001/1712168/blob/main/data/rating.csv)

Vì IDBm không có API để lấy thông tin user, nên phần này mình hoàn toàn dựa vào parse HTML để làm. Mỗi bộ phim mình sẽ lần lượt lấy top profilic user đánh giá từ 1 đến 10 sao. Lí do chọn profilic user vì những user này đánh giá nhiều phim, mình sẽ có tỉ lệ cao tìm được user đó đánh giá những phim khác trong data movie, từ đó việc recommend sẽ hiệu quả hơn.

Với khoảng 9000 bộ phim, mình crawl được tầm hơn 1 triệu row (movie_id, user_id, rating). Nếu dùng 1 file để crawl toàn bộ, sẽ mất khoảng 43 giờ, nên mình làm nhiều file rồi lấy kết quả kết hợp lại, tuy nhiên trong báo cáo này mình chỉ để 1 file đại diện.

# EDA
Folder: [main](https://github.com/bathinh001/1712168/tree/main/main).

File thực hiện: [EDA.ipynb](https://github.com/bathinh001/1712168/blob/main/main/EDA.ipynb).

Mình sẽ vẽ biểu đồ phân tích một số thông tin về thời lượng phim, rating phim, phân bố genre cũng như top movie của mọi thời đại.

# Main
Folder: [main](https://github.com/bathinh001/1712168/tree/main/main).

File thực hiện: [main.ipynb](https://github.com/bathinh001/1712168/blob/main/main/main.ipynb)

Prediction đóng một vai trò khá quan trọng trong nhiều Recommendation Systems.

Phần này sẽ là phần chính của đồ án, gồm 3 task prediction:

- Content Based Filtering: predict những bộ phim khác từ một bộ phim dựa trên những thuộc tính của nó như title, đạo diễn, diễn viên, thể loại, overview,... (dùng TF-IDF và CountVectorizer để vectorize, sau đó dùng cosine similarity matrix để dự đoán)
- Content Based Filtering: predict điểm số mà user có thể muốn cho một phim nào đó (dùng SVD)
- Collaborative Filtering: predict những bộ phim cho một user dựa trên những users có độ liên quan gần nhau (KNN)

Task predict đầu tiên sẽ phù hợp nếu chúng ta không biết user là ai.

2 task predict sau, bằng sự kết hợp lẫn nhau chọn ra top phim chung, sẽ phù hợp để recommend từ sở thích của user.

## Dự đoán phim dựa vào thuộc tính riêng

Ở bước này, ta thường thấy rằng một bộ phim có sự tương đồng với bộ phim khác nằm ở thể loại, kịch bản, cốt truyện, và thậm chí là diễn viên, đạo diễn.

Thế nên, mình sẽ lấy hết text từ các trường title, overview, genres từ file `movie.csv`, cùng toàn bộ các trường của file `credit.csv`, join hai file bằng `movie_id`

## Dự đoán preference của user đối với phim từ bảng rating của mỗi user với một số phim

Mình sẽ dùng một kĩ thuật gọi là SVD (Single Value Decomposition), với metric đánh giá là Root Mean Square Error (RMSE). Giá trị của RMSE càng thấp gần về 0 thì performance càng tốt.
