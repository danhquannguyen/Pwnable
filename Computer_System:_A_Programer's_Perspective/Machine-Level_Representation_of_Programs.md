* Source: [Computer System: A Programmer's Perspective](https://www.cs.sfu.ca/~ashriram/Courses/CS295/assets/books/CSAPP_2016.pdf)

# CHAPTER 3: Machine-Level_Representation of Programs (Biểu diễn chương trình ở cấp độ máy tính)

  * Máy tính thực thi **mã máy** (machine code), tức là các chuỗi byte mã hóa cho các hoạt động cấp thấp nhằm thao tác với dữ liệu, quản lý bộ nhớ, đọc và ghi dữ liệu trên các thiết bị lưu trữ, cũng giống như giao tiếp qua mạng. Một **trình biên dịch** (compiler) sẽ tạo ra mã máy thông qua một loạt các giai đoạn, dựa trên các quy tắc của một ngôn ngữ lặp trình, tập lênh(instruction set) của máy đích, và các quy ước mà hệ điều hành tuân thủ. Trình biên dịch **C** `gcc` tạo ra đầu ra của nó dưới dạng **mã hợp ngữ** (Assembly code), mọt dạng biểu diễn bằng văn bản của mã máy cho thấy các câu lệnh riêng lẻ trong chương trình. Sau đó, `gcc` kích hoạt (gọi) cả **trình dịch hợp ngữ** (assembler) và **trình liên kết** (linker) để tạo ra mã máy có thể thực thi từ đoạn mã hợp ngữ đó. Trong chương trình này, chúng ta sẽ xem xét kỹ lưỡng về mã máy và dạng biểu diễn mà con người có thể đọc hiểu được của nó dưới dạng mã hợp ngữ (assembly).

  * Khi lập trình bằng một **ngôn ngữ bậc cao** (high-level language) như **C**, và thậm chí điều này còn rõ hơn ở **Java**, chúng ta không phải bận tâm khỏi các chi tiết triên khai ở cấp độ máy của chương trình. Ngược lại, khi viết chương trình bằng **Assembly code** - giống như cách làm trong những ngày đầu tiên của kỷ nguyên máy tính - lập trình viên phải chỉ định rõ từng **lệnh bậc thấp** (low-level instructions) mà chương trình sử dụng để thực hiện một phép tính toán. Hầu hết thời gian, làm việc ở __mức độ trừu tượng__ (level of abstraction) cao hơn do ngôn ngữ bặc cao cung cấp sẽ mang lại năng suất và độ tin cậy cao hơn nhiều. Quá trình **kiểm tra kiểu** (type checking) do một **compiler** cung cấp giúp phát hiện nhiều lỗi chương trình và đảm bảo rằn chúng ta tham chiếu cũng như thao tác với dữ liệu theo những cách nhất quán. Với các trình biên dịch tối ưu hóa hiện đại, mã được tạo ra thường có hiệu suất (tốc độ và mức tiêu thụ tài nguyên) ít nhất cũng ngang bằng với những gì một lập trình viên assembly lành nghề có thể viết bằng tay. Tuyệt vời nhất là, một chương trình được viết bằng ngôn ngữ bậc cao có thể được biên dịch và thực thi trên nhiều loại máy tính khác nhau, trong khi mã hợp ngữ lại **mang tính đặc thù rất cao cho từng dòng máy** (highly machine specific).

  * Vậy tại sao chúng ta lại nên dành thời gian để học machine code? Mặc dù compilers đã đảm nhiệm phần lớn công việc trong việc tạo ra assembly code, nhưng khả năng đọc và hiểu được nó lại là một kỹ năng quan trọng đối với các lập trình viên chuyên nghiệp. Bằng cách gọi trình biên dịch kèm theo các tham số dòng lệnh (command-line parameters) phù hợp, trình biên dịch sẽ tạo ra một tệp hiển thị đầu ra của nó dưới dạng assembly code. Thông qua việc đọc đoạn mã này, chúng ta có thể hiểu được khả năng tối ưu hóa của trình biên dịch và phân tích những sự kém hiệu quả tiềm ẩn bên trong mã nguồn. Như chúng ta sẽ trải nghiệm ở Chương 5, các lập trình viên khi tìm cách tối đa hóa hiệu năng của một đoạn mã quan trọng thường thử nhiều biến thể (variations) khác nhau của mã nguồn; và sau mỗi lần như vậy, họ sẽ biên dịch rồi kiểm tra mã hợp ngữ được tạo ra để có cái nhìn trực quan về mức độ hiệu quả khi chương trình thực thi. Hơn thế nữa, có những lúc lớp trừu tượng (layer of abstraction) do ngôn ngữ bậc cao cung cấp lại che giấu đi những thông tin về hành vi lúc chạy (run-time behavior) của một chương trình mà chúng ta thực sự cần phải hiểu. Ví dụ, khi viết các chương trình đồng thời (concurrent programs) sử dụng một gói luồng (thread package) như được đề cập trong Chương 12, việc hiểu rõ cách thức dữ liệu chương trình được chia sẻ hay được giữ riêng tư bởi các luồng khác nhau, cũng như biết chính xác dữ liệu dùng chung được truy cập như thế nào và ở đâu là điều rất quan trọng. Những thông tin như vậy chỉ có thể được nhìn thấy rõ ở cấp độ mã máy. Một ví dụ khác, nhiều phương thức tấn công chương trình – tạo điều kiện cho phần mềm độc hại (malware) xâm nhập vào hệ thống – đều liên quan đến những sắc thái tinh vi trong cách mà các chương trình lưu trữ thông tin điều khiển lúc chạy của chúng. Nhiều cuộc tấn công liên quan đến việc khai thác các điểm yếu trong các chương trình hệ thống để ghi đè (overwrite) thông tin, và qua đó chiếm quyền kiểm soát hệ thống. Để hiểu được các lỗ hổng (vulnerabilities) này phát sinh như thế nào cũng như làm thế nào để phòng chống chúng, đòi hỏi chúng ta phải có kiến thức về cách biểu diễn của chương trình ở cấp độ máy. Nhu cầu học mã máy của các lập trình viên trong những năm qua đã dịch chuyển: từ việc phải có khả năng viết chương trình trực tiếp bằng mã hợp ngữ sang việc có khả năng đọc và hiểu được đoạn mã do các trình biên dịch tạo ra.

  * Trong chương này, chúng ta sẽ tìm hiểu chi tiết về **assembly language** cụ thể và xem cách các chương trình **C** được biên dịch thành dạng mã máy này. Việc đọc mã hợp ngữ do một trình biên dịch tạo ra đòi hỏi một bộ kỹ năng khác biệt so với việc tự viết mã hợp ngữ bằng tay. Chúng ta phải hiểu được những phép biến đổi mà các trình biên dịch điển hình thực hiện khi chuyển đổi các cấu trúc của ngôn ngữ **C** thành mã máy. So với các phép tính toán được thể hiện trong mã **C**, các trình biên dịch tối ưu hóa (optimizing compilers) có thể sắp xếp lại thứ tự thực thi, loại bỏ các phép tính không cần thiết, thay thế các phép toán chậm bằng các phép toán nhanh hơn, và thậm chí biến đổi các phép tính đệ quy (recursive) thành các phép tính lặp (iterative). Việc hiểu được mối quan hệ giữa mã nguồn và mã hợp ngữ được tạo ra thường có thể là một thử thách — nó rất giống với việc bạn cố ráp một bộ tranh xếp hình nhưng các mảnh ghép lại có thiết kế hơi khác so với hình mẫu in trên vỏ hộp. Đây là một **reverse engineering** (kỹ thuật đảo ngược) — cố gắng thấu hiểu quá trình tạo ra một hệ thống bằng cách nghiên cứu chính hệ thống đó và lần ngược lại các bước. Trong trường hợp này, hệ thống là một chương trình hợp ngữ do máy móc tự động tạo ra, chứ không phải là thứ do con người thiết kế. Điều này giúp đơn giản hóa nhiệm vụ kỹ thuật đảo ngược, bởi vì đoạn mã được tạo ra tuân theo các khuôn mẫu khá quy tắc và chúng ta có thể tiến hành các thử nghiệm bằng cách yêu cầu trình biên dịch tạo mã cho nhiều chương trình khác nhau. Trong phần trình bày của mình, chúng tôi đưa ra nhiều ví dụ và cung cấp một số bài tập minh họa cho các khía cạnh khác nhau của hợp ngữ và trình biên dịch. Đây là một môn học mà việc nắm vững các chi tiết là điều kiện tiên quyết để thấu hiểu các khái niệm sâu sắc và mang tính nền tảng hơn. Những ai nói rằng "Tôi đã hiểu các nguyên lý chung, tôi không muốn bận tâm đến việc học các chi tiết" thực chất đang tự ru ngủ bản thân. Việc bạn dành thời gian để nghiên cứu các ví dụ, làm các bài tập và đối chiếu giải pháp của mình với các đáp án được cung cấp là điều cực kỳ quan trọng. 

  * Phần trình bày của chúng tôi dựa trên x86-64, ngôn ngữ máy dành cho hầu hết các bộ vi xử lý có trong máy tính xách tay và máy tính để bàn ngày nay, cũng như những bộ vi xử lý đang vận hành các trung tâm dữ liệu khổng lồ và siêu máy tính. Ngôn ngữ này đã trải qua một lịch sử tiến hóa lâu dài, bắt đầu từ bộ vi xử lý 16-bit đầu tiên của Tập đoàn Intel vào năm 1978, cho đến quá trình mở rộng lên 32-bit, và gần đây nhất là lên 64-bit. Trong suốt chặng đường đó, nhiều tính năng đã được bổ sung nhằm tận dụng tốt hơn công nghệ bán dẫn hiện có, đồng thời để đáp ứng nhu cầu của thị trường. Phần lớn sự phát triển này được thúc đẩy bởi Intel, nhưng đối thủ của họ là Advanced Micro Devices (AMD) cũng đã có những đóng góp quan trọng. Kết quả tạo ra là một thiết kế khá kỳ lạ với những tính năng mà ta chỉ có thể thấy hợp lý khi nhìn nhận từ góc độ lịch sử. Thiết kế này cũng chứa đầy các tính năng nhằm cung cấp khả năng tương thích ngược (backward compatibility) — những tính năng mà các trình biên dịch và hệ điều hành hiện đại không còn dùng đến nữa. Chúng tôi sẽ chỉ tập trung vào một tập con (subset) các tính năng được sử dụng bởi trình biên dịch gcc và hệ điều hành Linux. Điều này cho phép chúng ta tránh được phần lớn sự phức tạp cũng như nhiều tính năng cổ lỗ của kiến trúc x86-64.

  * Phần trình bày kỹ thuật của chúng tôi bắt đầu bằng một chuyến dạo quanh nhanh để cho thấy mối quan hệ giữa _C_, _assembly code_ và _machine code_. Sau đó, chúng tôi tiến tới các chi tiết của kiến trúc **x86-64**, bắt đầu với việc biểu diễn và thao tác dữ liệu, cùng với việc triển khai các lệnh điều khiển.Chúng ta sẽ xem cách các cấu trúc điều khiển (control constructs) trong **C**, chẳng hạn như các câu lệnh `if`, `while` và `switch`, được triển khai như thế nào. Sau đó, chúng tôi đề cập đến việc triển khai các thủ tục (`procedures` / `hàm`), bao gồm cả cách mà chương trình duy trì một ngăn xếp lúc chạy (**run-time stack**) để hỗ trợ việc truyền dữ liệu và luồng điều khiển giữa các thủ tục, cũng như cung cấp không gian lưu trữ cho các biến cục bộ (**local variables**).Tiếp theo, chúng tôi xem xét cách các cấu trúc dữ liệu như mảng (**arrays**), cấu trúc (**structures / struct**) và **unions** được triển khai ở cấp độ máy. Với nền tảng kiến thức về lập trình cấp độ máy này, chúng ta có thể kiểm tra các vấn đề liên quan đến việc tham chiếu bộ nhớ ngoài giới hạn (**out-of-bounds memory references**) và lỗ hổng khiến hệ thống dễ bị mắc phải các cuộc tấn công tràn bộ đệm (**buffer overflow attacks**).Chúng tôi hoàn thành phần trình bày này với một số mẹo sử dụng trình gỡ lỗi GDB (**GDB debugger**) để kiểm tra hành vi lúc chạy của một chương trình ở cấp độ máy. Chương này khép lại bằng phần trình bày về cách biểu diễn của chương trình máy tính đối với các đoạn mã liên quan đến dữ liệu và các phép toán số thực dấu phẩy động (**floating-point**).

  * Ngành công nghiệp máy tính gần đây đã thực hiện bước chuyển đổi từ các hệ thống máy 32-bit sang 64-bit. Một cỗ máy 32-bit chỉ có thể sử dụng tối đa khoảng 4 gigabyte (2^32 byte) bộ nhớ truy cập ngẫu nhiên (**Random Access Memory - RAM**). Với việc giá thành bộ nhớ đang giảm ở mức độ chóng mặt, cùng với nhu cầu tính toán và kích thước dữ liệu của chúng ta ngày càng tăng cao, việc vượt qua giới hạn này đã trở nên khả thi về mặt kinh tế lẫn đáng mong đợi về mặt kỹ thuật.Các cỗ máy 64-bit hiện tại có thể sử dụng bộ nhớ lên tới 256 terabyte (2^48 byte), và có thể dễ dàng được mở rộng để sử dụng tới 16 exabyte (2^64 byte). Mặc dù thật khó để tưởng tượng việc sở hữu một cỗ máy với dung lượng bộ nhớ khổng lồ đến vậy, nhưng hãy nhớ rằng 4 gigabyte từng được xem là một lượng bộ nhớ cực kỳ lớn (không tưởng) vào thời điểm các máy 32-bit trở nên phổ biến trong những năm 1970 và 1980.

  * Phần trình bày của chúng tôi tập trung vào các loại chương trình ở cấp độ máy (_machine-level programs_) được tạo ra khi biên dịch ngôn ngữ _C_ và các ngôn ngữ lập trình tương tự nhắm đến các hệ điều hành hiện đại. Do đó, chúng tôi không cố gắng đi sâu mô tả nhiều tính năng của kiến trúc **x86-64** – những tính năng vốn phát sinh từ việc hỗ trợ kế thừa / tương thích ngược (legacy support) đối với phong cách viết chương trình trong những ngày đầu của kỷ nguyên vi xử lý, thời điểm mà phần lớn mã lệnh được viết thủ công và các lập trình viên phải vật lộn với phạm vi địa chỉ bộ nhớ hạn hẹp do các hệ thống máy 16-bit mang lại.
<br>

## 3.1 A Historical Perspective (Góc nhìn lịch sử)

  * Dòng vi xử lý Intel, thường được gọi quen thuộc là **x86**, đã trải qua một quá trình tiến hóa lâu dài. Quá trình này bắt đầu với một trong những bộ vi xử lý 16-bit đơn chip (_single-chip_) đầu tiên, nơi mà nhà sản xuất đã phải đưa ra nhiều sự thỏa hiệp (nhượng bộ/cắt giảm) do những hạn chế về khả năng của công nghệ mạch tích hợp (integrated circuit) vào thời điểm đó. Kể từ đó, dòng vi xử lý này đã không ngừng phát triển nhằm tận dụng những cải tiến của công nghệ, cũng như để đáp ứng các nhu cầu về hiệu năng cao hơn và hỗ trợ các hệ điều hành tiên tiến hơn.

  * Danh sách dưới đây trình bày một số dòng (mô hình) vi xử lý Intel và một số tính năng chính của chúng, đặc biệt là những tính năng có ảnh hưởng đến việc lập trình ở cấp độ máy (**machine-level programming**). Chúng tôi sử dụng số lượng bóng bán dẫn (**transistors**) cần thiết để chế tạo các vi xử lý này như một thước đo cho thấy mức độ phức tạp của chúng đã tiến hóa ra sao. Trong bảng này, "K" biểu thị 1.000 ($10^3$), "M" biểu thị 1.000.000 ($10^6$), và "G" biểu thị 1.000.000.000 ($10^9$).
    * 8086 (Năm 1978, 29 ngàn bóng bán dẫn). Một trong những bộ vi xử lý 16-bit đơn chip đầu tiên. Phiên bản 8088, một biến thể của 8086 với bus bên ngoài 8-bit, đã tạo nên trái tim của các máy tính cá nhân IBM nguyên bản. IBM đã ký hợp đồng với Microsoft (lúc bấy giờ còn là một công ty rất nhỏ) để phát triển hệ điều hành MS-DOS. Các mẫu máy ban đầu có bộ nhớ 32.768 byte và hai ổ đĩa mềm (không có ổ cứng). Về mặt kiến trúc, các cỗ máy này bị giới hạn ở không gian địa chỉ 655.360 byte — các địa chỉ chỉ dài 20 bit (có thể định địa chỉ 1.048.576 byte), và hệ điều hành đã dành riêng 393.216 byte cho mục đích sử dụng của chính nó.
Năm 1980, Intel giới thiệu bộ đồng xử lý (coprocessor) số thực dấu phẩy động 8087 (45 ngàn bóng bán dẫn) để hoạt động song song với bộ xử lý 8086 hoặc 8088, đảm nhiệm thực thi các lệnh dấu phẩy động. 8087 đã thiết lập mô hình dấu phẩy động cho dòng x86, thường được gọi tắt là "x87".
    * 80286 (Năm 1982, 134 ngàn bóng bán dẫn). Bổ sung thêm các chế độ định địa chỉ (hiện nay đã lỗi thời). Dòng chip này tạo nên nền tảng cho máy tính cá nhân IBM PC-AT, nền tảng ban đầu cho hệ điều hành MS Windows.
    * i386 (Năm 1985, 275 ngàn bóng bán dẫn). Mở rộng kiến trúc lên 32-bit. Bổ sung mô hình định địa chỉ phẳng (flat addressing model) được sử dụng bởi Linux và các phiên bản hệ điều hành Windows gần đây. Đây là cỗ máy đầu tiên trong dòng vi xử lý này có thể hỗ trợ đầy đủ một hệ điều hành Unix.
    * i486 (Năm 1989, 1,2 triệu bóng bán dẫn). Cải thiện hiệu năng và tích hợp khối xử lý dấu phẩy động (FPU) lên trên cùng một chip xử lý nhưng không làm thay đổi đáng kể tập lệnh.
    * Pentium (Năm 1993, 3,1 triệu bóng bán dẫn). Cải thiện hiệu năng nhưng chỉ bổ sung thêm một số phần mở rộng nhỏ vào tập lệnh.
    * PentiumPro (Năm 1995, 5,5 triệu bóng bán dẫn). Giới thiệu một thiết kế vi xử lý hoàn toàn mới, được biết đến trong nội bộ với tên gọi vi kiến trúc P6 (P6 microarchitecture). Bổ sung một lớp các lệnh "di chuyển có điều kiện" (conditional move) vào tập lệnh.
    * Pentium/MMX (Năm 1997, 4,5 triệu bóng bán dẫn). Bổ sung lớp lệnh mới cho bộ xử lý Pentium để thao tác với các vector số nguyên. Mỗi đơn vị dữ liệu (datum) có thể dài 1, 2 hoặc 4 byte. Mỗi vector có tổng chiều dài 64 bit.
    * Pentium II (Năm 1997, 7 triệu bóng bán dẫn). Là sự tiếp nối của vi kiến trúc P6.
    * Pentium III (Năm 1999, 8,2 triệu bóng bán dẫn). Giới thiệu SSE, một lớp các lệnh để thao tác với các vector dữ liệu số nguyên hoặc số thực dấu phẩy động. Mỗi dữ liệu có thể là 1, 2, hoặc 4 byte, được đóng gói vào các vector 128 bit. Các phiên bản sau này của chip này đạt tới 24 triệu bóng bán dẫn do việc tích hợp bộ nhớ đệm level-2 (cache L2) lên trên chip.
    * Pentium 4 (Năm 2000, 42 triệu bóng bán dẫn). Mở rộng SSE thành SSE2, bổ sung thêm các kiểu dữ liệu mới (bao gồm số thực dấu phẩy động độ chính xác kép - double-precision), cùng với 144 lệnh mới cho các định dạng này. Với những phần mở rộng này, các trình biên dịch (compilers) có thể sử dụng các lệnh SSE thay vì các lệnh x87 để biên dịch mã dấu phẩy động.
    * Pentium 4E (Năm 2004, 125 triệu bóng bán dẫn). Bổ sung siêu phân luồng (hyperthreading), một phương pháp để chạy hai chương trình đồng thời trên một bộ xử lý đơn, cũng như EM64T - bản triển khai của Intel đối với phần mở rộng 64-bit cho kiến trúc IA32 do Advanced Micro Devices (AMD) phát triển, mà chúng ta thường gọi là x86-64.
    * Core 2 (Năm 2006, 291 triệu bóng bán dẫn). Quay trở lại với một vi kiến trúc tương tự như P6. Là bộ vi xử lý đa lõi (multi-core) đầu tiên của Intel, trong đó nhiều bộ vi xử lý được triển khai trên cùng một con chip duy nhất. Dòng này không hỗ trợ siêu phân luồng.
    * Core i7, Nehalem (Năm 2008, 781 triệu bóng bán dẫn). Kết hợp cả siêu phân luồng và đa lõi, với phiên bản ban đầu hỗ trợ hai chương trình thực thi trên mỗi lõi và lên đến bốn lõi trên mỗi chip.
    * Core i7, Sandy Bridge (Năm 2011, 1,17 tỷ bóng bán dẫn). Giới thiệu AVX, một phần mở rộng của SSE để hỗ trợ dữ liệu được đóng gói vào các vector 256 bit.
    * Core i7, Haswell (Năm 2013, 1,4 tỷ bóng bán dẫn). Mở rộng... (Ghi chú: Đoạn văn gốc của bạn bị cắt ngang ở chữ "Extende...", nhưng thực tế là nó tiếp tục với "Extended AVX to AVX2..." nghĩa là Mở rộng AVX thành AVX2).
  * Mỗi thế hệ vi xử lý kế tiếp đều được thiết kế để có khả năng tương thích ngược (**backward compatible**) — tức là có thể chạy được mã lệnh đã được biên dịch cho bất kỳ phiên bản nào trước đó. Như chúng ta sẽ thấy, có rất nhiều **"tàn tích" kỳ lạ** (strange artifacts) tồn tại trong tập lệnh do hệ quả của di sản mang tính tiến hóa này.Intel đã sử dụng một vài tên gọi khác nhau cho dòng vi xử lý của họ, bao gồm **IA32**, viết tắt của "Intel Architecture 32-bit" (Kiến trúc Intel 32-bit), và gần đây nhất là **Intel64**, phần mở rộng 64-bit cho IA32, mà chúng ta sẽ gọi nó là **x86-64**. Chúng ta sẽ gọi chung toàn bộ dòng vi xử lý này bằng cái tên thông tục thường được sử dụng là **"x86"**, phản ánh quy ước đặt tên vi xử lý của hãng cho đến tận thời kỳ chip i486.
  * Trong nhiều năm qua, một số công ty đã sản xuất các bộ vi xử lý **tương thích** (compatible) với vi xử lý của Intel, có khả năng chạy chính xác cùng các **chương trình ở cấp độ máy** (machine-level programs). Đứng đầu trong số này là Advanced Micro Devices (AMD).Trong nhiều năm, AMD luôn tụt hậu ngay phía sau Intel về mặt công nghệ, buộc họ phải áp dụng một chiến lược tiếp thị là sản xuất ra các bộ vi xử lý có giá thành rẻ hơn mặc dù hiệu năng có phần thấp hơn. Họ trở nên cạnh tranh hơn vào khoảng năm 2002, khi trở thành công ty đầu tiên phá vỡ rào cản **tốc độ xung nhịp** (clock-speed) 1-gigahertz đối với một bộ vi xử lý thương mại (bán đại trà trên thị trường), và giới thiệu **x86-64** – phần mở rộng 64-bit được áp dụng vô cùng rộng rãi cho kiến trúc IA32 của Intel.Mặc dù chúng tôi sẽ chủ yếu nói về các bộ vi xử lý của Intel, phần trình bày của chúng tôi vẫn hoàn toàn đúng và có thể áp dụng tương đương cho các bộ vi xử lý tương thích do các đối thủ của Intel sản xuất.
  * Phần lớn sự phức tạp của kiến trúc x86 không phải là mối bận tâm đối với những ai chỉ quan tâm đến các chương trình dành cho hệ điều hành Linux được tạo ra bởi trình biên dịch `gcc`. **Mô hình bộ nhớ** (memory model) được cung cấp trong bộ vi xử lý 8086 nguyên bản và các phần mở rộng của nó trong 80286 đã trở nên lỗi thời (obsolete) với sự xuất hiện của dòng i386. Các tập lệnh **số thực dấu phẩy động x87** (x87 floating-point instructions) nguyên bản cũng đã trở nên lỗi thời với sự ra đời của tập lệnh mở rộng SSE2. Mặc dù chúng ta vẫn thấy những **tàn tích** (vestiges) về quá trình tiến hóa lịch sử của x86 bên trong các chương trình x86-64, nhưng nhiều tính năng **tối nghĩa và cổ lỗ nhất** (most arcane features) của x86 đã không còn xuất hiện nữa.

> [!Note]
> **IA32 programing:**
> IA32, phiên bản tiền nhiệm 32-bit của x86-64, đã được Intel giới thiệu vào năm 1985. Nó đã đóng vai trò là ngôn ngữ máy được lựa chọn hàng đầu trong vài thập kỷ. Hầu hết các bộ vi xử lý x86 được bán ra hiện nay, cũng như hầu hết các hệ điều hành được cài đặt trên những cỗ máy này, đều được thiết kế để chạy x86-64.Tuy nhiên, chúng cũng có thể thực thi các chương trình IA32 trong một chế độ tương thích ngược (backward compatibility mode). Kết quả là, nhiều chương trình ứng dụng hiện nay vẫn đang được xây dựng dựa trên IA32. Thêm vào đó, nhiều hệ thống hiện tại không thể thực thi x86-64 do những hạn chế về phần cứng hoặc phần mềm hệ thống của chúng.Do đó, IA32 vẫn tiếp tục là một ngôn ngữ máy quan trọng. Bạn sẽ nhận thấy rằng việc có sẵn nền tảng kiến thức về x86-64 sẽ giúp bạn học ngôn ngữ máy IA32 một cách khá dễ dàng và nhanh chóng.
<br>

> [!Note]
> **Moore's Law**
> 
> <img width="513" height="307" alt="image" src="https://github.com/user-attachments/assets/6f70cd67-1ee4-4d0e-a30b-2ec92330b8d3" />
>
> Nếu chúng ta vẽ biểu đồ số lượng **bóng bán dẫn** (transistors) trong các bộ vi xử lý Intel khác nhau theo năm ra mắt, và sử dụng thang đo logarit (logarithmic scale) cho trục y, chúng ta có thể thấy rằng sự tăng trưởng này thật sự phi thường. Khi kẻ một đường thẳng khớp với các điểm dữ liệu (fitting a line through the data), chúng ta thấy rằng số lượng bóng bán dẫn tăng với tốc độ hàng năm xấp xỉ 37%, nghĩa là số lượng bóng bán dẫn tăng gấp đôi sau mỗi khoảng 26 tháng. Sự tăng trưởng này đã được duy trì bền bỉ trong suốt lịch sử nhiều thập kỷ của các bộ vi xử lý x86.Năm 1965, Gordon Moore, một nhà đồng sáng lập của Tập đoàn Intel, đã **ngoại suy** (extrapolated) từ công nghệ chip thời bấy giờ (khi đó họ có thể chế tạo các mạch điện với khoảng 64 bóng bán dẫn trên một con chip duy nhất) để đưa ra dự đoán rằng số lượng bóng bán dẫn trên mỗi chip sẽ tăng gấp đôi mỗi năm trong 10 năm tiếp theo. Dự đoán này sau đó được biết đến với tên gọi **Định luật Moore** (Moore's Law). Hóa ra, dự đoán của ông hơi có một chút lạc quan (vì thực tế là mất nhiều hơn 1 năm để gấp đôi), nhưng đồng thời cũng quá thiển cận/ngắn hạn (vì ông chỉ dự đoán cho 10 năm). Trong hơn 50 năm qua, **ngành công nghiệp bán dẫn** (semiconductor industry) đã có khả năng tăng gấp đôi số lượng bóng bán dẫn với chu kỳ trung bình cứ sau mỗi 18 tháng.Những **tốc độ tăng trưởng theo cấp số nhân** (exponential growth rates) tương tự cũng đã diễn ra đối với các khía cạnh khác của công nghệ máy tính, bao gồm dung lượng lưu trữ của đĩa từ (ổ cứng HDD) và các bộ nhớ bán dẫn (RAM, SSD). Những tốc độ tăng trưởng đáng chú ý này chính là những động lực chính thúc đẩy cuộc cách mạng máy tính.

<br>

## 3.2 Program Encodings (Mã hóa chương trình)

  * Giả sử chúng ta viết một chương trình C dưới dạng hai tệp p1.c và p2.c. Sau đó, chúng ta có thể biên dịch (compile) đoạn mã này bằng cách sử dụng một dòng lệnh Unix (Unix command line):
    ```bash
    linux> gcc -Og -o p p1.c p2.c
    ```

  * Lệnh `gcc` chỉ định **gcc C compiler**. Vì đây là trình biên dịch mặc định trên hệ điều hành Linux, chúng ta cũng có thể gọi (kích hoạt) nó bằng một lệnh đơn giản là `cc`. `Command-line option -Og` chỉ thị cho trình biên dịch áp dụng một mức độ **tối ưu hóa** (optimization) sao cho tạo ra mã máy bám sát với cấu trúc tổng thể của mã C ban đầu. Việc gọi các mức độ tối ưu hóa cao hơn có thể tạo ra đoạn mã bị biến đổi mạnh đến mức mối quan hệ giữa mã máy được tạo ra và mã nguồn ban đầu trở nên rất khó hiểu.Do đó, chúng ta sẽ sử dụng mức tối ưu hóa `-Og` như một công cụ học tập (để dễ đọc hiểu code), và sau đó sẽ xem xét điều gì xảy ra khi chúng ta tăng mức độ tối ưu hóa lên. Trong thực tế, các mức tối ưu hóa cao hơn (ví dụ: được chỉ định bằng tùy chọn `-O1` hoặc `-O2`) được coi là sự lựa chọn tốt hơn xét về mặt hiệu năng của chương trình (program performance) được tạo ra.
  * Lệnh `gcc` kích hoạt toàn bộ một chuỗi các chương trình để biến đổi mã nguồn (source code) thành mã thực thi (executable code).
    * **Đầu tiên, bộ tiền xử lý C** (C preprocessor) mở rộng mã nguồn để chèn thêm bất kỳ tệp nào được chỉ định bởi các lệnh `#include` và mở rộng bất kỳ macro nào được chỉ định bởi các khai báo `#define`.
    * Thứ hai, **compiler** tạo ra các phiên bản **assembly code** của hai tệp mã nguồn với tên là `p1.s` và `p2.s`.
    * Tiếp theo, **assembler** chuyển đổi **assembly code** thành các tệp mã đối tượng nhị phân (binary object-code files) `p1.o` và `p2.o`. Mã đối tượng là một dạng của mã máy — nó chứa các biểu diễn nhị phân của toàn bộ các câu lệnh, nhưng địa chỉ của các giá trị toàn cục (global values) thì vẫn chưa được điền vào.
    * Cuối cùng, `trình liên kết` (linker) gộp hai tệp mã đối tượng này lại với nhau cùng với mã triển khai các hàm thư viện (ví dụ như `printf`) và tạo ra tệp mã thực thi cuối cùng `p` (như được chỉ định bởi chỉ thị dòng lệnh `-o p`).
  * Mã thực thi là dạng mã máy thứ hai mà chúng ta sẽ xem xét — đây chính xác là dạng mã được thực thi bởi bộ vi xử lý (processor). Mối quan hệ giữa các dạng mã máy khác nhau này và quá trình liên kết sẽ được mô tả chi tiết hơn trong Chương 7.

### 3.2.1 Machine-level Code
<br>

  * Như được mô tả trong Phần 1.9.3, các hệ thống máy tính sử dụng một số dạng trừu tượng hóa (abstraction) khác nhau, che giấu đi những chi tiết triển khai bên dưới thông qua việc sử dụng một mô hình trừu tượng đơn giản hơn. Hai trong số các dạng trừu tượng này đặc biệt quan trọng đối với việc lập trình ở cấp độ máy. **Đầu tiên**, định dạng và hành vi của một chương trình ở cấp độ máy được xác định bởi **kiến trúc tập lệnh** (instruction set architecture), hay gọi tắt là **ISA**. ISA định nghĩa trạng thái của bộ vi xử lý, định dạng của các câu lệnh, và tác động mà mỗi câu lệnh này tạo ra đối với trạng thái đó. Hầu hết các ISA, bao gồm cả kiến trúc x86-64, đều mô tả hành vi của một chương trình như thể mỗi câu lệnh được thực thi **tuần tự** (in sequence), với một câu lệnh phải hoàn thành xong trước khi câu lệnh tiếp theo bắt đầu. Phần cứng của bộ vi xử lý thực tế lại phức tạp hơn rất nhiều, nó thực thi nhiều lệnh **đồng thời** (concurrently), nhưng nó sử dụng các **cơ chế bảo vệ** (safeguards) để đảm bảo rằng hành vi tổng thể của chương trình vẫn khớp hoàn toàn với hoạt động tuần tự do ISA quy định. **Thứ hai**, các địa chỉ bộ nhớ được sử dụng bởi một chương trình cấp độ máy là các **địa chỉ ảo** (virtual addresses), qua đó cung cấp một mô hình bộ nhớ trông giống như một **mảng byte rất lớn** (a very large byte array). Việc triển khai thực tế của hệ thống bộ nhớ này đòi hỏi sự kết hợp của nhiều bộ nhớ phần cứng (RAM, Cache, Disk...) và phần mềm hệ điều hành, như sẽ được mô tả trong Chương 9.
  * Compiler thực hiện phần lớn công việc trong toàn bộ chuỗi quá trình biên dịch, chuyển đổi các chương trình được biểu diễn bằng mô hình thực thi trừu tượng tương đối của ngôn ngữ C thành các câu lệnh cơ bản nhất mà bộ vi xử lý thực thi. Assembly code có dạng biểu diễn rất gần với mã máy. Đặc điểm chính của nó là được thể hiện dưới dạng văn bản (text) dễ đọc hơn khi so sánh với định dạng binary của machine code. Việc có khả năng hiểu được Assembly code và cách nó liên hệ với mã nguồn C gốc là một bước then chốt trong việc thấu hiểu cách máy tính thực thi chương trình.
  * Machine code của kiến trúc x86-64 khác biệt rất lớn so với mã C ban đầu. Ở cấp độ này, các phần của trạng thái bộ vi xử lý (processor state) bị lộ ra — những thứ mà thông thường bị ẩn đi đối với lập trình viên viết ngôn ngữ C:
    * **Bộ đếm chương trình** (Program Counter - PC): Thường được gọi là PC, và trong kiến trúc x86-64 được gọi là `%rip`, nó chỉ ra địa chỉ trong bộ nhớ của lệnh tiếp theo cần được thực thi.
    * **Tệp thanh ghi số nguyên** (Integer register file): Chứa 16 vị trí có tên gọi, lưu trữ các giá trị 64-bit. Các thanh ghi này có thể giữ địa chỉ (tương ứng với con trỏ trong C) hoặc dữ liệu số nguyên. Một số thanh ghi được sử dụng để theo dõi các phần quan trọng của trạng thái chương trình, trong khi các thanh ghi khác được dùng để giữ dữ liệu tạm thời, chẳng hạn như các đối số (arguments) và biến cục bộ (local variables) của một thủ tục (hàm), cũng như giá trị cần trả về bởi một hàm.
    * **Các thanh ghi mã điều kiện** (Condition code registers): Lưu trữ thông tin trạng thái về lệnh số học hoặc logic vừa được thực thi gần nhất. Chúng được sử dụng để triển khai các thay đổi có điều kiện trong luồng điều khiển hoặc luồng dữ liệu, ví dụ như điều cần thiết để thực hiện các câu lệnh `if` và `while`.
    * **Một tập hợp các thanh ghi vector** (Vector registers): Mỗi thanh ghi này có thể chứa một hoặc nhiều giá trị số nguyên hoặc giá trị số thực dấu phẩy động.
   
  * Trong khi ngôn ngữ C cung cấp một mô hình, trong đó các đối tượng với nhiều kiểu dữ liệu khác nhau có thể được khai báo và cấp phát trong bộ nhớ, thì mã máy (machine code) lại nhìn nhận bộ nhớ một cách đơn giản như một mảng byte lớn có thể đánh địa chỉ. Các kiểu dữ liệu tập hợp (Aggregate data types) trong C như mảng (arrays) và cấu trúc (structures) được biểu diễn trong mã máy dưới dạng các tập hợp byte liên tiếp. Ngay cả đối với các kiểu dữ liệu vô hướng (scalar data types), mã hợp ngữ (assembly code) cũng không phân biệt giữa số nguyên có dấu hay không dấu, giữa các kiểu con trỏ khác nhau, hay thậm chí là giữa con trỏ và số nguyên.
  * Bộ nhớ chương trình (program memory) chứa machine code thực thi của chương trình, một số thông tin cần thiết cho hệ điều hành, một **ngăn xếp thời gian chạy** (run-time stack) để quản lý việc gọi và trả về thủ tục (hàm), và các khối bộ nhớ do người dùng cấp phát (ví dụ: bằng cách sử dụng hàm thư viện `malloc`). Như đã đề cập trước đó, bộ nhớ chương trình được đánh địa chỉ bằng cách sử dụng **địa chỉ ảo** (virtual addresses). Tại bất kỳ thời điểm nào, chỉ một số phạm vi con giới hạn của các địa chỉ ảo mới được coi là hợp lệ. Ví dụ, các địa chỉ ảo x86-64 được biểu diễn bằng các từ (words) 64-bit. Trong các cách triển khai hiện tại của các cỗ máy này, 16 bit cao nhất phải được đặt là 0, vì vậy một địa chỉ có thể chỉ định (xác định) một byte trong phạm vi $2^{48}$, tương đương với 64 terabyte. Các chương trình thông thường hơn sẽ chỉ có quyền truy cập vào một vài megabyte, hoặc có thể là vài gigabyte. Hệ điều hành quản lý không gian địa chỉ ảo này, chuyển đổi các địa chỉ ảo thành các địa chỉ vật lý của các giá trị trong bộ nhớ thực của bộ vi xử lý.
  * Một câu lệnh máy (machine instruction) đơn lẻ chỉ thực hiện một phép toán cơ bản nhất. Ví dụ, nó có thể cộng hai con số được lưu trữ trong các thanh ghi (registers), chuyển dữ liệu giữa bộ nhớ và thanh ghi, hoặc nhảy (rẽ nhánh) có điều kiện đến một địa chỉ câu lệnh mới. Compiler phải tạo ra các chuỗi gồm những câu lệnh như vậy để triển khai (hiện thực hóa) các cấu trúc chương trình như: đánh giá biểu thức số học, vòng lặp, hoặc các thủ tục gọi hàm (procedure calls) và trả về (returns).

> [!Note]
> **The ever-changing forms of generated code**
> Trong phần trình bày của mình, chúng tôi sẽ hiển thị đoạn mã do một phiên bản `gcc` cụ thể tạo ra với các cài đặt tùy chọn dòng lệnh cụ thể. Nếu bạn biên dịch mã trên máy tính của chính mình, khả năng cao là bạn sẽ sử dụng một trình biên dịch khác hoặc một phiên bản `gcc` khác, và do đó, mã lệnh được tạo ra sẽ khác biệt. Cộng đồng mã nguồn mở hỗ trợ `gcc` liên tục thay đổi trình tạo mã (code generator) với mục tiêu tạo ra mã hiệu quả hơn, tuân theo các hướng dẫn về mã lệnh luôn thay đổi từ các nhà sản xuất bộ vi xử lý.
>
>Mục tiêu của chúng tôi khi nghiên cứu các ví dụ trong tài liệu này là để minh họa cách kiểm tra assembly code và ánh xạ nó ngược lại với các cấu trúc tìm thấy trong các ngôn ngữ lập trình bậc cao. Bạn sẽ cần phải điều chỉnh, thích nghi các kỹ thuật này để phù hợp với phong cách mã lệnh được tạo ra bởi trình biên dịch cụ thể mà bạn đang sử dụng.

<br>

### 3.2.2 Code Examples

 * Giả sử chúng ta viết một tệp mã nguồn C có tên `mstore.c` chứa một định nghĩa hàm sau:

   ```c
   long mult2(long, long);

   void multstore(long x, long y, long *dest) {
       long t = mult2(x, y);
       *dest = t;
   }
   ```

 * Để xem assembly code do trình biên dịch C tạo ra, chúng ta có thể sử dụng tùy chọn `-S` trên dòng lệnh:

   ```bash
   linux> gcc -Og -S mstore.c
   ```

 * Điều này sẽ khiến `gcc` chạy trình biên dịch để tạo ra một tệp hợp ngữ có tên là `mstore.s`, và sau đó dừng lại (không thực hiện các bước tiếp theo). (Thông thường, sau bước này, trình biên dịch sẽ tự động gọi trình dịch hợp ngữ — assembler — để tạo ra một tệp mã đối tượng object-code).
 * Tệp mã hợp ngữ (assembly-code file) chứa nhiều khai báo khác nhau, bao gồm cả tập hợp các dòng mã sau đây:

   ```asm
   multstore:
     pushq   %rbx
     movq    %rdx, %rbx
     call    mult2
     movq    %rax, (%rbx)
     popq    %rbx
     ret
   ```

 * Mỗi dòng thụt lề trong đoạn mã tương ứng với một câu lệnh máy (machine instruction) đơn lẻ. Ví dụ, câu lệnh `pushq` biểu thị rằng nội dung của thanh ghi `%rbx` cần được đẩy (push) vào **ngăn xếp chương trình** (program stack). Mọi thông tin về tên biến cục bộ hoặc kiểu dữ liệu đã bị **loại bỏ hoàn toàn** (stripped away).
 * Nếu chúng ta sử dụng command-line option `-c`, `gcc` sẽ thực hiện cả hai bước là biên dịch (compile) và dịch hợp ngữ (assemble) đoạn mã đó.

   ```bash
   linux> gcc -Og -c mstore.c
   ```
   
 * Điều này sẽ tạo ra một **tệp mã đối tượng** (object-code file) `mstore.o` ở định dạng nhị phân (binary format) và do đó không thể xem trực tiếp được (bằng các trình soạn thảo văn bản thông thường). Nằm ẩn bên trong 1.368 byte của tệp mstore.o là một chuỗi gồm 14 byte với dạng biểu diễn **thập lục phân** (hexadecimal) như sau:

   ```bash
   53 48 89 d3 e8 00 00 00 00 48 89 5b c3
   ```

 * Đây là **mã đối tượng** (object code) tương ứng với các lệnh assembly đã được liệt kê trước đó. Một bài học quan trọng cần rút ra từ điều này là: chương trình được thực thi bởi máy tính chỉ đơn giản là một **chuỗi các byte** (sequence of bytes) dùng để mã hóa một loạt các câu lệnh. Máy tính có rất ít thông tin về đoạn mã nguồn (source code) ban đầu mà từ đó các câu lệnh này được tạo ra.Để kiểm tra nội dung của các tệp mã máy (machine-code files), một lớp các chương trình được gọi là **trình rã mã** (disassemblers) có thể mang lại giá trị vô giá. Những chương trình này tạo ra một định dạng tương tự như mã assembly từ machine code gốc. Đối với các hệ thống Linux, chương trình `objdump` (viết tắt của "object dump" - kết xuất đối tượng) có thể đảm nhiệm vai trò này khi được cung cấp command-line flag `-d`:

   ```bash
   linux> objdump -d mstore.o
   ```

 * Kết quả thu được (trong đó chúng tôi đã bổ sung thêm các số thứ tự dòng ở lề bên trái và các chú thích bằng chữ in nghiêng) là như sau:

   ```bash
           Disassembly of function sum in binary file mstore.o

   1  0000000000000000 <multstore>:
      offset    Byte                       Equivalent assembly language
   2      0:    53                         push   %rbx
   3      1:    48 89 d3                   mov    %rdx, %rbx
   4      2:    e8 00 00 00 00             callq  9 <multstore+0x9>
   5      3:    48 89 03                   mov    %rax, (%rbx)
   6      c:    5b                         pop    %rbx
   7      d:    c3                         retq
   ```

 *  Ở lề bên trái, chúng ta thấy 14 giá trị byte hexadecimal được liệt kê trong chuỗi byte đã chỉ ra trước đó, được phân chia thành các nhóm có từ 1 đến 5 byte. Mỗi nhóm này đại diện cho một câu lệnh (instruction) đơn lẻ, với dạng assembly-language tương đương được hiển thị ở lề bên phải. Một vài đặc điểm đáng chú ý về machine code và dạng biểu diễn rã mã (disassembled representation) của nó:
   
    * Các câu lệnh x86-64 có thể có độ dài dao động từ 1 đến 15 byte. Việc mã hóa lệnh (instruction encoding) được thiết kế sao cho các lệnh thường được sử dụng và những lệnh có ít **toán hạng** (operands) hơn sẽ yêu cầu số lượng byte nhỏ hơn so với các lệnh ít phổ biến hoặc các lệnh có nhiều toán hạng.
    * Định dạng lệnh được thiết kế theo cách mà từ một vị trí bắt đầu nhất định, chỉ có duy nhất một cách **giải mã** (decoding) các byte đó thành các lệnh máy. Ví dụ, chỉ có lệnh `pushq %rbx` mới có thể bắt đầu bằng giá trị byte là `53`.
    * Disassembler xác định assembly code hoàn toàn chỉ dựa trên các chuỗi byte nằm trong machine-code files. Nó hoàn toàn không cần quyền truy cập vào mã nguồn (source code) hay các tệp assembly code của chương trình. Disassembler sử dụng một quy ước đặt tên (naming convention) cho các lệnh hơi khác một chút so với assembly code được tạo ra bởi trình biên dịch `gcc`. Trong ví dụ của chúng ta, nó đã lược bỏ **hậu tố** (suffix) ‘q’ khỏi rất nhiều câu lệnh. Những hậu tố này là các ký hiệu chỉ định kích thước (size designators) và có thể được lược bỏ trong hầu hết các trường hợp. Ngược lại, disassembler lại thêm hậu tố ‘q’ vào các lệnh call và ret. Một lần nữa, các hậu tố này hoàn toàn có thể được lược bỏ một cách an toàn.
  * Để tạo ra **mã thực thi thực tế** (actual executable code), chúng ta cần chạy một trình **liên kết** (linker) trên một tập hợp các **tệp mã đối tượng** (object-code files), trong đó bắt buộc phải có một tệp chứa hàm `main`. Giả sử trong tệp `main.c`, chúng ta có hàm sau đây:

    ```c
    #include<stdio.h>

    void multstore(long, long, long *);

    int main(){
        long d;
        multstore(2, 3, &d);
        printf("2 * 3 --> %ld\n", d);
        return 0;
    }

    long mult2(long a, long b){
        long s = a * b;
        return s;
    }
    ```  
   
  * Sau đó, chúng ta có thể tạo ra một **chương trình thực thi** (executable program) có tên là `prog` như sau:

    ```bash
    linux> gcc -Og -o prog main.c mstore.c
    ```

  * Tệp `prog` đã tăng kích thước lên 8.655 byte, bởi vì nó chứa không chỉ machine code cho các thủ tục mà chúng ta đã cung cấp, mà còn bao gồm cả mã lệnh được dùng để khởi động và kết thúc chương trình, cũng như các đoạn mã dùng để tương tác với hệ điều hành.
    * Chúng ta có thể disassemble tệp `prog` như sau:

       ```bash
       linux> objdump -d prog
       ```

  * Disambler sẽ trích xuất ra nhiều chuỗi mã lệnh khác nhau, bao gồm cả những đoạn sau đây:

    ```bash
                  Disassembly of function sum in binary file prog
    1      0000000000400540 <multstore>:
    2          400540: 53                             push %rbx
    3          400541: 48 89 d3                       mov %rdx,%rbx
    4          400544: e8 42 00 00 00                 callq 40058b <mult2>
    5          400549: 48 89 03                       mov %rax,(%rbx)
    6          40054c: 5b                             pop %rbx
    7          40054d: c3                             retq
    8          40054e: 90                             nop
    9          40054f: 90                             nop
    ```

 * Đoạn mã này gần như giống hệt với đoạn mã được tạo ra khi disassembly tệp `mstore.c`. Một điểm khác biệt quan trọng là các địa chỉ được liệt kê dọc theo cạnh trái đã khác đi —  linker đã dịch chuyển vị trí của đoạn mã này sang một dải địa chỉ khác. Điểm khác biệt thứ hai là linker đã điền sẵn địa chỉ mà lệnh `callq` cần dùng để gọi hàm `mult2` (dòng 4 trong đoạn disassembly). Một nhiệm vụ của linker là khớp nối (match) các lời gọi hàm với vị trí thực tế chứa mã thực thi của các hàm đó. Điểm khác biệt cuối cùng là chúng ta thấy có thêm hai dòng mã phụ (dòng 8–9). Những câu lệnh này sẽ không có bất kỳ tác động nào đến chương trình, vì chúng xuất hiện sau câu lệnh trả về (dòng 7). Chúng được chèn vào chỉ để kéo dài kích thước mã của hàm này cho tròn 16 byte, qua đó cho phép sắp xếp khối mã tiếp theo ở một vị trí tốt hơn nhằm tối ưu hóa hiệu năng của hệ thống bộ nhớ.

> [!Note]
> **How do I display the byte representation of a program**
>
> Để hiển thị mã đối tượng nhị phân (binary object code) của một chương trình (ví dụ: `mstore`), chúng ta sử dụng disassembler - sẽ được mô tả bên dưới để xác định xem mã của thủ tục (procedure) đó dài bao nhiêu byte. Trong trường hợp này, mã đó dài 14 byte. Sau đó, chúng ta chạy công cụ gỡ lỗi GNU `gdb` trên tệp `mstore.o` và nhập lệnh:
>
>  ```bash
>  (gdb) x/14xb multstore
>  ```
>
> Lệnh này yêu cầu `gdb` hiển thị (viết tắt là `x` — examine) 14 byte ở định dạng thập lục phân (cũng ký hiệu là `x`) với mỗi đơn vị là 1 byte (`b`), bắt đầu từ địa chỉ nơi hàm `multstore` được lưu trữ. Bạn sẽ thấy rằng `gdb` sở hữu nhiều tính năng hữu ích để phân tích các chương trình ở cấp độ máy, như sẽ được thảo luận trong Mục 3.10.2.


### 3.2.3 Notes on Formatting
<br>

 * Assembly code được tạo ra bởi `gcc` rất khó để con người đọc hiểu. Một mặt nó chứa đựng những thông tin mà chúng ta không cần phải bận tâm đến; mặt khác, nó lại không cung cấp bất kỳ mô tả nào về chương trình hoặc cách thức hoạt động của nó, Ví dụ, giả sử chúng ta thực thi lệnh:

   ```bash
   linux> gcc -Og -S mstore.c
   ```

 * để tạo ra tệp `mstore.s`. Nội dung đầy đủ của tệp này như sau:

   ```bash
           file "010-mstore.c"
           .text
           .globl multstore
           .type multstore, @function
   multstore:
           pushq %rbx
           movq %rdx, %rbx
           call mult2
           movq %rax, (%rbx)
           popq %rbx
           ret
           .size multstore, .-multstore
           .ident "GCC: (Ubuntu 4.8.1-2ubuntu1~12.04) 4.8.1"
           .section .note.GNU-stack,"",@progbits
   ```

 * Tất cả các dòng bắt đầu bằng dấu ‘.’ đều là các **chỉ thị** (directives) dùng để hướng dẫn assembler và linker. Chúng ta thường có thể bỏ qua các dòng này. Mặt khác, trong mã hợp ngữ gốc không có các chú thích giải thích về việc các câu lệnh thực hiện công việc gì hoặc chúng liên hệ như thế nào với mã nguồn C ban đầu. Để trình bày mã hợp ngữ một cách rõ ràng hơn, chúng tôi sẽ hiển thị nó dưới một dạng thức lược bớt hầu hết các chỉ thị, đồng thời bổ sung thêm số thứ tự dòng và các chú thích giải thích. Đối với ví dụ của chúng ta, một phiên bản có chú thích sẽ xuất hiện như sau:
   ```bash
                   void multstore(long x, long y, long *dest)
                   x in %rdi, y in %rsi, dest in %rdx
                 1 multstore:
                 2    pushq %rbx                   Save %rbx
                 3    movq %rdx, %rbx              Copy dest to %rbx
                 4    call mult2                   Call mult2(x, y)
                 5    movq %rax, (%rbx)            Store result at *dest
                 6    popq %rbx                    Restore %rbx
                 7    ret                          Return
   ```

 * Chúng tôi thường chỉ hiển thị những dòng mã có liên quan đến vấn đề đang được thảo luận. Mỗi dòng đều được đánh số ở phía bên trái để tiện tham chiếu và được chú thích ở phía bên phải bằng một mô tả ngắn gọn về tác động của lệnh đó cũng như cách nó liên hệ với các phép toán trong đoạn mã nguồn C gốc. Đây là một phiên bản đã được cách điệu theo cách mà các lập trình viên sử dụng assembly language thường định dạng mã của họ.

 * Chúng tôi cũng cung cấp các phần "Web asides" (phụ lục trên web) để bao hàm các tài liệu dành cho những người đặc biệt đam mê machine language. Một phụ lục trên web mô tả mã máy IA32. Khi đã có nền tảng về x86-64, việc học IA32 trở nên khá đơn giản. Một phụ lục khác trình bày ngắn gọn các cách để kết hợp mã hợp ngữ vào trong các chương trình C. Đối với một số ứng dụng, lập trình viên buộc phải "hạ cấp" xuống assembly code để truy cập vào các tính năng cấp thấp của máy tính. Một cách tiếp cận là viết toàn bộ các hàm bằng assembly code và kết hợp chúng với các hàm C trong giai đoạn liên kết (linking stage). Cách thứ hai là sử dụng tính năng hỗ trợ của `gcc` để nhúng assembly code trực tiếp vào bên trong các chương trình C.

> [!Note]
> **ATT verus Intel assembly code formats**
> 
> Trong phần trình bày của mình, chúng tôi sử dụng mã hợp ngữ ở định dạng **AT&T** (được đặt tên theo AT&T, công ty đã điều hành Phòng thí nghiệm Bell trong nhiều năm) — đây là định dạng mặc định cho `gcc`, `objdump` và các công cụ khác mà chúng ta sẽ xem xét. Các công cụ lập trình khác, bao gồm cả những công cụ từ Microsoft cũng như tài liệu từ Intel, lại hiển thị mã hợp ngữ theo **định dạng Intel**. Hai định dạng này khác biệt nhau ở một số điểm. Ví dụ, `gcc` có thể tạo ra mã theo định dạng Intel cho hàm multstore bằng cách sử dụng dòng lệnh:
>
>  ```bash
>  linux> gcc -Og -S -nasm=intel mstore.c
>  ```
>
> Dòng lệnh này cho ra đoạn assembly code như sau:
>
>  ```bash
>  multstore:
>    push    rbx
>    mov     rbx, rdx
>    call    mult2
>    mov     QWORD PTR [rbx], rax
>    pop     rbx
>    ret
>  ```
>
> Chúng ta thấy rằng định dạng Intel và AT&T khác nhau ở những điểm sau:
>
>  * **Định dạng Intel lược bỏ các hậu tố chỉ định kích thước**. Chúng ta thấy các lệnh như `push` và `mov` thay vì `pushq` và `movq`.
>  *  **Định dạng Intel lược bỏ ký tự ‘%’** ở phía trước tên thanh ghi, sử dụng `rbx` thay vì `%rbx`.
>  *  **Định dạng Intel có cách mô tả vị trí trong bộ nhớ khác biệt** — ví dụ: `QWORD PTR [rbx]` thay vì `(%rbx)`.
>  *  **Các lệnh có nhiều toán hạng liệt kê chúng theo thứ tự ngược lại**. Điều này có thể gây rất nhiều nhầm lẫn khi chuyển đổi giữa hai định dạng.
>
> Mặc dù chúng tôi sẽ không sử dụng định dạng Intel trong phần trình bày của mình, nhưng bạn sẽ bắt gặp nó trong các tài liệu từ Intel và Microsoft.
> 
   
   
## 3.3 Data Formats (Định dạng dữ liệu)
<br>

 * Do nguồn gốc là kiến trúc 16-bit rồi mở rộng lên 32-bit, Intel sử dụng thuật ngữ 'word' để chỉ kiểu dữ liệu 16-bit. Dựa trên cơ sở đó, họ gọi các đại lượng 32-bit là 'double words' và các đại lượng 64-bit là 'quad words' . Hình 3.1 cho thấy các cách biểu diễn x86-64 được sử dụng cho các kiểu dữ liệu nguyên thủy của C. Các giá trị `int` tiêu chuẩn được lưu trữ dưới dạng 'double words' (32-bit). Các con trỏ (được hiển thị ở đây dưới dạng `char *`) được lưu trữ dưới dạng 'quad words' 8-byte, đúng như dự kiến trên một máy 64-bit. Với kiến trúc x86-64, kiểu dữ liệu `long` được triển khai với 64 bit, cho phép một dải giá trị rất rộng. Hầu hết các ví dụ mã nguồn trong chương này sử dụng các kiểu dữ liệu con trỏ và `long`, vì vậy chúng sẽ thao tác trên các 'quad words'. Tập lệnh x86-64 cũng bao gồm đầy đủ các lệnh cho byte, word và double word.

 * Các số dấu phẩy động (floating-point) có hai định dạng chính: giá trị độ chính xác đơn (4-byte), tương ứng với kiểu dữ liệu `float` trong C, và giá trị độ chính xác kép (8-byte), tương ứng với kiểu dữ liệu `double` trong C. Các bộ vi xử lý trong dòng x86 trước đây đã triển khai tất cả các phép toán dấu phẩy động với định dạng dấu phẩy động 80-bit (10-byte) đặc biệt (xem Bài tập 2.86). Định dạng này có thể được chỉ định trong các chương trình C bằng cách sử dụng khai báo `long double`. Tuy nhiên, chúng tôi khuyên bạn không nên sử dụng định dạng này. Nó không có tính tương thích (portable) với các dòng máy khác và thường không được triển khai với phần cứng hiệu năng cao như các phép toán độ chính xác đơn và kép.

 * |**C declaration**|**Intel data type**|**Assembly code suffix**|**Size(bytes)**|
   |:--|:---|:---|:---|
   |char|Byte|b|1|
   |short|Word|w|2|
   |int|Double word|l|4|
   |long|Quad word|q|8|
   |char *|Quad word|q|8|
   |float|Single precision|s|4|
   |double|Double precision|l|8

   **Figure 3.1** Size of C data in x86_64. With a 64-bit machine, pointers are 8 bytes long.

 * Như bảng ở Hình 3.1 chỉ ra, hầu hết các lệnh hợp ngữ do `gcc` tạo ra đều có một hậu tố (suffix) gồm một ký tự duy nhất biểu thị kích thước của toán hạng. Ví dụ, lệnh di chuyển dữ liệu có bốn biến thể: `movb` (di chuyển byte), `movw` (di chuyển word), `movl` (di chuyển double word) và `movq` (di chuyển quad word). Hậu tố 'l' được sử dụng cho double word, vì các đại lượng 32-bit được coi là "long words". Mã hợp ngữ sử dụng hậu tố 'l' để chỉ định một số nguyên 4-byte cũng như một số dấu phẩy động độ chính xác kép 8-byte. Điều này không gây ra bất kỳ sự mơ hồ nào, vì mã dấu phẩy động liên quan đến một tập hợp các lệnh và thanh ghi hoàn toàn khác biệt
  

## 3.4 Accessing Information (Truy cập thông tin)
<br>

 * Một bộ xử lý trung tâm (CPU) x86-64 chứa một tập hợp gồm 16 thanh ghi đa năng (general-purpose registers) dùng để lưu trữ các giá trị 64-bit. Các thanh ghi này được sử dụng để lưu trữ cả dữ liệu số nguyên lẫn các con trỏ (pointers). Hình 3.2 minh họa 16 thanh ghi này. Tên của tất cả chúng đều bắt đầu bằng ký tự `%r`, nhưng lại tuân theo nhiều quy ước đặt tên khác nhau do sự tiến hóa lịch sử của tập lệnh. Bộ vi xử lý 8086 đời đầu có tám thanh ghi 16-bit (hiển thị trong Hình 3.2 là từ `%ax` đến `%bp`). Mỗi thanh ghi này có một mục đích cụ thể, do đó chúng được đặt tên phản ánh cách thức sử dụng chúng. Với sự mở rộng lên IA32 (32-bit), các thanh ghi này được mở rộng thành 32-bit, được gắn nhãn từ `%eax` đến `%ebp`. Trong bước mở rộng tiếp theo lên x86-64 (64-bit), tám thanh ghi gốc này lại được mở rộng thành 64-bit, được gắn nhãn từ `%rax` đến `%rbp`. Ngoài ra, tám thanh ghi mới đã được thêm vào và chúng được đặt tên theo một quy ước mới: từ `%r8` đến `%r15`.

 * <img width="611" height="716" alt="image" src="https://github.com/user-attachments/assets/440c8537-d6f1-432b-a227-119ed319cbc6" />


 * Như các ô lồng nhau trong Hình 3.2 chỉ ra, các lệnh (instructions) có thể thao tác trên dữ liệu với kích thước khác nhau được lưu trữ trong các byte thấp của 16 thanh ghi này. Các thao tác cấp byte có thể truy cập vào byte có trọng số thấp nhất (least significant byte), các thao tác 16-bit có thể truy cập vào 2 byte thấp nhất, các thao tác 32-bit truy cập vào 4 byte thấp nhất, và các thao tác 64-bit có thể truy cập vào toàn bộ thanh ghi.

 * Trong các phần sau, chúng tôi sẽ trình bày một số lệnh để sao chép và tạo ra các giá trị 1, 2, 4 và 8 byte. Khi các lệnh này có đích đến là thanh ghi, có hai quy ước nảy sinh về việc các byte còn lại trong thanh ghi sẽ như thế nào đối với các lệnh tạo ra ít hơn 8 byte: Những lệnh tạo ra dữ liệu 1 hoặc 2 byte sẽ giữ nguyên các byte còn lại của thanh ghi. Những lệnh tạo ra dữ liệu 4 byte sẽ đặt 4 byte cao của thanh ghi về 0. Quy ước thứ hai này được áp dụng như một phần của quá trình mở rộng từ IA32 lên x86-64.

 * Như các chú thích dọc theo cạnh phải của Hình 3.2 chỉ ra, các thanh ghi khác nhau đóng những vai trò khác nhau trong các chương trình thông thường. Độc đáo nhất trong số đó là con trỏ ngăn xếp (stack pointer) %rsp, được sử dụng để chỉ vị trí cuối cùng của ngăn xếp thời gian chạy (run-time stack). Một số lệnh đọc và ghi vào thanh ghi này một cách đặc biệt. 15 thanh ghi còn lại có sự linh hoạt hơn trong cách sử dụng. Một số ít lệnh sử dụng cụ thể một vài thanh ghi nhất định. Quan trọng hơn, một tập hợp các quy ước lập trình tiêu chuẩn sẽ điều khiển cách sử dụng các thanh ghi để quản lý ngăn xếp, truyền tham số hàm, trả về giá trị từ hàm, và lưu trữ dữ liệu cục bộ hoặc tạm thời. Chúng tôi sẽ đề cập đến các quy ước này trong bài trình bày của mình, đặc biệt là trong Mục 3.7, nơi chúng tôi mô tả việc thực thi các thủ tục (procedures).

### 3.4.1 Operand Specifiers
<br>

 * Hầu hết các câu lệnh đều có một hoặc nhiều toán hạng (operands) xác định các giá trị nguồn được sử dụng để thực hiện một thao tác và vị trí đích để đặt kết quả. Kiến trúc x86-64 hỗ trợ một số dạng toán hạng (xem Hình 3.3). Các giá trị nguồn có thể được cung cấp dưới dạng hằng số, hoặc được đọc từ thanh ghi (registers) hay bộ nhớ (memory). Kết quả có thể được lưu trữ trong thanh ghi hoặc bộ nhớ. Do đó, các khả năng toán hạng có thể được phân loại thành ba kiểu:
   * Kiểu thứ nhất, tức thời (**immediate**): Dành cho các giá trị hằng số. Trong assembly code định dạng AT&T, chúng được viết bằng một dấu `$` theo sau là một số nguyên sử dụng ký hiệu chuẩn của ngôn ngữ C — ví dụ: `$-577` hoặc `$0x1F`. Các câu lệnh khác nhau cho phép các dải giá trị tức thời khác nhau; assembler sẽ tự động chọn cách mã hóa một giá trị sao cho nhỏ gọn nhất.
   * Kiểu thứ hai, thanh ghi (**register**): Biểu thị nội dung của một thanh ghi, đó là một trong mười sáu phần bậc thấp (low-order portions) có kích thước 8, 4, 2, hoặc 1 byte của các thanh ghi, tương ứng dành cho các toán hạng có 64, 32, 16, hoặc 8 bit.
 * Trong Hình 3.3, chúng tôi sử dụng ký hiệu ra để biểu thị một thanh ghi `a` bất kỳ và biểu thị giá trị của nó bằng tham chiếu $R[r_a]$, xem tập hợp các thanh ghi như một mảng `R` được đánh chỉ số bởi các mã định danh thanh ghi (register identifiers).
   * Kiểu thứ ba là tham chiếu bộ nhớ (**memory reference**), trong đó chúng ta truy cập vào một vị trí bộ nhớ nào đó dựa trên một địa chỉ được tính toán, thường được gọi là **địa chỉ hiệu dụng** (effective address). Vì chúng ta coi bộ nhớ như một mảng byte khổng lồ, chúng ta sử dụng ký hiệu $M_b[Addr]$ để biểu thị một tham chiếu đến giá trị $b$-byte được lưu trữ trong bộ nhớ bắt đầu tại địa chỉ $Addr$. Để đơn giản hóa vấn đề, chúng ta thường sẽ lược bỏ chỉ số dưới $b$.
 * Như Hình 3.3 cho thấy, có nhiều chế độ định địa chỉ (addressing modes) khác nhau cho phép các dạng tham chiếu bộ nhớ khác nhau. Dạng tổng quát nhất được hiển thị ở cuối bảng với cú pháp $Imm(r_b, r_i, s)$. Một tham chiếu như vậy có bốn thành phần: một độ lệch tức thời (immediate offset) $Imm$, một thanh ghi cơ sở (base register) $r_b$, một thanh ghi chỉ số (index register) $r_i$, và một hệ số tỷ lệ (scale factor) $s$, trong đó $s$ bắt buộc phải là 1, 2, 4, hoặc 8. Cả thanh ghi cơ sở và thanh ghi chỉ số đều phải là các thanh ghi 64-bit. Địa chỉ hiệu dụng được tính toán theo công thức: $Imm + R[r_b] + R[r_i] \cdot s$. Dạng tổng quát này thường thấy khi tham chiếu đến các phần tử của mảng. Các dạng khác chỉ đơn giản là những trường hợp đặc biệt của dạng tổng quát này, trong đó một số thành phần được lược bỏ. Như chúng ta sẽ thấy, các chế độ định địa chỉ phức tạp hơn rất hữu ích khi tham chiếu đến các phần tử của mảng (array) và cấu trúc (structure).

 * |**Type**|**Form**|**Operand value**|**Name**|
   |:--|:--|:--|:--|
   |Immediate|$_Imm_|$Imm$|Immadiate|
   |Register|$r_a$|$M[R[r_a]]$|Register|
   |Memory|$Imm$|$M[Imm]$|Absolute|
   |Memory|($r_a$)|$M[R[r_a]]$|Indirect|
   |Memory|_Imm_($r_b$)|$M[Imm+R[r_b]]$|Base + displacement|
   |Memory|$(r_b,r_i)$|$M[R[r_b]+R[r_i]]$|Indexed|
   |Memory|_Imm_$(r_b,r_i)$|$M[Imm+R[r_b]+R[r_i]]$|Indexed|
   |Memory|$(,r_i,s)$|$M[R[r_i].s]$|Scaled indexed|
   |Memory|$Imm(,r_i,s)$|$M[Imm+R[r_i]].s]$|Scaled indexed|
   |Memory|$(r_b,r_i,s)$|$M[R[r_b]+R[r_i].s]$|Scaled indexed|
   |Memory|$Imm(r_b,r_i,s)$|$M[Imm+R[r_b]+R[r_i]].s]$|Scaled indexed|

   **Figure 3.3 Operand forms**. Operands can donote immediate(constant) values, register values, or values from memory. The scaling factor _s_ must be either 1,2,4, or 8.

