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

💻 Các file object tạo ra sẽ nằm chung thư mục vói file .c
```cpp
# Tên file thực thi
TARGET = main

# Thư mục chứa header và source files
HEADER_DIR = Header
SOURCE_DIR = Source

# Các tệp nguồn và tệp object tương ứng
SOURCES = $(wildcard $(SOURCE_DIR)/*.c)
OBJECTS = $(SOURCES:.c=.o)

# Trình biên dịch và các cờ
CC = gcc
CFLAGS = -I$(HEADER_DIR) -Wall -Wextra

# Quy tắc đầu tiên là quy tắc mặc định sẽ được thực thi
all: $(TARGET)	# make

# Quy tắc để tạo ra tệp thực thi
$(TARGET): $(OBJECTS)
	$(CC) -o $@ $^

# Quy tắc để biên dịch các tệp .c thành .o
$(SOURCE_DIR)/%.o: $(SOURCE_DIR)/%.c
	$(CC) $(CFLAGS) -c -o $@ $<

# Quy tắc để làm sạch thư mục build
clean:
	rm -f $(SOURCE_DIR)/*.o $(TARGET)

run: $(TARGET)
	./$(TARGET)

# Quy tắc giả (phony target)
.PHONY: all
```

<br>

💻 Các file object tạo ra sẽ nằm ở 1 thư mục riêng
```cpp
# Tên file thực thi
TARGET = main

# Thư mục chứa header và source files
HEADER_DIR = Header
SOURCE_DIR = Source
BINARY_DIR = Bin

# Các tệp nguồn và tệp object tương ứng
SOURCES = $(wildcard $(SOURCE_DIR)/*.c)
OBJECTS = $(SOURCES:$(SOURCE_DIR)/%.c=$(BINARY_DIR)/%.o)

# Trình biên dịch và các cờ
CC = gcc
CFLAGS = -I$(HEADER_DIR) -Wall -Wextra

# Quy tắc đầu tiên là quy tắc mặc định sẽ được thực thi
all: $(BINARY_DIR)/$(TARGET)	# make

# Tạo thư mục bin nếu chưa tồn tại
$(BINARY_DIR):
	mkdir -p $(BINARY_DIR)

# Quy tắc để tạo ra tệp thực thi
$(BINARY_DIR)/$(TARGET): $(OBJECTS)
	$(CC) -o $@ $^

# Quy tắc để biên dịch các tệp .c thành .o
$(BINARY_DIR)/%.o: $(SOURCE_DIR)/%.c 
	$(CC) $(CFLAGS) -c -o $@ $<

# Quy tắc để làm sạch thư mục build
clean:
	rm -f $(BINARY_DIR)/*.o $(BINARY_DIR)/$(TARGET)

run: $(BINARY_DIR)/$(TARGET)
	./$(BINARY_DIR)/$(TARGET)

# Quy tắc giả (phony target)
.PHONY: all
```

<br>

</p>
</details>


