---
Title: npm pack時にsrc下の__tests__を無視したい（2021/02/08更新）
Category:
- JavaScript/TypeScript
- プログラミング
- ノウハウ
- 生産性向上
Date: 2021-02-08T01:22:19+09:00
URL: https://korosuke613.hatenablog.com/entry/2021/02/08/npm_pack_ignore_tests
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613688706218
---

<!-- ここに導入を書く -->
`npm pack`をする際に、`src`下に配置したテストファイルを除外する方法です。ググってもあんまりいい回答がヒットしなかったので記事にします。

** 2021/02/08 更新：npm v7 に対応しました。**

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

<!-- ここに広告が入る -->
---

# 2021/02/08 追記

元々は`!__tests__`で除外ができていたのですが、どうやら npm v7 では`!__tests__`が使えないことが判明しました。（npm v6 で検証していました...）

回避方法としては`!**/__tests__`という風にアスタリスクをつけることで npm v6/v7 両方で除外ができます。本記事もそのように修正いたしました。

問題と対策は [@teppeis](https://twitter.com/teppeis/status/1358651980915220484?s=20) さんから教えていただきました。ありがとうございました🙇

さらに追記。
`!**__tests__`となっていたところを`!**/__tests__`と直しました。前者のままだと` hoge__tests__ `も除外されてしまいますね。なんとこれも @teppeis さんにご指摘いただきました🙇



# TL;DR（要約）
`package.json`の`files`に`ソースディレクトリ名（またはアウトプットディレクトリ）`と`!**テストディレクトリ名`を加えましょう。

例
```json
  "files": [
    "src",
    "!**/__tests__"
  ],
```

# npm packで圧縮されるファイル

`npm pack`すると、`package.json`があるディレクトリのファイルを全て`.tgz`ファイルに圧縮します。
そのため、`.gitignore`や`.eslintrc.js`などのnpmパッケージには必要ないようなファイルも含まれてしまいます。

そこで、多くの人が`package.json`の`files`を利用していると思います。

## 指定したファイルやフォルダのみをnpmパッケージに含まれるfilesフィールド
`package.json`の`files`にファイルやディレクトリを指定することで、指定したファイル名やディレクトリ名以下のファイルのみをパッケージに含ませることができます。

例えばTypeScriptを使っている場合、`tsconfig.json`の`compilerOptions.outDir`に設定したディレクトリ名を入れます。

package.json
```json
  "files": [
    "lib"
  ],
```

この状態で`npm pack`すると以下のようになります(( \`npm pack\`に \`--dry-run\`オプションをつけることで、.tgz ファイルを作成せずに圧縮するファイルやサイズを確認できます。 ))。

```
❯ npm pack --dry-run

npm notice 
npm notice 📦  linear-webhook@0.1.4
npm notice === Tarball Contents === 
npm notice 1.1kB LICENSE                                      
npm notice 894B  lib/__tests__/data/createComment.js          
npm notice 1.2kB lib/__tests__/data/createIssue.js            
npm notice 3.5kB lib/__tests__/Handler.CallbackTest.js        
npm notice 2.5kB lib/__tests__/Handler.getWebhookInfoTest.js  
npm notice 1.9kB lib/Handler.js                               
npm notice 684B  lib/HandlerError.js                          
npm notice 481B  lib/__tests__/examples/handlerExample.js     
npm notice 901B  lib/__tests__/data/index.js                  
npm notice 828B  lib/index.js                                 
npm notice 535B  lib/Interfaces.js                            
npm notice 550B  lib/__tests__/data/removeComment.js          
npm notice 1.2kB lib/__tests__/data/removeIssue.js            
npm notice 1.2kB lib/__tests__/data/unknownAction.js          
npm notice 1.2kB lib/__tests__/data/unknownType.js            
npm notice 1.1kB lib/__tests__/data/updateComment.js          
npm notice 1.9kB lib/__tests__/data/updateIssueLabel.js       
npm notice 1.4kB package.json                                 
npm notice 1.3kB CHANGELOG.md                                 
npm notice 1.1kB README.md                                    
npm notice 115B  lib/__tests__/data/createComment.d.ts        
npm notice 109B  lib/__tests__/data/createIssue.d.ts          
npm notice 11B   lib/__tests__/Handler.CallbackTest.d.ts      
npm notice 551B  lib/Handler.d.ts                             
npm notice 11B   lib/__tests__/Handler.getWebhookInfoTest.d.ts
npm notice 270B  lib/HandlerError.d.ts                        
npm notice 11B   lib/__tests__/examples/handlerExample.d.ts   
npm notice 2.1kB lib/__tests__/data/index.d.ts                
npm notice 85B   lib/index.d.ts                               
npm notice 4.2kB lib/Interfaces.d.ts                          
npm notice 115B  lib/__tests__/data/removeComment.d.ts        
npm notice 109B  lib/__tests__/data/removeIssue.d.ts          
npm notice 751B  lib/__tests__/data/unknownAction.d.ts        
npm notice 749B  lib/__tests__/data/unknownType.d.ts          
npm notice 115B  lib/__tests__/data/updateComment.d.ts        
npm notice 114B  lib/__tests__/data/updateIssueLabel.d.ts     
npm notice === Tarball Details === 
npm notice name:          linear-webhook                          
npm notice version:       0.1.4                                   
npm notice filename:      linear-webhook-0.1.4.tgz                
npm notice package size:  7.7 kB                                  
npm notice unpacked size: 34.9 kB                                 
npm notice shasum:        a6198289407abbe10b044d0827bc1b3e1ade31bc
npm notice integrity:     sha512-Gss/vdAdgO326[...]eOWzE1RNiIypQ==
npm notice total files:   36                                      
npm notice 
linear-webhook-0.1.4.tgz
```

`npm notice === Tarball Contents ===`から`npm notice === Tarball Details ===`に挟まれた行のファイルがnpmパッケージに含まれるファイルになります。
`lib`下のファイルのみ含まれていることがわかります。

また、以下のファイルは設定に関係なくnpmパッケージに含まれます。（[参考](https://docs.npmjs.com/cli/v6/configuring-npm/package-json#files
)）

- package.json
- README
- CHANGES / CHANGELOG / HISTORY
- LICENSE / LICENCE
- NOTICE
- "main"に設定したファイル

しかし、**`src/__tests__`((僕はTSのテストフレームワークに[jest](https://jestjs.io/)を使っています。srcの外に\_\_tests\_\_を出せばいいだけな気もしますが、僕はsrc下に配置するようにしています。))下にテストファイル((テストコードやテストデータ))を配置しているため、コンパイル後の`lib/__tests__`下のファイルもnpmパッケージに含まれてしまっています。**（上記`npm pack --dry-run`結果を参照）

npmパッケージを利用する際にテストファイルは不必要であるため、これらは含まないようにしたいです。

# npm packから特定のフォルダを除外する
では実際に`lib/__tests__`下のファイルを含まないようにしてみましょう。

## .npmignoreを使うとめんどくさい
`npm pack`で除外するファイルやフォルダを指定する方法に`.npmignore`を使う方法があります。

[https://zellwk.com/blog/ignoring-files-from-npm-package/:embed:cite]

しかし、**`.npmignore`と`package.json`の`files`の両方を利用すると、`files`に指定したファイルやフォルダを`.npmignore`は除外しないようになっています。**

> Files included with the "package.json#files" field cannot be excluded through .npmignore or .gitignore.
> 
> https://docs.npmjs.com/cli/v6/configuring-npm/package-json#files

そのため、`files`を削除して`.npmignore`のみ利用すると、以下のように除外するファイルやフォルダを全て記述しなければならなくなります。非常にめんどくさいです。

```
lib/__tests__
src/__tests__
jest.config.js
.idea
...
```

そこで、`.npmignore`を使わずに`files`のみを利用することにします。

## filesで!を使おう
package.jsonの`files`は指定したファイルやフォルダ（と、package.jsonなど）のみを`npm pack`時にnpmパッケージに含ませる、いわばホワイトリストの役割をしていることはすでに説明しました。

しかし、実はドキュメントには「.gitignoreに似た構文が使えるよ((2021/02/08追記：npm v7 では\`!\_\_tests\_\_\`という書き方は使えないため、そこまで .gitignore と同じように使えるわけではありませんでした。もしかしたら npm のバグかもしれません))」みたいなことが書いてあります。

> File patterns follow a similar syntax to .gitignore
> 
> https://docs.npmjs.com/cli/v6/configuring-npm/package-json#files

そのため、**なんと`!**`を頭につけることでそのファイルやフォルダを無視することができます**。

実際に`__tests__`を除外するために`!**/__tests__`を`files`に追加してみます。
```json
  "files": [
    "lib",
    "!**/__tests__"
  ],
```


この状態で`npm pack`すると...
```
❯ npm pack --dry-run
npm notice 
npm notice 📦  linear-webhook@0.1.4
npm notice === Tarball Contents === 
npm notice 1.1kB LICENSE              
npm notice 1.9kB lib/Handler.js       
npm notice 684B  lib/HandlerError.js  
npm notice 828B  lib/index.js         
npm notice 535B  lib/Interfaces.js    
npm notice 1.4kB package.json         
npm notice 1.3kB CHANGELOG.md         
npm notice 1.1kB README.md            
npm notice 551B  lib/Handler.d.ts     
npm notice 270B  lib/HandlerError.d.ts
npm notice 85B   lib/index.d.ts       
npm notice 4.2kB lib/Interfaces.d.ts  
npm notice === Tarball Details === 
npm notice name:          linear-webhook                          
npm notice version:       0.1.4                                   
npm notice filename:      linear-webhook-0.1.4.tgz                
npm notice package size:  4.6 kB                                  
npm notice unpacked size: 14.0 kB                                 
npm notice shasum:        dd08096327abf2384736e0621af2f54dfe76ff54
npm notice integrity:     sha512-/BKVDLrPb56H6[...]KCrBEKU6O6uuw==
npm notice total files:   12                                      
npm notice 
linear-webhook-0.1.4.tgz
```

見ての通り、**`lib`以下でかつ`lib/__tests__`以下でないファイルのみがnpmパッケージに含まれるようになりました**。

ファイルサイズも
```
npm notice package size:  7.7 kB                                  
npm notice unpacked size: 34.9 kB   
```
から
```
npm notice package size:  4.6 kB                                  
npm notice unpacked size: 14.0 kB   
```
に減らすことができました。**`.npmignore`を使う方法よりよっぽど簡単です**。

# おわりに
自分が作ったnpmパッケージにテストファイルが含まれてしまっていたため、なんとか無くしたいなとおもいつつ、ググってもいい回答は見つけられませんでした。（`.npmignore`でがんばろうねっていう内容ばかり）

案外`package.json`の`files`で`!`が使えることを知らない人は多いかもしれません。自分も今回試しに`!`をつけてみたらなんかできたという風に知りました。

この記事で似たような内容で困っている人たちの助けになれればいいなと思います。

ちなみに今回実際に使ったリポジトリはこちらです。

[https://github.com/korosuke613/linear-webhook:embed:cite]


<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
