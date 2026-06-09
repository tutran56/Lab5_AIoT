<h1>Lab5_AIoT</h1>

<h2>Dockerized Multi-Model AIoT Inference Service</h2>

<p>
  Dự án này là bài Lab 5 trong học phần <b>Triển khai, phát triển ứng dụng AI và IoT</b>.
  Mục tiêu của lab là xây dựng một <b>AIoT inference service</b> có thể chạy local,
  đóng gói bằng Docker, chạy bằng Docker container và Docker Compose.
</p>

<p>
  Service sử dụng <b>FastAPI</b> để cung cấp API cho dữ liệu telemetry và ảnh upload.
  Phần vision inference sử dụng model <b>SqueezeNet ONNX ImageNet-1K</b>
  để phân loại ảnh và trả về top-k predictions.
</p>

<hr>

<h2>Demo Web</h2>

<p>
  Giao diện <code>/classify-image-demo</code> cho phép upload ảnh, gọi model ONNX
  và hiển thị ảnh kết quả có nhãn dự đoán.
</p>

<p align="center">
  <img src="IMG" alt="Demo giao diện web Lab 5" width="900">
</p>

<hr>

<h2>Mục tiêu Lab</h2>

<p>
  Lab này không tập trung train lại model, mà tập trung vào việc triển khai model đã có
  thành một service hoàn chỉnh.
</p>

<pre>
Model / Inference logic
→ FastAPI service
→ Chạy local
→ Build Docker image
→ Chạy Docker container
→ Chạy Docker Compose
→ Quan sát logs và kết quả inference
</pre>

<hr>

<h2>Công nghệ sử dụng</h2>

<ul>
  <li>Python 3.11</li>
  <li>FastAPI</li>
  <li>Uvicorn</li>
  <li>ONNX Runtime</li>
  <li>SqueezeNet ONNX ImageNet-1K</li>
  <li>Pillow</li>
  <li>NumPy</li>
  <li>Docker</li>
  <li>Docker Compose</li>
</ul>

<hr>

<h2>Chức năng chính</h2>

<h3>1. API trạng thái service</h3>

<table>
  <tr>
    <th>Endpoint</th>
    <th>Chức năng</th>
  </tr>
  <tr>
    <td><code>GET /</code></td>
    <td>Trang root giới thiệu service</td>
  </tr>
  <tr>
    <td><code>GET /health</code></td>
    <td>Kiểm tra service và trạng thái load model</td>
  </tr>
  <tr>
    <td><code>GET /docs</code></td>
    <td>Swagger UI để test API</td>
  </tr>
  <tr>
    <td><code>GET /model-info</code></td>
    <td>Thông tin service/model</td>
  </tr>
</table>

<h3>2. API telemetry AIoT</h3>

<table>
  <tr>
    <th>Endpoint</th>
    <th>Chức năng</th>
  </tr>
  <tr>
    <td><code>POST /detect-anomaly</code></td>
    <td>Phát hiện bất thường từ dữ liệu cảm biến</td>
  </tr>
  <tr>
    <td><code>POST /forecast</code></td>
    <td>Dự báo giá trị tương lai</td>
  </tr>
  <tr>
    <td><code>POST /predict-risk</code></td>
    <td>Dự đoán mức rủi ro và khuyến nghị</td>
  </tr>
</table>

<h3>3. API vision inference</h3>

<table>
  <tr>
    <th>Endpoint</th>
    <th>Chức năng</th>
  </tr>
  <tr>
    <td><code>GET /vision/model-info</code></td>
    <td>Xem thông tin model ảnh</td>
  </tr>
  <tr>
    <td><code>POST /classify-image</code></td>
    <td>Trả JSON top-k class, confidence, inference time</td>
  </tr>
  <tr>
    <td><code>POST /classify-image-annotated</code></td>
    <td>Trả ảnh có gắn nhãn dự đoán</td>
  </tr>
  <tr>
    <td><code>GET /classify-image-demo</code></td>
    <td>Giao diện web upload ảnh</td>
  </tr>
</table>

<hr>

<h2>Cấu trúc project</h2>

