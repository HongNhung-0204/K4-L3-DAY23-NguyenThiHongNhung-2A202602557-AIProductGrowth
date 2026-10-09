# Worksheet — AI Quản trò Ma Sói trên Discord

Họ tên: Nguyễn Thị Hồng Nhung · MSSV: 2A202602557 · Ngày làm: 09/10/2026

**Số đầu vào dùng trong bài** (lấy từ bài của chính mình, không đặt mới):

| Số | Giá trị | Lấy từ đâu |
|---|---|---|
| Giá gói (ARPU / server trả phí) | $3,99/tháng gồm 30 ván hoàn thành | Lab 22, tab 2_Pricing |
| Giá / ván hoàn thành | $0,133 | Lab 22, `2_Pricing!B19` |
| Cost/ván (Cost/Job) | $0,0435, trong đó LLM $0,0056 | Lab 22, `1_Cost_Job!B66`, `B27` |
| Gross margin mô hình | 67,3% | Lab 22, `2_Pricing!B21` |
| Tỷ lệ ván hoàn thành dùng trong mô hình | 85,7% — **ước tính**, chưa đo | Lab 22, `1_Cost_Job!B10` |
| Định nghĩa ván hoàn thành, activation, retention, North Star | Theo tuần, đơn vị người chơi | Lab 20, metrics-pack mục 1.2, 3.1, 3.3, 04 |

Không có runway: đội 4 người làm dự án học tập, chưa có vốn và chưa trả lương.

---

## Trạm 1 — Loại mô hình

**Câu chốt loại:** Chúng tôi là **B2C**. Người trả tiền là **cá nhân**: admin hoặc chủ một server Discord, trả gói $3,99/tháng từ tiền túi hoặc quỹ nhóm. Người dùng là chính admin đó và nhóm bạn chơi cùng trong server, không phải khách hàng của một doanh nghiệp. Chúng tôi chạm trực tiếp từng người chơi qua bot trong Discord.

Vì sao không phải B2B2C: server Discord của một nhóm bạn không phải doanh nghiệp, admin không bán lại gì cho bạn bè. Một người trả, cả nhóm cùng chơi, giống mô hình "bạn bè chơi miễn phí" của Friends & Fables (Lab 22).

**Bảng đèn B2C (HANDBOOK §3.1)** — tình trạng hôm nay 09/10/2026:

| Đèn | ✅ / 🔧 / ❌ | Số nằm ở đâu / cần gì để đo |
|---|---|---|
| Đường cong retention có phẳng không | 🔧 | Cần event `game_completed` + `player_game_completed` (Lab 20 §6.1). eventLogger đang làm (Điền, 08/10/2026). Có log xong thì cần thêm 4 tuần mới vẽ được W1–W4 |
| Activation rate | 🔧 | Cần event `lobby_joined` + cài bot vào server có ghi `guild_id`. Làm được trong 2 tuần |
| p95 cost/user/tháng ÷ ARPU | 🔧 | Cần log token theo `guild_id` cho mỗi lời gọi LLM (`EVAL-01` §6.1 đã liệt kê trường). Hiện chỉ có token đo từ prompt, chưa có theo server |
| Trial → paid | ❌ | Chưa có gói trả phí. Phải đăng ký Discord Premium Apps và tạo SKU; chưa kiểm tra bot có đủ điều kiện không |
| Retention M12 | ❌ | Cần 12 tháng có người trả tiền. Sớm nhất 10/2027 |
| Chi phí free tier ÷ tổng COGS | 🔧 | Lab 22 thiết kế bản miễn phí chỉ dùng lời dẫn mẫu (không gọi LLM). Cần cờ `tier` trong log để tách chi phí |
| Tỷ lệ refund | ❌ | Chưa có giao dịch. Cần dữ liệu thanh toán từ Discord |
| LTV/CAC · CAC payback · GM | 🔧 | GM chỉ có từ mô hình Lab 22. Cần hóa đơn OpenAI + Railway thật (việc tháng 1 trong Lab 22) |

Ngoài bảng §3.1 mình thêm 1 đèn riêng của sản phẩm: **tỷ lệ ván hoàn thành** (Lab 20, Leading 3). Lý do: đây là mẫu số của Cost/ván ở Lab 22, nên nó vừa báo trước retention vừa báo trước GM.

| Đèn riêng | ✅ / 🔧 / ❌ | Số nằm ở đâu / cần gì để đo |
|---|---|---|
| Tỷ lệ ván hoàn thành | ✅ (nội bộ) · 🔧 (người dùng thật) | Nhóm chơi thử 10 ván, cả 10 ván đi tới màn công bố phe thắng → **10/10 = 100%**. Giới hạn: (1) mẫu nhỏ — khoảng tin cậy 95% của 10/10 là ~69%–100% (cận dưới = 1 − 0,025^(1/10)); (2) người chơi là đội phát triển, thuộc luật, ít AFK/rớt mạng hơn người dùng thật. Số này chứng minh engine chạy hết ván, chưa chứng minh người dùng thật chơi hết ván. Cần eventLogger + pilot 5 server để đo trên người dùng thật |

