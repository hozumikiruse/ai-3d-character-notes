# PR-005｜銀醬本機生模實測

2026-11-07。按「哪個先下載完成就先試」執行。實際順序為 Hunyuan3D-2mv → TRELLIS.2 → StdGEN。目標仍是 new.png 的銀醬，沒有改成較容易達成的通用角色。

開啟 [互動對照頁](PR-005-REVIEW.html)，可選候選、正／側／45°／背面、生成色彩／灰模／線框，以及疊圖透明度。圖像是實際網格的 Blender 渲染。原圖沒有 45°，該角度只作結構檢查。

## 目前結論

三個候選都已在本機成功產出真正的 3D 網格。**StdGEN 的有色正面最有角色辨識度，但灰模揭示眼、口和鼻部幾何仍很弱；目前仍是粗模。** Hunyuan 與 TRELLIS 可提供髮型與體積參考，也都未通過造型門檻。這輪沒有找到只修幾個洞即可完成的角色。

正式 Blender、GLB、Godot 和 Windows 展示版沒有被候選覆蓋。這是候選研究，不是宣布角色精修完成；骨架、表情、重定向、遊戲效能及使用者外觀驗收均不能由生模成功取代。

## 實驗條件

- Windows、RTX 4070 12,282 MiB、32 GB RAM、Blender 5.2.3。
- Hunyuan：頭部正、側、背三視圖；shape-only，384 體素解析度、30 步、CFG 7.5、種子 42／123。沒有安裝 Paint，不以缺少眼睛色彩評其幾何失敗。
- TRELLIS.2：ComfyUI INT8，1024 生成管線；頭部種子 42／123，另作全身種子 42 對照。完整身體對照只改輸入範圍；不把失敗的頭部裁切概括為模型必然無效。
- StdGEN：完整人物正面 RGBA → A-pose → 6 視角及語意層 → S-LRM → refine。使用獨立 Python 3.10／Torch 2.1 CUDA 11.8 環境，與現有 ComfyUI 環境隔離。
- 模型推理與 Blender GPU 渲染依序執行。耗時是暖機、載入／推理／輸出範圍內的單次觀測，不含前面安裝下載；不同工具的顯存統計口徑不同，不可當成精準橫向效能排名。

## Hunyuan：生成器與網格轉換分開判斷

| 試樣 | 三角面 | 生成／輸出耗時 | 結果 |
| --- | ---: | ---: | --- |
| 原生 Surface Net，42 | 2,532,014 | 142.5 秒 | 大片條紋，468,276 非流形邊、584,457 面朝向不一致邊 |
| Lewiner Marching Cubes，42 | 2,091,602 | 143.8 秒 | 條紋消失；0 非流形、0 邊界、0 面朝向不一致邊；造型仍不合格 |
| Lewiner Marching Cubes，123 | 1,719,588 | 91.3 秒 | 耳區局部改善，但瀏海、眼周、嘴及側髮問題沒有突破 |

