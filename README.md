# Pipeline Tự động hóa: Từ STEP 3D đến Bản vẽ Kỹ thuật SVG 2D

Đây là một dự án tự động hóa hoàn chỉnh có khả năng chuyển đổi các mô hình CAD 3D ở định dạng `.step` thành các bản vẽ kỹ thuật 2D đa hình chiếu ở định dạng `.svg`. Pipeline này sử dụng Docker để tạo một môi trường nhất quán, tận dụng sức mạnh của FreeCAD để xử lý hình học và thư viện `ezdxf` của Python để tự động thêm các kích thước cơ bản.

![Sơ đồ quy trình](https://i.imgur.com/your-diagram-image.png) <!-- Gợi ý: Bạn có thể tạo một sơ đồ đơn giản và tải lên imgur để có link ảnh -->

---

## Tính năng chính

*   **Tự động hoàn toàn:** Chạy toàn bộ quy trình chỉ bằng một lệnh duy nhất.
*   **Tạo đa hình chiếu:** Tự động tạo ra các hình chiếu 2D (Đứng, Bằng, Cạnh) và một hình chiếu 3D (Trục đo) từ file STEP.
*   **Tự động ghi kích thước:** Tự động thêm các kích thước cơ bản (kích thước bao, đường kính, bán kính) vào các hình chiếu 2D.
*   **Lồng vào Khung bản vẽ:** Tự động sắp xếp, co giãn và căn chỉnh các hình chiếu để vừa vặn hoàn hảo vào các template khung bản vẽ SVG tiêu chuẩn (A0, A1, A2, v.v.).
*   **Linh hoạt và Tùy biến:** Dễ dàng cấu hình các thông số như tỉ lệ, kiểu chiếu, khoảng cách, và các tùy chọn ghi kích thước thông qua một giao diện dòng lệnh tương tác.
*   **Đóng gói bằng Docker:** Đảm bảo pipeline chạy nhất quán trên mọi hệ điều hành (Windows, macOS, Linux) mà không cần cài đặt FreeCAD hay các thư viện Python phức tạp trên máy cá nhân.

---

## 1. Điều kiện Tiên quyết

Để chạy dự án này, máy tính của bạn **chỉ cần có một yêu cầu duy nhất**:

*   **Docker và Docker Compose:**
    *   **Windows / macOS:** Cài đặt **Docker Desktop**. Nó đã bao gồm tất cả mọi thứ bạn cần.
        *   Tải về tại: [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
        *   Trên Windows, hãy đảm bảo bạn đã kích hoạt WSL 2 backend để có hiệu năng tốt nhất.
    *   **Linux:** Cài đặt Docker Engine và Docker Compose.
        *   Làm theo hướng dẫn chính thức tại: [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)

> **Lưu ý:** Bạn **KHÔNG** cần phải cài đặt FreeCAD, Python, hay bất kỳ thư viện Python nào (`ezdxf`, `numpy`,...) trên máy của mình. Tất cả đã được đóng gói sẵn trong Docker image.

---

## 2. Hướng dẫn sử dụng

### Bước A: Thiết lập lần đầu

1.  **Clone Repository:**
    ```bash
    git clone https://github.com/ten-cua-ban/ten-repository.git
    cd ten-repository
    ```
2.  **Đặt file Input:**
    *   Sao chép các file mô hình 3D (`.step` hoặc `.stp`) của bạn vào thư mục `input/`.

### Bước B: Chạy Pipeline

1.  **Mở Terminal:** Mở một cửa sổ terminal (hoặc PowerShell trên Windows) và trỏ đến thư mục gốc của dự án.
2.  **Thực thi Script:** Chạy script tương tác sau:
    ```bash
    ./scripts/run.sh
    ```
    *(Trên Windows, nếu bạn đang dùng Git Bash hoặc WSL, lệnh này sẽ hoạt động. Nếu dùng PowerShell hoặc CMD, bạn có thể cần chạy `bash ./scripts/run.sh`)*

3.  **Làm theo Hướng dẫn:**
    *   Script sẽ yêu cầu bạn chọn file STEP để xử lý.
    *   Sau đó, chọn template khung bản vẽ mong muốn.
    *   Cuối cùng, cấu hình các thông số cho bản vẽ (bạn có thể nhấn Enter để dùng các giá trị mặc định).

4.  **Chờ đợi:**
    *   **Lần chạy đầu tiên** sẽ mất khá nhiều thời gian (5-20 phút) để Docker build image (tải và cài đặt FreeCAD).
    *   **Tất cả các lần chạy sau** sẽ cực kỳ nhanh (vài giây để build) vì Docker sẽ sử dụng lại cache.

5.  **Kiểm tra Kết quả:**
    *   Sau khi quy trình hoàn tất, file bản vẽ cuối cùng (`final_drawing.svg`) sẽ nằm trong thư mục `output/`.
    *   Script sẽ cố gắng tự động mở file này cho bạn.
    *   Các file debug (như `DEBUG_step1...dxf` và `debug_document.FCStd`) cũng sẽ được lưu trong thư mục `output/` để giúp bạn kiểm tra các bước trung gian.

---

## 3. Lưu ý và Hạn chế Hiện hữu

Dự án này là một công cụ mạnh mẽ nhưng vẫn đang trong quá trình phát triển. Dưới đây là một số điểm cần lưu ý:

*   **Phiên bản FreeCAD:** Pipeline này được xây dựng và thử nghiệm với **FreeCAD 0.19**. Các phiên bản mới hơn có thể có các API khác và có thể yêu cầu cập nhật mã nguồn (đặc biệt là các file trong `scripts/`).
*   **Chất lượng Ghi Kích thước:**
    *   Logic `dxf_add_dim.py` hiện tại còn khá cơ bản. Nó có thể ghi các kích thước trùng lặp hoặc đặt chúng ở vị trí chưa tối ưu.
    *   Nó chưa có khả năng "hiểu" các đặc trưng kỹ thuật phức tạp (ví dụ: một cụm lỗ khoan theo quy luật).
    *   Khả năng ghi kích thước góc và các loại kích thước khác vẫn đang trong giai đoạn thử nghiệm.
*   **Hiệu năng:** Việc xử lý các file STEP rất lớn và phức tạp có thể tốn nhiều tài nguyên CPU và RAM.
*   **Chỉ hỗ trợ file STEP:** Hiện tại, pipeline chỉ được thiết kế để đọc file `.step` hoặc `.stp`. Các định dạng CAD khác (IGES, SLDPRT,...) chưa được hỗ trợ.

---

## 4. Đóng góp và Phát triển

Chúng tôi hoan nghênh mọi sự đóng góp! Nếu bạn muốn cải thiện dự án, đây là một vài ý tưởng:
*   Nâng cấp `dxf_add_dim.py` để trở nên thông minh hơn.
*   Tối ưu hóa `Dockerfile` để giảm kích thước image.
*   Hỗ trợ thêm các định dạng CAD đầu vào.
*   Nâng cấp pipeline để tương thích với các phiên bản FreeCAD mới hơn.```