---

## Trạm 2 — Thẻ đèn

**North Star:** **Weekly Connected Players** (Lab 20 §3.3) — số người chơi trong tuần có ≥1 ván hoàn thành cùng ≥4 người thật khác — **hiện tại: chưa có số** (chưa ra mắt) — **mục tiêu ngày 90: ≥ 100**. Mục tiêu suy từ kế hoạch Lab 22: 20 server trả phí × 5 người thật mỗi ván.

**Đèn bật trước của B2C:** đèn số 1, đường cong retention server. North Star đếm người chơi; đèn 1 đếm server vì server là đơn vị trả tiền.

| # | Tầng | Đèn | Định nghĩa (đếm gì · **không** đếm gì) | Công thức | Nhịp · ai lấy số | Báo trước cho |
|---|---|---|---|---|---|---|
| 1 | L ⭐ | **Đường cong retention server theo tuần** | Cohort = server có ván hoàn thành đầu tiên trong cùng tuần lịch (T2–CN, giờ VN). Server "còn ở lại" tuần Wn nếu có ≥1 ván hoàn thành với ≥5 người thật. **Không** đếm ván toàn AI Player, server test của đội, ván bị hủy | Số server cohort còn ở lại tuần Wn ÷ số server của cohort, vẽ W1→W8 | Tuần · Điền | Trial→paid (đèn 6) sau 4–8 tuần → CAC/LTV (đèn 8) sau 1–2 quý |
| 2 | L | **Activation server** | Server mới cài bot có ván hoàn thành đầu tiên (≥5 người thật) **trong 7 ngày** kể từ khi cài. **Không** đếm server của đội phát triển; server cài lại bot thì vẫn tính theo lần cài đầu | Server activate ÷ server cài bot trong tuần | Tuần · Sơn | Retention server W4 (đèn 1) sau 4 tuần |
| 3 | L | **Tỷ lệ ván hoàn thành** | Ván đã bắt đầu (`game_started`) đi tới `GAME_OVER` có công bố phe thắng. **Không** đếm phòng chờ chưa bắt đầu; ván người chơi bấm hủy trong 60 giây đầu không tính vào mẫu số | `game_completed` ÷ `game_started` trong tuần | Ngày · Điền | Cost/ván (đèn 4, 5) ngay trong tuần → GM (đèn 7) cuối tháng; retention (đèn 1) sau 2–4 tuần |
| 4 | O | **Chi phí LLM / ván hoàn thành** | Tổng tiền token của mọi lời gọi LLM (lời dẫn, hỏi luật, sau này AI Player) chia cho ván hoàn thành. Gồm cả lời gọi bị timeout. **Không** gồm Railway, phí Discord | Σ(token × giá gpt-4o-mini) ÷ số ván hoàn thành, trong tuần | Tuần · Chinh | GM (đèn 7) sau 1 tháng — bắt chi phí AI trước khi lên hóa đơn |
| 5 | O | **p95 chi phí AI / server trả phí / tháng ÷ ARPU** | Với mỗi server trả phí: (số ván × Cost/ván) + chi phí hỏi luật vượt mức. Lấy server ở **phân vị 95**, không lấy trung bình. **Không** tính server dùng miễn phí | Chi phí server p95 ÷ $3,99 | Tháng · Chinh | GM (đèn 7) cùng tháng; quyết định đặt trần usage |
| 6 | O | **Trial → paid** | Server đã activate (đèn 2) mua gói $3,99 trong 30 ngày kể từ khi activate. **Không** đếm server được đội tặng gói | Server mua gói ÷ server activate 30 ngày trước | Tháng · Sơn | CAC (đèn 8) và doanh thu tháng sau |
| 7 | G | **Gross margin thực tế** | (Doanh thu sau phí Discord − OpenAI − Railway − QA) ÷ doanh thu, theo hóa đơn thật. **Không** dùng số mô hình | Theo công thức Lab 22 nhưng thay số ước tính bằng hóa đơn | Tháng · Nhung | — |
| 8 | G | **CAC / server trả phí** | Tiền quảng bá + giờ công đi mời server (quy đổi 50.000 ₫/giờ, như Lab 22) ÷ số server mua gói mới | Chi phí kéo khách ÷ server mua gói mới | Quý · Nhung | — |

Đèn chi phí AI là đèn số: **4** (theo tuần, bắt trước GM) và **5** (đuôi power user).
Tầng: 3 Leading · 3 Operating · 2 Lagging.

---

## Trạm 3 — Ngưỡng

