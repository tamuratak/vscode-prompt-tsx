`removeLowestPriorityChild` が末尾の `<cacheBreakpoint>` しか残っていないコンテナに対して `BudgetExceededError` を投げていたのを、内容が空ならそのノードごと切り捨てるようにして再現しないようにしました。

- **挙動修正**: 残りの子がすべて `MaterializedChatMessageBreakpoint` の場合は `BudgetExceededError` を投げずに親コンテナごと削除し、トークン不足で「No lowest priority node found」が出ていたケースを回避しました。[src/base/materialized.ts](src/base/materialized.ts#L682-L690)
- **回帰テスト**: 端的にキャッシュブレークポイントだけが残ったときに `removeLowestPriorityChild` が例外を吐かずコンテナを削除することを検証するテストを追加しました。[src/base/test/materialized.test.ts](src/base/test/materialized.test.ts#L91-L123)

テスト: `npm run test:unit`

次の一手としては、この修正を踏まえて実際のプロンプトで「breakpoint だけが残って予期せずダウンする」ケースを再確認するか、追加のトークン制限テストで同様の状態を再現しておくと安心です。