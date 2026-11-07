# PR-006｜StdGEN 銀醬的 20 組動作展示

2026-11-07。功能展示已完成；使用者已回覆「ok看到了」。外觀基準仍是 `new.png`，本輪不把可動性當作外觀精修通過。

## 開啟成果

- [Windows 展示程式](../builds/stdgen/Ginchan-StdGEN-Motions.exe)：雙擊離線執行，無須啟動 AI 模型或伺服器。
- [完整 Godot 播放影片](../renders/pr006/stdgen-motion20.mp4)：1440×960、30 FPS、3524 幀、117.47 秒，約 24.2 MB。
- [Godot 4.6.3 工程](../godot-stdgen/project.godot)：開啟後 F5。
- [Blender 可編輯模型及全部動畫](../experiments/pr006/stdgen-motion20.blend)、[GLB](../experiments/pr006/stdgen-motion20.glb)。
- [20 組完整文字 prompt](PR-006-MOTION-PROMPTS.md)、[生成條件 JSON](../motion/pr006/motion-briefs.json)。
- [獨立審查與未解決缺陷](PR-006-INDEPENDENT-REVIEW.md)、[需求與工作計劃](PR-006-STDGEN-MOTION-SHOWCASE.md)。

清單可上下捲動選擇 20 組動作；支援暫停／播放、重播、時間軸拖曳、0.25／0.5／1／1.5 倍速、重複播放、正面／45°／側面／背面。角色區拖曳旋轉、滾輪縮放，空白鍵暫停，左右方向鍵切換動作。移動片段的重播會回到起點，不是無縫遊戲 locomotion loop。

## 實際使用的模型與流程

使用 PR-005 **StdGEN 完整 refine 結果**的 body、clothing、hair 三個部件，沒有拿舊 Pixal3D 角色替代。展示副本降到 **224,390 三角面、21 骨骼**，保留原始頂點色；身體熱擴散權重、短褲權重轉移、頭髮隨頭骨，並對手臂做局部窄混合權重。上衣原本就在 body 部件上，不是獨立布料模擬。

**MotionBricks 不是直接讀文字的 text-to-motion 模型。** 這 20 組 prompt 是設計規格，腳本先把內容轉成自行設計的 SOMA 姿勢與時間條件，再由本機 MotionBricks 實際推理生成過渡動作。沒有套用官方範例動畫充數。每組原始 NPZ、姿勢條件、分段推理紀錄與 SHA-256 保留在 `motion/pr006/`。

重定向做了骨架比例／A-pose 對應、四肢方向保留而減少不適用的軸向扭轉、手掌跟隨前臂、垂直接地修正。**這不是完全未修改的原始 AI 輸出。** 小跳保留離地，不逐幀拉回地面；骨架接觸點最高約離地 0.199 公尺，已用 Godot 最高點截圖確認。詳見 [重定向紀錄](../logs/pr006-retarget.json)。

