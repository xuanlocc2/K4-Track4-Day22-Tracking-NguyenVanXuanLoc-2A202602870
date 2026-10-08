# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** 2 thành viên **Thành viên:** Nguyễn Văn Xuân Lộc (2A202602870), Hồ Đăng Phúc (2A202602796)

Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

Mỗi video: tracker bạn nộp, `conf`, `iou`, điều bạn **nhìn thấy** trên video, và một cấu hình đã thử rồi loại.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---|---|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | botsort | 0.3 | 0.7 | Nhiều người nhỏ ở xa, detector bỏ sót nhiều (DetRe chỉ ~19%). Re-ID tracker bắt thêm được người nhỏ, nên HOTA cao hơn. | bytetrack conf 0.3 iou 0.5: HOTA 26.9, IDF1 25.7 (thấp hơn do bỏ sót nhiều hơn). Cũng loại conf 0.5 (HOTA 27.2 với botsort, DetA tụt về 14.3). |
| video_2 (phố đêm, tĩnh, rất đông) | bytetrack | 0.3 | 0.5 | Camera đứng yên, đông người, ánh sáng đèn đường mạnh. Người đi rìa ảnh giữ ID ổn định suốt clip (người ở đáy ảnh giữ ID 3 từ frame 200 đến 800). | botsort conf 0.3: cùng người đó đổi ID 3 → 69 → 63; độ dài track trung vị 102 frame so với 177 của bytetrack, và có 67 ID so với 47. |
| video_3 (camera di động, ảnh nhỏ) | bytetrack | 0.3 | 0.5 | Camera đặt thấp, đi sát người đi bộ; hộp lớn, che khuất nhau liên tục, hình mờ do chuyển động. | botsort conf 0.3: 167 ID với 93 track ngắn dưới 15 frame, so với 127 ID và 63 track ngắn của bytetrack, số hộp mỗi frame gần bằng nhau (5.7 vs 5.1). |
| video_4 (trong nhà, camera di chuyển) | bytetrack | 0.3 | 0.5 | Trung tâm thương mại, sàn bóng và kính phản chiếu. Trong các frame đã xem (200, 400, 600, 800) không thấy hộp ma từ phản chiếu ở conf 0.3. Người đi gần camera chiếm gần hết khung hình. | botsort conf 0.3: người áo trắng đổi ID 6 → 49 trong khi bytetrack giữ 5 → 45; 73 ID so với 61. Cũng cân nhắc bytetrack conf 0.5 (64 ID), không thấy lợi ích rõ. |
| video_5 (trên xe bus, giao lộ đông) | botsort | 0.3 | 0.5 | Camera trên xe chuyển động, người ở xa rất nhỏ, xe và đèn giao thông che khuất. | bytetrack conf 0.3: chỉ 2.7 hộp mỗi frame so với 4.1 của botsort, bỏ sót nhiều người đi bộ nhỏ ở xa (ví dụ frame 300, 450). |

## 2. Số liệu video_1

Bảng do `scripts/evaluate_practice.py` in ra cho `runs/nop_bai/video_1.txt` (botsort, conf 0.3, iou 0.7):

```
HOTA: final_video1-pedestrian      HOTA      DetA      AssA      DetRe     DetPr     AssRe     AssPr     LocA
video_1                            29.969    18.408    49.061    19.176    74.385    52.38     80.959    83.019

CLEAR: final_video1-pedestrian     MOTA      MOTP      MODA      CLR_Re    CLR_Pr    CLR_TP    CLR_FN    CLR_FP    IDSW
video_1                            19.025    80.817    19.202    22.491    87.244    4179      14402     611       33

Identity: final_video1-pedestrian  IDF1      IDR       IDP       IDTP      IDFN      IDFP
video_1                            29.703    18.68     72.463    3471      15110     1319
```

Tóm tắt: **HOTA 30.0, MOTA 19.0, IDF1 29.7**.

So sánh các cấu hình đã chấm (cùng video_1):

