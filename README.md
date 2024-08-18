# 自分用FLOCSS_SCSSのテンプレート
自分用のHTML/CSS(SCSS)のテンプレートファイルです。以下の特徴を持ちます。
- classの命名規則はオリジナルの[FLOCSS](https://github.com/hiloki/flocss)をベースにカスタマイズしています。
- リキッドレイアウト対応として、サイズ指定はclamp関数を使ってViewportに応じて表示サイズを拡縮する形を想定。  
※SASS関数とカスタムプロパティを用いてコーディングの負担を軽減。

## class（セレクタ）の命名規則の基本ルール・構成
### 文法
- BEM記法をベースにBlock, Element名はケバブケースで記載
- BlockとElementの間はアンダースコア2つのスネークケースで区切る。
- **Modifierは別クラスとして分離**する。
- CSSセレクタはModifierをwhereでくくることで詳細度を高めないようにする。  
  
【記載例】  
```html
<!-- HTMLのclass -->
 class="p-block-name__element-name m-modifier"
```
```CSS
/* CSSのセレクタ */
 .p-block-name__element-name:where(.m-modifier)
```

### レイヤー構成
- Reset
- Base
- Layout
- Component
- Project
- Modifier
- Utility
- JavaScript
- Status

### Reset
リセットCSS。自作はせずにトレンドの物を用いる。
冒頭に出典を明記すること。  
※現在使用している物：[`@acab/reset.css`](https://github.com/mayank99/reset.css/blob/main/package/index.css)

### Base
サイト全体で用いる設定。例として以下の物を定義  
1. Sass  
- _functions.css (関数)
- _mixin.scss (mixin)
- _colors_and_fonts.scss (色とフォントのScss変数)
2. CSS  
- _global.scss  
  `:root`に設定するカスタムプロパティ  
  `body`に設定するサイト全体のフォントや背景の既定値  
  各要素（タグ）の基本的なスタイル  

### Layout
- 各ページの基本構造を定義するために用いる。  
- 原則各ページに1つのみのユニークな要素。
- 色やフォントなどの見栄え、内包する要素の配置のスタイリングは行わない。  
（同名のprojectで行う）
- セレクタの命名規則  
  - プレフィックス= `l-`
  - 状況に応じてIDで指定するのも許容  
  ※IDで設定する場合は詳細度を高めないようにする。例） `:where(#hoge)`   

### Component
- 再利用を前提とした最小限の機能。
- ボタン、見出し、テキスト、画像、エフェクトやシンプルなフレックスやグリッド構造も含む。
- 色やサイズの指定は可変にするのが望ましい。
- 同一の機能の色やサイズ違いのバリエーションを作る場合はModifierの別クラスを用いる。
- ネストは原則2段階（子・孫要素）まで
- セレクタの命名規則  
  - プレフィックス= `c-`
  - 記載例:  
    親要素　: c-hoge  
    子孫要素: c-hoge__{element}  

### Project
- サイト固有のパターン。いくつかのComponent・子Projectを格納する。
- 各パーツの配置やサイズ、使用する色などをまとめる。
- ネストは原則2段階（子・孫要素）まで
- Projectの中に、役割を細分化できる機能群（カード・リスト・表等）がある場合、  
別のProjectやComponentとして定義して詳細を設定する。
- セレクタの命名規則  
  - プレフィックス= `p-`
  - 記載例:  
    親要素　: p-hoge  
    子孫要素: p-hoge__{element}  

### Modifier
- ComponentやProjectのバリエーションの作成に用いる。
- **単独のセレクタとしてスタイリングは行わない**。  
必ずComponent・Projectのクラスとセットで運用する。
- 原則親要素に設定し、ネストを利用し:whereを用いてセレクタで指定する。
- セレクタの命名規則  
  - プレフィックス= `m-`
  - 記載例:    
    &:where(.m-fuga)

### Utility
- Component、Projectでは定義しきれない細かい調整に用いる。
-  細かい余白（margin, padding）の設定など、単独で機能し、単一のスタイルを指定する。
-  UtilityはElementは持たず、別クラスのModifierも設定しない。
-  **※多用しないこと**
- セレクタの命名規則  
  - プレフィックス= `u-`
  - 記載例:    
    u-hoge

### Javascript
- JavaScriptで要素を取得・操作するための識別用セレクタ。
- このclass名をセレクタとして使用してスタイリングは行わない。
- セレクタの命名規則  
  - プレフィックス= `js-`
  - 例） 
    js-{jsのクラス名}-{要素・項目名}

### Status
- 主にJavaScriptでコンテンツを変化させる際に、要素の状態を指定するために用いる。
- コンポーネントもしくはプロジェクトとセットで利用し、単独のスタイリングは行わない。
- セレクタの命名規則  
  - プレフィックス= `is-`
  - 記載例:  
    is-{状態}, is-active, is-show, is-hidden ...  
    .p-modal.is-open

## リキッドレイアウト&Webアクセシビリティ対応
- remでの指定をベースとします。pxのような絶対長の単位は使いません。
- 閲覧環境の横画面幅に応じて表示サイズを調整する箇所は、`clamp()`を用いてvwでサイズを変動させます。
- clamp関数による記述を最小限にするために、基本的に`:root`にカスタムプロパティでベースサイズを定義し、各プロパティでのサイズ指定は`calc()`でベースサイズに乗算する形にします。
- 文字サイズに応じて余白等を変えるcomponentなどでは`em`単位を利用。  

### `:root`に指定するベースサイズ
- 原則デスクトップ（PC）とモバイル端末（SP）それぞれにベースサイズを定義します。
  - **PC版のベースサイズ：**  
    SP版デザインへのブレークポイントからコンテンツ幅までの範囲でサイズを変動させます。  
    最大値はviewportがコンテンツ幅と一致するときに1rem(=16px)とし、そこからviewpointに比例してブレークポイントの数値までサイズを変動させます。  

  - **SP版のベースサイズ：**  
    配慮する最小サイズのモバイルの画面幅からSP版デザインのアートボードの幅までの範囲でサイズを変動させます。  
    最大値は1rem(=16px)とし、そこからviewpointに比例してブレークポイントの数値までサイズを変動させます。  
      - ※検討材料  
        SP版のリキッドレイアウト対応はJavaScriptによるViewportの固定する方法も検討

- 定義例
```CSS
:root {
  /* PC版とスマホ版のベースサイズ */
  /* PC版 SP版デザインへのブレークポイントを768px,　コンテンツ幅を1440pxと想定  */
  --liquid-size-pc: clamp(0.5rem, 1.19vw - 0.071rem, 1rem);
  /* SP版 配慮するモバイルの最小幅を320px,　デザインのアートボード幅を430pxと想定  */
  --liquid-size-sp: clamp(0.75rem, 3.636vw + 0.023rem, 1rem);

  /* PC・SPのベースサイズはメディアクエリで切り替える */
  --liquid-size-base: var(--liquid-size-pc);
}
@media (width < 768px) {
  :root {
    --liquid-size-base: var(--liquid-size-sp);
  }
}
```

### 入力補助用のSASS関数
計算式のコード入力の簡略化と可読性を考慮して以下のSASS関数を使用します。  
関数式の詳細は[_functions.scss](./scss/base/_functions.scss)を参照。  

#### **PxToRem()**　　
- 1rem=16pxとして、引数にpx単位の数値入力するとrem単位の値を返す関数です。
```SCSS
// SCSS　サンプル
pxToRem(24) // -> 1.5rem
```

#### **clampWithPxToRem()**　　
- `clamp()`の入力補助用の関数です。  
- 引数は最小サイズ・最大サイズ・最小Viewport・最大Viewportの4つで、それぞれpx単位の数値を入力します。
- 返り値はvwに応じて1次式で値が変化する`clamp()`を得ます。
```SCSS
// SCSS　サンプル
clampWithPxToRem(8, 16, 768, 1440)
 // -> clamp(0.5rem, 1.19vw - 0.071rem, 1rem)
```

#### **pxToLiquidSize()**　　
- px値を乗数として{ベースサイズ * 乗数}の形式の`calc()`に変換する関数です。 
- 第1引数にpx値・第2引数に使用するベースサイズのカスタムプロパティを指定します。  
  - 選択できる第2引数（※省略する場合は"base"）を取ります。  
    "base" : "--liquid-size-base"  
    "pc" : "--liquid-size-pc"  
    "sp" : "--liquid-size-sp"  
- 返り値は1rem=16pxとして変換した`calc()`を得ます。
```SCSS
// SCSS　サンプル
pxToLiquidSize(16, base)
 // -> calc(var(--liquid-size-base) * 16 * 0.0625)
```
## ライセンス

このプロジェクトはMITライセンスのもとで公開されています。詳細は[LICENSE](LICENSE.txt)ファイルを参照してください。  

This project is licensed under the MIT License - see the [LICENSE](LICENSE.txt) file for details.