官方介面依據：[MotionBricks](https://github.com/NVlabs/MotionBricks) 與 [Runtime API](https://github.com/NVlabs/MotionBricks/blob/main/docs/runtime_api.md)。StdGEN 研究用途限制沿用 PR-005；本輪只做本機研究展示。

## 20 組實際動畫

表內秒數是 Godot 匯入的片段長度；原始樣本第一幀在 0 秒，NPZ 時間跨度會少 1/30 秒。

| ID | 動作 | 幀數 | Godot 秒數 |
| --- | --- | ---: | ---: |
| 01_wave | 揮手招呼 | 183 | 6.10 |
| 02_bow | 禮貌鞠躬 | 190 | 6.33 |
| 03_cheer | 雙手歡呼 | 215 | 7.17 |
| 04_point | 左右指示 | 199 | 6.63 |
| 05_present | 雙手展示 | 190 | 6.33 |
| 06_stretch | 上舉側伸展 | 190 | 6.33 |
| 07_dance | 左右律動舞步 | 174 | 5.80 |
| 08_walk | 前走與停步 | 157 | 5.23 |
| 09_jog | 快步小跑 | 170 | 5.67 |
| 10_backstep | 警覺後退 | 149 | 4.97 |
| 11_sidestep_left | 向左側移 | 157 | 5.23 |
| 12_sidestep_right | 向右側移 | 157 | 5.23 |
| 13_turn | 轉身觀察再回正 | 190 | 6.33 |
| 14_squat | 蹲下再站起 | 190 | 6.33 |
| 15_jump | 原地小跳 | 158 | 5.27 |
| 16_dodge_left | 向左閃避 | 157 | 5.23 |
| 17_dodge_right | 向右閃避 | 157 | 5.23 |
| 18_jab_cross | 左右直拳 | 175 | 5.83 |
| 19_front_kick | 抬膝前踢 | 183 | 6.10 |
| 20_block_counter | 防禦與反擊 | 182 | 6.07 |

## 已驗證與證據界線

- Blender QA 無 error：沒有無權重頂點、超過四個權重、零長邊、退化面或檢出的 z-fighting。頂點色材質不需要 UV，UV 檢查明確停用。仍有 9 對衣物背貼面及衣物包覆分類警告；這個結構檢查**不等於關節變形通過**。[QA](../logs/pr006-rig-qa.json)
- Godot 找到 20 個唯一動畫、21 根骨骼，逐片取樣確定有骨骼變化；檢查了暫停、速度、時間軸、視角，以及「較長片段播完後切換較短片段」的回歸案例。[執行查核](../logs/pr006-godot-verify-final.log)
- 真實 Windows 成品已實際操作：動作 7／15／20 切換、捲動選單、重複、暫停、拖曳時間軸、0.5 倍速、45° 視角。最終執行 stderr 為空；目前視窗留在小跳半速循環。
- 最終取樣共 236 張，涵蓋 20 組正面及 45° 多時點，另補跳、拳、踢的條件峰值。獨立審查實看初輪 64 張與最終 12 張；靜態證據不冒充流暢度驗收。
- Godot 連續播放全部 20 組並自行結束，MovieWriter 記錄 `STDGEN_TOUR_COMPLETE`。影片完整解碼 3524 幀，另開啟抽樣畫面確認內容。[影片紀錄](../logs/pr006-video.json)
- 本輪沒有做正式效能壓力測試；畫面 FPS 和固定 30 FPS 錄影不作為整體效能保證。

## 尚未達到的品質

這仍是粗模：臉、頭髮、衣服邊緣與 `new.png` 的差距沒有在本輪修掉。手肘外側突塊、內側擠壓與膝彎硬折仍明顯；下蹲和踢腿時短褲有壓縮／拉扯。沒有手指骨與握拳，因此「直拳」目前是**張掌示意拳路**。小跑偏像快走，側伸展幅度較小，移動的腳滑及循環接縫尚未精修。沒有表情、頭髮物理、即時攝影機追蹤或對嘴。

獨立截圖評分：姿勢辨識 6.5/10、局部變形 4.5/10、UI 可讀性 8/10。現在適合挑選動作方向，還不適合當作完成精修的直播／動畫主角。下一步最有價值的是整理肘膝拓撲、短褲權重與手指 rig，再處理臉髮造型；不是繼續大量生成更多相似動畫。

## 本輪新增容量與保留

**未下載新模型、未安裝新軟件**，沿用已就緒的本機環境。完成後 PR-006 新增內容約 **436.8 MB**；其中可單獨攜帶的 EXE 約 110.7 MB。數字包含中間 .blend、各輪檢查截圖與匯入快取，並非全都屬於最終播放所需。

| 項目 | 邏輯大小 |
| --- | ---: |
| 20 組原始動作與條件 | 1.8 MB |
| Blender、GLB 與重定向副本 | 63.2 MB |
| 獨立 Godot 工程及匯入快取 | 15.2 MB |
| Windows 展示程式 | 110.7 MB |
| 各輪取樣截圖及完整影片 | 245.8 MB |
| 腳本、文件與紀錄 | 0.1 MB |

量測時 C 槽約剩 **133.9 GiB**。比對 PR-004 保存清單與 PR-005 三份 StdGEN GLB，共 **63 檔雜湊一致**，原圖、舊正式 Godot／EXE 與備份保留。[容量與雜湊報告](../logs/pr006-storage-and-preservation.json)

## 重製入口

依序使用 `design_pr006_motions.py` → Motion 環境執行 `generate_pr006_motions.py` → Blender 執行 `build_stdgen_showcase_rig.py`、`retarget_pr006_motions.py` → `prepare_pr006_godot.py` → Godot 匯入及匯出。這些腳本會更新 PR-006 對應產物，手改工程前應保留自己的副本。

Godot 的 `--verify`、`--capture-dir=...`、`--tour` 是本地查核入口；全部離線，不需常駐服務。所有路徑相對 `[PROJECT]`。