<pre>
lab5_dockerized_multimodel_aiot_inference_service_v4/
├── app/
│   ├── main.py
│   ├── schemas.py
│   ├── sensor_inference.py
│   ├── vision_inference.py
│   └── logging_utils.py
├── models/
│   └── vision/
│       ├── squeezenet1.1-7.onnx
│       └── imagenet_classes.txt
├── outputs/
│   └── vision_inference_log.csv
├── sample_images/
├── sample_requests/
├── scripts/
├── docs/
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
└── README.md
</pre>

<hr>

<h2>Chạy local</h2>

<h3>1. Tạo virtual environment</h3>

<pre><code>python3 -m venv .venv
source .venv/bin/activate</code></pre>

<h3>2. Cài dependencies</h3>

<pre><code>python -m pip install --upgrade pip setuptools wheel
pip install -r requirements.txt</code></pre>

<h3>3. Tải model ONNX</h3>

<pre><code>python scripts/download_vision_model.py</code></pre>

<p>Kiểm tra model:</p>

<pre><code>ls -lh models/vision</code></pre>

<p>Cần có:</p>

<pre><code>squeezenet1.1-7.onnx
imagenet_classes.txt</code></pre>

<h3>4. Chạy FastAPI service</h3>

<pre><code>uvicorn app.main:app --reload</code></pre>

<p>Mở trình duyệt:</p>

<pre><code>http://127.0.0.1:8000/health
http://127.0.0.1:8000/docs
http://127.0.0.1:8000/classify-image-demo</code></pre>

<hr>

<h2>Test API bằng curl</h2>

<h3>Health check</h3>

<pre><code>curl http://127.0.0.1:8000/health</code></pre>

<h3>Detect anomaly</h3>

<pre><code>curl -X POST http://127.0.0.1:8000/detect-anomaly \
  -H "Content-Type: application/json" \
  -d @sample_requests/detect_anomaly_request.json</code></pre>

<h3>Forecast</h3>

<pre><code>curl -X POST http://127.0.0.1:8000/forecast \
  -H "Content-Type: application/json" \
  -d @sample_requests/forecast_request.json</code></pre>

<h3>Classify image</h3>

<pre><code>curl -X POST "http://127.0.0.1:8000/classify-image?top_k=5" \
  -F "file=@sample_images/classroom_object.jpg"</code></pre>

<hr>

<h2>Chạy bằng Docker</h2>

<h3>1. Build Docker image</h3>

<pre><code>docker build -t lab5-aiot-inference:v4 .</code></pre>

<p>Kiểm tra image:</p>

<pre><code>docker images | grep lab5-aiot-inference</code></pre>

<h3>2. Chạy Docker container</h3>

<pre><code>docker rm -f lab5-aiot-api 2&gt;/dev/null || true

docker run -d \
  --name lab5-aiot-api \
  -p 8000:8000 \
  -v "$(pwd)/outputs:/app/outputs" \
  -v "$(pwd)/models/vision:/app/models/vision" \
  lab5-aiot-inference:v4</code></pre>

<h3>3. Kiểm tra container</h3>

<pre><code>docker ps
docker logs lab5-aiot-api</code></pre>

<p>Mở trình duyệt:</p>

<pre><code>http://127.0.0.1:8000/health
http://127.0.0.1:8000/docs
http://127.0.0.1:8000/classify-image-demo</code></pre>

<hr>

<h2>Chạy bằng Docker Compose</h2>

<p>Dừng container cũ nếu đang chạy:</p>

<pre><code>docker rm -f lab5-aiot-api 2&gt;/dev/null || true</code></pre>

<p>Chạy Compose:</p>

<pre><code>docker compose up --build -d</code></pre>

<p>Kiểm tra service:</p>

<pre><code>docker compose ps</code></pre>

<p>Xem logs:</p>

<pre><code>docker compose logs</code></pre>

<p>Dừng Compose:</p>

<pre><code>docker compose down</code></pre>

<hr>

<h2>Giải thích port mapping</h2>

<p>Khi chạy local, FastAPI chạy trực tiếp trên máy tại:</p>