| # | Đèn | 🟢 | 🟡 | 🔴 | Nguồn | Lý do (1 câu) · ngày kiểm tra nếu [BM] |
|---|---|---|---|---|---|---|
| 1 | Retention server | Phẳng từ W4 | Phẳng từ W8 | Chưa phẳng ở W8 | [TB] + [MH] | Chưa có benchmark cho bot game xã hội trên Discord (Lab 20 §04). Đo 2 cohort đầu làm baseline; mức phẳng phải đủ cao theo [MH] 3 bên dưới |
| 2 | Activation server | ≥ baseline + 30% | ± 30% quanh baseline | < baseline | [TB] | Chưa có chuẩn. Đo 2 cohort tuần đầu ở pilot 5 server, có số trước 08/11/2026 |
| 3 | Tỷ lệ ván hoàn thành | ≥ 80% | 70–80% | < 70% | [MH] 1 | Dưới 70% thì GM < 60% ở giá $0,133; 80% giữ GM ≥ 65% |
| 4 | Chi phí LLM / ván | ≤ $0,0081 | $0,0081–0,0135 | > $0,0135 | [MH] 2 | Trên $0,0135 thì Cost/ván vượt $0,0532 và GM < 60%, dù mọi thứ khác giữ nguyên |
| 5 | p95 chi phí AI / server ÷ ARPU | < 0,40 | 0,40–0,50 | > 0,50 | [MH] 4 | Server p95 vẫn phải có GM ≥ 50%. Chi phí trung bình một server dùng đủ 30 ván hiện là 0,33 |
| 6 | Trial → paid | ≥ 8,5% | 5,6–8,5% | < 5,6% | [BM] | Trung vị app AI 8,5%, app không AI 5,6% (RevenueCat 2026, qua TechCrunch 10/03/2026) · **kiểm tra 09/10/2026** |
| 7 | Gross margin thực tế | ≥ 60% | 50–60% | < 50% | [MH] + [BM] | Mô hình Lab 22 ra 67,3%; dưới 50% là vùng nguy hiểm. Trung vị công ty AI 2026P là 53% (ICONIQ 07/2026, theo HANDBOOK §8, chốt 27/08/2026) |
| 8 | CAC / server trả phí | ≤ $7,4 | $7,4–11,0 | > $11,0 | [MH] 3 | Với retention 12 tháng bằng trung vị app AI, LTV/server ≈ $22; CAC ≤ $7,4 mới đạt LTV/CAC ≥ 3 |

### Phụ lục [MH] — phép tính

**[MH] 1 — Tỷ lệ ván hoàn thành**

```
Đầu vào (Lab 22, tại 3.000 ván thử/tháng):
  Giá / ván P = $0,133
  Chi phí biến đổi / ván thử v = LLM 0,0056 × 1,05 (retry 5%) + Railway 0,0067 + phí Discord 15% × 0,133
                               = $0,0325
  QA nội bộ / ván thử q = 5% × 3 phút × $1,91/giờ = $0,0048
  Cost/ván hoàn thành = (v + q) / R, với R = tỷ lệ ván hoàn thành
Điều kiện GM ≥ g:  (v + q) / R ≤ P × (1 − g)  →  R ≥ (v + q) / (P × (1 − g))
  GM 65%: R ≥ 0,0373 / 0,0466 = 80,0%
  GM 60%: R ≥ 0,0373 / 0,0532 = 70,0%
  GM 50%: R ≥ 0,0373 / 0,0665 = 56,0%
Kết quả → 🟢 ≥ 80% · 🟡 70–80% · 🔴 < 70%
```

**[MH] 2 — Chi phí LLM / ván hoàn thành**

```
Đầu vào: như [MH] 1, R = 85,7% (giá trị trong mô hình Lab 22)
Cost/ván = (LLM × 1,05 + Railway 0,0067 + Discord 0,0200) / 0,857 + QA 0,0056
Giải ngược LLM tối đa cho từng mức GM:
  GM 65%: Cost/ván ≤ 0,0466 → LLM ≤ $0,0081  (1,5 lần mức hiện tại $0,0056)
  GM 60%: Cost/ván ≤ 0,0532 → LLM ≤ $0,0135  (2,4 lần)
  GM 50%: Cost/ván ≤ 0,0665 → LLM ≤ $0,0244  (4,4 lần)
Kết quả → 🟢 ≤ $0,0081 · 🟡 $0,0081–0,0135 · 🔴 > $0,0135
Ghi chú: thêm AI Player (Chinh đang tích hợp, 08/10/2026) sẽ thêm nhiều lời gọi LLM mỗi ván.
Đây là lý do đèn này đo theo tuần chứ không chờ hóa đơn tháng.
```

**[MH] 3 — CAC tối đa / server trả phí**

