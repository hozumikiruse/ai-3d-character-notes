# PR-009｜轉向 2D 前的專案清理清單

日期：2026-11-08。狀態：**清單已整理，尚未刪除；等待使用者看過清單後指示。**

## 需求與已確認範圍

使用者要求：保留 Ginchan 設定及 Godot／Blender，其餘專案內容可清除，正式刪除前先列清單。後續已確認：**只保留軟體本身，舊 Godot 與 Blender 3D 工程也列入刪除。**

清理範圍僅為 `[PROJECT]`。不刪根資料夾；不處理 `VE_pic`、專案外軟體、全域快取、共用 Python、Codex skills 或 GitHub 遠端倉庫。下一步準備 2D，但這份 PR 不選工具、不安裝套件、不建立伺服器。

本清單取代前次「保留 MotionBricks、Uka 與 3D 工作檔」的建議；使用者現在已選擇把它們也列入清理。

## 保留清單

| 路徑／項目 | 保留內容 |
|---|---|
| `new.png` | 銀醬原始三視圖，原檔與雜湊不變 |
| `references/` | 四張原有去背／正／側／背參考圖 |
| `GINCHAN.md` | 從既有紀錄摘錄的角色名、署名、造型及原圖入口；沒有新增人設 |
| `README.md` | 目前不修改歷史內容；正式清理時改為角色設定與 2D 準備入口，移除失效的舊 3D 連結 |
| `requirements/PR-009-PROJECT-CLEANUP.md` | 本清單與執行邊界 |
| `reports/cleanup-2026-11-08/` | 本次明確刪除範圍及後續執行紀錄 |
| Godot／Blender 軟體 | 都在專案外，程式、偏好設定與匯出模板不動 |
| 已公開的 GitHub 總結 | 遠端倉庫與 Pages 不動；本機發布副本列刪除，可日後重新 clone |

保留的原始圖與工作參考合計約 3.62 MB，另加少量文字與清理紀錄。

## 刪除候選

以下容量為 2026-11-08 的檔案邏輯大小，不是保證釋放的實體空間。原始範圍約 72.78 GB；保留上述角色圖文後，候選約 **72.78 GB**（四捨五入）。

| 項目 | 路徑 | 約 GB |
|---|---|---:|
| 所有本機 AI 模型、推理環境、3D 工具來源副本 | `runtime/` | 57.838 |
| 安裝包、專案 uv 快取、檢查瀏覽器資料 | `setup/` | 10.282 |
| 全部舊 Godot 工程，含貼圖、模型、動畫及匯入快取 | `godot/`、`godot-stdgen/`、`godot-mmd/`、`godot-motion-playground/`、`godot-ginchan-mmd/`、`godot-ginchan-v17/`、`godot-ginchan-v23/` | 1.211 |
| Blender 首版、v2 與 GLB 匯出 | `blender/`、`exports/` | 0.060 |
| StdGEN、Uka、銀醬 v1–v24 等 3D 實驗候選 | `experiments/` | 0.830 |
| 渲染、影片、對照圖及其中的 review.blend | `renders/` | 1.209 |
| 全部 Windows 3D 展示成品 | `builds/` | 0.653 |
| 舊備份 | `backups/` | 0.268 |
| Uka、BOOTH 等第三方來源素材 | `assets/` | 0.213 |
| Charakuru 發行包 | `charakuru/` | 0.017 |
| PR-003～PR-008 舊計劃、研究、比較 HTML | `requirements/` 既有檔案，排除 PR-009 | 0.171 |
| 舊容量報告、公開文章本機副本及 Git 物件、文章模板 | `reports/` 既有三項，排除本次 cleanup 資料夾 | 0.016 |
| 原始動作、製作腳本、工作流、日誌與測試輸出 | `motion/`、`scripts/`、`workflows/`、`logs/`、`output/`、`.playwright-cli/` | 0.010 |

`runtime/` 明確包括 ComfyUI／Pixal3D／TRELLIS.2／Hunyuan3D-2mv、StdGEN、MotionBricks、三套 Python 虛擬環境、TensorRT 編譯快取、局部 CUDA、MMD Tools／MPFB 原始碼副本及輔助套件。刪除後不能直接在本機重新生成這些 3D 模型或動作。Blender 與 Godot 軟體本體仍可啟動。

`backups/` 雖是先前要求保留的備份，依本次「舊 3D 工程也列入刪除」的新指示，現在明確列在待確認清單中；沒有在看清單前刪掉。

## 不在本次範圍

- Blender：`[USER]/AppData/Local/Programs/Blender Foundation/Blender 5.2`。
- Godot：`[SHARED_TOOLS]/Godot`；使用者 Godot 匯出模板與設定。
- 已安裝的 VRoid Studio 位於專案外，本次不解除安裝；`setup/` 內的 VRoid 安裝包會列刪除。
- 使用者 uv／pip／Hugging Face 共用快取、全域 Python、Codex skills 與其他專案。
- GitHub 公開文章：[ai-3d-character-notes](https://alan52013141314.github.io/ai-3d-character-notes/)。

## 執行計劃

1. **本輪只做清單。** 建立明確路徑清單、容量與保留檔雜湊，等待使用者看過後的指示。
2. 收到執行指示後，重新檢查候選是否改動、是否有程序正在使用，以及所有解析後的目標均位於 `VTProject` 下。新出現的檔案不自動加入。
3. 再核對五張角色圖的 SHA-256 與 `GINCHAN.md`；不把使用者原圖視為可再生成的快取。
4. 執行方式預設移到資源回收筒，不自動清空；若使用者另行要求永久刪除，才改用該方式。移到回收筒未必立刻增加可用空間，容量／硬連結也可能使實際釋放量低於邏輯大小。
5. 只處理清單中的路徑；`requirements/` 與 `reports/` 必須逐項處理，不能刪掉含有保留檔的整個上層資料夾。不刪專案根目錄。
6. 清理後核對角色圖文仍可讀、程式本體仍存在，重寫 README 的舊入口，留下實際刪除項目及容量紀錄。沒有授權時不開始 2D 製作。

## 本輪驗收

- 已確認軟體與工程的保留界線。
- 已查看原有銀醬設定並另存既有事實，沒有新增角色設計。
- 已列出所有候選，包含 MotionBricks、備份及本機公開文章副本。
- **沒有刪除、移動或解除安裝任何項目。**

明確範圍與容量：[清單資料夾](../reports/cleanup-2026-11-08/)。下一步只等待使用者看過本清單後的指示。
