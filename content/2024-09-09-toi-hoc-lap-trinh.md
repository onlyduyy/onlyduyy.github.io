---
title: Tôi học lập trình (Rust) và làm nhiều thứ.
description: "Một bài viết về sự yêu sự ghét"
date: 2024-09-09
---

Khá muộn khi viết bài này nhưng mình đã học lập trình được khá lâu rồi nhưng đến gần đây thì mình mới cảm nhận được sự tiến bộ và hiểu hơn về lập trình.

Có khá nhiều "ngôn ngữ lập trình" (Thứ để bạn giao tiếp với máy tính) ngoài kia nhưng mình sau một thời gian thử (Gần 1 năm) thì quyết định dừng lại từ Kotlin, rồi định cư ở Rust cho đến hiện tại. Mình thích lập trình bằng ngôn ngữ này, (nhưng) có khá nhiều vấn đề là (Không vấn đề lắm với mình, nhưng với bạn thì có thể):

- Tài nguyên học bằng Tiếng Việt gần như không có (Không quá vấn đề).
- Cộng đồng lập trình ngôn ngữ này không lớn (Ở trong nước, nhưng ở nước ngoài cũng không nhiều đến mức vậy).
- Là một ngôn ngữ không quá phổ biến.

### The Book

Mình thấy rất nhiều người gợi ý [The Book](https://doc.rust-lang.org/book/title-page.html) làm điểm khởi đầu, và cá nhân mình cũng mới học hết chương 15 của sách và bỏ rồi tự đi mò rồi lập trình các thứ khác suốt. Đợt này mình sẽ quay lại cày và hoàn thành sách.

### Rustlings
Mình đã hoàn thành Rustlings cách đây khá lâu. Dù thi thoảng (Khoảng 3 bài) vẫn gian lận là đi xem đáp án trên Github nhưng về cơ bản là tự làm.

### Học thêm về CLI, sử dụng Cookbook
Đây là hai cuốn sách có thể đọc hoàn toàn miễn phí (Sách mã nguồn mở) trên Internet, và nó siu chất lượng!

- [Rust CLI Book](https://rust-cli.github.io/book/index.html) - Mình đọc thêm về cách xử lý lỗi, parse args, học thêm về những mẹo hay khi lập trình ứng dụng dòng lệnh
- [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) - Những cách "de-facto" để viết Rust một cách _idiomatically_.

Một repo mình tự làm để tổng hợp nhiều phần code hay từ các phần mềm khác nhau trên Github mà bạn có thể đọc [tại đây](https://codeberg.org/duykhanh471/mia-rs)

### Xây dựng dự án CLI
Mình đã làm đâu đó khoảng 20 ứng dụng CLI, có khá nhiều là bị bỏ dở, hoặc đúng hơn là mình đã chạy thành công và hoàn thành những chức năng cơ bản nhưng code của mình vẫn chưa được _idiomatic_ cho lắm, nên chưa tính là hoàn thành.

Danh sách các dự án khác (Bắt đầu làm, và không phải là của mình):

- [pngme_book](https://jrdngr.github.io/pngme_book/)

### Những quy tắc mở rộng
Mình cần định hướng và học thêm những thứ khác nữa, như Design Patterns hoặc cách để viết Rust hiệu quả.

- [Patterns](https://rust-unofficial.github.io/patterns/intro.html)
- [Effective Rust](https://www.lurklurk.org/effective-rust/title-page.html)

### Ownership & Lifetime
Một trong những vấn đề mà mình gặp phải rất nhiều là sự hiểu thiếu hoặc hiểu chưa rõ về Lifetime trong Rust, đây sẽ là những định hướng cho vấn đề trên.

- [Ownership - nomicon](https://doc.rust-lang.org/nomicon/ownership.html)
- [Common Rust Lifetime Misconceptions](https://github.com/pretzelhammer/rust-blog/blob/master/posts/common-rust-lifetime-misconceptions.md)

### Async
- [Sách học Async của Rust](https://rust-lang.github.io/async-book/01_getting_started/01_chapter.html)
- [tokio.rs](https://tokio.rs/tokio/tutorial)

### Xử lý lỗi
- [Error Handling](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/)
- [modular errors](https://sabrinajewson.org/blog/errors)
- [railway oriented programming](https://fsharpforfunandprofit.com/posts/recipe-part2)

### Các chủ đề khác
Các phần này khá nâng cao nên mình sẽ để học cuối.

- [Architecture](https://matklad.github.io/2021/09/05/Rust100k.html)
- [Too many lists](https://rust-unofficial.github.io/too-many-lists/)
- [dangerust](https://cliffle.com/p/dangerust)
- [nomicon](https://doc.rust-lang.org/nomicon/intro.html)

### Quizzes
- <https://dtolnay.github.io/rust-quiz/>
- <https://boxyuwu.github.io/rust-quiz/index.html>