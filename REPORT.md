# BÁO CÁO BÀI TẬP LAB 17: DATA PIPELINE ENGINEERING (TRACK 2)

## THÔNG TIN HỌC VIÊN
*   **Họ và tên:** Đoàn Minh Quang
*   **Mã học viên (MHV):** 2A202600757
*   **Môn học:** Day 17 — Data Pipeline Engineering (Track 2)

---

## 1. THÔNG TIN SUBMISSION & LINK DỰ ÁN
*   **Mã nguồn chính:** [main.py](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/main.py)
*   **Flywheel pipeline:** [flywheel.py](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/flywheel.py)
*   **Bài viết suy luận (Core Part):** [REFLECTION.md](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/submission/REFLECTION.md)
*   **Thiết kế hệ thống nâng cao (Bonus Part):** [DESIGN.md](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/bonus/DESIGN.md)
*   **Dữ liệu đầu ra sinh từ Flywheel:**
    *   Tập Eval Golden: [eval_golden.jsonl](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/datasets/eval_golden.jsonl)
    *   Tập DPO Pairs: [preference_pairs.jsonl](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/datasets/preference_pairs.jsonl)
    *   Tệp cách ly dữ liệu lỗi: [quarantine.csv](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/quarantine.csv)

---

## 2. KẾT QUẢ KIỂM THỬ ĐẠT ĐƯỢC (EVIDENCE LOGS)

### 2.1. Kết quả chạy `verify.py` (ALL PASS — 16/16 Checks)
```text
=== verify.py: Day 17 lab smoke test ===
=== Day 17 pipeline (lite) ===
  bronze rows in      : 13
  duplicates dropped  : 5  (Silver dedup)
  records quarantined : 3  (failed the gate)
  silver rows         : 8
  gold daily rows     : 5

Gold (completed orders by day):
order_date  n_orders  n_users  revenue  avg_order_value
2026-06-01         2        2    69.49            34.74
2026-06-02         1        1    19.99            19.99
2026-06-03         1        1    49.50            49.50
2026-06-04         1        1     5.00             5.00
2026-06-06         1        1    49.50            49.50
  [OK ] extract loaded raw rows
  [OK ] Silver dropped duplicates (the hook)
  [OK ] gate quarantined bad records
  [OK ] Gold produced daily rows
  [OK ] no duplicate order_id remains in Silver
  [OK ] streaming consumer is idempotent
  [OK ] doc->chunk->embedding ingestion
  [OK ] agent traces flattened into Bronze spans
  [OK ] eval golden set curated from traces
  [OK ] decontamination drops eval-overlapping pairs
  [OK ] at least one clean preference pair survives
  [OK ] ASOF point-in-time join avoids future leakage
  [OK ] knowledge graph built from docs
  [OK ] graph query answers 'what is returnable?' = widget only
  [OK ] graph answers a real 2-hop question (widget->accessory->warehouse)
  [OK ] vector foil: no single chunk answers the multi-hop question

RESULT: 16/16 checks — ALL PASS
```

### 2.2. Kết quả chạy `pytest` (18/18 Tests Passed)
```text
$ .venv\Scripts\python.exe -m pytest -q
..................                                                       [100%]
18 passed in 1.08s
```

