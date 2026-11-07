# PR-003 執行紀錄

## 2026-11-07｜PR-005 重新生模候選查核

- 使用者希望先試換 AI 模型生模，合適動漫底模作後備；建立 `PR-005-GINCHAN-MODEL-CANDIDATES.md`。
- 核對官方文件、本機 ComfyUI 節點／模板及 Hugging Face 檔案 metadata。TRELLIS.2 INT8 主權重約 5.25 GB，可共用已有 DINOv3 與 VAE；Hunyuan3D-2mv 約 4.93 GB，先做幾何對照。
- 記錄 TripoSG、StdGEN 與 UniForge3D 的適用性和限制，沒有把研究限定權重列為直播製作主線。
- README、PR-004 指向新方向；新權重未下載，模型未再生成，正式資產未變。狀態：PLAN_CREATED_IMPLEMENTATION_NOT_STARTED。
- 下一目標：TRELLIS.2 頭部小樣，以少量固定種子和真實多角度渲染判定，再決定是否進行第二候選。

## 2026-11-07｜PR-004 臉髮重建候選交付

- 使用者授權後，先備份 v1 至 `backups/pr004-before-20261107-102521`，再固定版本安裝 7 個 Blender skills 與 concept-target-visual-gate。
- 已實際建模、修訂、清理 UV／重疊面及焊接頭頸，另存 `ginchan-refined-v2.blend`／`.glb`；原圖和正式 v1 檔案雜湊保持一致。
- 真實正／側／45°／背面與全身渲染、線框、原圖／v1／v2 疊圖入口：`requirements/PR-004-HEAD-REVIEW.html`。45° 沒有原圖，只作 v1／v2 比較。
- 最終獨立視覺審查確認接縫與長髮末端改善，但髮束偏厚、眉眼神態仍不貼合原圖；原圖還原未通過。PR-004 目標 1 保持進行中，不以 QA 技術通過代替造型通過。
- 新版只有中立臉，新表情及 Godot 回歸尚未完成；正式 Godot 可動模型保持 v1。詳見 `requirements/PR-004-HEAD-REVIEW.md`。

## 2026-11-07｜PR-004 原圖對照與精修計劃

- 使用者指出與原圖差距很大，要求先對照、可用疊圖，提出臉／頭髮／衣服精修計劃，並搜尋 3D agent skills。
- 已讀原圖、最終模型三面、Godot 臉部近照及臉部生成腳本；獨立背景 Blender 渲染 REST／表情歸零／無光照底色，建立離線 HTML 等比例疊圖。
- 判定：目前是可動粗模，視覺還原未通過；眼眶／眼皮、瀏海與主髮束、衣服網格和顏色需局部重建。不能將 PR-003 技術完成解讀為外觀合格。
- 新計劃 `requirements/PR-004-GINCHAN-REFINEMENT.md`、對照頁 `requirements/PR-004-GINCHAN-COMPARISON.html`，README 指向已更新。
- 查核 EL4CTEO Blender skills、concept-target-visual-gate、blendsmith 與 Blender MCP 上游公開文件；未安裝候選、未執行上游腳本。
- 狀態：PLAN_CREATED_IMPLEMENTATION_NOT_STARTED。原圖與正式 Blender／GLB 成品保持不變。下一實作目標為頭臉和主要髮束 v2，先驗證中立臉三角度。

## 完整可動 3D 首版完成（2026-11-07）