* **Practice Problem 3.1**

  Giả sử các giá trị sau đây được lưu trữ tại memory addresses và registers được chỉ định:

  |Address|Value|Register|Value|
  |:--:|:--:|:--:|:--:|
  |0x100|0xff|%rax|0x100|
  |0x104|0xab|%rcx|0x1|
  |0x108|0x13|%rdx|0x3|
  |0x10c|0x11|

  Điền vào bảng sau để hiện thị các giá trị tương ứng cho các operands được chỉ định:

  |Operand|Value|
  |:--:|:--:|
  |%rax|0x100|
  |0x104|0xab|
  |$0x108|0x108|
  |(%rax)|0xff|
  |4(%rax)|0xab|
  |9(%rax,%rdx)|0x11|
  |260(%rcx,%rdx)|0x13|
  |0xfc(,%rcx,4)|0xff|
  |(%rax,%rdx,4)|0xab|


 ### 3.4.2 Data Movement Instructions
<br>

 * Trong số các lệnh được sử dụng nhiều nhất là những lệnh sao chép dữ liệu từ một vị trí này sang một vị trí khác. Tính tổng quát của ký hiệu toán hạng cho phép một lệnh di chuyển dữ liệu đơn giản thể hiện một loạt các khả năng, điều mà ở nhiều hệ thống máy tính khác sẽ đòi hỏi nhiều lệnh khác biệt nhau. Chúng tôi sẽ trình bày một số lệnh di chuyển dữ liệu khác nhau, phân biệt dựa trên kiểu nguồn và đích của chúng, các phép chuyển đổi mà chúng thực hiện, cũng như các tác dụng phụ khác mà chúng có thể gây ra. Trong phần trình bày của mình, chúng tôi phân nhóm nhiều lệnh khác nhau thành các **lớp lệnh** (instruction classes), trong đó các lệnh trong cùng một lớp thực hiện cùng một thao tác nhưng với kích thước toán hạng khác nhau.

 * Hình 3.4 liệt kê dạng đơn giản nhất của các lệnh di chuyển dữ liệu — `mov`. Những lệnh này sao chép dữ liệu từ một vị trí nguồn đến một vị trí đích mà không thực hiện bất kỳ sự biến đổi nào. Lớp này bao gồm bốn lệnh: `movb`, `movw`, `movl` và `movq`. Cả bốn lệnh này đều mang lại hiệu ứng tương tự nhau; chúng khác biệt chủ yếu ở chỗ chúng thao tác trên các dữ liệu có kích thước khác nhau: tương ứng là 1, 2, 4 và 8 byte.
 * <img width="407" height="193" alt="image" src="https://github.com/user-attachments/assets/0da0e23f-39d0-477c-b5c6-ace32d24e51d" />

 * **Toán hạng nguồn** (source operand) chỉ định một giá trị có thể là dạng tức thời (immediate), được lưu trữ trong một thanh ghi (register), hoặc được lưu trữ trong bộ nhớ (memory). Toán hạng đích (destination operand) chỉ định một vị trí có thể là một thanh ghi hoặc một địa chỉ bộ nhớ. Kiến trúc x86-64 áp đặt một hạn chế là: move instruction **không thể có cả hai toán hạng cùng tham chiếu đến các vị trí bộ nhớ**. Việc sao chép một giá trị từ một vị trí bộ nhớ này sang một vị trí bộ nhớ khác đòi hỏi phải dùng đến hai câu lệnh — lệnh thứ nhất để tải (load) giá trị nguồn vào một thanh ghi, và lệnh thứ hai để ghi (write) giá trị của thanh ghi này vào đích đến. Tham chiếu đến Hình 3.2, các toán hạng thanh ghi cho những lệnh này có thể là các phần được gắn nhãn của bất kỳ thanh ghi nào trong số 16 thanh ghi, trong đó **kích thước của thanh ghi phải khớp với kích thước được chỉ định bởi ký tự cuối cùng của lệnh** (‘b’, ‘w’, ‘l’, hoặc ‘q’). Trong hầu hết các trường hợp, lệnh `mov` sẽ chỉ cập nhật đúng các byte thanh ghi hoặc các vị trí bộ nhớ cụ thể được chỉ định bởi toán hạng đích. **Ngoại lệ duy nhất** là khi lệnh movl có đích đến là một thanh ghi, nó cũng sẽ thiết lập 4 byte bậc cao (high-order 4 bytes) của thanh ghi đó về `0`. Ngoại lệ này bắt nguồn từ một quy ước được áp dụng trong x86-64, đó là bất kỳ lệnh nào tạo ra giá trị 32-bit cho một thanh ghi thì cũng đều tự động thiết lập phần bậc cao của thanh ghi đó về `0`.
 * Các ví dụ về lệnh `mov` sau đây minh họa năm tổ hợp có thể có của các kiểu **toán hạng nguồn** (source) và toán hạng đích (destination). Hãy nhớ rằng toán hạng nguồn luôn đứng trước và toán hạng đích đứng sau

   ```bash
   1 movl $0x4050,%eax        Immediate--Register, 4 bytes
   2 movw %bp,%sp             Register--Register, 2 bytes
   3 movb (%rdi,%rcx),%al     Memory--Register, 1 byte
   4 movb $-17,(%esp)         Immediate--Memory, 1 byte
   5 movq %rax,-12(%rbp)      Register--Memory, 8 bytes
   ```

 * Lệnh cuối cùng được ghi lại trong Hình 3.4 dùng để xử lý dữ liệu tức thời 64-bit (64-bit immediate data). Lệnh `movq` thông thường chỉ có thể nhận các toán hạng nguồn tức thời có thể được biểu diễn dưới dạng số bù hai 32-bit (32-bit two’s-complement numbers). Giá trị này sau đó sẽ được **mở rộng dấu** (sign extended) để tạo ra giá trị 64-bit cho đích đến. Trong khi đó, lệnh `movabsq` có thể nhận một giá trị tức thời 64-bit tùy ý làm toán hạng nguồn và chỉ có thể dùng một thanh ghi (register) làm đích đến.

 * <img width="521" height="200" alt="image" src="https://github.com/user-attachments/assets/2cf3747c-1ec3-4c35-9f27-b96ae475e2ea" />
 
 * Hình 3.5 và 3.6 ghi nhận hai lớp lệnh di chuyển dữ liệu được sử dụng khi **sao chép một giá trị nguồn nhỏ hơn vào một đích đến lớn hơn**. Tất cả các lệnh này sao chép dữ liệu từ một nguồn (có thể là một thanh ghi hoặc được lưu trữ trong bộ nhớ) đến một đích đến bắt buộc phải là thanh ghi.
   * Các lệnh trong lớp `movz` sẽ lấp đầy các byte còn lại của đích đến bằng các số 0 (mở rộng bằng 0 - zero extension).
   * Các lệnh trong lớp `movs` sẽ lấp đầy các byte còn lại bằng cách mở rộng dấu (sign extension), tức là sao chép lặp lại bit có trọng số cao nhất (most significant bit) của toán hạng nguồn.
 * Hãy quan sát rằng mỗi tên lệnh đều có các ký tự chỉ định kích thước ở hai ký tự cuối cùng — ký tự đầu tiên chỉ định kích thước nguồn (source size), và ký tự thứ hai chỉ định kích thước đích (destination size). Như có thể thấy, có ba lệnh trong mỗi lớp này, bao trùm tất cả các trường hợp nguồn có kích thước 1 và 2 byte, và đích có kích thước 2 và 4 byte (tất nhiên chỉ xem xét các trường hợp đích có kích thước lớn hơn nguồn).
 * <img width="574" height="262" alt="image" src="https://github.com/user-attachments/assets/d3bcbabf-fca6-4320-bfaf-be336edbd5ca" />

 * Hãy lưu ý sự vắng mặt của một lệnh cụ thể dùng để mở rộng bằng số 0 (zero-extend) một giá trị nguồn 4 byte vào một đích đến 8 byte trong Hình 3.5. Theo logic, một lệnh như vậy sẽ được đặt tên là `movzlq`, nhưng lệnh này không hề tồn tại. Thay vào đó, kiểu di chuyển dữ liệu này có thể được thực hiện bằng cách sử dụng một lệnh `movl` với đích đến là một thanh ghi. Kỹ thuật này tận dụng một thuộc tính: bất kỳ lệnh nào tạo ra giá trị 4 byte với đích đến là một thanh ghi sẽ tự động lấp đầy 4 byte cao (upper 4 bytes) bằng các số 0. Mặt khác, đối với các đích đến 64-bit, thao tác di chuyển kèm mở rộng dấu (sign extension) được hỗ trợ cho cả ba loại kích thước nguồn (1, 2, và 4 byte), còn di chuyển kèm mở rộng bằng số 0 chỉ được hỗ trợ cho hai loại kích thước nguồn nhỏ hơn (1 và 2 byte).
 * Hình 3.6 cũng ghi nhận lệnh `cltq`. Lệnh này không có toán hạng (operands) nào — nó luôn luôn sử dụng thanh ghi `%eax` làm nguồn và `%rax` làm đích đến cho kết quả đã được mở rộng dấu. Do đó, nó mang lại kết quả hoàn toàn giống hệt với lệnh `movslq %eax, %rax`, nhưng lại có mã hóa (encoding) nhỏ gọn hơn.

 * **Practice Problem 3.2**
   
   Đối với mỗi dòng mã hợp ngữ (assembly language) dưới đây, hãy xác định hậu tố lệnh (instruction suffix) phù hợp dựa trên các toán hạng (operands) của nó. (Ví dụ: lệnh mov có thể được viết lại thành movb, movw, movl, hoặc movq.)

   ```asm
   movl  %eax, (%rsp)
   movw  (%rax), %dx
   movb  $0xFF, %bl
   movb  (%rsp,%rdx,4), %dl
   movq  (%rdx), %rax
   movw  %dx, (%rax)
   ```

