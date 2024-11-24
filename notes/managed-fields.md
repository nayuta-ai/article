# 変更差分から Managed Field が持つ manager を特定する方法
## Kubernetes でのリソースの表現方法
- Unstructured 型：型安全ではなく Kubernetes で表現できる方法
- Structured 型（こういう言い方するか分からない、runtime.Object）：struct で定義し型付けした表現
## 課題
- Managed Field の FieldV1 では key（field の path）の情報しかなく値を取得する方法を別途定義する必要あり
- Structured 型では必要ないフィールドに対しても値が入ってしまうので比較するオブジェクトとしては不適当だった（比較程度なら十分だが内包関係は特に困難）

→ [公式実装(ManagedFields)](https://github.com/kubernetes/apimachinery/tree/master/pkg/util/managedfields)を参考に Managed Field を扱うのに適当な [structured-merge-diff](https://github.com/kubernetes-sigs/structured-merge-diff) パッケージを採用

structured-merge-diff の typed パッケージには二つの Object を比較する Compare メソッドが存在しフィールド毎に追加されたもの、削除されたもの、編集されたものを計算してくれるので今回の要件に有益。（https://github.com/kubernetes-sigs/structured-merge-diff/blob/master/typed/compare.go#L28-L40）
## 参考文献
- https://zenn.dev/ap_com/articles/63d61765d43983
- https://zenn.dev/zoetro/scraps/3bf0f5879de20c