- 以下段落為歷史紀錄；以本節為最新狀態。MotionBricks 安裝、引擎建置、推理、重定向及 Windows 交付均已完成，沒有仍須等待的安裝 session。
- `generate_motion.py` 真正執行 SOMA FP16 推理：IdleStart 26 幀、WalkStop 58 幀、Settle 34 幀，去除片段邊界重複後 116 幀。輸入為自建姿勢，未使用官方 sample motion。原始 NPZ、姿勢、SHA 和時間紀錄均已保存。
- 首次推理包含編譯約 3.85 秒，其後兩段約 0.062 秒；此數字不含下載與第一次 TensorRT 引擎建置。來源 `logs/motion-generation.json`。
- 原始模型手臂外展過大，已在重定向時修整上臂（最大約 65.39°），並依腳部接觸做有限水平修正（最大約 7.29 cm）與高度修正。模型生成的腿部及根位移保留；接觸點數學檢查不等同整張皮膚無腳滑保證。來源 `logs/motion-retarget.json`。
- 最終 `blender/ginchan-character.blend` 實際重開：21 骨骼、四個 actions、四個獨立臉部 shape keys，`logs/final-blender-verification.json` 通過。最終 GLB 重新匯入渲染正、側、背及 45°，約 129,344 三角面。
- `builds/windows/Ginchan.exe` 為正式展示入口。Windows 實際啟動並驗證視角、表情／身體動畫切換、暫停及重播；debug assertions 通過 `GINCHAN_CONTROLS_VERIFIED`。
- 正式 EXE 以真正 1920×1080 viewport 測試 300 幀，排除暖機和 PNG 擷取階段後平均 reported FPS 59.833，設定 60 FPS 上限；短測試只代表本機單角色場景。原始 `renders/windows-1080-metrics.json`，stderr 無錯誤。
- 最後一次衣服局部修正後已重新輸出 Blender／GLB／EXE、四視角圖片及影片。`renders/ginchan-motionbricks-preview.mp4` 已解碼確認 300 幀、1280×900、30 FPS、10 秒；動畫約 3.87 秒，其餘時間維持停止姿勢。
- 造型限制：髮絲和衣服邊緣偏粗、上衣仍偏色、眼睛與臉部距原圖仍有差距；技術可動不等於使用者已認可造型。未加入直播追蹤、語音對嘴或頭髮物理。
- 本次指定資料夾邏輯合計約 28.39 GB，含 uv 安裝快取約 9.41 GB；硬連結可能重複計數，共用軟件／全域快取另計。C 槽剩約 179.45 GiB。原圖 SHA-256 維持不變。`logs/storage-report.json` 有明細。
- ComfyUI 佇列確認為空後，僅停止本專案的兩個 Python 服務程序；模型與環境保留。停止紀錄 `logs/comfy-server-stopped.json`，Windows 成品不需要服務。

## MotionBricks 執行環境完成；首次引擎建置中

- Installer session 65266、validation session 95678 均 exit 0。TensorRT 10.8.0.43／Warp 1.19.0，CUDA 裝置 RTX 4070 可見；完整版本在 `logs/motion-environment-lock.txt`。
- `motion/soma-reference.json` 已實際產生：77 關節，Y up，+Z forward；Hips 高約 1.012 m，LeftFoot 約 0.072 m。已依此確認模型腳本的坐標假設。
- `scripts/generate_motion.py` 已啟動：exec session `71323`，實際 Python PID `38568`（wrapper PID 38560、PowerShell 36192）。`motion/authored-poses.json` 已保存；目前正在 TensorRT 首次引擎建置，尚未產生三段動畫輸出。日誌 `logs/motion-generation-run.log`，快取目標 `runtime/motion-trt-cache`。
- 目前引擎程序仍有 CPU 工作，不能因日誌未更新就重新啟動。下一步先輪詢原 session，或檢查 PID CPU 時間與快取；有真實終止／錯誤才修正。
- 已備好 `scripts/retarget_motion.py`：讀真正生成的 NPZ，相對自建 idle pose 對應身體旋轉與根位移，依預測 foot contacts 做有限接地修正，再烘焙 Blender／GLB。尚未執行，不能當作動作交付。
- Godot 已加中文動畫選單、全身／臉部、重播；切換會清除上一動作殘留的骨骼／表情值。腳本重新匯入無錯誤，最終仍須播放真實 MotionBricks 成果與 Windows 成品。

## MotionBricks 模型已校驗

