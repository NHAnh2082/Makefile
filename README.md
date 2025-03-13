<details><summary><b>Biến</b></summary>
<p>

- **Makefile** là một tệp văn bản chứa tập hợp các quy tắc (rules) để tự động hóa quá trình biên dịch và xây dựng chương trình. Nó giúp lập trình viên dễ dàng quản lý việc biên dịch các chương trình lớn có nhiều tệp nguồn mà không cần phải nhập lệnh ``` gcc ``` hoặc ``` g++ ``` thủ công mỗi lần.
- Makefile thường được sử dụng với **GNU Make**, một công cụ phổ biến để tự động hóa các tác vụ biên dịch.

<br>

**Tại sao cần Makefile?**

1. **Tự động hóa**: Giúp tránh phải gõ nhiều lệnh dài mỗi khi biên dịch chương trình.

2. **Quản lý sự phụ thuộc**: Chỉ biên dịch lại những tệp bị thay đổi, giúp tiết kiệm thời gian.

3. **Tổ chức tốt hơn**: Định nghĩa rõ ràng cách biên dịch từng phần của dự án.

4. **Dễ dàng mở rộng**: Có thể thêm nhiều quy tắc khác nhau để thực hiện kiểm thử, đóng gói, triển khai,...

<br>

</p>
</details>

<br>

<details><summary><b>Biến</b></summary>
<p>

Biến trong Makefile giúp giảm lặp lại và dễ dàng quản lý cấu hình.

Biến được chia làm 2 loại:

- **Biến do người dùng tự định nghĩa**: Được định nghĩa bởi người dùng để lưu giá trị cố định hoặc đường dẫn.

```cpp
CC = gcc		# Trình biên dịch
CFlags = -Wall -Wextra	# Các cờ biên dịch
```

- **Biến tự động**: Các biến được Makefile tự động gán giá trị trong các quy tắc:

	+ $@: Tên file mục tiêu (Target)

	+ $<: File nguồn đầu tiên trong danh sách phụ thuộc

	+ $^: Danh sách tất cả các file phụ thuộc (loại bỏ trùng lặp).

	+ $?: Danh sách các phụ thuộc mới hơn target

	+ $*: Tên file không có phần mở rộng trong quy tắc mẫu

```cpp
$(CC) -o $@ $^ 		# $@ là mục tiêu, $^ là các file phụ thuộc
```

<br>

</p>
</details>

<br>

<details><summary><b>Ký tự mẫu (% và *)</b></summary>
<p>

- Ký tự %: Đại diện cho chuỗi bất kỳ trong tên file, được sử dụng trong pattern rules.
- Ký tự *: Đại diện cho chuỗi bất kỳ trong tên file, thường được dùng trong hàm **wildcard** hoặc các biểu thức thay thế.

# Phân biệt %.o, %.c, *.c, *.o trong Makefile

- %.o :	Đại diện cho một file .o cụ thể nhưng không cố định (phần tên file là biến).	

Trong quy tắc %.o: %.c, foo.c sẽ tạo ra foo.o.

- %.c :	Đại diện cho một file .c cụ thể nhưng không cố định (phần tên file là biến).	

Trong quy tắc %.o: %.c, foo.o sẽ phụ thuộc vào foo.c.

- *.c :	Đại diện cho tất cả các file .c trong thư mục hiện tại.	

Khi sử dụng $(wildcard *.c), sẽ trả về danh sách các file .c trong thư mục hiện tại.

- *.o :	Đại diện cho tất cả các file .o trong thư mục hiện tại.	

Khi sử dụng rm -f *.o, tất cả các file .o trong thư mục hiện tại sẽ bị xóa.


# Liệt kê tất cả các file .c trong thư mục

```cpp
SOURCES = $(wildcard Source/*.c)

all:
	@echo "Danh sách file nguồn: "
	@$(foreach file, $(SOURCES), echo $(file);)
```

<br>

</p>
</details>

<br>

<details><summary><b>Quy tắc (Rules)</b></summary>
<p>

- Quy tắc trong Makefile là tập hợp các hướng dẫn để tạo ra một file mục tiêu (Target) từ các file phụ thuộc (prerequisites) bằng cách thực hiện các commands (lệnh).

```cpp
target: prerequisites
	commands
```

- target: Kết quả mong muốn (ví dụ: file thực thi, file object).
- prerequisites: Các tệp mà target phụ thuộc vào (ví dụ: file nguồn .c, file header .h).
- commands: Lệnh shell để tạo target từ prerequisites. Lệnh phải được thụt đầu dòng bằng tab.

```cpp
main.o: main.c
	gcc -c main.c -o  main.o
```

main.o là target.

main.c là phụ thuộc.

gcc -c main.c -o main.o là lệnh để biên dịch main.c thành main.o.

<br>

</p>
</details>

<br>

<details><summary><b>Pattern rules</b></summary>
<p>

- Quy tắc mẫu cho phép xử lý nhiều tệp hoặc tạo file theo một mẫu chung.

```cpp
    %.o : %.c
        gcc -c $< -o $@
        $(CC) $(CFlags) -c $< -o $@
```

