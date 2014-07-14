*This is a Japanese translation from [the original document in English][original].*
*原文:[States and Fates by Domenic Denicola][original]*

[original]: https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md

======
# State と Fate

この文書は Promise とともによく使われるいつくかの形容詞の意味を明確にするためのものです。そのために、ここではそれらの用語を２つのカテゴリー、State と Fate に分類します。

## 概要および運用上の定義

### State

Promise は３つの相互排他的な State を持ちます。すなわち、fulfilled、rejected、pending です。

- `promise.then(f)` の呼び出しにおいて、`f` が「すぐさま」呼び出される場合、promise は *fulfilled* である。
- `promise.then(undefined, r)` の呼び出しにおいて、`r` が「すぐさま」呼び出される場合、promise は *rejected* である。
- fulfilled でも rejected でもない場合、promise は *pending* である。

pending でない場合、つまり fulfilled か rejected のどちらかである場合、promise は *settled* であると言いいます。settled は便宜上の用語であり、State ではありません。

### Fate

Promise は２つの相互排他的な Fate を持ちます。すなわち、resolved と unresolved です。

- promise を resolve もしくは reject しても何の効果も無い場合、すなわち、すでに fulfilled か rejected であるか、もしくは他の promise を待つために「固定」されている場合、promise は *resolved* である。
- resolve されていない場合、つまり promise を resolve もしくは reject すれば何らかの効果がある場合、promise は *unresolved* である。

promise は 別の promise もしくは thenable に resolve されるか、非 promise の値に resolve されます。前者の場合、promise もしくは thenable は後で処理されるまで保持されます。後者の場合、非 promise の値で fulfill されます。

### State と Fate の関係

promise の Fate が resolved の場合、State は以下のいずれかになります。

- 非 promise の値に resolve された場合、もしくは thenable に resolve されたがすぐさま引数の fulfill コールバック関数が呼ばれた場合、もしくは他のすでに fulfill された promise に resolve された場合、State は fulfilled になります。
- 直接 reject されたか、もしくは thenable にresolve されたがすぐさま引数の reject コールバック関数が呼ばれた場合、もしくは他のすでに reject された promise に resolve された場合、State は rejected になります。
- thenable に resolve されたがすぐさまコールバック関数が呼ばれない場合、もしくは他の pending の promise に resolve された場合、State は pending になります。

promise の Fate が unresolved の場合、State は必然的に pending になります。

これらの関係は再帰的です。たとえば、thenable に resolve されて、すぐさま fulfill コールバック関数が呼ばれたが、渡された値がすでに reject された promise であった場合、State は rejected になります。

## 仕様との関係

promise の State は仕様の [[PromiseState]] internal slot に対応します。

promise の Fate は暗黙的に "resolving function" の一部として保持されています。