* **Practice Problem 3.3**

  Mỗi dòng mã lệnh dưới đây đều phát sinh một thông báo lỗi khi chúng ta gọi trình dịch hợp ngữ (assembler). Hãy giải thích lỗi sai của từng dòng.

  ```text
  movb $0xf, (%ebx)          Lỗi sai kích thước thanh ghi làm con trỏ bộ nhớ: Trong kiến trúc x86_64 (64-bit), địa chỉ bộ nhớ là 64-bit nên bắt buộc phải dùng thanh ghi 64-bit để trỏ (%rbx). Việc dùng (%ebx) là sai vì thanh ghi 32-bit không thể đại diện cho một địa chỉ bộ nhớ 64-bit.

  movl %rax, (%rsp)          Lỗi sai kích thước dữ liệu truyền đi: Lệnh movl yêu cầu di chuyển dữ liệu 32- bit, nhưng thanh ghi nguồn %rax lại là thanh ghi 64-bit. Kích thước lệnh và thanh ghi nguồn không khớp nhau.
  
  movw (%rax), 4(%rsp)       Lỗi di chuyển trực tiếp từ bộ nhớ sang bộ nhớ (Memory-to-Memory): Kiến trúc x86_64 không cho phép một câu lệnh mov có cả toán hạng nguồn (%rax) và toán hạng đích 4(%rsp) đều là ô nhớ [1]. Phần cứng CPU không hỗ trợ luồng xử lý này trong cùng một chu kỳ lệnh.

  movb %al, %sl              Không có register sl
  
  movq %rax, $0x123          Lỗi ghi dữ liệu vào hằng số: Trong cú pháp AT&T, thứ tự là mov nguồn, đích. Câu lệnh này đang cố gắng ghi giá trị của thanh ghi %eax vào hằng số $0x123. Hằng số là một giá trị cố định được nạp trực tiếp từ mã máy, không phải là một ô nhớ hay thanh ghi nên không thể bị ghi đè dữ liệu vào
  
  movl %eax, %dx             Lỗi kích thước toán hạng đích không phù hợp: Thanh ghi nguồn %eax có kích thước 4 bytes (32-bit), trong khi thanh ghi đích %dx chỉ chứa được tối đa 2 bytes (16-bit). Bạn không thể nhét một dữ liệu lớn vào một thanh ghi đích có kích thước nhỏ hơn.
  
  movb %si, 8(%rbp)          Lỗi lệch kích thước giữa lệnh và thanh ghi nguồn: Hậu tố b của lệnh movb quy định di chuyển dữ liệu kích thước 1 byte (8-bit), nhưng thanh ghi nguồn %si lại là thanh ghi 2 bytes (16-bit). Định danh thanh ghi và lệnh không đồng bộ với nhau.
  ```