| cấu hình | HOTA | DetA | AssA | MOTA | IDSW | IDF1 |
|---|---|---|---|---|---|---|
| bytetrack c0.3 i0.5 | 26.9 | 15.1 | 48.1 | 17.3 | 12 | 25.7 |
| bytetrack c0.2 | 27.5 | 15.4 | 49.0 | 17.8 | 12 | 26.9 |
| bytetrack c0.5 | 25.3 | 13.9 | 45.8 | 15.9 | 14 | 23.4 |
| botsort c0.3 i0.5 | 29.5 | 18.1 | 48.2 | 19.8 | 25 | 29.4 |
| botsort c0.15 i0.5 | 29.3 | 19.2 | 45.1 | 20.7 | 27 | 29.6 |
| botsort c0.5 i0.5 | 27.2 | 14.3 | 51.6 | 15.3 | 10 | 24.6 |
| botsort c0.3 i0.4 | 29.3 | 17.4 | 49.6 | 19.5 | 19 | 29.8 |
| **botsort c0.3 i0.7** | **30.0** | 18.4 | 49.1 | 19.0 | 33 | 29.7 |
| strongsort c0.3 | 28.7 | 17.7 | 46.6 | 19.7 | 41 | 29.9 |
| ocsort c0.3 | 27.5 | 17.9 | 42.4 | 19.8 | 42 | 28.7 |
| deepocsort c0.3 | 27.4 | 17.8 | 42.2 | 19.8 | 51 | 27.8 |

`video_2` đến `video_5` không có nhãn trong gói lab. Không điền số cho các video đó. Lựa chọn của bốn video này dựa trên xem bằng mắt (ảnh ghép ID ở nhiều frame) và thống kê không cần nhãn (số ID, độ dài track, số hộp mỗi frame). Đây chỉ là chỉ báo gián tiếp, không phải HOTA/MOTA/IDF1.

Lưu ý về cách chấm: gói lab thiếu `lab_data/data_lab21/video_1/eval_config.json`. Tôi tự tạo file này với `{"benchmark": "MOT17", "split": "train"}` (suy ra từ tên seq MOT17-02-FRCNN trong `seqinfo.ini`), nên các số trên đúng với giả định đó. Tôi cũng vá `np.float/np.int` trong `scripts/evaluate_practice.py` vì TrackEval không chạy được với NumPy mới.

## 3. Phân tích

**video_2 (phố đêm, đông, camera tĩnh, chỉ đánh giá bằng mắt).** Bytetrack giữ ID tốt hơn botsort ở đây: người đi ở đáy ảnh giữ ID 3 từ frame 200 đến 800, còn với botsort cùng người đó đổi 3 → 69 → 63, và track trung vị chỉ 102 frame (bytetrack: 177). Camera đứng yên nên chuyển động dự đoán đã đủ tốt; ngược lại, ảnh đêm, người nhỏ và đông chen nhau làm đặc trưng Re-ID kém tin cậy và dễ kéo ID sai. Vì vậy tracker chỉ dùng chuyển động hợp hơn tracker có Re-ID trong cảnh này.

**video_5 (trên xe bus, đánh giá bằng mắt).** Ở đây nút thắt là detector: người đi bộ ở xa rất nhỏ nên nhiều khi bị bỏ. Botsort conf 0.3 cho 4.1 hộp mỗi frame so với 2.7 của bytetrack, và trong ảnh ghép thấy botsort giữ thêm được người nhỏ ở xa. Hạn chế: botsort cũng tạo nhiều track ngắn (31 track dưới 15 frame so với 24), tức là một phần số hộp tăng thêm có thể là phát hiện chập chờn hoặc hộp giả; tôi chưa có nhãn để kiểm tra phần này.

**video_1 (có nhãn).** Botsort hơn bytetrack chủ yếu nhờ DetA (18.4 vs 15.1) chứ không phải nhờ AssA (49.1 vs 48.1), nên giả thuyết "Re-ID giúp giữ ID tốt hơn" không được video_1 ủng hộ rõ; cái được là bắt thêm được người. iou 0.7 cho HOTA cao nhất nhưng đổi ID nhiều hơn (33 so với 19 ở iou 0.4); chênh lệch giữa các iou nhỏ.

**video_3 và video_4.** Ở hai video camera di động, bytetrack ít phân mảnh ID hơn botsort (video_3: 127 so với 167 ID; video_4: 61 so với 73 ID) với số hộp mỗi frame gần nhau, nên tôi chọn bytetrack. Đây là kết luận từ số liệu gián tiếp và vài frame đã xem, không có nhãn xác nhận.

## 4. Nếu có thêm thời gian

Chạy đủ quét `conf`/`iou` cho cả 5 video (hiện botsort mới quét đủ ở video_1 và video_3) và xem kỹ các frame gây đổi ID. Thử Re-ID khác hoặc detector lớn hơn, vì video_1 bị giới hạn bởi recall của detector chứ không phải bởi thuật toán gán.