- git lfs session 88151 exit 0。FP16 ONNX 380,327,058 bytes，SHA-256 `6c9f78e23decbda8e1cfd1b0cc96980ea4b24b3ca2c808d03b058f48f82fc9ef`；SOMA USD 2,036,435 bytes，SHA-256 `4ab93d78a0f1c06d3bcbedee5ceebb5d9d5b002fd0a88638e9399ed80b61871d`，均與 LFS 指標相符。
- 安裝 session 65266／uv PID 24628 仍存活。TensorRT libs wheel 已建立，17 packages 已解析，Warp 等下載完成，剩餘 TensorRT meta build 安裝進行中；不要重啟原 installer。
- `scripts/finish_motion_setup.ps1` worker session 95678 等待原 installer，再驗證 TensorRT／Warp 與 CUDA、執行 `inspect_soma.py` 保存官方 reference rig 至 `motion/soma-reference.json`，最後保存環境 lock。輸出在 `logs/motion-setup-validation.log`。
- `scripts/generate_motion.py` 已備好：用官方骨架自行建立放下雙臂的站姿，三段 IdleStart／WalkStop（1.1 m 前進）／Settle，使用 FP16 SOMA、本機 TensorRT、保存原始逐幀 transforms 與 foot contacts。此腳本尚未執行；需先確認骨架坐標與環境檢查通過。
- ComfyUI `/free` 已回傳 200，卸載模型釋放 VRAM；服務仍在 127.0.0.1:8188，沒有關閉或新增對外服務。
- 最新角色四視角：`renders/ginchan-review-{front,left,back,quarter}.png`，由最新 GLB 重開渲染。Blender importer 的 Icosphere 是骨骼顯示用物件，已排除 review bounds，未作角色幾何交付。

## 表情與關節已接通，開始 MotionBricks 準備

- `scripts/build_face.py` 由 `ginchan-basic.blend` 建立可編輯眼白、虹膜、瞳孔、眼皮、嘴形，輸出 `blender/ginchan-face.blend` 與 `exports/ginchan-face.glb`。四個獨立 morph：EyeBlinkLeft／EyeBlinkRight／MouthOpen／Smile。
- 已修正 Blender shape_key_add 會沿用先前形變的問題：每個新 key 明確還原 Basis 再修改。`inspect_face_keys.py` 驗證左右眼各只影響該側 2,370 個點，嘴形只影響嘴部。Godot 動畫值與輸出圖證明閉眼同步、張嘴不再牽動眼睛。
- Godot 可切換 BasicArmTest、ExpressionTest、JointCheck。實際截圖命名現為 `godot-face-ExpressionTest-*.png`、`godot-face-JointCheck-*.png`。20/21 附近閉眼、43 張嘴、75/100 微笑；JointCheck 60 是肩肘膝與輕微轉頭測試。
- 依原網格實際位置重新校正髖、膝、手腕與胸頸骨架，重算熱權重。膝蓋彎曲無大片撕裂；不把單腳關節壓力測試當作接地行走。
- 外觀仍為 AI 粗模修整首版：髮絲邊緣、眼皮接縫與衣服細節不等於專業手工建模品質，最終對照圖需如實交付。上衣顏色正在做最後局部修正；完成後再保存更新版，不應拿舊截圖冒充最新結果。
- MotionBricks 僅下載 SOMA FP16 ONNX（380,327,058 bytes）與最小人類 USD 骨架（2,036,435 bytes）。git lfs pull session `88151` 進行中；只取得骨架時 ONNX 仍為 pointer，須等待並校驗。
- 專用 Python 3.12.6 環境 `runtime/motion-env` 已建立，安裝官方 pyproject 依賴 session `65266`，日誌 `logs/motion-install.log`。目前正在取得 TensorRT-cu12-libs 10.8.0.43，勿重啟到同一環境。尚未生成 MotionBricks 動作。

## Godot 基本動作驗收完成