<pre><code>http://127.0.0.1:8000</code></pre>

<p>Khi chạy Docker, FastAPI chạy bên trong container ở port <code>8000</code>.</p>

<pre><code>-p 8000:8000</code></pre>

<p>Nghĩa là:</p>

<pre><code>Mac port 8000 → Docker → Container port 8000 → FastAPI</code></pre>

<p>Cú pháp:</p>

<pre><code>-p PORT_MAC:PORT_CONTAINER</code></pre>

<p>Ví dụ nếu muốn truy cập bằng port 5000 trên Mac:</p>

<pre><code>-p 5000:8000</code></pre>

<p>thì mở:</p>

<pre><code>http://127.0.0.1:5000</code></pre>

<hr>

<h2>Dockerfile dùng để làm gì?</h2>

<p><code>Dockerfile</code> là công thức đóng gói project thành Docker image.</p>

<ul>
  <li>Dùng <code>python:3.11-slim</code> làm image nền.</li>
  <li>Khai báo biến môi trường cho model và output.</li>
  <li>Đặt thư mục làm việc là <code>/app</code>.</li>
  <li>Cài <code>curl</code>.</li>
  <li>Copy <code>requirements.txt</code> và cài dependencies.</li>
  <li>Copy source code, model, sample images, sample requests và docs.</li>
  <li>Tạo thư mục <code>outputs</code> và <code>models/vision</code>.</li>
  <li>Expose port <code>8000</code>.</li>
  <li>Chạy app bằng Uvicorn.</li>
</ul>

<p>Lệnh chạy chính trong container:</p>

<pre><code>CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]</code></pre>

<hr>

<h2>Docker Compose dùng để làm gì?</h2>

<p>
  <code>docker-compose.yml</code> giúp chạy container bằng file cấu hình,
  thay vì phải gõ lệnh <code>docker run</code> dài.
</p>

<p>Compose cấu hình sẵn:</p>

<ul>
  <li>Tên service.</li>
  <li>Tên container.</li>
  <li>Port mapping.</li>
  <li>Volume outputs.</li>
  <li>Volume models/vision.</li>
  <li>Build từ Dockerfile.</li>
</ul>

<hr>

<h2>Log inference</h2>

<p>Sau mỗi lần phân loại ảnh, service ghi log vào:</p>

<pre><code>outputs/vision_inference_log.csv</code></pre>

<p>Ví dụ:</p>

<pre><code>timestamp_utc,endpoint,filename,content_type,status,top1_class,top1_confidence,inference_time_ms
2026-06-09T02:29:54.836615+00:00,/classify-image,photo.webp,image/webp,ok,tiger cat,0.728795,15.255</code></pre>

<table>
  <tr>
    <th>Cột</th>
    <th>Mô tả</th>
  </tr>
  <tr>
    <td><code>timestamp_utc</code></td>
    <td>Thời điểm gọi inference</td>
  </tr>
  <tr>
    <td><code>endpoint</code></td>
    <td>API được gọi</td>
  </tr>
  <tr>
    <td><code>filename</code></td>
    <td>Tên file ảnh</td>
  </tr>
  <tr>
    <td><code>status</code></td>
    <td>Trạng thái xử lý</td>
  </tr>
  <tr>
    <td><code>top1_class</code></td>
    <td>Nhãn dự đoán top-1</td>
  </tr>
  <tr>
    <td><code>top1_confidence</code></td>
    <td>Độ tin cậy</td>
  </tr>
  <tr>
    <td><code>inference_time_ms</code></td>
    <td>Thời gian chạy model</td>
  </tr>
</table>

<hr>

<h2>Kết quả thực nghiệm</h2>

<h3>Local</h3>

<ul>
  <li><code>/health</code> trả <code>service_status = ok</code>.</li>
  <li><code>/docs</code> hiển thị Swagger UI.</li>
  <li><code>/classify-image-demo</code> upload ảnh và hiển thị kết quả.</li>
</ul>

<h3>Docker container</h3>