> [!Note]
> **Understanding how data movement changes a destination register**
>
> Như đã mô tả, có hai quy ước khác nhau về việc liệu các lệnh di chuyển dữ liệu có sửa đổi các byte bậc cao (upper bytes) của thanh ghi đích hay không, và sửa đổi như thế nào. Sự khác biệt này được minh họa qua đoạn mã sau:
>
> ```asm
> 1   movabsp   $0x0011223344556677, %rax                  %rax=0011223344556677
> 2   movb      $-1, %al                                   %rax=00112233445566ff
> 3   movw      $-1, %ax                                   %rax=001122334455ffff
> 4   movl      $-1, %eax                                  %rax=00000000ffffffff
> 5   movq      $-1, %rax                                  %rax=ffffffffffffffff
> ```
>
> trong phần thảo luận dưới đây, chúng ta sử dụng hệ hexadecimal. Trong ví dụ này, câu lệnh ở dòng 1 khởi tạo thanh ghi `%rax` với chuỗi mẫu `0011223344556677`. Các câu lệnh còn lại đều có giá trị tức thời (immediate value) là `-1` làm giá trị nguồn. Hãy nhớ lại rằng biểu diễn thập lục phân của `-1` luôn có dạng `FF...F`, trong đó số lượng ký tự `F` gấp đôi số lượng byte của phần biểu diễn. Do đó, lệnh `movb` (dòng 2) sẽ thiết lập 1 byte bậc thấp (low-order byte) của `%rax` thành `FF`, trong khi lệnh `movw` (dòng 3) thiết lập 2 byte bậc thấp thành `FFFF`, và giữ nguyên các byte còn lại. Lệnh `movl` (dòng 4) thiết lập 4 byte bậc thấp thành `FFFFFFFF`, nhưng nó cũng đồng thời thiết lập 4 byte bậc cao thành `00000000`. Cuối cùng, lệnh `movq` (dòng 5) thiết lập toàn bộ thanh ghi thành `FFFFFFFFFFFFFFFF`.
>

