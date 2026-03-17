# bai4
- Sử dụng mã nguồn Buildroot, biên dịch thành công 1 Image cơ bản cho BBB
- 
  ![1](https://github.com/user-attachments/assets/0369d9fb-8994-4a52-96e6-b76f10906ea9)
  1. Lệnh sudo dd if=output/images/sdcard.img of=/dev/sda ...
Đây là lệnh "vàng" trong hệ điều hành nhúng.

if=...sdcard.img: Đây là file ảnh toàn bộ hệ điều hành (bao gồm cả Bootloader, Kernel và RootFS) mà Buildroot đã đóng gói cho bạn.

of=/dev/sda: Bạn đang ghi trực tiếp file ảnh này vào thiết bị lưu trữ (thẻ nhớ).

Ý nghĩa: Bước này chính là đưa toàn bộ công sức biên dịch của bạn từ môi trường ảo (máy tính) vào thực tế (thẻ nhớ) để board có thể chạy được.

2. Lệnh sync
Việc bạn gõ sync sau khi lệnh dd kết thúc là một thói quen của người làm nhúng chuyên nghiệp.

Tác dụng: Nó ép hệ thống phải đẩy toàn bộ dữ liệu còn sót lại trong bộ nhớ đệm (buffer) vào thẻ nhớ ngay lập tức. Nếu không có lệnh này mà bạn rút thẻ nhớ ra ngay, file hệ thống có thể bị lỗi (corrupt) và board sẽ không boot được.
- BBB khởi động thành công OS
- 
  ![2](https://github.com/user-attachments/assets/a96d40cb-a7b0-40c7-a607-2e512acc7ea6)
  
- Tùy chỉnh thêm, bỏ 1 số phần mềm như: vi, nano, top, htop... để nắm được cách thêm, bỏ package cho custom OS.
- 
  ![3](https://github.com/user-attachments/assets/5fb4ce51-af32-4d82-86a7-93ac86824cf5)
  1. Giám sát tài nguyên hệ thống (Phần trên cùng)
CPU: Đang chạy rất nhẹ (chỉ 0.7%).

Mem (RAM): Board BBB có tổng cộng khoảng 489MB RAM, và hệ điều hành tối giản bạn build chỉ đang dùng hết 17.1MB. Điều này cho thấy OS của bạn cực kỳ tối ưu và nhẹ.

Swp (Swap): Bộ nhớ ảo trên ổ cứng (thẻ nhớ), hiện đang là 0K (không cần dùng đến).

2. Quản lý tiến trình (Danh sách ở dưới)
Bảng này liệt kê các "chương trình" đang chạy ngầm trên board:

init (PID 1): Tiến trình cha khởi tạo toàn bộ hệ thống.

syslogd & klogd: Các dịch vụ ghi nhật ký hệ thống.

mosquitto: Như mình đã nói ở ảnh trước, đây là dịch vụ MQTT cho IoT.

htop: Chính là cái chương trình bạn đang mở để xem các thông số này.

3. Tác dụng của bức ảnh này trong báo cáo
Bức ảnh này có giá trị rất cao vì nó chứng minh được 3 điều:

Bạn đã biết dùng make menuconfig: Để tìm và cài đặt gói htop (vì mặc định Linux tối giản không có cái này).

Hệ thống hoạt động ổn định: Các dịch vụ quan trọng đều đang ở trạng thái S (Sleeping - sẵn sàng) hoặc R (Running).

Khả năng quản trị: Bạn có thể theo dõi xem board có đang bị quá tải hay không, chương trình nào đang chiếm nhiều RAM nhất.
- BT02: Viết chương trình Hello World cơ bản với C/C++.
- 
  ![4](https://github.com/user-attachments/assets/5b275adf-ec41-4317-84ef-b3a161dcf6e7)
   cách tạo 1 chương trình 
   Bước 1: Viết mã nguồn trên máy tính (Host - Ubuntu)
Bạn tạo một file có tên là hello.c trên máy tính chạy Ubuntu của mình (không phải trên board).

C
#include <stdio.h>

int main() {
    printf("Chao Quang! Hello World tu BeagleBone Black.\n");
    return 0;
}
Bước 2: Biên dịch chéo (Cross-compile)
Đây là bước quan trọng nhất. Bạn không dùng lệnh gcc thông thường vì nó sẽ tạo ra file chạy cho máy tính x86. Bạn phải dùng Toolchain của Buildroot mà bạn đã build ở Bài 1.

Mở Terminal trên máy tính Ubuntu, tìm đến thư mục chứa Toolchain của Buildroot và gõ lệnh:

Bash
# Đường dẫn thường là: output/host/bin/arm-linux-gcc
arm-linux-gcc hello.c -o hello_bbb
arm-linux-gcc: Trình biên dịch dành riêng cho kiến trúc ARM của board BBB.

hello_bbb: File thực thi cuối cùng sẽ chạy trên board.

Bước 3: Đưa file vào Board và chạy thử
Sau khi có file hello_bbb, bạn cần đưa nó vào RootFS của board. Có 2 cách:

Cách thủ công (Dùng thẻ nhớ):

Cắm thẻ nhớ vào máy tính.

Copy file hello_bbb vào thư mục /root hoặc /home trong phân vùng rootfs của thẻ nhớ.

Cắm lại vào BBB và khởi động.


Chạy chương trình trên Board:
Trên cửa sổ Terminal của board BBB, gõ:
- Thử khởi chạy chương trình thành công trên BBB
- 
  ![5](https://github.com/user-attachments/assets/1c79e178-f153-47e4-9793-23bcf790e864)
  
- Bài 03
-  Cấu hình thành công chương trình Helloworld đã làm thành 1 custom package trong Buildroot.
- 
  ![6](https://github.com/user-attachments/assets/c2ceb525-1793-4cc2-9a12-46923db0d08f)
  
- Bật gói Helloworld trong menuconfig, biên dịch lại. Chứng minh package tự động cài đặt sẵn trong OS đã build.
- 
  ![7](https://github.com/user-attachments/assets/a054a6cf-111a-4a28-b927-db38957f5687)

1. Tạo thư mục
Truy cập vào thư mục package của Buildroot và tạo thư mục cho ứng dụng:

Bash
mkdir package/helloworld
2. Tạo file cấu hình Config.in
Để gói hiện lên trong menuconfig. Tạo file package/helloworld/Config.in:

Plaintext
config BR2_PACKAGE_HELLOWORLD
    bool "helloworld"
    help
      Chuong trinh Hello World tu viet.
3. Tạo file biên dịch helloworld.mk
Để hướng dẫn Buildroot cách build. Tạo file package/helloworld/helloworld.mk:

Makefile
HELLOWORLD_VERSION = 1.0
HELLOWORLD_SITE = $(TOPDIR)/package/helloworld/src
HELLOWORLD_SITE_METHOD = local

define HELLOWORLD_BUILD_CMDS
	$(TARGET_CC) $(@D)/hello.c -o $(@D)/helloworld
endef

define HELLOWORLD_INSTALL_TARGET_CMDS
	$(INSTALL) -D -m 0755 $(@D)/helloworld $(TARGET_DIR)/usr/bin/helloworld
endef

$(eval $(generic-package))
4. Kích hoạt và Build
Mở file package/Config.in, thêm dòng: source "package/helloworld/Config.in" vào cuối menu.

Chạy make menuconfig, chọn Target packages -> Miscellaneous -> tích chọn [*] helloworld.

Lưu lại và gõ lệnh make.