MC 對照保留相同 seed、VAE、384 解析度與 **0.6 閾值**，只替換轉網格演算法，沒有雕刻或平滑。解析球體探針確認封閉性與外向表面，體積 0.522346，解析值約 0.523599。Hunyuan 官方也提供 [Marching Cubes 表面提取](https://github.com/Tencent-Hunyuan/Hunyuan3D-2/blob/main/hy3dgen/shapegen/models/autoencoders/surface_extractors.py)。

兩份 MC 模型皆仍有許多獨立小島、近重合點及小退化面；髮束的穿透空隙可由封閉表面圍成，不能因邊界為 0 就說沒有洞。超過 300k 三角面的部分相交檢查被跳過。工具的 render profile `pass: true` **不是正式遊戲資產驗收**。

獨立審查兩個 MC 試樣皆約 **4／10**（主觀造型評分，不是相似度百分比）。主要問題：瀏海厚且遮眼、眼瞼模糊、嘴偏厚、耳部混入頭髮，以及髮束穿孔與黏連。詳見 [獨立審查](PR-005-INDEPENDENT-REVIEW.md)。

## TRELLIS.2：頭部裁切失敗，全身輸入形成體積

| 試樣 | 三角面 | 耗時 | 結果 |
| --- | ---: | ---: | --- |
| 頭部 42，pad 1.1 | 2,625,342 | 60.7 秒 | 矩形薄片，深度僅寬度約 0.0072% |
| 頭部 123，pad 1.0 | 4,194,244 | 40.5 秒 | 仍為薄片；早期結構輸出已是平面 |
| 全身 42，pad 1.0 | 2,451,464 | 40.8 秒 | 有真正頭身體積，但表面與造型仍有問題 |

已核對安裝版本的官方 ComfyUI 模板，並查看側面、灰模、包圍盒及早期形狀輸出。不是只看正面貼圖便宣告成功。全身對照證明本機管線能產出立體；本次頭部輸入未成功，不代表其他輸入也必定失敗。

保留原始 GLB，另做僅統一面朝向的 Blender 副本。黑條紋大幅減少；不一致面朝向由 677,490 降至 104,081 條邊，但 66,815 非流形邊、64,643 邊界邊未因此消失。頂點位置、三角面數與包圍盒未作造型修改。黑點、裂線與缺口仍在；不能把副本稱為已修好的角色。

獨立審查全身頭部約 **4.5／10**。髮型與整體比例有參考價值，但下巴尖、下頜硬、眼睛成為暗灰凹區，側面鼻唇不足，側髮偏厚，頭髮也偏粉。原始與面朝向對照共用造型評分，不能因表面改善而宣稱幾何已重塑。

## StdGEN 階段紀錄

四個階段全部成功，沒有降解析度。A-pose 原始 1024 輸出保存在 `experiments/pr005/stdgen/apose/ginchan.png`；這是生成流程中間圖，**不是 3D 模型渲染**。

| 階段 | 成功執行耗時 | CUDA allocated 峰值 | CUDA reserved 峰值 |
| --- | ---: | ---: | ---: |
| A-pose 標準化 | 109.92 秒 | 7.154 GiB | 8.438 GiB |
| 多視圖，1024×576、6 視角、3 層 | 236.68 秒 | 5.557 GiB | 7.119 GiB |
| S-LRM 網格重建 | 63.59 秒 | 14.565 GiB | 16.746 GiB |
| 分層 refine | 81.81 秒 | 5.780 GiB | 7.334 GiB |

成功路徑合計約 **8.2 分鐘**，不含下載、安裝、相容性修復及失敗重試。**S-LRM 計數超過顯示卡 12 GB 專用容量**：本機確實完成，但不能宣稱全流程都完全駐留專用顯存。Windows 可能涉及共享記憶體／分頁；本輪未同步量測其占用，不能把 Torch 計數當成實體 VRAM 讀值。

最終 `refine/ginchan/out_2.glb`、`out_1.glb`、`out_0.glb` 分別為身體、服裝、頭髮，合計 **1,233,108 三角面**。同場渲染及審閱工程在 [StdGEN review.blend](../renders/pr005/stdgen-refined/review.blend)，三個 GLB 未被額外雕刻。

有色正面比前兩個候選更容易辨認銀醬；灰模則幾乎沒有可用的眼瞼與嘴部結構，側面鼻唇很弱。頭髮變成較厚的整片外殼，外翹與細髮束沒有忠實保留，長髮束缺口明顯。全身亦可見短褲腰邊破損、手腳粗糙。這不是可直接加入眨眼對嘴的完成角色。

QA：身體 792,792 面、服裝 166,252 面、頭髮 274,064 面；共 1,158 邊界邊、4 非流形邊、4 面朝向不一致邊，沒有 UV。部分相交檢查因面數上限跳過。保留這些警告以呈現生成器真實結果，沒有為取得驗收通過而修改原始試樣。

獨立審查給頭部造型 **4.0／10**：比 Hunyuan 表面連續、比 TRELLIS 彩色臉部容易閱讀，但髮束層次更弱，沒有整體造型突破。這是三維造型主觀評分，不能誤讀為有色正面的相似度百分比；詳見 [獨立審查](PR-005-INDEPENDENT-REVIEW.md)。

Windows 相容補丁與環境版本已保存在 `logs/stdgen-windows.patch`、`logs/stdgen-environment-lock.txt`。實際 GPU 探針已驗證 xformers、PyTorch3D、torch-scatter 及 nvdiffrast。只跳過 S-LRM 的可選 120 幀影片，不跳過模型輸出。

補丁包含 Windows 路徑、CPU offload 的 normalizer 裝置處理、PyMeshLab `PercentageValue` 及 Trimesh 清面 API 相容；失敗紀錄另存，沒有以成功紀錄覆蓋失敗證據。主權重依來源 SHA-256 驗證；canonical 權重另作完整重查。SAM 使用官方下載連結，記錄本機 SHA-256，但未將本機雜湊冒稱為發布者校驗值。

## 實際磁碟空間與保留驗證

GB 使用十進位；GiB 為 Windows 常見的二進位容量。以下是檔案邏輯大小，快取與環境可能有硬連結，不宜與 C 槽差額直接相加。

| 本輪項目 | 邏輯大小 |
| --- | ---: |
| StdGEN 程式、主要權重、SAM、DINO | 19.80 GB |
| StdGEN 獨立 Python／GPU 環境 | 6.53 GB |
| 局部 CUDA 工具、編譯快取與下載包 | 0.28 GB |
| TRELLIS.2 新增主權重 | 5.25 GB |
| Hunyuan3D-2mv 主權重 | 4.93 GB |
| 試樣、渲染及審閱工程 | 約 0.98 GB |
| **以上合計** | **約 37.8 GB** |

本輪開始至結束，**C 槽可用空間淨減約 48.9 GB，目前約剩 133.3 GiB**；差額包含全域套件快取、Python 及同時期其他程式寫入，並非精準專案實體占用。未下載 Hunyuan Paint、TripoSG 或其他大型生成器。既有 ComfyUI 在試跑後停止，沒有新增服務。

SHA-256 核對 `new.png`、正式 Blender、匯出 GLB、Godot GLB 及 Windows EXE **全部未變**。PR-004 備份仍保留。

## 授權與後續用途

- [StdGEN 官方 README](https://github.com/hyz317/StdGEN) 指定 checkpoints 限研究用途。本輪是研究試樣，不自動把它升為直播或遊戲發布資產。
- [TRELLIS.2 程式授權](https://github.com/microsoft/TRELLIS.2/blob/main/LICENSE) 是 MIT；量化檔、編碼器及其他依賴仍需分別看其來源條款，不能把整條鏈一概稱為 MIT。
- [Hunyuan3D-2 Community License](https://github.com/Tencent-Hunyuan/Hunyuan3D-2/blob/main/LICENSE) 有地域、規模等條件；條文亦涉及輸出的展示／分發。不能直接視為全球直播與發布皆無限制。此輪不據此發布資產。

本輪不下載更多生成器，不以無限換種子取代造型工作。若研究試樣仍未達到可精修底稿的程度，下一方向是使用授權合適、拓撲完整的動漫底模，在 Blender 重做銀醬的臉和髮型；既有 Godot、動畫及展示管線可以沿用，不需要整個專案重做。

## 證據入口

- 生成工作流：`workflows/pr005-*.json`；原始歷史：`logs/pr005-*-history.json`。
- 轉換探針：`logs/pr005-marching-cubes-probe.json`。
- 真實渲染、Blender 審閱工程與 QA：`renders/pr005/`。
- 試驗原始輸出：`runtime/ComfyUI/output/pr005/`、`experiments/pr005/stdgen/`。
- 實際容量與原圖／正式資產 SHA-256 保留檢查：`logs/pr005-storage-and-preservation.json`。
- 對照頁已實際在 Chrome 開啟，檢查候選、角度、材質及疊圖控制；畫面證據：`output/playwright/pr005-*.png`。