> [!Note]
>
> **Comparing byte movement instructions**
>
> Ví dụ sau đây minh họa cách các lệnh di chuyển dữ liệu khác nhau có làm thay đổi hay không các byte bậc cao (high-order bytes) của đích đến. Hãy chú ý rằng ba lệnh di chuyển byte movb, movsbq, và movzbq khác biệt nhau ở những điểm rất tinh tế. Dưới đây là một ví dụ:
>
> ```asm
> 1 movabsq $0x0011223344556677, %rax          %rax=0011223344556677
> 2 movb    $0xaa ,%dl                         %dl=aa
> 3 movb    %dl,%al                            %rax=00112233445566aa
> 4 movsbq  %dl,%rax                           %rax=ffffffffffffffaa
> 5 movzbq  %dl,%rax                           %rax=00000000000000aa
> ```
>
> Trong phần thảo luận dưới đây, chúng ta sử dụng ký hiệu thập lục phân (hexadecimal) cho tất cả các giá trị. Hai dòng mã đầu tiên khởi tạo các thanh ghi %rax và %dl tương ứng với các giá trị 0011223344556677 và AA. Các lệnh còn lại đều thực hiện việc sao chép byte bậc thấp (low-order byte) của %rdx sang byte bậc thấp của %rax.
> * Lệnh `mov` không làm thay đổi các byte khác của register dest.
> * Lệnh `movsbq` thiết lập 7 byte còn lại thành toàn bộ các bit `1` hoặc toàn bộ các bit `0` tùy thuộc vào bit bậc cao nhất (bit dấu) của byte nguồn. Vì ký tự thập lục phân `a` tương đương với giá trị nhị phân `1010`, quá trình mở rộng dấu (sign extension) khiến từng byte bậc cao hơn đều được thiết lập thành `ff`.
> * Lệnh `movzbq` luôn thiết lập 7 byte còn lại thành số `0`.
### 3.4.3 Data Movement Example
<br>

* Như một ví dụ về đoạn mã sử dụng các lệnh di chuyển dữ liệu, hãy xem xét thủ tục hoán đổi dữ liệu (data exchange routine) được hiển thị trong Hình 3.7, dưới cả hai dạng: mã C và mã hợp ngữ (assembly code) được tạo ra bởi `gcc`.
* <img width="516" height="320" alt="image" src="https://github.com/user-attachments/assets/469d76d0-7227-47a2-9cd8-6cf5043ee6c3" />

* Như Hình 3.7(b) cho thấy, hàm exchange được thực thi chỉ với ba câu lệnh: hai lệnh di chuyển dữ liệu (movq) cộng với một lệnh để quay trở lại vị trí mà từ đó hàm được gọi (ret). Chúng ta sẽ đề cập đến các chi tiết về cơ chế gọi và trả về của hàm trong Mục 3.7. Từ giờ cho đến lúc đó, bạn chỉ cần biết rằng các đối số (arguments) được truyền vào các hàm thông qua các thanh ghi (registers). Đoạn mã hợp ngữ có chú thích của chúng tôi đã ghi chú rõ những điều này. Một hàm trả về một giá trị bằng cách lưu trữ giá trị đó trong thanh ghi %rax, hoặc trong một trong các phần bậc thấp (low-order portions) của thanh ghi này."
* Khi hàm bắt đầu thực thi, các tham số của hàm là `xp` và `y` được lưu trữ tương ứng trong các thanh ghi `%rdi` và `%rsi`. Câu lệnh thứ 2 sau đó đọc `x` từ bộ nhớ và lưu giá trị đó vào thanh ghi `%rax`, đây là một bước triển khai trực tiếp cho phép toán `x = *xp` trong chương trình C. Về sau, thanh ghi `%rax` sẽ được sử dụng để trả về một giá trị từ hàm, và do đó giá trị trả về sẽ là `x`. Câu lệnh thứ 3 ghi `y` vào vị trí bộ nhớ được chỉ định bởi `xp` nằm trong thanh ghi `%rdi`, một bước triển khai trực tiếp cho phép toán `*xp` = `y`. Ví dụ này minh họa cách các lệnh `mov` có thể được sử dụng để đọc từ bộ nhớ vào thanh ghi (dòng 2), và ghi từ thanh ghi ra bộ nhớ (dòng 3).
* Có hai đặc điểm về đoạn assembly code này rất đáng chú ý. Thứ nhất, chúng ta thấy rằng những gì chúng ta gọi là "con trỏ" (pointers) trong C thực chất chỉ là các địa chỉ. Việc giải tham chiếu (dereferencing) một con trỏ bao gồm việc sao chép con trỏ đó vào một thanh ghi, và sau đó sử dụng thanh ghi này trong một tham chiếu bộ nhớ. Thứ hai, các biến cục bộ (local variables) như `x` thường được giữ trong các thanh ghi thay vì được lưu trữ tại các vị trí bộ nhớ. Việc truy cập thanh ghi nhanh hơn rất nhiều so với truy cập bộ nhớ.

* **Practice Problem 3.4**

  Giả sử các biến sp và dp được khai báo với các kiểu sau:

  ```c
  src_t *sp;
  dest_t *dp;
  ```

  trong đó `src_t` và `dest_t` là các kiểu dữ liệu được khai báo bằng `typedef`. Chúng ta muốn sử dụng một cặp lệnh di chuyển dữ liệu (data movement instructions) phù hợp để thực hiện phép toán:

  ```c
  *dp = (dest_t) *sp;
  ```

  Giả sử rằng giá trị của `sp` và `dp` được lưu trữ tương ứng trong các thanh ghi `%rdi` và `%rsi`. Đối với mỗi mục trong bảng, hãy viết ra hai câu lệnh thực hiện việc di chuyển dữ liệu được chỉ định.
  Câu lệnh đầu tiên trong chuỗi phải đọc dữ liệu từ bộ nhớ, thực hiện thao tác chuyển đổi phù hợp, và gán giá trị cho một phần tương ứng của thanh ghi `%rax`. Câu lệnh thứ hai sau đó sẽ ghi phần tương ứng của `%rax` ra bộ nhớ. Trong cả hai trường hợp, các phần của thanh ghi có thể là `%rax`, `%eax`, `%ax`, hoặc `%al`, và chúng có thể khác nhau ở hai lệnh này.
  Hãy nhớ lại rằng khi thực hiện một phép ép kiểu (cast) liên quan đến cả việc thay đổi kích thước (size) và thay đổi "tính có dấu/không dấu" (signedness) trong C, phép toán phải ưu tiên thay đổi kích thước trước (xem Mục 2.2.6).

  |src_t|dest_t|Instruction|
  |:--|:--|:--|
  |long|long|movq (%rdi), %rax|
  |||movq %rax, (%rsi)|
  |char|int|movsbl (%rdi), %eax|
  |||movl %eax, (%rsi)|
  |char|unsigned|movzbw (%rdi), %eax|
  |||movl %eax, (%rsi)|
  |unsigned char|long|movzbl (%rdi), %rax|
  |||movl %eax, (%rsi)|
  |int|char|movl (%rdi), %eax|
  |||movb %al, (%rsi)|
  |unsigned|unsigned char|movl (%rdi), %eax|
  |||movb %al, (%rsi)|
  |char|short|movsbw (%rdi), %ax|
  |||movw %ax, (%rsi)|

  Quy tắc cốt lõi:
   * Quy tắc ép kiểu(casting rule): Khi ép kiểu liên quan đến cả việc thay đổi kích thước và tính có dấu/ không dấu, phải thay đổi kích thước trước.
   * Quy tắc thu hẹp(truncation rule): Khi ép kiểu từ kiểu lớn xuống kiểu nhỏ, ta chỉ cần đọc dữ liệu vào thanh ghi, sau đó ghi phần thanh ghi nhỏ (bậc thấp) tương ứng ra bộ nhớ. 


