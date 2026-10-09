# OPERATING DASHBOARD — AI Quản trò Ma Sói trên Discord

**Loại mô hình:** B2C — admin server tự trả gói $3,99/tháng, cả nhóm bạn chơi cùng · **Cập nhật:** 09/10/2026 · Nguyễn Thị Hồng Nhung – 2A202602557
**NORTH STAR:** Weekly Connected Players (người chơi có ≥1 ván hoàn thành cùng ≥4 người thật trong tuần) — hiện tại **chưa có số** — mục tiêu ngày 90 **≥ 100**

### Đèn báo sớm (Leading — nhìn hằng ngày/tuần)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|---|---|---|---|
| ⭐ Đường cong retention server theo tuần (W1→W8) | chưa có | phẳng từ W4 / từ W8 / chưa phẳng ở W8 | [TB] | Trial→paid, CAC/LTV |
| Activation server (ván hoàn thành đầu tiên trong 7 ngày sau khi cài bot) | chưa có | ≥ baseline+30% / ±30% / < baseline | [TB] | Retention W4 |
| Tỷ lệ ván hoàn thành (`GAME_OVER` ÷ ván bắt đầu) | 100% (10/10 ván chơi thử nội bộ) — chưa đủ mẫu, khoảng tin cậy 95% xuống tới ~69% | ≥ 80% / 70–80% / < 70% | [MH] 1 | Cost/ván → GM; retention |

### Đèn vận hành (Operating — nhìn hằng tuần/tháng)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|---|---|---|---|
| Chi phí LLM / ván hoàn thành | $0,0056 (đo từ prompt) 🟢 | ≤ $0,0081 / –$0,0135 / > $0,0135 | [MH] 2 | GM (trước hóa đơn 1 tháng) |
| p95 chi phí AI / server trả phí ÷ ARPU | 0,33 (trung bình mô hình) | < 0,40 / 0,40–0,50 / > 0,50 | [MH] 4 | GM; trần usage |
| Trial → paid (trong 30 ngày sau activate) | chưa có | ≥ 8,5% / 5,6–8,5% / < 5,6% | [BM] RevenueCat 2026, kiểm tra 09/10/2026 | CAC, doanh thu |

### Đèn kết quả (Lagging — nhìn hằng quý)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn |
|---|---|---|---|
| Gross margin theo hóa đơn thật | 67,3% (mô hình Lab 22) | ≥ 60% / 50–60% / < 50% | [MH] Lab 22 + [BM] ICONIQ 53% (2026P) |
| CAC / server trả phí | chưa có | ≤ $7,4 / $7,4–11 / > $11 | [MH] 3 |

### 5 luật quyết định (⏹ = luật dừng)

1. ⏹ **NẾU** retention server chưa phẳng ở W4 **TRONG** 2 cohort tuần liên tiếp **VÀ** mỗi cohort ≥ 20 server **THÌ** dừng mời server mới, cả đội 3 tuần sửa ván đầu tiên + phỏng vấn 5 admin đã rời **KHÔNG THÌ** không mở thêm kênh kéo server để bù số bỏ đi.
2. ⏹ **NẾU** tỷ lệ ván hoàn thành < 70% **TRONG** 7 ngày **VÀ** ≥ 50 ván bắt đầu **THÌ** đóng băng tính năng mới, chỉ merge PR sửa crash/AFK/rớt mạng tới khi 2 tuần liền ≥ 80% **KHÔNG THÌ** không bật vai mới hay AI Player để giữ chân.
3. **NẾU** chi phí LLM / ván > $0,0135 **TRONG** 2 tuần liền **THÌ** sprint kế chỉ gửi mục luật liên quan thay vì cả rulebook + trần 3 câu hỏi luật / người / ván **KHÔNG THÌ** không đổi sang model rẻ hơn khi chưa chạy lại bộ test EVAL-01.
4. **NẾU** p95 chi phí AI / server > 0,50 ARPU **TRONG** 1 tháng **VÀ** ≥ 20 server trả phí **THÌ** đặt trần 100 câu hỏi luật / server / tháng + tính phí vượt cho AI Player theo ván **KHÔNG THÌ** không tăng giá gói cho mọi server.
5. ⏹ **NẾU** trial → paid < 5,6% **TRÊN** 3 phiên bản màn mời mua **VÀ** mỗi bản ≥ 30 server activate **THÌ** dừng thử màn mời mua, 2 tuần thử tính tiền theo buổi chơi với 10 server **KHÔNG THÌ** không giảm giá dưới $3,99 (chỉ cao hơn giá sàn 2%).

### Cổng gác 90 ngày

| Ngày | Metric (1) | Ngưỡng qua cổng | Bằng chứng | Nếu trượt |
|---|---|---|---|---|
| 30 · 08/11/2026 | Số ván có log đủ để tính tỷ lệ hoàn thành | ≥ 30 ván bắt đầu, từ ≥ 5 server pilot | Export eventLogger + báo cáo trong `eval/results/` | FIX: sửa eventLogger / tìm thêm server |
| 60 · 08/12/2026 | Retention W4 của cohort pilot | ≥ 3/5 server còn ván hoàn thành ở W4 | Bảng cohort xuất từ Postgres | FIX 1 lần (sửa ván đầu); lần 2 → PIVOT sang CLB / quán board game |
| 90 · 07/01/2027 | Trial → paid | ≥ 5,6% trên ≥ 30 server activate | Export entitlement từ Discord Developer Portal | PIVOT đơn vị tính tiền (luật 5); đã pivot → KILL |

**KILL CRITERIA:** Ngày 07/01/2027, nếu tỷ lệ ván hoàn thành 4 tuần gần nhất vẫn < 70% sau 1 lần FIX, hoặc có < 5 server trả phí, thì dừng bán gói, để bot miễn phí chỉ dùng lời dẫn mẫu (không tốn LLM).

**CHƯA ĐO ĐƯỢC:** retention, activation, tỷ lệ ván hoàn thành với người dùng thật — hiện chỉ có 10 ván nội bộ (cần eventLogger + pilot 5 server — có số đầu 08/11/2026, đường cong W4 có 08/12/2026) · trial→paid, refund (cần bật Discord Premium Apps, chưa kiểm tra bot đủ điều kiện — sớm nhất 12/2026) · GM thật, p95 (cần log token theo server + hóa đơn OpenAI, Railway — 08/11/2026) · retention 12 tháng (sớm nhất 10/2027).