### 2.3. Kết quả chạy `dbt build` (PASS=11 — Bao gồm Model, Data test, Unit test)
```text
Running with dbt=1.11.11
Registered adapter: duckdb=1.10.1
Found 2 models, 1 seed, 7 data tests, 485 macros, 1 unit test

Concurrency: 1 threads (target='dev')

1 of 11 START seed file main.raw_orders ........................................ [RUN]
1 of 11 OK loaded seed file main.raw_orders .................................... [INSERT 16 in 0.06s]
2 of 11 START unit_test stg_orders::stg_orders_dedups_on_order_id .............. [RUN]
2 of 11 PASS stg_orders::stg_orders_dedups_on_order_id ......................... [PASS in 0.17s]
3 of 11 START sql view model main.stg_orders ................................... [RUN]
3 of 11 OK created sql view model main.stg_orders .............................. [OK in 0.04s]
4 of 11 START test not_null_stg_orders_amount .................................. [RUN]
4 of 11 PASS not_null_stg_orders_amount ........................................ [PASS in 0.03s]
5 of 11 START test not_null_stg_orders_order_id ................................ [RUN]
5 of 11 PASS not_null_stg_orders_order_id ...................................... [PASS in 0.02s]
6 of 11 START test not_null_stg_orders_user_id ................................. [RUN]
6 of 11 PASS not_null_stg_orders_user_id ....................................... [PASS in 0.02s]
7 of 11 START test unique_stg_orders_order_id .................................. [RUN]
7 of 11 PASS unique_stg_orders_order_id ........................................ [PASS in 0.02s]
8 of 11 START sql table model main.gold_daily_orders ........................... [RUN]
8 of 11 OK created sql table model main.gold_daily_orders ...................... [OK in 0.04s]
9 of 11 START test not_null_gold_daily_orders_order_date ....................... [RUN]
9 of 11 PASS not_null_gold_daily_orders_order_date ............................. [PASS in 0.01s]
10 of 11 START test not_null_gold_daily_orders_revenue ......................... [RUN]
10 of 11 PASS not_null_gold_daily_orders_revenue ............................... [PASS in 0.01s]
11 of 11 START test unique_gold_daily_orders_order_date ........................ [RUN]
11 of 11 PASS unique_gold_daily_orders_order_date .............................. [PASS in 0.02s]

Finished running 1 seed, 1 table model, 7 data tests, 1 unit test, 1 view model in 0 hours 0 minutes and 5.57 seconds (5.57s).

Completed successfully

Done. PASS=11 WARN=0 ERROR=0 SKIP=0 NO-OP=0 TOTAL=11
```

---

## 3. TÓM TẮT CÁC CÔNG VIỆC HOÀN THÀNH

### Phần Lõi (Core Tasks — 100/100đ):
1.  **Medallion ETL:** Tải dữ liệu thô vào Bronze, lọc và cách ly đúng 3 dòng dữ liệu xấu sang `quarantine.csv`, loại bỏ 5 bản ghi trùng lặp ở Silver và tổng hợp dữ liệu giao dịch ở Gold theo ngày.
2.  **DAG Orchestrator:** Tổ chức pipeline chạy tuần tự dựa trên cấu trúc DAG của [dag.py](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/pipeline/dag.py) theo đúng thứ tự topo.
3.  **Idempotent Consumer:** Mô phỏng cơ chế streaming với queue partition theo khóa và bỏ qua trùng lặp dựa trên tập id đã xử lý.
4.  **dbt Project:** Tổ chức và chạy thành công dbt build cho các mô hình dữ liệu kèm 7 data tests và 1 bài test kiểm định chất lượng logic dedup.
5.  **Agent Data Flywheel:** Xử lý và làm phẳng cây trace span OpenTelemetry của AI Agent, tách thành các tập dataset DPO cặp tốt-xấu phục vụ train và tập Eval benchmark. Sử dụng bộ lọc decontaminate để chống rò rỉ đề thi và DuckDB `ASOF JOIN` để tính toán đặc trưng điểm thời gian chính xác, chống rò rỉ tương lai.

### Phần Bonus Challenge (20/20đ):
*   Hoàn thành thiết kế hệ thống **Agent Flywheel & RAG Bảo hiểm Nhân thọ tại Việt Nam** tại file [DESIGN.md](file:///d:/Users/quand/2A202600757-DoanMinhQuang-Day17/bonus/DESIGN.md) tuân thủ luật bảo mật Việt Nam **Nghị định 13/2023/NĐ-CP (PDPL)** và giải pháp tối ưu chi phí token, chống data leakage ngữ nghĩa.