- % : Đại diện cho một phần bất kỳ trong tên file.
- File mục tiêu: %.o → Tất cả các file .o.
- File phụ thuộc: %.c → File .c tương ứng với .o.
- $<: Đại diện cho file phụ thuộc (ở đây là file .c).
- $@: Đại diện cho target (ở đây là file .o).

**Ví dụ**:

```cpp
# Thư mục chứa header và source files
HEADER_DIR = Header
SOURCE_DIR = Source

# Trình biên dịch và các cờ
CC = gcc
CFLAGS = -I$(HEADER_DIR) -Wall -Wextra

$(SOURCE_DIR)/%.o: $(SOURCE_DIR)/%.c
    $(CC) $(CFLAGS) -c -o $@ $<

```

</p>
</details>

<br>

<details><summary><b>Quy tắc giả (Phony targets)</b></summary>
<p>

- Quy tắc giả không tạo ra file thực sự, thường được sử dụng cho các mục tiêu đặc biệt như ``` clean, all, run ```.

```cpp
    clean:  
        rm -f *.o myprogram
    
    run: $(TARGET)
        ./$(TARGET)

    .PHONY: clean run
```

<br>

</p>
</details>

<br>

<details><summary><b>Ví dụ</b></summary>
<p>

💻 **Các file object tạo ra sẽ nằm chung thư mục với các file source**
```Makefile
# Tên file thực thi đầu ra
TARGET = main

# Thư mục chứa header và source files
HEADER_DIR = Header	# Chứa các file header (.h)
SOURCE_DIR = Source	# Chứa các file nguồn (.c)

# Tìm tất cả các file nguồn (.c) trong thư mục Source
SOURCES = $(wildcard $(SOURCE_DIR)/*.c)

# Chuyển đổi danh sách file nguồn thành danh sách file object (.o)
OBJECTS = $(SOURCES:.c=.o)

# Trình biên dịch sử dụng (gcc/g++) và các cờ biên dịch
CC = gcc
CFLAGS = -I$(HEADER_DIR) -Wall -Wextra	 # -I: thêm thư mục header, -Wall -Wextra: bật cảnh báo

# Quy tắc mặc định sẽ được thực thi khi gọi `make`
all: $(TARGET)	# make

# Quy tắc để tạo ra tệp thực thi
$(TARGET): $(OBJECTS)
	$(CC) -o $@ $^

# Quy tắc để biên dịch các tệp .c thành .o
$(SOURCE_DIR)/%.o: $(SOURCE_DIR)/%.c
	$(CC) $(CFLAGS) -c -o $@ $<

# Quy tắc để dọn dẹp các file object và file thực thi
clean:
	rm -f $(SOURCE_DIR)/*.o $(TARGET)

# Quy tắc để chạy chương trình sau khi biên dịch
run: $(TARGET)
	./$(TARGET)

# Quy tắc giả (phony target) để đảm bảo không xung đột với file thực tế
.PHONY: all
```

<br>

💻 **Các file object tạo ra sẽ nằm ở 1 thư mục riêng**
```cpp
# Tên file thực thi đầu ra
TARGET = main

# Thư mục chứa các file header, source và binary (object files + executable)
HEADER_DIR = Header     # Chứa các file header (.h)
SOURCE_DIR = Source     # Chứa các file nguồn (.c)
BINARY_DIR = Bin        # Chứa các file biên dịch (.o) và file thực thi

# Tìm tất cả các file nguồn (.c) trong thư mục Source
SOURCES = $(wildcard $(SOURCE_DIR)/*.c)

# Chuyển đổi danh sách file nguồn thành danh sách file object (.o) trong thư mục Bin
OBJECTS = $(SOURCES:$(SOURCE_DIR)/%.c=$(BINARY_DIR)/%.o)

# Trình biên dịch sử dụng (gcc/g++) và các cờ biên dịch
CC = gcc
CFLAGS = -I$(HEADER_DIR) -Wall -Wextra	# -I: thêm thư mục header, -Wall -Wextra: bật cảnh báo

# Quy tắc mặc định sẽ được thực thi khi gọi `make`
all: $(BINARY_DIR)/$(TARGET)	

# Tạo thư mục Bin nếu chưa tồn tại (để lưu file object và file thực thi)
$(BINARY_DIR):
	mkdir -p $(BINARY_DIR)

# Quy tắc để tạo file thực thi từ các file object (.o)
$(BINARY_DIR)/$(TARGET): $(OBJECTS) | $(BINARY_DIR)
	$(CC) -o $@ $^		# Liên kết các file object thành một file thực thi

# Quy tắc để biên dịch từng file nguồn (.c) thành file object (.o)
$(BINARY_DIR)/%.o: $(SOURCE_DIR)/%.c | $(BINARY_DIR)
	$(CC) $(CFLAGS) -c -o $@ $<  # Biên dịch file .c thành file .o

# Quy tắc để dọn dẹp các file object và file thực thi
clean:
	rm -f $(BINARY_DIR)/*.o $(BINARY_DIR)/$(TARGET)

# Quy tắc để chạy chương trình sau khi biên dịch
run: $(BINARY_DIR)/$(TARGET)
	./$(BINARY_DIR)/$(TARGET)

# Quy tắc giả (phony target) để đảm bảo không xung đột với file thực tế
.PHONY: all
```

<br>

</p>
</details>
