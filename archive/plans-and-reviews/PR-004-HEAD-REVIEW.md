# PR-004｜臉與頭髮 v2 本輪紀錄

2026-11-07。狀態：已安裝技能並實作頭臉／髮束重建候選；**尚未通過原圖還原驗收，不能稱精修完成**。衣服與正式 Godot 模型仍使用 v1。

- [原圖／v1／v2／疊圖互動頁](PR-004-HEAD-REVIEW.html)
- [v2 Blender 工作檔](../blender/ginchan-refined-v2.blend)
- [v2 GLB](../exports/ginchan-refined-v2.glb)
- [正面](../renders/refined-v2/v2-front.png)、[側面](../renders/refined-v2/v2-side.png)、[45°](../renders/refined-v2/v2-quarter.png)、[全身長髮](../renders/refined-v2/v2-front-full.png)、[線框檢查](../renders/refined-v2/v2-wire-front.png)

## 備份與保留範圍

執行前備份至 `backups/pr004-before-20261107-102521/`，包含 Blender、GLB、Godot 專案、Windows 成品、製作腳本及原圖。清單與 SHA-256 見 `logs/pr004-backup.json`。

已逐項核對：原始 `new.png`、`blender/ginchan-character.blend`、`exports/ginchan.glb`、`godot/assets/ginchan.glb`、`builds/windows/Ginchan.exe` 與備份雜湊一致。本輪僅另存 v2，正式可動版未替換。

## 技能安裝

使用系統 skill-installer 安裝至 `[USER]/.codex/skills/`；下一輪對話可自動發現。本輪已直接讀取需要的技能並執行環境及 QA 腳本。

- EL4CTEO/blender-skills 固定版本 `fb92cfb4cc5a238c22113a6a230b09f535bd0656`：`blender-core`、`blender-retopology-uv`、`blender-facial-shapekeys`、`blender-clothing`、`blender-hair-grooming`、`blender-stylized-npr`、`blender-asset-qa`。
- oh-ashen-one/blender-agent-skills 固定版本 `e45ebb2b4ef29eace83572f083daff94c10215a1`：`concept-target-visual-gate`。
- 未安裝 Blender MCP、blendsmith、新生成模型或收費服務。

## 實際改動

1. 以原圖三面作參考，替換舊頭部和破碎髮面，保留身體與原有骨架／動作資料。
2. 建立帶眼部開口、連續眼皮環線的頭部網格；重做眼白、藍灰虹膜、眉、睫毛、小嘴與耳朵。這是中立臉，沒有把舊表情 delta 套到不同拓撲上。
3. 髮型分為底層、瀏海、側髮、後髮、外層與兩側長髮，各束可單獨編輯。修過根部覆蓋、側後交疊、尖端及長髮末端分散。
4. 頭頸切口重建並焊接；修復中途版本出現的黑色斷口。新增部件清理重疊面、重新展 UV；色彩以材質及頂點顏色完成。
5. 原圖與正式 v1 未改，沒有用 AI 生成的漂亮示意圖冒充模型渲染。

## 查驗結果及限制

- Blender 5.2.3 環境腳本成功，RTX 4070 可見。記錄 `logs/pr004-blender-environment.log`。
- 新 GLB 已重新匯入 Blender，53 個網格物件、113,512 三角面、21 根骨骼；保留 BasicArmTest、JointCheck、MotionBricksWalkStop。`logs/pr004-delivery-verification.json`。
- `blender-asset-qa` 以 render profile、epsilon 0.05 mm、接近容差 20 mm 檢查頭髮分層與眼部組件，結果 `pass: true`、無 error；這不是遊戲最終驗收。仍有 2 個 warning：合併後身體／頭部物件有 2,185 個頂點超過 4 骨影響、4 個近重複頂點。前者源於保留的身體權重，本輪未重新限制以免改動全身變形；後者記錄為後續拓撲清理項目。原始報告 `logs/pr004-skill-qa.json`。
- 已實際查看正、側、45°、全身及線框；HTML 以真實渲染與未修改原圖顯示。原圖沒有 45°，該方向只比較 v1／v2 同相機，不能宣稱與不存在的 45° 原圖疊合。
- 新頭臉還沒有眨眼／張嘴形變；Godot 回歸與新權重的動作壓力測試在後續階段，不把本輪中立造型版稱為替代原版的可用直播模型。

## 視覺審查

依 concept-target-visual-gate，另外交給沒有參與建模的審查代理看原圖與實際截圖。前兩輪結論都是「表面和分層改善，但未通過原圖還原」；這促成了髮根覆蓋、後髮交疊、頭頸及虹膜的後續修正。

即使最新技術檢查通過，仍保留以下未解項：

- 主髮束偏規則、偏厚，與原圖柔薄且自然分流的髮型仍有差距。
- 側臉、下顎到頸部及眉眼神態仍需更精確的造型判斷。
- 長髮仍有帶狀感；衣服保留原版偏色，未在本輪處理。

最終獨立審查已重開最新正／側／45°／全身圖片：確認頭頸斷口和水平接痕消失、長髮尾端分出不同長度；仍指出厚葉片式髮束、側後髮硬折，以及較圓／警醒的娃娃眼。判定「足以展示本輪改善，不能稱精修完成」。

下一個小目標仍是頭臉與髮型，而不是跳到衣服或增加表情數量。先依本輪可見差異修主髮束連續性、側面下顎及眉眼，再重做表情與整體動畫回歸。