<ul>
  <li>Build thành công image <code>lab5-aiot-inference:v4</code>.</li>
  <li>Chạy thành công container <code>lab5-aiot-api</code>.</li>
  <li><code>/health</code> trả <code>vision_model_loaded = true</code>.</li>
</ul>

<h3>Docker Compose</h3>

<ul>
  <li>Chạy thành công service <code>aiot-api</code>.</li>
  <li>Logs có request <code>200 OK</code>.</li>
  <li>Volume <code>outputs</code> ghi được log inference.</li>
</ul>

<hr>

<h2>Một số lỗi đã xử lý</h2>

<h3>1. Lỗi onnxruntime trên MacBook Intel</h3>

<pre><code>No matching distribution found for onnxruntime==1.20.1</code></pre>

<p>Cách xử lý:</p>

<pre><code>Đổi onnxruntime==1.20.1 về onnxruntime==1.19.2</code></pre>

<h3>2. Lỗi SSL khi tải model</h3>

<pre><code>SSL: CERTIFICATE_VERIFY_FAILED</code></pre>

<p>Cách xử lý:</p>

<pre><code>pip install --upgrade certifi
export SSL_CERT_FILE="$(python -m certifi)"
python scripts/download_vision_model.py</code></pre>

<h3>3. Lỗi giao diện bị kẹt ở “Đang gọi model...”</h3>

<p>Nguyên nhân: sai tên biến JavaScript <code>formDa/formData</code> hoặc <code>formDatata2/formData2</code>.</p>

<p>Cách xử lý: sửa <code>app/main.py</code> để thống nhất tên biến <code>formData</code> và <code>formData2</code>.</p>

<hr>

<h2>So sánh local và Docker</h2>

<table>
  <tr>
    <th>Tiêu chí</th>
    <th>Chạy local</th>
    <th>Chạy Docker</th>
  </tr>
  <tr>
    <td>Môi trường</td>
    <td>Phụ thuộc Python và package trên máy</td>
    <td>Được đóng gói trong image</td>
  </tr>
  <tr>
    <td>Cài đặt</td>
    <td>Cần tạo venv và pip install</td>
    <td>Chỉ cần build/run image</td>
  </tr>
  <tr>
    <td>Port</td>
    <td>FastAPI chạy trực tiếp trên Mac port 8000</td>
    <td>Mac port map vào container port 8000</td>
  </tr>
  <tr>
    <td>Log</td>
    <td>Ghi trực tiếp vào thư mục local</td>
    <td>Cần mount volume để giữ log</td>
  </tr>
  <tr>
    <td>Chia sẻ</td>
    <td>Dễ lỗi version/package</td>
    <td>Dễ chạy lại trên máy khác</td>
  </tr>
  <tr>
    <td>Debug</td>
    <td>Nhanh, trực tiếp</td>
    <td>Cần xem docker logs</td>
  </tr>
  <tr>
    <td>Triển khai</td>
    <td>Phù hợp development</td>
    <td>Gần môi trường production hơn</td>
  </tr>
</table>

<hr>

<h2>Kết luận</h2>

<p>
  Project đã triển khai thành công một AIoT inference service có thể chạy ở local,
  Docker container và Docker Compose.
</p>

<p>Kết quả đạt được:</p>

<ul>
  <li>FastAPI service chạy thành công.</li>
  <li>Model SqueezeNet ONNX được load thành công.</li>
  <li>API <code>/classify-image</code> trả top-k predictions.</li>
  <li>API <code>/classify-image-annotated</code> trả ảnh có nhãn dự đoán.</li>
  <li>Giao diện <code>/classify-image-demo</code> upload ảnh và hiển thị kết quả.</li>
  <li>Docker image đã được build.</li>
  <li>Container chạy thành công với port mapping.</li>
  <li>Docker Compose chạy thành công.</li>
  <li>Log inference được ghi vào <code>outputs/vision_inference_log.csv</code>.</li>
</ul>

<p>
  Docker không làm model chính xác hơn, nhưng giúp đóng gói môi trường chạy model
  ổn định hơn, dễ chia sẻ hơn và dễ triển khai hơn.
</p>
