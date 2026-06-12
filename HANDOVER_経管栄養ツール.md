# HANDOVER — 経管栄養プラン作成ツール

> **対応プロジェクト**：経管栄養ツール（TPN/PPNツールは別ファイル `HANDOVER_TPNツール.md` を参照）
> **最終更新**：2026年5月14日

---

## ツールファイル

| ファイル名 | 説明 | 保存場所 |
|---|---|---|
| `enteral_nutrition_planner.html` | 経管栄養プラン作成ツール（旧版・変更なし） | iCloud Drive / 経管栄養・TPNツール / |
| `enteral_nutrition_planner_v2.html` | 改良版（本メモの対象・約1999行） | iCloud Drive / 経管栄養・TPNツール / |

ブラウザで直接開いて使用（インターネット接続不要）。

---

## 新しいチャットへの依頼テンプレート

```
HANDOVER_経管栄養ツール.mdを読んでenteral_nutrition_planner_v2.htmlを改善しようと思います。
まずは確認をしてください。次に改善点を相談させていただきます。
```

---

## ツール概要

- **対象**：成人（ICU/CCU・一般病棟）
- **使用者**：医師（自分のみ）
- **形式**：単一 HTML ファイル、日本語 UI、オフライン動作
- **UI構成**：2ペインレイアウト（左320px固定入力パネル + 右タブ式結果パネル）
- **カラースキーム**：グリーン（--green-dark: #276749、--green-mid: #38a169）

---

## タブ構成（右パネル）

| タブ | 内容 |
|---|---|
| 📊 栄養目標 | カロリー・タンパク質目標、NPC/N比、根拠表示 |
| 💊 製剤・投与 | プランA/B推奨カード + 全13製剤グリッド（手動選択対応） |
| 📋 ステップアップ | Day別カードタイムライン + 耐性不良プロトコル |
| 💧 水分バランス | 必要水分量・内訳・追加補水推奨 |
| 🔬 モニタリング | 病態別モニタリング項目 |
| 📄 投与指示 | 患者情報・栄養目標・投与指示・水分管理まとめ（印刷用途） |

---

## 左パネル入力項目

- 患者基本情報：年齢・性別・身長・体重（BMI・標準体重・計算用体重を自動算出）
- **病棟切替タブ**：ICU/CCU ⇔ 一般病棟
  - ICU：入室日数・循環動態・人工呼吸器・RRT・合併疾患チェックボックス
  - 一般病棟：侵襲度・活動レベル・合併疾患チェックボックス
- NRS-2002スコア（数値入力、≥3で高栄養リスクフラグ）
- 投与経路・投与方法（持続 / 間欠 / 半固形）
- 投与時間（持続）or 投与回数（間欠）
- 目標達成率（%）
- チューブ洗浄水（ml/日）
- その他水分（ml/日）
- 水分制限量（ml/日）（任意）

---

## 院内採用製剤（13製剤・令和5年7月改訂）

| 製剤名 | kcal/100ml | 主な適応 |
|---|---|---|
| メイバランス 1.0 | 100 | 一般（標準） |
| ラクフィア | 100 | 下痢（乳酸菌配合） |
| メイバランス 1.5 | 150 | 高エネルギー・高タンパク |
| アイソカルS(1.5) | 150 | 糖尿病・下痢（PHGG配合） |
| メイバランス 2.0 | 200 | 高エネルギー・水分制限 |
| インスロー | 100 | 糖尿病 |
| リーナレン 1.0(LP) | 160 | 腎臓病（低タンパク） |
| リーナレン 3.5(MP) | 160 | 腎臓病（中タンパク） |
| ペプチーノ | 100 | 無脂肪・消化態 |
| ペプタメンS | 150 | 消化態・下痢 |
| プルモケア | 150 | COPD |
| メディエフ 1P(120g) | 300/パック | 半固形（PEG用） |
| F2ライト 1P(400g) | 300/パック | とろみ栄養食（PEG用） |

---

## 製剤選択の優先順位（selectFormula / selectSecondaryFormula）

