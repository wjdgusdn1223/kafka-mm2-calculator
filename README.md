# Kafka & MM2 リソース計算機

Apache Kafka (KRaft モード) と MirrorMaker2 を Kubernetes 環境にデプロイする際に必要なリソース（CPU、メモリ、ストレージ）を算定するウェブツールです。

## ⚠️ 重要な注意事項

**本ツールの計算結果は参考値・概算値であり、正確な最終回答ではありません。** Kafka と MirrorMaker2 導入時に「どの程度のリソースが必要か」を把握するための目安としてご活用ください。実際のプロダクション環境では、これらの値を出発点として**必ず負荷テストとモニタリングによる調整が必要**です。

## 🚀 特徴

- **日本語対応**: 全ての UI とドキュメントが日本語で統一
- **実践的な計算**: Confluent、AWS、Instaclustr などの実運用事例に基づいた精密な計算ロジック
- **段階的な計算過程表示**: 各リソース算定の全ての計算ステップを詳細に表示し、完全に納得できる根拠を提供
- **Blue/Green双方向ミラーリング対応**: Active-Active 構成での処理量とパーティション数の増加を自動考慮
- **豊富なベース知識**: 計算根拠となる理論、公式文書、技術ブログの参照情報を充実したナレッジベースで提供
- **信頼できる出典**: 公式ドキュメントと実測データに基づく透明性の高い計算式
- **ユーザーフレンドリー**: MirrorMaker2の複製トラフィックは無視して入力するだけの簡単操作

## 🏗️ プロジェクト構造

```
kafka-mm2-calculator/
├── README.md          # このファイル
├── requirements.md    # 要件定義書とベース知識
├── index.html         # メインの計算機ウェブページ
└── CLAUDE.md         # 開発設定
```

## 💻 使用方法

1. `index.html` をウェブブラウザで開く
2. **リソース計算** タブで以下のパラメータを入力:
   - メッセージ処理量（メッセージ/秒）
   - 平均メッセージサイズ（KB）
   - 総パーティション数
   - メッセージ保管期間（日）
   - レプリケーションファクター
   - コンシューマーグループ数
   - ミラーリングタイプ（双方向がデフォルト、単方向同期の場合のみ変更）
   - 圧縮・TLS設定
3. 「リソースを計算する」ボタンをクリック
4. 結果として以下が表示されます:
   - Kafka コントローラーのリソース要件（KRaft モード対応）
   - Kafka ブローカーのリソース要件（パーティション制約＋スループット制約）
   - MirrorMaker2 のリソース要件（tasks.max 最適化含む）
   - **段階的な計算根拠**: 各数値がどこから来たのか完全に追跡可能

## 📊 計算対象リソース

### Kafka コントローラー
- Pod 数（推奨: 3台）
- CPU（vCPU/pod）
- メモリ（GB/pod）
- ストレージ（GB/pod）

### Kafka ブローカー
- Pod 数（パーティション制約: 4,000個/台 + 処理量制約: 170MB/s実効スループット）
- CPU（Request/Limit、圧縮・TLS負荷考慮）
- メモリ（Request/Limit、JVMヒープ6GB + 30秒分ページキャッシュ）
- ストレージ（PVC容量、85%使用率制限＋安全マージン考慮）

### MirrorMaker2
- インスタンス数（単方向: 1、双方向: 2）
- ワーカー数/インスタンス（推奨: 2）
- tasks.max（並列タスク数）
- CPU・メモリ要件

## 📚 ベース知識

**ベース知識** タブでは以下の豊富な情報を参照できます:
- 入力パラメータの詳細説明（双方向ミラーリング時の考慮事項含む）
- リソース計算の詳細根拠（実測データに基づく具体的数値）
- 計算式の段階的説明（30秒分キャッシュ、85%ディスク利用率など）
- 運用上の注意事項とモニタリングポイント
- **参考文献・出典**: 全ての計算根拠となるリンク集

計算ロジックは以下の信頼できるソースに基づいています:
- **公式ドキュメント**: Confluent、AWS MSK、Google Cloud
- **技術ブログ**: AWS Big Data Blog、Instaclustr サイジングガイド
- **MirrorMaker2専門**: Aiven、Redpanda ガイド
- **実測データ**: 3ノード・RF=3・2消費グループで800MB/s理論値などの実証済み数値

## 🔧 技術仕様

- **フロントエンド**: HTML5 + CSS3 + Vanilla JavaScript
- **依存関係**: なし（単一 HTML ファイル）
- **ブラウザ要件**: モダンブラウザ（Chrome、Firefox、Safari、Edge）

## ⚠️ 使用上の注意事項

- **免責事項**: 本ツールは概算・参考値を提供するものであり、正確な最終的なリソース要件を保証するものではありません
- **推奨運用手順**: 
  1. 本ツールで初期リソース量を概算
  2. 実際の環境で負荷テストを実施
  3. モニタリング結果に基づいてリソースを調整
  4. 継続的な監視とチューニング
- **運用基準**: ディスク使用率85%以下、CPU使用率60%以下、メモリページキャッシュ活用
- **考慮事項**: ネットワーク帯域幅制約、レイテンシ要件、可用性要件も別途検討が必要
- **入力のコツ**: MirrorMaker2による複製トラフィック・パーティションは無視して原本データのみ入力

## 📝 ライセンス

MIT License

## 🤝 コントリビューション

改善提案やバグ報告は Issue または Pull Request でお願いします。