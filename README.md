# EQS - Earthquake Simulator
### Physics-based Seismic Wave Propagation & Intensity Engine
*(物理演算ベース 地震波伝播・震度シミュレーター)*

**EQS** は、HTML5 Canvasと最新のWeb標準技術を用いて構築された、ブラウザベースの地震シミュレーションエンジンです。
震源からの距離減衰式、地盤増幅率（AVS30/ARV）、および非線形特性をリアルタイムで演算し、日本列島における地震波（P波・S波・表面波）の伝播と、各観測点における計測震度を精密に可視化します。

---

## 🌟 プロジェクトの概要 (Overview)

本プロジェクトは、静的な地図表示にとどまらず、**「地震動の物理的な挙動」** をWebブラウザ上で再現することを目的としています。
数千箇所の観測点データと地盤情報を保持しており、ユーザーが設定したパラメータ（マグニチュード、深さ、断層方位など）や、外部APIからのリアルタイムデータに基づいて、瞬時にシミュレーションを実行します。

### 🤖 AI Collaboration
> **Development Note:**
> 本プロジェクトのコアアルゴリズムの実装、UI設計、およびパフォーマンス最適化は、**Google Gemini 3 Pro** (AI) とのペアプログラミングによって行いました。

---

## ✨ コア機能 (Core Features)

### 1. 物理ベースの波動伝播 (Physics Engine)
地震波の性質を簡易的な物理モデルで再現しています。
* **P波 (Primary Wave - 青)**: 縦波。6.0〜8.0km/sで高速伝播し、初期微動をトリガーします。
* **S波 (Secondary Wave - 赤)**: 横波。3.0〜4.5km/sで伝播し、主要動（破壊的な揺れ）を引き起こします。
* **表面波 (Surface Wave - 黄)**: 大規模地震や浅発地震で見られる、長周期でゆっくりと伝わる波を再現。
* **指向性 (Directivity)**: 断層の走向（Strike）設定により、特定の方向に揺れが強く伝わる効果をシミュレート。

### 2. 高精度な震度計算 (Intensity Calculation)
距離減衰式（司・翠川の式などを簡略化したモデル）に加え、以下の要素を考慮して地点ごとの震度を算出しています。
* **地盤増幅 (Site Amplification)**: 各観測点の `AVS30` (30m平均S波速度) および `ARV` (表層地盤増幅率) データを使用。同じ距離でも、軟弱地盤（平野・埋立地）では震度が大きく跳ね上がります。
* **深度補正 (Depth Correction)**: 深発地震における異常震域や、直下型地震の急激な揺れの変化を補正係数として実装。
* **非線形特性**: 巨大地震発生時、地盤が揺れの上限に達する非線形効果を簡易的に考慮しています。

### 3. 多彩な可視化モード (Visualization)
* **Station Mode**: 観測点を円/矩形で描画。気象庁震度階級に応じた色（白→青→黄→赤→紫）でリアルタイム変化します。
* **E-Sim (Mesh) Mode**: 地図全体をメッシュ化し、推計震度分布図のようなヒートマップを生成します。
* **EEW Simulation**: 緊急地震速報（予報・警報）のシミュレーション。PLUM法（観測点主導）の挙動や、S波到達予想円を表示します。

### 4. リアルタイムデータ & アーカイブ (Data Integration)
* **API Mode**: [P2P地震情報 API](https://www.p2pquake.net/) とWebSocket/HTTP通信を行い、日本で発生した最新の地震を自動取得・再生します。
* **History Archives**: 過去の顕著な地震（震度6弱以上）のデータベースを内蔵。リストから選択してリプレイ可能です。
* **Disaster Presets**: 「令和6年能登半島地震」「東日本大震災(3.11)」「南海トラフ想定」などのシナリオプリセットを搭載。

---

## 🚀 インストール・導入方法 (Installation)

本アプリケーションは静的サイトとして動作するため、特別なビルド環境は不要です。

### ⚠️ 重要：ファイル構成
自動読み込みシステム（Auto-Loader）を実装しているため、個別にファイルを読み込む作業は不要です。

| ファイル名 (File Name) | 内容・役割 |
| :--- | :--- |
| `index.html` | アプリケーション本体 |
| `japan.geo.json` | 日本地図データ (GeoJSON形式) |
| `stations_with_amp_revised.json` | 観測点・地盤増幅率データベース |
| `震央地名.geo.json` | 震央地名のポリゴンデータ |
| `jma_all_quakes.json` | 過去の地震履歴データ |

---

## 🎮 操作マニュアル (Controls)

### メイン操作
* **地図操作**: 左ドラッグで移動、マウスホイールでズーム。
* **地震発生 (Manual)**: 地図上を **左クリック** すると、現在の設定値で地震が発生します。

### 設定パネル (Control Panel)

#### ⚙️ SETTINGS タブ
シミュレーションのパラメータを調整します。
* **Magnitude (Mj)**: 地震の規模 (4.0 - 9.0+)。
* **Depth (km)**: 震源の深さ。浅いほど局所的に強く、深いほど広範囲に揺れます。
* **Speed**: 波の伝播速度倍率。
* **Strike**: 断層の走向（角度）。揺れの広がりの向きを制御します。
* **Hypocenter**: 緯度経度を手動入力して震源を指定することも可能です。

#### 📡 REAL DATA タブ
実際の地震データを使用します。
* **Get Latest Quake**: APIから最新情報を取得。
* **History List**: 過去の地震リストから選択して再生。
* **Presets**: 代表的な災害地震をワンクリックで再現。

#### 📊 LOGS パネル (画面下部)
* データのロード状況、API通信の結果、エラー情報などが表示されます。
* 「Resize」ボタンで高さを変更可能です。

---

## 🛠️ 技術スタック (Tech Stack)

* **Frontend**: HTML5, CSS3 (Grid/Flexbox), JavaScript (ES2020+)
* **Graphics Engine**: HTML5 Canvas API (Off-screen rendering applied for performance)
* **Geospatial Data**: GeoJSON
* **External API**: P2PQuake API (JSON-RPC)

---

## ⚠️ 免責事項 (Disclaimer)

1.  **シミュレーションの限界**: 本ツールは学習・可視化を目的とした簡易シミュレーターです。実際の地下構造（プレート境界、堆積層の厚さ等）を完全に反映したものではありません。
2.  **防災利用**: このシミュレーション結果を、実際の災害時における避難判断等の根拠として使用しないでください。必ず気象庁や自治体の公式情報に従ってください。

---

## 📚 クレジット / 出典 (Credits)

* **Software Design & Coding**: Generated with **Google Gemini 3 Pro**
* **Earthquake Data Provider**: [P2P地震情報 (P2PQuake)](https://www.p2pquake.net/)
* **Map Data**: 国土地理院 地球地図日本 (Global Map Japan) を加工して利用
* **Station & Soil Data**: NIED J-SHIS (防災科学技術研究所 地震ハザードステーション) / 気象庁

---

&copy; 2026 EQS Project.
