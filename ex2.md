# BÀI 2: TỐI ƯU PROMPT (THỰC HÀNH VIẾT PROMPT GIẢI NGHĨA STACK TRACE VÀ GỠ LỖI)
## Họ và tên: Vũ Đình Kiên
## Môn học: AI Application | Lesson 4

---

### 1. Nội dung Prompt sau khi tối ưu (Áp dụng cấu trúc 5 thành phần)

> "Hãy đóng vai là một Chuyên gia gỡ lỗi Java (Java Debugger) dày dặn kinh nghiệm. Tôi đang gặp lỗi sập chương trình khi vận hành một lớp quản lý người dùng. 
> 
> **Ngữ cảnh hệ thống (Context & Stack Trace):** > Mã nguồn lớp `UserManager` của tôi như sau:
> ```java
> import java.util.List;
>  
> public class UserManager {
>     private List<String> users; 
>    
>     public void addUser(String user) {
>         users.add(user); 
>     }
> }
> ```
> Khi chạy ứng dụng, hệ thống ném ra dấu vết lỗi (Stack Trace) trên console:
> ```text
> Exception in thread "main" java.lang.NullPointerException: Cannot invoke "java.util.List.add(Object)" because "this.users" is null
> 	at UserManager.addUser(UserManager.java:7)
> 	at Main.main(Main.java:6)
> ```
> 
> **Ràng buộc xử lý (Constraints):** > 1. Hãy giải thích ngắn gọn nguyên nhân gốc rễ (Root Cause) dẫn đến lỗi dựa trên Stack Trace đã cung cấp.
> 2. Đề xuất phương án sửa lỗi an toàn nhất bằng cách khởi tạo danh sách với `ArrayList` nhưng phải đảm bảo giữ vững tính bao đóng (Encapsulation) của lập trình hướng đối tượng (OOP), không để lộ trực tiếp danh sách ra bên ngoài một cách tùy tiện.
> 
> **Định dạng đầu ra (Format):** Trình bày lời giải nghĩa logic kèm khối mã nguồn Java sau khi sửa lỗi hoàn chỉnh."

---

### 2. Đoạn mã nguồn Java hoàn chỉnh và an toàn do AI sinh ra

Chào em, dưới đây là phần phân tích nguyên nhân và mã nguồn đã được sửa đổi an toàn theo đúng các nguyên lý thiết kế hướng đối tượng:

#### Phân tích nguyên nhân gốc rễ (Root Cause):
Dựa vào Stack Trace: `Cannot invoke "java.util.List.add(Object)" because "this.users" is null`, nguyên nhân là do biến thuộc tính `users` mới chỉ được khai báo dưới dạng một tham chiếu interface (`List<String>`) mà chưa hề được cấp phát vùng nhớ (chưa khởi tạo đối tượng cụ thể bằng từ khóa `new`). Do đó, khi phương thức `addUser()` gọi đến hành vi `.add()`, JVM không tìm thấy đối tượng thực tế nào trong bộ nhớ Heap để thực thi, dẫn đến lỗi `NullPointerException` tại dòng số 7.

#### Mã nguồn Java hoàn chỉnh, an toàn (Đảm bảo tính bao đóng OOP):

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class UserManager {

    // Đảm bảo tính bao đóng: Khởi tạo danh sách bằng ArrayList ngay khi khai báo 
    // và giữ phạm vi truy cập private để ngăn chặn các tác động tùy tiện từ bên ngoài.
    private final List<String> users = new ArrayList<>();

    /**
     * Phương thức thêm người dùng an toàn
     * @param user Tên người dùng cần thêm
     */
    public void addUser(String user) {
        // Chặn lỗi biên nếu tham số truyền vào bị null hoặc rỗng
        if (user != null && !user.trim().isEmpty()) {
            this.users.add(user);
        }
    }

    /**
     * Phương thức lấy danh sách người dùng (Bảo vệ tính bao đóng)
     * Thay vì trả về trực tiếp tham chiếu 'users', ta bọc nó lại bằng unmodifiableList.
     * Điều này ngăn các lớp bên ngoài tự ý dùng lệnh .add() hoặc .clear() làm thay đổi trạng thái nội tại của UserManager.
     */
    public List<String> getUsers() {
        return Collections.unmodifiableList(this.users);
    }
}