1. 消化管機能不全 → ペプチーノ / ペプタメンS
2. CKD（非透析） → リーナレン LP / MP
3. COPD → プルモケア
4. 心不全（水分制限） → メイバランス 2.0
5. 糖尿病＋下痢 → アイソカルS(1.5)
6. 糖尿病 → インスロー
7. 下痢 → ラクフィア
8. 透析 / CRRT → メイバランス 1.5
9. 標準 → メイバランス 1.0

プランBは上記順位でプランAと異なる製剤を自動選択し、理由テキストを付与。

---

## カロリー・タンパク質計算の根拠

### カロリー（体重ベース）

| 病態 | 目標 |
|---|---|
| ICU ショック（循環動態不安定） | 10 kcal/kg（trophic feeding） |
| ICU 急性期早期（Day 1–2） | 20 kcal/kg |
| ICU 回復期（Day 3〜） | 25 kcal/kg |
| 一般病棟（安定） | 25 kcal/kg |
| 一般病棟（中等度侵襲） | 28 kcal/kg |
| 一般病棟（高度侵襲） | 30 kcal/kg |

一般病棟は活動レベル補正あり（床上安静 +0、低活動 +1.5、中活動 +3 kcal/kg）。

### タンパク質（体重ベース）

| 病態 | 目標 |
|---|---|
| ICU 標準 | 1.5 g/kg |
| ICU ショック | 0.8 g/kg |
| CKD（非透析） | 0.6–0.8 g/kg |
| 透析 / CRRT | 1.8 g/kg |
| 一般病棟 標準 | 1.0–1.5 g/kg |

> BMI ≥ 30 の場合は補正体重（IBW + 0.25 × (ABW − IBW)）を使用

---

## コード構造（主要関数）

| 関数 | 行の目安 | 役割 |
|---|---|---|
| `calculate()` | ~1126 | メイン計算・全タブ更新 |
| `selectFormula()` | ~929 | プランA製剤選択 |
| `selectSecondaryFormula()` | ~995 | プランB製剤選択 |
| `computeFormulaStats()` | ~1056 | 任意製剤の栄養・速度計算 |
| `pickFormula(name)` | ~1106 | 手動製剤選択・selectedFormula設定 |
| `resetFormulaSelection()` | ~1118 | 手動選択解除 |
| `renderStats()` | ~1293 | ヘッダー統計バッジ更新 |
| `renderTargets()` | ~1303 | 栄養目標タブ描画 |
| `renderFormula()` | ~1357 | 製剤・投与タブ描画（プランA/B＋全製剤グリッド） |
| `renderStepUp()` | ~1502 | ステップアップ・タイムライン描画 |
| `buildToleranceSection()` | ~1687 | 耐性不良プロトコルHTML生成 |
| `computeFluidBalance()` | ~1730 | 水分バランス計算ヘルパー（renderFluid/renderOrderSheet共用） |
| `renderFluid()` | ~1763 | 水分バランスタブ描画 |
| `renderMonitor()` | ~1802 | モニタリングタブ描画 |
| `renderOrderSheet()` | ~1831 | 投与指示タブ描画（水分管理セクション含む） |
| `renderComorbidityGrid()` | ~859 | 合併疾患チェックボックスグリッド描画 |
| `switchWard()` | ~887 | ICU/一般病棟切替 |
| `updateMethodUI()` | ~900 | 投与方法UIの表示切替 |
| `clearResults()` | ~1958 | 結果クリア |
| `resetForm()` | ~1971 | フォーム全リセット |

### 主要グローバル変数

```javascript
let currentWard = 'icu';     // 'icu' | 'general'
let R = null;                // 計算結果オブジェクト
let selectedFormula = null;  // null=自動, string=手動選択製剤名
```

### R オブジェクトの主なプロパティ

```javascript
R = {
  calcBW, bmi, ibw, bwType, height, weight, age, sex,
  currentWard, icuDay, hemodynamics, rrt,
  stress, activity, cond, method, route, pct, hours, times,
  fluidLimit, flushMl, otherFluid,
  kcalPerKg, targetKcal, kcalNote,
  protPerKg, targetProt, protNote,
  fName, f, glText, isManual,
  planAName, planAGl, planAStats,
  planBName, planBReason, planBStats,
  volPerDay, packs,
  providedKcal, providedProt, providedSalt, providedK, providedWater,
  dailyCost, rateStr, volStr, rateLabelStr, volLabelStr,
  nrsHigh, nrsVal
}
```