* **Practice Problem 3.5**

  Bạn được cung cấp các thông tin sau. Một hàm với nguyên mẫu

  ```c
  void decode1(long *xp, long *yp, long *zp);
  ```
  được biên dịch sang assembly code, thu được kết quả như sau:

  ```asm
     void decode1(long *xp, long *yp, long *zp)
     xp in %rdi, yp in %rsi, zp in %rdx

  decode1:
    movq (%rdi), %r8
    movq (%rsi), %rcx
    movq (%rdx), %rax
    movq %r8, (%rsi)
    movq %rcx, (%rdx)
    movq %rax, (%rdi)
    ret
  ```

  Các tham số xp, yp, và zp được lưu trữ lần lượt trong các thanh ghi %rdi, %rsi, và %rdx.

  Hãy viết mã C cho hàm decode1 sao cho nó có tác dụng tương đương với đoạn assembly code đã cho.

  ```c
  void decode1(long *xp, long *yp, long *zp){
     long tmp1 = *xp;
     long tmp2 = *yp;
     long tmp3 = *zp;
     *yp = tmp1;
     *zp = tmp2;
     *xp = tmp3;
  }


### 3.4.4 Pushing and Popping Stack Data
<br>

 * Hai thao tác di chuyển dữ liệu cuối cùng được sử dụng để đẩy (push) dữ liệu vào và lấy (pop) dữ liệu ra khỏi ngăn xếp của chương trình (program stack), như được ghi chú trong Hình 3.8. Như chúng ta sẽ thấy, ngăn xếp đóng một vai trò quan trọng trong việc xử lý các lệnh gọi thủ tục (procedure calls). Nói qua về kiến thức nền, ngăn xếp là một cấu trúc dữ liệu nơi các giá trị có thể được thêm vào hoặc xóa đi, nhưng chỉ tuân theo kỷ luật "vào sau, ra trước" (last-in, first-out - LIFO). Chúng ta thêm dữ liệu vào ngăn xếp thông qua thao tác đẩy (push) và loại bỏ dữ liệu thông qua thao tác lấy ra (pop), với thuộc tính là giá trị được lấy ra sẽ luôn là giá trị được đẩy vào gần đây nhất và vẫn còn nằm trên ngăn xếp. Một ngăn xếp có thể được triển khai dưới dạng một mảng, nơi chúng ta luôn chèn và xóa các phần tử từ cùng một đầu của mảng. Đầu này được gọi là đỉnh của ngăn xếp (top of the stack). Với kiến trúc x86-64, ngăn xếp chương trình được lưu trữ trong một vùng nào đó của bộ nhớ. Như được minh họa trong Hình 3.9, ngăn xếp phát triển hướng xuống dưới (grows downward) sao cho phần tử trên cùng của ngăn xếp có địa chỉ thấp nhất trong tất cả các phần tử của ngăn xếp. (Theo quy ước, chúng ta vẽ các ngăn xếp lộn ngược, với "đỉnh" của ngăn xếp được hiển thị ở phần dưới cùng của hình vẽ.) Con trỏ ngăn xếp (stack pointer) `%rsp` lưu giữ địa chỉ của phần tử nằm trên đỉnh ngăn xếp.
 * <img width="411" height="159" alt="image" src="https://github.com/user-attachments/assets/d43d6d80-a126-4614-a85e-8dd67045f651" />

 * <img width="571" height="440" alt="image" src="https://github.com/user-attachments/assets/8c09fa1e-f059-4346-b902-891970ec1b13" />

 * Lệnh `pushq` cung cấp khả năng đẩy (push) dữ liệu vào ngăn xếp (stack), trong khi lệnh `popq` lấy (pop) dữ liệu ra khỏi đó. Mỗi lệnh này chỉ nhận một toán hạng (operand) duy nhất — đóng vai trò là nguồn dữ liệu đối với thao tác đẩy, và là đích đến của dữ liệu đối với thao tác lấy ra.
 * Lệnh pushq cung cấp khả năng đẩy (push) dữ liệu vào ngăn xếp (stack), trong khi lệnh popq lấy (pop) dữ liệu ra khỏi đó. Mỗi lệnh này chỉ nhận một toán hạng (operand) duy nhất — đóng vai trò là nguồn dữ liệu đối với thao tác đẩy, và là đích đến của dữ liệu đối với thao tác lấy ra.
 * Do đó, hành vi của lệnh `pushq %rbp` hoàn toàn tương đương với cặp lệnh sau:

   ```asm
   subq $8, %rsp         Decrement stack pointer
   movq %rbp, (%rsp)     Store %rbp on stack
   ```

 * ...ngoại trừ một điểm khác biệt là lệnh `pushq` được mã hóa trong machine code chỉ bằng một byte duy nhất, trong khi cặp lệnh hiển thị ở trên đòi hỏi tổng cộng 8 byte. Hai cột đầu tiên trong Hình 3.9 minh họa kết quả của việc thực thi lệnh `pushq %rax` khi `%rsp` đang có giá trị là `0x108` và `%rax` là `0x123`. Đầu tiên, `%rsp` được giảm đi `8`, tạo ra địa chỉ `0x100`, và sau đó giá trị `0x123` được lưu trữ tại địa chỉ bộ nhớ `0x100`.
 * Việc lấy (popping) một giá trị quad word bao gồm việc **đọc** dữ liệu từ vị trí đỉnh ngăn xếp hiện tại và sau đó `tăng` con trỏ ngăn xếp thêm 8. Do đó, lệnh `popq %rax` tương đương với cặp lệnh sau:

   ```asm
   movq (%rsp), %rax      Read %rax from stack
   addq $8, %rsp          Increment stack pointer
   ```

 * Cột thứ ba của Hình 3.9 minh họa kết quả của việc thực thi lệnh `popq %rdx` ngay sau khi đã thực hiện lệnh `pushq`. Giá trị `0x123` được đọc từ bộ nhớ và ghi vào thanh ghi `%rdx`. Con trỏ ngăn xếp `%rsp` được tăng trở lại giá trị `0x108`. Như được thể hiện trong hình, giá trị `0x123` vẫn còn nằm tại vị trí bộ nhớ `0x104` cho đến khi nó bị ghi đè (ví dụ: bởi một thao tác push khác). Tuy nhiên, đỉnh của ngăn xếp luôn được coi là địa chỉ do %rsp chỉ định.
 * Vì ngăn xếp được chứa trong cùng vùng bộ nhớ với mã chương trình và các dạng dữ liệu chương trình khác, nên các chương trình có thể truy cập vào các vị trí tùy ý bên trong ngăn xếp bằng cách sử dụng các phương thức định địa chỉ bộ nhớ tiêu chuẩn. Ví dụ, giả sử phần tử nằm trên cùng của ngăn xếp là một `quad word` (8 byte), thì lệnh `movq 8(%rsp), %rdx` sẽ sao chép `quad word` thứ hai từ ngăn xếp vào thanh ghi `%rdx`.

## 3.5 Arithmetic and Logical Operations (Các phép toán số học và logic)
<br>

 * Hình 3.10 liệt kê một số lệnh tính toán số học và logic của x86-64. Hầu hết các thao tác này được đưa ra dưới dạng các lớp lệnh (instruction classes), vì chúng có thể có các biến thể khác nhau với kích thước toán hạng khác nhau. (Chỉ riêng lệnh `leaq` là không có các biến thể kích thước khác). Ví dụ, lớp lệnh `add` bao gồm bốn lệnh cộng: `addb`, `addw`, `addl`, và `addq`, lần lượt thực hiện cộng trên các đơn vị dữ liệu là byte, word (2 byte), double word (4 byte) và quad word (8 byte). Thực tế, mỗi lớp lệnh được hiển thị đều có các lệnh để vận hành trên bốn kích thước dữ liệu khác nhau này. Các thao tác này được chia thành bốn nhóm: `tải địa chỉ hiệu dụng` (load effective address), `toán tử một ngôi` (unary), `toán tử hai ngôi` (binary), và `dịch bit` (shifts). Các thao tác hai ngôi có hai toán hạng, trong khi các thao tác một ngôi chỉ có một toán hạng. Các toán hạng này được xác định bằng cách sử dụng cùng một ký hiệu (notation) như đã mô tả trong Mục 3.4

### 3.5.1 Load Effective Address
<br>

 * <img width="553" height="467" alt="image" src="https://github.com/user-attachments/assets/f0236f89-f8c9-4ef8-9ac9-43c742f6c930" />

 * Lệnh **tải địa chỉ hiệu dụng** (load effective address) `leaq` thực chất là một biến thể của lệnh `movq`. Nó có hình thức của một lệnh đọc dữ liệu từ memory to register, nhưng thực tế nó **không hề truy cập bộ nhớ**. Toán hạng đầu tiên của nó trông giống như một tham chiếu bộ nhớ, nhưng thay vì đọc dữ liệu từ vị trí được chỉ định, lệnh này lại sao chép địa chỉ hiệu dụng (**effective address**) đó vào đích đến. Chúng ta biểu thị phép tính này trong Hình 3.10 bằng toán tử địa chỉ `&` của ngôn ngữ C.Lệnh này có thể được sử dụng để tạo các con trỏ cho các tham chiếu bộ nhớ sau này. Ngoài ra, nó còn có thể được dùng để mô tả một cách ngắn gọn các phép toán số học thông thường. Ví dụ, nếu thanh ghi `%rdx` chứa giá trị `$x$`, thì lệnh `leaq 7(%rdx,%rdx,4)`, `%rax` sẽ thiết lập thanh ghi `%rax` thành `$5x + 7$`. Compilers thường tìm ra những cách sử dụng `leaq` rất thông minh mà chẳng liên quan gì đến việc tính toán địa chỉ hiệu dụng cả. Toán hạng đích bắt buộc phải là một thanh ghi.

 * **Practice Problem 3.6**
   Giả sử thanh ghi `%rbx` chứa giá trị `$p$` và thanh ghi `%rdx` chứa giá trị `$q$`. Hãy điền vào bảng bên dưới các công thức biểu thị giá trị sẽ được lưu trữ trong thanh ghi `%rax` cho mỗi lệnh assembly được đưa ra:
   
   |Instruction|Result|
   |:--:|:--:|
   |leaq 9(%rdx), %rax|q+9|
   |leaq (%rdx,%rbx), %rax|q+p|
   |leaq (%rdx,%rbx,3), %rax|q+3p|
   |leaq 2(%rbx,%rbx,7) %rax|8p+2|
   |leaq 0xe(,%rdx,3), %rax|3q+0xe|
   |leaq 6(%rbx,%rbx,7)), %rax|7p+q+6|


 * **Practice Problem 3.7**

   ```c
   short scale3(short x, short y, short z){
      short t = z + 10y + xy;
   }

### 3.5.2 Unary and Binary Operations
<br>

 * Các phép toán trong nhóm thứ hai là các **phép toán một ngôi** (unary operations), với toán hạng duy nhất đóng vai trò vừa là nguồn (source) vừa là đích (destination). Toán hạng này có thể là một thanh ghi (register) hoặc một vị trí bộ nhớ (memory location). Ví dụ, lệnh `incq (%rsp)` làm cho phần tử 8-byte nằm trên đỉnh ngăn xếp (stack) được tăng thêm giá trị. Cú pháp này gợi nhớ đến các toán tử tăng (`++`) và giảm (`--`) trong ngôn ngữ C
 * Nhóm thứ ba bao gồm các **phép toán hai ngôi** (binary operations), trong đó toán hạng thứ hai được sử dụng làm cả nguồn và đích. Cú pháp này gợi nhớ đến các toán tử gán trong C, chẳng hạn như `x -= y`. Tuy nhiên, hãy lưu ý rằng toán hạng nguồn được đưa ra trước và toán hạng đích được đưa ra sau. Điều này trông có vẻ kỳ lạ đối với các phép toán không có tính giao hoán (noncommutative operations). Ví dụ, lệnh `subq %rax,%rdx` sẽ giảm giá trị của thanh ghi `%rdx` đi một lượng bằng giá trị nằm trong `%rax`. (Sẽ dễ nhớ hơn nếu bạn đọc lệnh này là "Trừ  `%rax` khỏi `%rdx`".) Toán hạng thứ nhất có thể là một giá trị trực tiếp (immediate value), một thanh ghi, hoặc một vị trí bộ nhớ. Toán hạng thứ hai có thể là một thanh ghi hoặc một vị trí bộ nhớ. Tương tự như các lệnh `mov`, hai toán hạng này không thể cùng lúc là vị trí bộ nhớ. Lưu ý rằng khi toán hạng thứ hai là một vị trí bộ nhớ, bộ vi xử lý phải đọc giá trị từ bộ nhớ, thực hiện phép toán, và sau đó ghi kết quả trở lại bộ nhớ.
 
 * **Practice Problem 3.8**
   
   Dưới đây là bản dịch chi tiết cho bài tập của bạn:

   |Address|Value|Register|Value|
   |:--|:--|:--|:--|
   |0x100|0xff|%rax|0x100|
   |0x108|0xab|%rcx|0x1|
   |0x110|0x13|%rdx|0x3|
   |0x118|0x11|

   Hãy điền vào bảng sau đây để cho thấy tác động của các câu lệnh bên dưới, xét về mặt thanh ghi hoặc vị trí bộ nhớ nào sẽ được cập nhật và giá trị kết quả tương ứng là bao nhiêu:

   |Instruction|Destination|Value|
   |:--|:--|:--|
   |addq %rcx, (%rax)|0x100|0x100|
   |subq %rdx, 8(%rax)|0x108|0xa8|
   |imulq $16, (%rax,%rdx,8)|0x118|0x110|
   |incq 16(%rax)|0x110|0x14|
   |decq %rcx|0x0|
   |subq %rdx, %rax|0x100|0xfd|


### 3.5.3 Shift Operations
<br>

* Nhóm lệnh cuối cùng bao gồm các phép toán dịch bit (**shift operations**), trong đó lượng dịch (shift amount) được chỉ định trước và giá trị cần dịch được chỉ định sau. Có thể thực hiện cả phép dịch phải số học (**arithmetic right shift**) và dịch phải logic (**logical right shift**). Các lệnh dịch bit khác nhau có thể quy định lượng dịch dưới dạng một giá trị tức thời (immediate value) hoặc thông qua thanh ghi 1-byte `%cl`. (Các lệnh này khá đặc biệt ở chỗ chúng chỉ cho phép sử dụng duy nhất thanh ghi này làm toán hạng). Về nguyên tắc, việc sử dụng lượng dịch kích thước 1 byte cho phép mã hóa các giá trị dịch lên đến $2^8 - 1 = 255$. Trong kiến trúc x86-64, một lệnh dịch thao tác trên các giá trị dữ liệu có độ dài $w$ bit sẽ xác định lượng dịch dựa trên $m$ bit bậc thấp (low-order bits) của thanh ghi `%cl`, với điều kiện $2^m = w$. Các bit bậc cao hơn (của thanh ghi `%cl`) sẽ bị bỏ qua. Do đó, ví dụ, khi thanh ghi `%cl` chứa giá trị thập lục phân là `0xFF` (tất cả các bit đều là 1):Lệnh `salb` (dịch byte - 8 bit, $m=3$) sẽ dịch đi **7** bit (vì 3 bit cuối của 0x`FF` là `111` tức là `7`).Lệnh `salw` (dịch word - 16 bit, $m=4$) sẽ dịch đi `15` bit.Lệnh `sall` (dịch long/double word - 32 bit, $m=5$) sẽ dịch đi `31` bit.Lệnh `salq` (dịch quad word - 64 bit, $m=6$) sẽ dịch đi `63` bit.
* Như Hình 3.10 minh họa, lệnh dịch trái có hai tên gọi: `sal` và `shl`. Cả hai lệnh này đều có cùng một tác dụng, đó là dịch các bit sang trái và điền các số `0` vào vị trí trống ở phía bên phải. Các lệnh dịch phải thì có sự khác biệt: `sar` thực hiện phép dịch số học (lấp đầy các vị trí trống bên trái bằng bản sao của bit dấu - sign bit, nhằm giữ nguyên dấu của số). `shr` thực hiện phép dịch logic (lấp đầy các vị trí trống bên trái bằng các số 0). Toán hạng đích (destination operand) của một phép dịch có thể là một thanh ghi hoặc một vị trí trong bộ nhớ. Chúng ta ký hiệu hai phép dịch phải khác nhau trong Hình 3.10 là `>>A` (dịch số học - arithmetic) và `>>L` (dịch logic - logical).

* **Problem 3.9**

  Giả sử chúng ta muốn tạo assembly code cho hàm C sau đây:

  ```c
  long shift_left4_rightn(long x, long n){
      x <<= 4;
      x >>= n;
      return x;
  }
  ```

  Đoạn mã dưới đây là một phần của mã hợp ngữ thực hiện các phép toán dịch bit thực tế và lưu giá trị cuối cùng vào thanh ghi `%rax`. Hai lệnh quan trọng đã bị lược bỏ. Các tham số `x` và `n` lần lượt được lưu trữ trong các thanh ghi `%rdi` và `%rsi`.

  ```asm
     long shift_left4_rightn(long x, long n){
     x in %rdi, n in %rsi

  shift_left4_rightn:
    movq %rdi, %rax    Get x
    salq $4, %rax      x <<= 4
    movl %esi, %ecx    Get n (4bytes)
    sarq %cl, %rax     x >>= n
  ```

### 3.5.4 Discussion
<br>
* <img width="432" height="389" alt="image" src="https://github.com/user-attachments/assets/3f84390f-a2c1-4b49-84c5-1f51bcf50b3a" />