- 實際 Godot 截圖顯示單純減面仍有重疊表面，已增加 384 solid remesh，再減至 119,968 三角面。最終 remesh job `7f186b06-4f71-43d8-b323-1596bfe2dd4e` 成功；先前 DynamicCombo API 應使用 `sign_mode: solid` 加 `sign_mode.fill` 等平面欄位，已依原始碼修正。
- 初始距離權重造成衣服／身體拉伸，已改成 Blender 自動熱權重，髮束另綁 Head；實際抬臂截圖已消除大片拉伸。測試 rig 共 21 bones。正式骨架與臉部後續仍須驗收。
- `renders/godot-basic-002.png`、`031.png`、`060.png`、`100.png` 為實際 Godot GPU 視窗輸出。`renders/ginchan-basic.avi` 已錄製 300 frames／30 FPS／10 秒／1280×900；Godot 日誌確認完成，無執行錯誤。不能把離線固定幀錄製統計當成互動 FPS 基準。
- 目標 2 完成，目標 3 開始：眼部缺少瞳孔與自然眼皮、嘴部缺結構、頭髮偏紫，正在局部修整。MotionBricks 尚未安裝環境與實際推理；完整 goal 尚未完成。

## 粗模完成，開始 Godot 鏈路

- 512 形狀生成 20.00 秒、材質 5.64 秒；1024 修訂 12.79 秒（重用前段快取）。兩次均無 OOM。輸出位於 `runtime/ComfyUI/output/ginchan/rough_color_00001.glb` 與 `rough_color_1024_00001.glb`。
- 已用 Blender 實際重開並渲染正、側、背、45°：`renders/rough1-*.png` 與 `rough2-*.png`。1024 身體、衣服和髮束改善，四肢可分離；眼窩過黑、臉部不可動及局部表面瑕疵仍需目標 3 修整。目標 1 完成。
- Blender 5.2.3 對該高面數網格直接 decimate 發生原生崩潰（BM_vert_splice），證據 `logs/blender-decimate-crash.txt`。改用 ComfyUI 原生 Weld／Decimate／FillHoles／SmoothNormals，不重試相同崩潰流程。cleanup job `7cdacaf8-a769-4698-92e6-d2069d366fac` 成功，約 1.81 秒。
- 清理後 35,624 vertices／114,890 triangles，建立 21 bones 的測試骨架與 BasicArmTest。`blender/ginchan-basic.blend`、`exports/ginchan-basic.glb`（約 6.9 MB）已實際匯出；這是距離權重草稿，不是正式綁定品質驗收。
- 最小 Godot 專案建立，使用 skill dispatcher 保存主場景，GLB 已匯入無錯誤。正啟動渲染與截圖檢查；目標 2 持續進行中。

## 最新進展：服務、三視角與首次粗模

- ComfyUI 依賴已安裝，環境清單存於 `logs/comfy-environment-lock.txt`。服務僅監聽 `127.0.0.1:8188`，啟動紀錄為 `logs/comfy-server.json`。
- 五份模型全部完成下載，`scripts/verify_pixal_models.py` 實際比對官方 SHA-256，結果 5/5 正確。主下載 session 1378 已 exit 0。
- BiRefNet 首次去背雖技術成功，但目視發現背面少了一側長髮束，因此沒有用該遮罩生模。紀錄保存為 `logs/comfy-inputs-biref-history.json`。
- 已使用內建 image_gen 製作透明背景工作副本，保存 `references/ginchan-turnaround-cutout.png`；原 `new.png` 不覆寫。原生 ComfyUI 裁切採副本 alpha，正、左側、背面已分別查看，兩側長髮及身體輪廓保留，輸入存於 `references/front.png`、`left.png`、`back.png`。
- 新輸入 job `a4554bc6-15dd-4ecc-8359-c5e762d8b1a7` 成功，耗時 0.50 秒。
- 首次 shape job `ba298a6a-e695-4f38-9ff5-f5e5866682cd` 已通過 API schema 並執行 GPU 推理。目前仍在生成；必須先查 `comfy_status.py shape`，不可重複提交。
- Blender 綁定、Godot、MotionBricks 推理與 Windows 交付均仍未完成，整體 goal 維持 active。