```
Đầu vào: ARPU $3,99 · GM 67,3% (Lab 22) → lãi gộp / server / tháng = 3,99 × 0,673 = $2,685
Retention 12 tháng: chưa có số của mình → dùng trung vị app AI 21,1% [BM] làm giả định
  churn tháng = 1 − 0,211^(1/12) = 12,2%
  thời gian ở lại trung bình = 1 / 0,122 = 8,2 tháng
  LTV = 2,685 × 8,2 = $22,1
CAC tối đa để LTV/CAC ≥ 3 = 22,1 / 3 = $7,4   (payback = 7,4 / 2,685 = 2,7 tháng)
CAC tại LTV/CAC = 2           = 22,1 / 2 = $11,0
Kết quả → 🟢 ≤ $7,4 · 🟡 $7,4–11,0 · 🔴 > $11,0
Hệ quả cho đèn 1: muốn chi CAC đến mức payback 6 tháng ($16,1) mà vẫn LTV/CAC ≥ 3,
  cần ở lại ≥ 18 tháng → churn ≤ 5,6%/tháng → retention 12 tháng ≥ 50%, gấp 2,4 lần trung vị app AI.
  Vì vậy đường cong không chỉ cần phẳng mà còn phải phẳng ở mức cao; trước khi có số thật, chỉ dùng kênh gần như 0 đồng (PLG).
```

**[MH] 4 — p95 chi phí AI / server ÷ ARPU**

```
Đầu vào: Cost/ván $0,0435 · gói 30 ván · ARPU $3,99
Server dùng đủ gói: 30 × 0,0435 = $1,31 → 1,31 / 3,99 = 0,33  (GM của server này 67%)
Mỗi server phải tự có lãi: chi phí ≤ ARPU × (1 − GM)
  GM 60% → ≤ 0,40 ARPU  ·  GM 50% → ≤ 0,50 ARPU
Ván vượt gói được tính thêm $0,133 nên không làm lỗ; rủi ro là phần KHÔNG tính tiền theo ván:
  câu hỏi luật (~8.500 token/câu) và lời gọi AI Player.
Kết quả → 🟢 < 0,40 · 🟡 0,40–0,50 · 🔴 > 0,50
```

---

## Trạm 4 — 5 luật quyết định

Đánh dấu ⏹ cho luật dừng (có 3).

1. ⏹ **NẾU** đường cong retention server chưa phẳng ở W4 **TRONG** 2 cohort tuần liên tiếp **VÀ** mỗi cohort ≥ 20 server **THÌ** dừng mọi việc mời server mới (gỡ bài quảng bá, ngừng đăng lên danh sách bot), cả đội dành 3 tuần sửa trải nghiệm ván đầu tiên và gọi phỏng vấn 5 admin đã rời **KHÔNG THÌ** không được mở thêm kênh kéo server mới để bù số server bỏ đi.
2. ⏹ **NẾU** tỷ lệ ván hoàn thành < 70% **TRONG** 7 ngày **VÀ** có ≥ 50 ván đã bắt đầu **THÌ** đóng băng mọi tính năng mới (vai mới, AI Player, chế độ luyện tập): chỉ merge PR sửa crash, AFK, rớt mạng, cho tới khi 2 tuần liên tiếp đạt ≥ 80% **KHÔNG THÌ** không bật thêm vai mới hay AI Player "cho ván vui hơn" để kéo người chơi ở lại.
3. **NẾU** chi phí LLM / ván hoàn thành > $0,0135 **TRONG** 2 tuần liên tiếp **THÌ** trong sprint kế tiếp chuyển trợ lý luật sang chỉ gửi các mục `RB-*` liên quan thay vì cả rulebook, và đặt trần 3 câu hỏi luật / người / ván **KHÔNG THÌ** không đổi sang model rẻ hơn khi chưa chạy lại bộ test `EVAL-01` (các tiêu chí Chặn QA-C3, SUM-C1, SUM-C3).
4. **NẾU** p95 chi phí AI / server trả phí > 0,50 ARPU **TRONG** 1 tháng **VÀ** có ≥ 20 server trả phí **THÌ** đặt trần 100 câu hỏi luật / server / tháng trong gói và bật tính phí vượt cho AI Player theo ván **KHÔNG THÌ** không tăng giá gói $3,99 cho tất cả server, vì sẽ mất server dùng nhẹ để bù cho vài server dùng nặng.
5. ⏹ **NẾU** trial → paid < 5,6% **TRÊN** 3 phiên bản màn mời mua gói khác nhau **VÀ** mỗi phiên bản có ≥ 30 server đã activate **THÌ** dừng thử màn mời mua; trong 2 tuần chạy thử đơn vị tính tiền khác (trả theo buổi chơi thay vì gói tháng) với 10 server **KHÔNG THÌ** không giảm giá gói xuống dưới $3,99, vì giá / ván $0,133 chỉ cao hơn giá sàn $0,130 khoảng 2%.