* Chúng ta thấy rằng hầu hết các lệnh được hiển thị trong Hình 3.10 đều có thể được sử dụng cho cả phép toán số học không dấu (unsigned) hoặc bù hai (two's-complement). Chỉ có phép dịch phải (right shifting) mới yêu cầu các lệnh phân biệt giữa dữ liệu có dấu và không dấu. Đây là một trong những đặc điểm khiến số học bù hai trở thành phương pháp được ưa chuộng nhất để cài đặt số học số nguyên có dấu.

* Hình 3.11 cho thấy một ví dụ về một hàm thực hiện các phép toán số học và bản dịch của nó sang mã hợp ngữ (assembly code). Các đối số `x`, `y`, và `z` ban đầu được lưu trữ tương ứng trong các thanh ghi `%rdi`, `%rsi`, và `%rdx`. Các lệnh của assembly tương ứng rất sát với các dòng mã nguồn C:

 * Dòng 2 tính toán giá trị của biểu thức `x^y` (x XOR y).

 * Dòng 3 và 4 tính toán biểu thức `z*48` thông qua sự kết hợp giữa lệnh `leaq` và lệnh dịch bit (shift instructions).

 * Dòng 5 thực hiện phép toán AND giữa `t1` và `0x0F0F0F0F`.

 * Dòng 6 thực hiện phép trừ cuối cùng.

 * Vì đích đến của phép trừ là thanh ghi `%rax`, đây sẽ chính là giá trị được trả về bởi hàm.

* Trong assembly code của Hình 3.11, chuỗi các giá trị nằm trong thanh ghi `%rax` tương ứng với các giá trị của chương trình là `3*z`, `z*48`, và `t4` (đóng vai trò là giá trị trả về). Nhìn chung, compilers sẽ tạo ra mã sử dụng các thanh ghi độc lập để lưu trữ nhiều giá trị chương trình khác nhau và luân chuyển các giá trị này qua lại giữa các thanh ghi.

* **Practice Problem 3.10**

  ```asm
  arith3:
    orq  %rsi, %rdx
    sarq $9, %rdx
    notq %rdx
    movq %rdx, %rax
    subq %rsi, %rax
    ret
  ```

  ```c
  short arith3(short x, short y, short z){
      short p1 = y | z;
      short p2 = p1 >> 9;
      short p3 =  ~p2;
      short p4 =  y - p3;
      return p4;
  }

* **Practice Problem 3.11**

  Chúng ta rất thường bắt gặp các dòng mã Assembly có dạng như sau...

  ```asm
  xorq %rcx, %rcx
  ```

  ...trong mã được tạo ra từ ngôn ngữ C, nơi mà không hề có phép toán EXCLUSIVE-OR (XOR) nào được sử dụng.

  * A. Lệnh này được sử dụng để thiết lập thanh ghi `%rcx` về giá trị `0`, bằng cách khai thác tính chất toán học là $x \text{ \^{} } x = 0$ (x XOR với chính nó luôn bằng 0) áp dụng cho mọi giá trị của $x$. Lệnh này tương đương với câu lệnh `x = 0` trong ngôn ngữ C.
  * B. Một cách trực tiếp (rõ ràng/dễ hiểu) hơn để thiết lập thanh ghi `%rcx` về `0` là sử dụng lệnh `movq $0, %rcx`.
  * C. Tuy nhiên, khi hợp dịch (assembling) và dịch ngược (disassembling) đoạn mã này, chúng ta phát hiện ra rằng phiên bản sử dụng lệnh `xorq` chỉ tốn **3 byte** bộ nhớ, trong khi phiên bản sử dụng lệnh `movq` đòi hỏi tới **7 byte**. Các cách khác để thiết lập `%rcx` về 0 thường dựa vào một tính chất đặc biệt (của kiến trúc x86-64): **bất kỳ lệnh nào cập nhật 4 byte thấp của một thanh ghi đều sẽ tự động làm cho các byte bậc cao (high-order bytes) của thanh ghi đó bị gán bằng 0**. Do đó, chúng ta cũng có thể sử dụng lệnh `xorl %ecx,%ecx` (chỉ tốn 2 byte) hoặc `movl $0,%ecx` (tốn 5 byte).


### 3.5.5 Special Arithmetic Operations
<br>

* Như chúng ta đã thấy ở Phần 2.3, việc nhân hai số nguyên 64-bit có dấu hoặc không dấu có thể tạo ra một tích số cần tới 128 bit để biểu diễn. Tập lệnh x86-64 cung cấp các hỗ trợ khá hạn chế đối với những phép toán liên quan đến các số 128-bit (16 byte). Tiếp nối quy ước đặt tên trước đó bao gồm **word** (2 byte), **double word** (4 byte), và **quad word** (8 byte), Intel gọi một đại lượng kích thước 16-byte là một **oct word**. Hình 3.12 mô tả các lệnh hỗ trợ việc tạo ra kết quả tích 128-bit đầy đủ từ hai số 64-bit, cũng như các lệnh hỗ trợ phép chia số nguyên.
  * <img width="548" height="281" alt="image" src="https://github.com/user-attachments/assets/8e0f79a4-2b16-4ac3-9f3a-7a413ee982a2" />

  * Lệnh `imulq` có hai dạng khác nhau:

    * Dạng thứ nhất (được hiển thị trong Hình 3.10) là một thành viên của nhóm lệnh imul. Ở dạng này, nó đóng vai trò là một lệnh nhân "hai toán hạng" (two-operand), giúp tạo ra một tích 64-bit từ hai toán hạng 64-bit. Nó triển khai các phép toán nhân *u64 (không dấu) và *t64 (có dấu) đã được mô tả ở Phần 2.3.4 và 2.3.5. (Hãy nhớ lại rằng: khi cắt ngắn tích số xuống chỉ còn 64 bit, cả phép nhân không dấu và phép nhân bù hai đều có cùng một cách thức hoạt động ở cấp độ bit).

    * Dạng thứ hai: Ngoài ra, tập lệnh x86-64 còn bao gồm hai lệnh nhân "một toán hạng" (one-operand) khác nhau nhằm tính toán ra toàn bộ tích 128-bit của hai giá trị 64-bit:

      * Lệnh `mulq` dành cho phép nhân không dấu.

      * Lệnh `imulq` dành cho phép nhân bù hai (có dấu).
  * Đối với cả hai lệnh một toán hạng này, một đối số bắt buộc phải nằm trong thanh ghi `%rax`, và đối số còn lại được cung cấp dưới dạng toán hạng nguồn (source operand) đi kèm trong lệnh. Kết quả tích số sau đó sẽ được tự động lưu trữ vào hai thanh ghi:

    * `%rdx` lưu giữ 64 bit bậc cao (high-order bits).

    * `%rax` lưu giữ 64 bit bậc thấp (low-order bits).
   
  * Mặc dù cùng một tên lệnh là `imulq` lại được sử dụng cho hai thao tác nhân hoàn toàn khác biệt nhau (loại hai toán hạng và loại một toán hạng), nhưng trình hợp dịch (assembler) vẫn có thể phân biệt chính xác ý đồ của người viết code bằng cách đếm số lượng toán hạng được truyền vào lệnh.
  * Để làm ví dụ, đoạn mã C sau đây minh họa việc tạo ra một tích 128-bit của hai số 64-bit không dấu `x` và `y`:

    ```c
    #include<nttypes.h>

    typedef unsigned __int128 uint128_t;

    void store_uprod(uint128_t *dest, uint64_t x, uint64_t y){
        *dest= x * (uint128_t) y;
    }
    ```

    Trong chương trình này, chúng ta khai báo rõ ràng `x` và `y` là các số 64-bit, sử dụng các định nghĩa được khai báo trong tệp `inttypes.h` (đây là một phần mở rộng của tiêu chuẩn C).

    Thật không may, tiêu chuẩn này không có điều khoản hỗ trợ cho các giá trị 128-bit. Thay vào đó, chúng ta phải dựa vào sự hỗ trợ của trình biên dịch `gcc` dành cho các số nguyên 128-bit, được khai báo thông qua từ khóa `__int128`. Đoạn mã của chúng ta sử dụng một khai báo `typedef` để định nghĩa kiểu dữ liệu `uint128_t`, tuân theo quy tắc đặt tên tương tự như các kiểu dữ liệu khác có trong `inttypes.h`. Đoạn mã cũng chỉ định rằng tích thu được (kết quả của phép nhân) sẽ được lưu trữ tại vùng nhớ 16 byte được trỏ tới bởi con trỏ `dest`.

    Mã hợp ngữ (assembly code) do gcc tạo ra cho hàm này như sau:

    ```asm
    void store_uprod(uint128_t *dest, uint64_t x, uint64_t y)
    dest in %rdi, x in %rsi, y in %rdx
    store_uprod:
       movq  %rsi, %rax            Copy x to multiplicand
       mulq  %rdx                  Multiply by y
       movq  %rax, (%rdi)          Store lower 8 bytes at dest
       movq  %rdx, 8(%rdi)         Store uppper 8 bytes at dest+8
       ret
    ```

  * Hãy chú ý rằng việc lưu trữ tích số (kết quả của phép nhân) đòi hỏi tới hai lệnh `movq`: một lệnh dành cho 8 byte bậc thấp (dòng 4) và một lệnh dành cho 8 byte bậc cao (dòng 5). Vì mã máy được tạo ra cho kiến trúc little-endian, nên các byte bậc cao sẽ được lưu trữ ở các địa chỉ bộ nhớ cao hơn, đúng như được chỉ định bởi đặc tả địa chỉ `8(%rdi)`.
  * Bảng các phép toán số học trước đây của chúng ta (Hình 3.10) không liệt kê bất kỳ phép toán chia hay phép chia lấy dư (modulus) nào. Các phép toán này được cung cấp bởi các lệnh chia một toán hạng (single-operand divide instructions), hoạt động tương tự như các lệnh nhân một toán hạng. Lệnh chia có dấu `idivq` lấy số bị chia (dividend) là một đại lượng 128-bit nằm trong các thanh ghi `%rdx` (chứa 64 bit bậc cao) và `%rax` (chứa 64 bit bậc thấp). Số chia (**divisor**) được cung cấp dưới dạng toán hạng đi kèm với lệnh. Lệnh này sẽ lưu trữ thương số (**quotient**) vào thanh ghi `%rax` và số dư (**remainder**) vào thanh ghi `%rdx`.
  * Đối với hầu hết các ứng dụng của phép chia 64-bit, số bị chia thường chỉ được cho dưới dạng một giá trị 64-bit. Giá trị này cần được lưu trữ trong thanh ghi `%rax`. Khi đó, các bit của thanh ghi `%rdx` phải được thiết lập thành toàn số 0 (đối với số học không dấu) hoặc lấp đầy bằng bit dấu của thanh ghi `%rax` (đối với số học có dấu). Thao tác thứ hai (mở rộng dấu) có thể được thực hiện bằng cách sử dụng lệnh $cqto^2$. Lệnh này không yêu cầu toán hạng nào—nó ngầm định đọc bit dấu từ `%rax` và sao chép bit dấu đó lên toàn bộ các bit của thanh ghi `%rdx`.
  * Để minh họa cho việc triển khai phép chia trong kiến trúc x86-64, hàm C dưới đây sẽ tính toán thương số và số dư của hai số nguyên 64-bit có dấu:

    ```c
    void remdiv(long x, long y, long *qp, long *rp){
        long q = x/y;
        long r = x%y;
        *qp = q;
        *rp = r;
    }
    ```

    ```asm
    void remdiv(long x, long y, long *qp, long *rp)
    x in %rdi, y in %rsi, qp in %rdx, rp in %rcx
    remdiv:
      movq   %rdx, %r8      copy qp
      movq   %rdi, %rax     move x to lower 8 bytes of dividend
      cqto                  sign-extend too upper 8 bytes of dividend
      idivq  %rsi           divide by y
      movq   %rax, (%r8)    store quotient at qp
      movq   %rdx, (%rcx)   store remainder at rp
    ```

    Trong đoạn mã này, đối số `rp` trước tiên phải được cất tạm vào một thanh ghi khác (ở dòng 2), bởi vì thanh ghi đối số `%rdx` bị bắt buộc sử dụng cho phép toán chia. Các dòng 3–4 sau đó chuẩn bị số bị chia (**dividend**) bằng cách sao chép và thực hiện mở rộng dấu (**sign-extending**) cho biến `x`. Sau khi thực hiện phép chia, thương số (**quotient**) nằm trong thanh ghi `%rax` sẽ được lưu vào vị trí do con trỏ qp chỉ định (dòng 6), trong khi số dư (**remainder**) nằm trong thanh ghi %rdx sẽ được lưu vào vị trí của con trỏ rp (dòng 7).

    Phép chia không dấu (**unsigned division**) thì sử dụng lệnh `divq`. Theo thông lệ, thanh ghi `%rdx` sẽ được thiết lập về giá trị 0 trước khi thực hiện lệnh này.



 * **Practice Problem 3.12**

   Hãy xem xét hàm sau đây dùng để tính thương số (quotient) và số dư (remainder) của hai số 64-bit không dấu (unsigned 64-bit numbers):

   ```c
   void uremdiv(unsigned long x, unsigned long y, unsigned long *qp, unsigned long *rp){
       unsigned long q= x/y;
       unsigned long r= x%y;
       *qp = q;
       *rp = r;
   }
   ```

   Hãy sửa đổi đoạn assembly code của phép chia có dấu (đã hiển thị ở phần trước) để triển khai (implement) hàm này.

   ```asm
   void uremdiv(long x, long y, long *qp, long *rp)
    x in %rdi, y in %rsi, qp in %rdx, rp in %rcx
   uremdiv:
     movq  %rdx, %r8
     movq  %rdi, %rax
     xorl  %edx, %edx
     divq  %rsi
     movq  %rax, (%r8)
     movq  %rdx, (%rcx)
   ```

    
## 3.6 Control (Điều khiển)
<br>

* Cho đến nay, chúng ta mới chỉ xem xét hành vi của **mã tuyến tính** (straight-line code), nơi các lệnh nối tiếp nhau một cách tuần tự. Một số cấu trúc trong C, chẳng hạn như câu lệnh điều kiện (conditionals), vòng lặp (loops) và câu lệnh rẽ nhánh (switches), yêu cầu **thực thi có điều kiện** (conditional execution), trong đó chuỗi các thao tác được thực hiện sẽ phụ thuộc vào kết quả của các phép kiểm tra áp dụng lên dữ liệu. Mã máy cung cấp hai cơ chế cấp thấp cơ bản để triển khai hành vi có điều kiện: nó kiểm tra các giá trị dữ liệu và sau đó thay đổi **luồng điều khiển** (control flow) hoặc **luồng dữ liệu** (data flow) dựa trên kết quả của các phép kiểm tra này.
* Luồng điều khiển phụ thuộc dữ liệu (Data-dependent control flow) là cách tiếp cận tổng quát và phổ biến hơn để triển khai hành vi có điều kiện, do đó chúng ta sẽ xem xét phương pháp này trước tiên. Thông thường, cả các câu lệnh trong C và các lệnh trong mã máy đều được thực thi một cách tuần tự, theo đúng thứ tự chúng xuất hiện trong chương trình. Thứ tự thực thi của một tập hợp các lệnh mã máy có thể được thay đổi bằng một **lệnh nhảy** (jump instruction). Lệnh này chỉ định rằng quyền điều khiển sẽ được chuyển sang một phần khác của chương trình, và việc chuyển quyền này có thể phụ thuộc (contingent) vào kết quả của một phép kiểm tra nào đó. Compiler phải tạo ra các chuỗi lệnh được xây dựng dựa trên cơ chế cấp thấp này để triển khai các cấu trúc điều khiển của ngôn ngữ C.
* Trong phần trình bày của mình, trước tiên chúng tôi sẽ đề cập đến hai cách triển khai các phép toán có điều kiện. Sau đó, chúng tôi sẽ mô tả các phương pháp để biểu diễn vòng lặp và câu lệnh switch.

### 3.6.1 Condition Codes
<br>

* Ngoài các thanh ghi số nguyên (integer registers), CPU còn duy trì một tập hợp các thanh ghi **mã điều kiện** (condition code registers) với kích thước mỗi thanh ghi chỉ là 1-bit, dùng để mô tả các đặc tính của phép toán số học hoặc logic gần nhất. Các thanh ghi này sau đó có thể được kiểm tra để thực hiện các lệnh rẽ nhánh có điều kiện (conditional branches). Các mã điều kiện hữu ích nhất bao gồm:
  * **CF (Carry flag - Cờ nhớ)**: Phép toán gần nhất đã tạo ra một giá trị nhớ (carry) từ bit có trọng số cao nhất (most significant bit). Cờ này được sử dụng để phát hiện tràn số cho các phép toán **không dấu** (unsigned).
  * **ZF (Zero flag - Cờ zero)**: Phép toán gần nhất cho kết quả bằng 0.
  * **SF (Sign flag - Cờ dấu)**: Phép toán gần nhất cho kết quả là một giá trị âm.
  * **OF (Overflow flag - Cờ tràn)**: Phép toán gần nhất đã gây ra tình trạng tràn số trong biểu diễn **bù hai (two’s-complement)**—có thể là tràn dương hoặc tràn âm.
* Ví dụ, giả sử chúng ta sử dụng một trong các lệnh `ADD` để thực hiện phép gán tương đương trong C là `t = a + b` (trong đó a, b, và t là các số nguyên). Khi đó, các mã điều kiện sẽ được thiết lập theo các biểu thức C sau:
  * CF (Cờ nhớ): (unsigned) t < (unsigned) a           (Tràn số không dấu)
  * ZF (Cờ zero): t == 0                               (Bằng 0)
  * SF (Cờ dấu): t < 0                                 (Số âm)
  * OF (Cờ tràn): (a < 0 == b < 0) && (t < 0 != a < 0) (Tràn số có dấu)

* <img width="542" height="309" alt="image" src="https://github.com/user-attachments/assets/dd588113-e2e9-487b-8bb4-6a9d7c02968d" />

* Lệnh `leaq` không làm thay đổi bất kỳ mã điều kiện nào, vì nó được thiết kế chỉ để sử dụng trong các tính toán địa chỉ. Ngược lại, tất cả các lệnh khác được liệt kê trong Hình 3.10 đều làm thay đổi mã điều kiện. Đối với các lệnh logic như `XOR`, cờ nhớ (carry flag) và cờ tràn (overflow flag) sẽ được đặt về 0. Đối với các lệnh dịch bit, cờ nhớ được đặt bằng giá trị của bit cuối cùng bị đẩy ra ngoài, trong khi cờ tràn được đặt về 0. Vì những lý do mà chúng ta sẽ không đi sâu vào, các lệnh `INC` (tăng) và `DEC` (giảm) làm thay đổi cờ tràn và cờ zero, nhưng chúng không làm thay đổi cờ nhớ.
* Ngoài việc thiết lập mã điều kiện thông qua các lệnh trong Hình 3.10, còn có hai nhóm lệnh (với các biến thể 8, 16, 32 và 64 bit) giúp thiết lập mã điều kiện mà **không làm thay đổi bất kỳ thanh ghi nào khác**; chúng được liệt kê trong Hình 3.13.
  * Các lệnh `CMP` (So sánh) thiết lập mã điều kiện dựa trên hiệu số của hai toán hạng. Chúng hoạt động giống như các lệnh `SUB` (trừ), ngoại trừ việc chúng thiết lập mã điều kiện mà không cập nhật giá trị vào toán hạng đích. Trong định dạng AT&T, các toán hạng được liệt kê theo thứ tự ngược lại, khiến mã nguồn khó đọc hơn. Các lệnh này thiết lập cờ zero nếu hai toán hạng bằng nhau. Các cờ còn lại có thể được sử dụng để xác định quan hệ thứ tự giữa hai toán hạng.
  * Các lệnh `TEST` (Kiểm tra) hoạt động theo cách tương tự như các lệnh `AND`, ngoại trừ việc chúng thiết lập mã điều kiện mà không làm thay đổi toán hạng đích. Thông thường, cùng một toán hạng sẽ được lặp lại (ví dụ: `testq %rax, %rax` để kiểm tra xem `%rax` là âm, bằng 0 hay dương), hoặc một trong các toán hạng sẽ là một mặt nạ (mask) chỉ định những bit nào cần được kiểm tra.
 
### 3.6.2 Accessing the Condition Codes
<br>

 * Thay vì đọc trực tiếp các mã điều kiện (condition codes), có ba cách phổ biến để sử dụng chúng: (1) đặt một byte đơn lẻ thành 0 hoặc 1 dựa trên một tổ hợp các mã điều kiện, (2) nhảy có điều kiện đến một phần khác của chương trình, hoặc (3) truyền dữ liệu có điều kiện.
   * Đối với trường hợp thứ nhất, các lệnh được mô tả trong Hình 3.14 sẽ đặt một byte đơn thành 0 hoặc 1 tùy thuộc vào một tổ hợp các mã điều kiện. Chúng ta gọi toàn bộ nhóm lệnh này là các lệnh set; chúng khác biệt nhau dựa trên tổ hợp các mã điều kiện mà chúng xem xét, được chỉ ra bởi các hậu tố khác nhau trong tên lệnh. Điều quan trọng cần nhận biết là các hậu tố này biểu thị các điều kiện khác nhau chứ không phải kích thước toán hạng. Ví dụ, lệnh `setl` và `setb` có nghĩa là "set nếu nhỏ hơn" (set less) và "set nếu thấp hơn" (set below), chứ không phải "set long word" hay "set byte".

* Một lệnh `set` có đích đến là một trong các thanh ghi 1-byte bậc thấp (Hình 3.2) hoặc một vị trí bộ nhớ 1-byte, đặt byte này thành 0 hoặc 1. Để tạo ra kết quả 32-bit hoặc 64-bit, chúng ta cũng phải xóa các bit bậc cao. Một chuỗi lệnh điển hình để tính biểu thức C `a < b` (với `a` và `b` đều thuộc kiểu `long`) diễn ra như sau:

  <img width="545" height="376" alt="image" src="https://github.com/user-attachments/assets/4d283323-45a1-42f4-a650-fe325f22c39b" />

  ```asm
  int comp(data_t a, data_t b)
  a in %rdi, b in %rsi

  comp:
    cmpq  %rsi, %rdi       compare a:b 
    setl  %al              Set low-order byte of %eax to 0 or 1
    movzbl %al, %eax       Clear rest of %rax (and rest of %eax)
    ret
  ```

  Lưu ý: Bạn hãy chú ý thứ tự so sánh của lệnh `cmpq` ở dòng 2. Mặc dù các đối số được liệt kê theo thứ tự `%rsi` (b), rồi đến `%rdi` (a), nhưng phép so sánh thực tế là giữa `a` và `b`. Cũng cần nhớ rằng, như đã thảo luận ở Phần 3.4.2, lệnh `movzbl` ở dòng 4 không chỉ xóa 3 byte bậc cao của `%eax`, mà còn xóa sạch toàn bộ 4 byte phía trên của toàn bộ thanh ghi `%rax`.
* Với một số lệnh máy cấp thấp, có nhiều tên gọi khả dĩ, chúng tôi liệt kê chúng là "từ đồng nghĩa" (synonyms). Ví dụ, cả `setg` (cho "set lớn hơn") và `setnle` (cho "set không nhỏ hơn hoặc bằng") đều cùng chỉ một lệnh máy. Các trình biên dịch và trình dịch ngược (disassembler) đưa ra các lựa chọn tùy ý về việc sử dụng tên gọi nào.
* Mặc dù tất cả các phép toán số học và logic đều thiết lập mã điều kiện, các mô tả về các lệnh set khác nhau chỉ áp dụng cho trường hợp một lệnh so sánh đã được thực thi, thiết lập mã điều kiện theo tính toán `t = a - b`. Cụ thể hơn, giả sử `a`, `b` và `t` là các số nguyên được biểu diễn dưới dạng bù hai (two’s-complement) bởi các biến tương ứng, và $t = a -^t_w b$, trong đó w phụ thuộc vào kích thước gắn với `a` và `b`.
* Hãy xét lệnh `sete` (set khi bằng nhau). Khi `a = b`, ta có `t = 0`, do đó cờ zero (ZF) chỉ ra sự bằng nhau. Tương tự, hãy xét việc kiểm tra so sánh có dấu với lệnh `setl` (set khi nhỏ hơn). Khi không có tràn số (OF = 0), ta có `a < b` khi $a -^t_w b < 0$, được chỉ ra bởi cờ dấu (SF) bằng 1; và `a >= b` khi $a -^t_w b >= 0$, chỉ ra bởi SF bằng 0. Mặt khác, khi có tràn số xảy ra, ta có `a < b` khi $a -^t_w b > 0$ (tràn âm) và `a > b` khi $a -^t_w b < 0$ (tràn dương). Không thể xảy ra tràn số khi `a = b`. Do đó, khi cờ OF = 1, ta có `a < b` khi và chỉ khi SF = 0. Kết hợp các trường hợp này lại, phép toán `XOR` giữa cờ tràn và cờ dấu (SF ^ OF) cung cấp một phép kiểm tra cho việc `a < b`. Các phép so sánh có dấu khác dựa trên các tổ hợp khác của SF ^ OF và ZF.
* Đối với việc kiểm tra so sánh không dấu, ta coi `a` và `b` là các số nguyên không dấu. Khi thực hiện `t = a - b`, cờ nhớ (CF) sẽ được lệnh cmp thiết lập khi `a - b < 0`, vì vậy các phép so sánh không dấu sử dụng tổ hợp giữa cờ nhớ (CF) và cờ zero (ZF).
* Điều quan trọng cần lưu ý là mã máy phân biệt (hoặc không phân biệt) giữa các giá trị có dấu và không dấu như thế nào. Không giống như trong C, mã máy không gắn kiểu dữ liệu với từng giá trị chương trình. Thay vào đó, nó chủ yếu sử dụng các lệnh giống nhau cho cả hai trường hợp, bởi vì nhiều phép toán số học có hành vi cấp độ bit giống nhau cho cả số không dấu và số bù hai. Một số trường hợp đòi hỏi các lệnh khác nhau để xử lý các phép toán có dấu và không dấu, chẳng hạn như dùng các phiên bản dịch phải khác nhau, các lệnh chia/nhân khác nhau và các tổ hợp mã điều kiện khác nhau.


### 3.6.3 Jump Instructions
<br>

* Trong quá trình thực thi thông thường, các lệnh được thực hiện lần lượt theo thứ tự liệt kê. Một lệnh nhảy (jump instruction) có thể khiến chương trình chuyển hướng thực thi đến một vị trí hoàn toàn mới. Các đích đến của lệnh nhảy này thường được chỉ định trong mã hợp ngữ bằng một cái **nhãn (label)**. Hãy xem xét chuỗi mã hợp ngữ (rất khiên cưỡng) sau đây:

  ```asm
    movq $0, %rax        Set %rax to 
    jmp .L1              Goto .L1
    movq (%rax), %rdx    Null pointer dereference (skipped)
  .L1
    popq %rdx            Jump target
  ```

  <img width="539" height="426" alt="image" src="https://github.com/user-attachments/assets/1827e220-bd19-4eaa-9fba-14954a7dd96f" />

* Lệnh `jmp .L1` sẽ khiến chương trình bỏ qua lệnh `movq` (tham chiếu con trỏ null) và thay vào đó tiếp tục thực thi từ lệnh `popq`. Trong quá trình tạo tệp mã đối tượng (object-code file), assembler xác định địa chỉ của tất cả các lệnh được gắn nhãn và mã hóa các **đích đến của lệnh nhảy (jump targets)** — tức là địa chỉ của các lệnh cần chuyển đến — như một phần của chính các lệnh nhảy đó.
* Lệnh jmp trong bảng trên thực hiện việc nhảy không điều kiện. Nó có thể là:
  * Nhảy trực tiếp (direct jump): Nơi đích nhảy được mã hóa ngay bên trong lệnh.
  * Nhảy gián tiếp (indirect jump): Nơi đích nhảy được đọc từ một thanh ghi hoặc một vị trí bộ nhớ. Các lệnh nhảy được viết trong mã hợp ngữ bằng cách cung cấp một nhãn làm đích đến. Các lệnh nhảy gián tiếp được viết bằng dấu `*` theo sau là một toán hạng chỉ định (operand specifier), sử dụng một trong các định dạng toán hạng bộ nhớ được mô tả ở Hình 3.3. Ví dụ:
    * `jmp *%rax`: Sử dụng giá trị trong thanh ghi `%rax` làm đích nhảy.
    * `jmp *(%rax)`: Đọc giá trị từ địa chỉ bộ nhớ được lưu trong `%rax` để dùng làm đích nhảy.
   
* Các lệnh nhảy còn lại trong bảng là các lệnh nhảy có điều kiện (conditional jumps) — chúng sẽ nhảy hoặc tiếp tục thực thi lệnh tiếp theo trong chuỗi mã tùy thuộc vào tổ hợp các mã điều kiện (condition codes). Tên của các lệnh này và điều kiện mà chúng nhảy khớp với các lệnh `SET` (xem Hình 3.14). Tương tự như các lệnh `SET`, một số lệnh máy cấp thấp cũng có nhiều tên gọi thay thế. Các lệnh nhảy có điều kiện chỉ có thể là nhảy trực tiếp.

### 3.6.4 Jump Instruction Encodings
<br>


* Phần lớn thời gian, chúng ta sẽ không quá quan tâm đến định dạng chi tiết của mã máy. Tuy nhiên, việc hiểu cách mã hóa đích đến (targets) của các lệnh nhảy sẽ trở nên quan trọng khi chúng ta nghiên cứu về quá trình **liên kết (linking)** trong Chương 7. Ngoài ra, điều này cũng hữu ích khi diễn giải kết quả đầu ra của disassembler. Trong assembly, đích nhảy được viết bằng các nhãn tượng trưng (symbolic labels). Assembler và sau đó là linker sẽ tạo ra các mã hóa phù hợp cho các đích nhảy này. Có vài phương thức mã hóa khác nhau cho các lệnh nhảy, nhưng một trong những phương thức phổ biến nhất là **PC-relative** **(tương đối theo bộ đếm chương trình - Program Counter)**. Nghĩa là, chúng mã hóa sự khác biệt giữa địa chỉ của lệnh đích và địa chỉ của lệnh nằm ngay sau lệnh nhảy. Các độ lệch (offsets) này có thể được mã hóa bằng 1, 2 hoặc 4 byte. Phương thức mã hóa thứ hai là cung cấp một địa chỉ "tuyệt đối" (absolute address), sử dụng 4 byte để chỉ định trực tiếp đích đến. Trình hợp dịch và trình liên kết sẽ lựa chọn các cách mã hóa đích nhảy phù hợp nhất.
* Lấy ví dụ về phương thức địa chỉ hóa PC-relative, đoạn mã hợp ngữ dưới đây của một hàm được tạo ra bằng cách biên dịch một tệp **branch.c**. Nó chứa hai lệnh nhảy: lệnh `jmp` ở dòng 2 nhảy về phía trước đến một địa chỉ cao hơn, trong khi lệnh `jg` (jump if greater) ở dòng 7 nhảy ngược lại về một địa chỉ thấp hơn.

  ```asm
     movq  %rdi, %rax
     jmp   .L2
  .L3:
     sarq  %rax
  .L2:
     testq %rax, %rax
     jg    .L3
     rep; ret
  ```

  Disassembled của định dạng tệp .o do assembler tạo ra:

  ```asm
  0:    48 89 f8                       mov    %rdi, %rax
  3:    eb 03                          jmp    8 <loop+0x8>
  5:    48 d1 f8                       sar    %rax
  8:    48 85 c0                       test   %rax, %rax
  b:    7f f8                          jg     5 <loop+0x5>
  d:    f3 c3                          repz retq
  ```

* Trong các chú thích ở phía bên phải do disassembler tạo ra, các đích nhảy (jump targets) được chỉ ra là `0x8` cho lệnh nhảy ở dòng 2 và `0x5` cho lệnh nhảy ở dòng 5 (trình dịch ngược liệt kê tất cả các số dưới dạng hệ thập lục phân). Tuy nhiên, khi nhìn vào các mã hóa byte của các lệnh này, chúng ta thấy rằng đích nhảy của lệnh nhảy đầu tiên được mã hóa (trong byte thứ hai) là `0x03`. Cộng giá trị này với `0x5` (là địa chỉ của lệnh tiếp theo ngay sau lệnh nhảy), ta được địa chỉ đích nhảy là `0x8` — chính là địa chỉ của lệnh ở dòng 4.
* Tương tự, đích nhảy của lệnh nhảy thứ hai được mã hóa là `0xf8` (thập phân là -8), sử dụng biểu diễn bù hai (two's-complement) 1 byte. Cộng giá trị này với `0xd` (thập phân là 13 — địa chỉ của lệnh ở dòng 6), ta được `0x5` — chính là địa chỉ của lệnh ở dòng 3.
* Như các ví dụ này minh họa, giá trị của **bộ đếm chương trình (program counter - PC)** khi thực hiện địa chỉ hóa PC-relative chính là địa chỉ của lệnh nằm ngay sau lệnh nhảy, chứ không phải là địa chỉ của chính lệnh nhảy đó. Quy ước này bắt nguồn từ các triển khai sơ khai, khi bộ xử lý cập nhật bộ đếm chương trình như là bước đầu tiên trong quá trình thực thi một lệnh.
* Dưới đây là phiên bản đã dịch ngược của chương trình sau khi **liên kết (linking)**:

  ```asm
  4004d0:    48 89 f8                  mov    %rdi, %rax
  4004d3:    eb 03                     jmp    8 <loop+0x8>
  4004d5:    48 d1 f8                  sar    %rax
  4004d8:    48 85 c0                  test   %rax, %rax
  4004db:    7f f8                     jg     5 <loop+0x5>
  4004dd:    f3 c3                     repz retq
  ```

* Các lệnh đã được di dời (relocated) đến các địa chỉ khác nhau, nhưng mã hóa của các đích nhảy ở dòng 2 và dòng 5 vẫn không hề thay đổi. Bằng cách sử dụng mã hóa PC-relative cho các đích nhảy, các lệnh có thể được mã hóa một cách nhỏ gọn (chỉ tốn 2 byte) và mã đối tượng (object code) có thể được dịch chuyển đến các vị trí khác nhau trong bộ nhớ mà không cần phải thay đổi nội dung của chính các lệnh đó.

> [!Note]
>
> **What do the instruction rep and repz do**
>
> Dòng 8 trong đoạn mã hợp ngữ ở trang 243 chứa tổ hợp lệnh `rep; ret`. Trong mã được dịch ngược (dòng 6), chúng được hiển thị là `repz retq`. Chúng ta có thể suy ra rằng `repz` là một từ đồng nghĩa của `rep`, cũng giống như `retq` là từ đồng nghĩa của `ret`. Khi tra cứu tài liệu của Intel và AMD về lệnh `rep`, chúng ta thấy rằng nó thường được dùng để thực hiện các thao tác chuỗi lặp lại. Việc sử dụng nó ở đây có vẻ hoàn toàn không phù hợp. Câu trả lời cho "câu đố" này nằm trong hướng dẫn dành cho các kỹ sư viết trình biên dịch của AMD. Họ khuyến nghị sử dụng tổ hợp lệnh `rep` đứng trước `ret` để tránh việc khiến lệnh `ret` trở thành đích đến của một lệnh nhảy có điều kiện. Nếu không có lệnh `rep`, lệnh `jg` (dòng 7 của mã hợp ngữ) sẽ trực tiếp dẫn đến lệnh ret khi nhánh nhảy không được thực hiện. Theo AMD, các bộ vi xử lý của họ không thể dự đoán chính xác đích đến của lệnh `ret` khi nó bị tiếp cận từ một lệnh nhảy. Trong trường hợp này, lệnh `rep` đóng vai trò như một hình thức lệnh **không thực hiện (no-operation - NOP)**, do đó việc chèn nó vào làm đích đến của lệnh nhảy không làm thay đổi hành vi của chương trình, ngoại trừ việc giúp mã chạy nhanh hơn trên các bộ vi xử lý của AMD. Chúng ta có thể an tâm bỏ qua bất kỳ lệnh rep hoặc repz nào mà chúng ta bắt gặp trong phần còn lại của cuốn sách này.


* **Practice Problem 3.15**

  Trong các đoạn trích dẫn sau đây từ một tệp nhị phân đã được dịch ngược (disassembled binary), một số thông tin đã được thay thế bằng các chữ X. Hãy trả lời các câu hỏi sau về những lệnh này:

  A. Đích đến (target) của lệnh `je` dưới đây là gì? (Bạn không cần biết bất cứ điều gì về lệnh callq ở đây.)

     ```asm
     4003fa:  74 02   je   XXXXXX
     4003fc:  ff d0   callq *rax
     ```

 B. Đích đến của lệnh je dưới đây là gì?

    ```asm
    40042f:   74 f4   je   XXXXXX
    400431:   5d      pop  %rbp
    ```

 C. Địa chỉ của lệnh ja và pop là gì?
    
    ```asm
    XXXXXX:   77 02   ja   400547
    XXXXXX:   5d      pop  %rbp
    ```

 D. Trong đoạn mã sau đây, đích nhảy được mã hóa dưới dạng **PC-relative** với số bù hai (two's-complement) 4 byte. Các byte được liệt kê từ ít quan trọng nhất đến quan trọng nhất (little-endian byte ordering của x86-64). Đích nhảy là địa chỉ nào?
    
    ```asm
    4005e8:   e9 73 ff ff ff        jmpq XXXXXXX
    4005ed:   90                    nop
    ```
    

  
## Procedures (Hàm)

## Array Allocation and Access (Cấp phát và truy cặp mảng)

## Heterogeneous Data Structures (Cấu trúc dữ liệu hỗn hợp)

## Combining Control and Data in Machine-Level Programs (Kết hợp điều khiển và dữ liệu trong chương trình cấp độ máy )

## Floating-Point Code (Mã lệnh số thực dấu phẩy động)

## Summary (Tóm tắt chương)