### computeFluidBalance() の返り値

```javascript
{
  mlPerKg,      // 使用した ml/kg 値
  basis,        // 根拠テキスト（年齢・制限理由）
  calcRequired, // 計算上の必要水分量 ml
  required,     // 実際に使う目標水分量（fluidLimit優先）
  flushRound,   // チューブ洗浄水（10ml単位丸め）
  totalIn,      // 合計摂取水分量 ml
  additional,   // 追加補水が必要な量 ml（不足時）
  excess,       // 超過量 ml
  totalPct,     // 充足率 %（上限100%）
  overLimit,    // 水分制限超過フラグ boolean
  barCls,       // プログレスバーのCSSクラス
  addNote       // 補足テキスト
}
```

---

## 参照ガイドライン

- JCCNG2024（日本版重症患者の栄養療法ガイドライン 2024）
- JSPEN 静脈経腸栄養ガイドライン第 3 版
- 院内濃厚流動食栄養成分表（令和 5 年 7 月 1 日改訂）

---

## 変更履歴

### 2026-05-10（初回v2作成）

1. TPN/PPNツールのUI設計を参考に全面リニューアル
   - 縦スクロール単一カラム → 2ペインレイアウト（左固定パネル + 右タブ）
   - ICU/一般病棟タブ切替対応
2. 活動レベルをカロリー計算に組み込み（旧版バグ修正）
3. 一般病棟にもリフィーディングリスクチェックボックス追加
4. NRS-2002スコア入力フィールド追加（≥3で高栄養リスク表示）
5. プランA/B推奨カード実装（`selectSecondaryFormula()`）
6. 全13製剤グリッドによる手動製剤選択UI実装（`pickFormula()`）
7. ステップアップタイムラインをday-card形式に刷新
8. 耐性不良プロトコル（胃残留・下痢・嘔吐）をステップアップタブ内に統合

### 2026-05-14（このセッションでの変更）

1. **`computeFluidBalance()` ヘルパー関数を新設**（~1730行）
   - 水分バランスの計算ロジックを `renderFluid()` から分離
   - `renderFluid()` と `renderOrderSheet()` の両方で呼び出して計算を共有
   - 引数：`{ age, calcBW, providedWater, flushMl, otherFluid, fluidLimit, cond }`

2. **`renderFluid()` をヘルパー使用に更新**
   - 計算ロジックを `computeFluidBalance(R)` の呼び出しに置き換え
   - 表示内容は変更なし

3. **`renderOrderSheet()` に「💧 水分管理」セクションを追加**
   - `computeFluidBalance(R)` で計算した最新値（水分制限量も含む）を表示
   - 表示内容：必要水分量（根拠明示）・水分内訳テーブル・プログレスバー・推奨追加補水量
   - 追加補水の推奨ボックス：不足時は「白湯 XXX ml を追加投与」を大きく表示、充足・過剰時はその旨を緑/赤でハイライト
   - 水分制限量を左パネルで変更すると `calculate()` 経由で投与指示タブにも即座に反映（同期済み）

---

## 設計上の注意点

- 具体的な数値（開始速度・増量幅等）はガイドラインではなくAIの医学知識から生成。施設プロトコルと照合すること。
- ガイドラインは方向性の裏付けとして引用しているが、実装ロジックの大部分はAIが独自に構築している。
- `selectedFormula` が `null` 以外のとき、計算結果は手動選択製剤を使用する。`resetFormulaSelection()` で自動選択に戻る。

---

## 今後の改善候補

- **昇圧剤用量（ノルアドレナリン換算）の入力フィールド** — ショック重症度の定量化
- **間接熱量測定値の入力対応** — 実測 REE を目標カロリーとして使用
- **病態別追加項目** — 膵炎重症度スコア・肝機能 Child-Pugh・熱傷面積（TBSA）
- **TPN/PPNツールとの統合** — 経腸＋静脈栄養の合計評価タブ
- **印刷/PDF出力機能** — 投与指示タブを印刷用レイアウトで出力
- **新採用製剤の追加** — 画面から製剤を動的追加できる機能
- **リフィーディング警告を栄養目標タブにも表示** — 現在はステップアップタブのみ