## 2026-11-07：開始目標 1

使用者已授權完整 3D 首版、所需模型下載及本機服務。目標包含真正的 MotionBricks 動作，基本動畫不能代替最終交付。

已完成：
- 查核現有 PR、原圖與 RTX 4070／12 GB GPU。
- 取得官方 ComfyUI，固定目前 commit `1332fabfcc507aa7c548cd5152071ed494c207e8`。
- 建立專用 `runtime/comfy-env`（Python 3.13.9），正在安裝 CUDA 13.0 版 PyTorch；未修改其他專案環境。
- 下載官方多視角流程至 `workflows/pixal-multiview-official.json`。
- 原圖複製至 ComfyUI input，未修改原檔；三視角低解析度草稿存於 `workflows/ginchan-multiview-draft.json`，尚待執行前 schema／視角檢查。
- 五份模型使用 `scripts/download_pixal_models.py` 下載至 ComfyUI 模型目錄，完成後逐檔檢查大小。尚未完成下載，不得視為可用。

目前程序（必須先檢查是否仍存活，不能僅憑此紀錄重啟）：
- 模型下載：exec session `1378`，Python PID `33664`，uv PID `36704`。
- PyTorch 安裝：exec session `39988`，uv PID `33060`。
- 首次查核模型兩個 incomplete 檔案仍為 0 bytes；正在檢查 Hugging Face 大檔傳輸。GPU 推理尚未開始。
- 已用 BiRefNet 官方下載 URL 執行 1024-byte Range 測試，約 2.9 秒收到 HTTP 206 與完整 1024 bytes；小範圍傳輸可用。`logs/hf-range-test.bin` 是傳輸測試資料，不是完整模型。後續兩個 live session 再次輪詢均未終止；模型暫存仍 0 bytes，PyTorch 已開始解壓 CUDA DLL。若模型完整下載繼續無資料，需要以可續傳分段下載改善傳輸，而非重複無條件重啟原流程。

下一步：先驗證上述兩個程序的實際狀態。PyTorch 成功後用同一環境安裝 `runtime/ComfyUI/requirements.txt`，檢查 GPU 可用；下載完成後啟動僅綁定 127.0.0.1 的 ComfyUI，檢查節點 schema、三視角方向與遮罩，再提交粗模生成。

尚未完成：銀醬網格、Blender 綁定與表情、Godot 展示、MotionBricks 動作、Windows 成品、實際圖片與錄影驗收。整體目標持續 active。

## 後續進展：模型與生模流程

- 此 goal turn 為 progress：DINOv3 1,215,214,176 bytes 及形狀 VAE 1,095,844,024 bytes 已實際下載，通過預期大小檢查；主模型與材質 VAE 仍在下載。原下載程序仍存活、有持續寫入，沒有重新啟動。
- 另外優先取得同一份必要 BiRefNet，exec session `60496`；它和主下載器共用 Hugging Face 的同檔案鎖與目錄，不建立第二份模型。
- `scripts/build_pixal_prompt.py` 產生三個原生 API 流程：`ginchan-inputs-api.json`（14 nodes）、`ginchan-shape-api.json`（31 nodes）、`ginchan-color-api.json`（36 nodes）。所有連線 ID 都存在，尚待實際 ComfyUI schema 驗證與 GPU 執行。
- 讀原始碼確認左右後視角均可選；左視角使用 +90°。原圖側面面向畫面左方，對應本流程左視角。模型輸入的自動裁切／遮罩只在推理流程內處理，原圖沒有覆寫。
- 官方升解析度節點最小 1024，已將 GUI 草稿從錯誤的 512 修正為 1024；首次 API 測試直接使用 512 基礎形狀、略過升解析度。原圖 SHA-256 再次核對不變。
- ComfyUI 其餘依賴安裝 worker：exec session `24778`，PowerShell PID `22552`，腳本 `scripts/finish_comfy_setup.ps1`。它等待現有 Torch installer PID `33060` 結束，檢查 GPU Torch 後才安裝 requirements。輸出寫入 `logs/comfy-dependency-setup.log`，成功後保存 `logs/comfy-environment-lock.txt`。
- 最新 Torch installer 的 WriteTransferCount 已約 1.93 GB，仍正常運作。下一輪先輪詢原 session，不重複安裝到相同環境。

