# PR-006｜StdGEN 銀醬與 20 組 MotionBricks 動作展示

日期：2026-11-07。狀態：功能展示完成，粗模造型與關節品質限制保留。使用者要求實際看 StdGEN 角色在 Godot 動起來，並設計、生成及展示 20 組動作；風格已選「日常、移動、輕度戰鬥混合」。使用者已回覆「ok看到了」，不把這視為造型或動作品質全面通過。

## 需求與界線

- 使用 PR-005 StdGEN 完整分層 refine 的身體、衣服、頭髮，不以舊 Pixal3D 模型代替。
- 20 組具名稱、文字 prompt、姿勢條件、實際 MotionBricks 輸出的動畫，目標每組約 4–8 秒；保留原始輸出及後處理紀錄。
- MotionBricks 本機版本是姿勢條件生成器，不是文字生成器。文字 prompt 是動作設計規格，由腳本轉為自建 SOMA 目標姿勢與節奏；不宣稱模型直接理解這些文字。
- 在獨立 Godot 專案提供 20 動作選單、播放／暫停、重播、進度拖曳、播放速度及視角控制，顯示當前動作描述。
- 本機研究展示。StdGEN 權重的研究用途限制沿用；不發布、不訂閱、不建伺服器。
- 保留 PR-003 可動版與 PR-005 原始 GLB。只在新候選副本做減面、骨架與權重；本輪不重塑臉髮、不新增表情、手指 rig 或頭髮物理。

## 已查核

StdGEN 有 3 個部件、1,233,108 三角面、頂點色，沒有骨架、UV 或可用眼口拓撲。現有 MotionBricks 獨立環境、SOMA ONNX 與 TensorRT 快取已可用。Godot 4.6.3 與 Windows 匯出模板已存在；沿用版本及匯出 preset。官方模型每次由 4 個歷史姿勢與 1–4 個目標姿勢生成一段動作，長動作需串接。

## 工作計劃

執行者 Codex；依序完成，每個目標需有實際證據才標記完成。

| # | 工作 | 狀態 | 驗收證據 |
| --- | --- | --- | --- |
| 1 | 20 組動作規格與可重現條件 | 完成 | motion/pr006/motion-briefs.json；PR-006-MOTION-PROMPTS.md |
| 2 | StdGEN 展示用網格與骨架 | 展示版完成；精修未通過 | experiments/pr006/stdgen-rig.blend；QA 無 errors，但肘膝仍有明顯變形缺陷 |
| 3 | MotionBricks 生成 20 組動畫並重定向 | 完成 | 20 個原始 NPZ／JSON、20 個烘焙 action；logs/pr006-retarget.json |
| 4 | Godot 展示與 Windows 可執行版 | 完成 | 獨立 godot-stdgen；Windows 實際操作，最終 stderr 無錯誤 |
| 5 | 動態視覺驗證與交付 | 完成並記錄限制 | 最終 236 張取樣、獨立審查、117.47 秒 Godot 實錄；見 PR-006-RESULTS.md |

## 首輪動作配置

日常互動 7 組：揮手、鞠躬、雙手歡呼、左右指示、雙手向前展示、伸展、左右舞步。
移動 7 組：前走停止、快步小跑、後退、左側移、右側移、轉身再回正、蹲下起身。
輕度戰鬥／敏捷 6 組：小跳、左閃避、右閃避、左右直拳、前踢、防禦後反擊。

## 驗證原則

生成成功、匯入成功、畫面在動、動作符合描述分別查核。保留原始模型輸出；任何接地、關節限制、平滑或骨架修正均要記錄。跳躍動作不得使用逐幀強制接地。發現不符描述或明顯穿插的動作，修正控制條件或重定向並重測，不拿換名字的同一段動畫湊數。

交付入口：[結果與操作方式](PR-006-RESULTS.md)、[獨立視覺審查](PR-006-INDEPENDENT-REVIEW.md)。下一輪若改善品質，優先處理肘膝變形拓撲、短褲權重與手指骨架；這些未在本輪宣稱完成。

## 來源

- [MotionBricks 官方](https://github.com/NVlabs/MotionBricks)
- [MotionBricks Runtime API](https://github.com/NVlabs/MotionBricks/blob/main/docs/runtime_api.md)
- [PR-005 結果及限制](PR-005-RESULTS.md)
