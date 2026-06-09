# Sử dụng image nền có sẵn Python 3.11 bản slim.
# "slim" nghĩa là bản nhẹ, ít package thừa, giúp image nhỏ hơn.
FROM python:3.11-slim


# Khai báo các biến môi trường bên trong container.
# Các biến này giúp app biết model nằm ở đâu, log ghi ở đâu.
ENV PYTHONDONTWRITEBYTECODE=1 \
    # Không tạo file cache .pyc khi chạy Python, giúp image/container gọn hơn.

    PYTHONUNBUFFERED=1 \
    # In log ra ngay lập tức, không bị giữ trong buffer.
    # Nhờ vậy khi dùng docker logs sẽ thấy log realtime hơn.

    MODEL_DIR=/app/models \
    # Thư mục chứa các model AI bên trong container.

    OUTPUT_DIR=/app/outputs \
    # Thư mục dùng để lưu output/log khi service chạy.

    VISION_MODEL_PATH=/app/models/vision/squeezenet1.1-7.onnx \
    # Đường dẫn đầy đủ tới file model ảnh ONNX.

    VISION_LABELS_PATH=/app/models/vision/imagenet_classes.txt
    # Đường dẫn đầy đủ tới file nhãn ImageNet.


# Đặt thư mục làm việc chính trong container là /app.
# Từ dòng này trở xuống, các lệnh COPY/RUN sẽ thao tác trong /app.
WORKDIR /app


# Cập nhật danh sách package của hệ điều hành trong container.
# Sau đó cài curl để container có công cụ tải/gọi URL nếu cần.
# --no-install-recommends giúp không cài thêm package phụ không cần thiết.
# Cuối cùng xóa cache apt để giảm dung lượng image.
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*


# Copy file requirements.txt từ máy thật vào thư mục /app trong image.
# File này chứa danh sách thư viện Python cần cài.
COPY requirements.txt .


# Cài các thư viện Python được liệt kê trong requirements.txt.
# --no-cache-dir giúp không lưu cache pip, làm image nhẹ hơn.
RUN pip install --no-cache-dir -r requirements.txt


# Copy thư mục app vào image.
# Đây là source code chính của FastAPI service.
COPY app/ app/


# Copy thư mục models vào image.
# Thư mục này chứa model ONNX và file labels nếu đã tải sẵn.
COPY models/ models/


# Copy các request JSON mẫu để test API telemetry.
COPY sample_requests/ sample_requests/


# Copy ảnh mẫu dùng để test API phân loại ảnh.
COPY sample_images/ sample_images/


# Copy các script hỗ trợ, ví dụ script tải model.
COPY scripts/ scripts/


# Copy tài liệu hướng dẫn chạy và quan sát.
COPY docs/ docs/


# Copy các ví dụ mở rộng, không bắt buộc nhưng có trong project.
COPY optional_examples/ optional_examples/


# Copy README.md và RUN_GUIDE.md vào thư mục /app.
# Đây là các file hướng dẫn tổng quan của project.
COPY README.md RUN_GUIDE.md ./


# Tạo sẵn thư mục outputs và models/vision trong container.
# /app/outputs dùng để ghi log.
# /app/models/vision dùng để chứa model ảnh.
RUN mkdir -p /app/outputs /app/models/vision


# Khai báo container sử dụng port 8000.
# Lưu ý: EXPOSE chỉ là khai báo port bên trong container.
# Muốn máy Mac truy cập được vẫn phải map port bằng docker run -p.
EXPOSE 8000


# Lệnh mặc định sẽ chạy khi container start.
# uvicorn là server chạy FastAPI.
# app.main:app nghĩa là:
#   thư mục app/
#   file main.py
#   biến FastAPI tên app
# --host 0.0.0.0 cho phép app nhận request từ bên ngoài container.
# --port 8000 nghĩa là FastAPI chạy ở port 8000 bên trong container.
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]