## GPU 測試與模型完整性

- 最新 goal turn 分類：progress。Torch installer session `39988` 已 exit 0，完成 PyTorch `2.14.1+cu130`、torchvision `0.29.1+cu130`、torchaudio `2.11.0+cu130`。獨立 GPU 測試辨識 RTX 4070，CUDA 上 16 個 ones 相加輸出 16.0。該檢查命令的 exec session `42220` 後續因查無 8188 監聽程序而 exit 1，不是 GPU 測試失敗。
- ComfyUI 依賴 worker session `24778` 仍在安裝，輸出至 `logs/comfy-dependency-setup.log`。尚未產生環境 lock 檔，尚未啟動本機服務。
- DINOv3、形狀 VAE、材質 VAE、BiRefNet 合計約 3.704 GB 已下載且 SHA-256 與 Hugging Face 官方公開 metadata 全部相符。證據：`logs/pixal-model-expected.json`、`logs/pixal-model-integrity.json`。驗證工具為 `scripts/verify_pixal_models.py`。
- BiRefNet 優先下載 session `60496` 已 exit 0。主模型 downloader session `1378` 仍活躍、PID `33664` 仍持續寫入；最後觀察 WriteTransferCount 約 5.59 GB。主模型未完成，不可依傳輸計數當作可用模型。
- 已備好 `scripts/start_comfy.ps1`：要求環境 lock 檔存在，檢查 8188 未被占用，再以隱藏程序、127.0.0.1:8188 啟動 ComfyUI。已備好 `scripts/comfy_submit.py`，會先查實際節點列表，再提交指定 API 流程並保存 job id；尚未提交任何推理任務。
- 等待下載時已取得 MotionBricks 官方程式碼，commit `54fc563aeaaf819a753d72cc9fdc6713989cdd43`。使用 GIT_LFS_SKIP_SMUDGE；ONNX／USD 目前仍為指標檔，不可當作已下載模型。尚未安裝 MotionBricks 執行環境、未載入或使用受限範例動作。已確認可直接建立自己的 Skeleton 和姿勢輸入，後續以自建姿勢生成真正動畫。

下一步：輪詢 session 24778 與 1378。環境完成後啟動本機 ComfyUI，等待 `/object_info` 可用，以 `comfy_submit.py inputs` 檢查三面遮罩（需實際查看輸出）；主模型完成並校驗後，再提交 shape／color。不要重複啟動既有下載器或服務。

## PR-005 完成紀錄（2026-11-07）

本輪按就緒順序完成 Hunyuan3D-2mv、TRELLIS.2、StdGEN 本機試跑；StdGEN 的 canonicalize、multiview、S-LRM、refine 全部成功。三者的真實 Blender 渲染、灰模、線框、疊圖和獨立審查已保存，造型仍未通過，不修改正式角色。原圖、Blender、GLB、Godot GLB 及 Windows EXE SHA-256 均未變，PR-004 備份保留。既有 ComfyUI 已停止，無新增服務。

最新入口：`requirements/PR-005-REVIEW.html`、`requirements/PR-005-RESULTS.md`、`requirements/PR-005-INDEPENDENT-REVIEW.md`。以上較早的下載中／未完成日誌是歷史狀態，勿依舊 PID 或 session 重啟工作。下一方向建議改為授權適合的動漫底模重塑，沿用既有動畫與 Godot 管線。
