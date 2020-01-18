# VSCodeではじめるOpenAPI 3.0 #

最近のAPIドキュメント作成といえばOpenAPIですが、意外にもOpenAPIの開発環境やプラクティスに関する記事があまりありません。
OpenAPIは素晴らしいツールなのですが、 APIの数を増やしていくと、あっという間に数千行のYAMLが誕生してしまいます。
長期的にドキュメントを保守していくには、開発環境を整え、ディレクトリ構造を整理してファイル分割するなど戦略を練らないといけません。
今回は、実開発にて手探りで調査・探求して、プラクティスがそれなりにまとまったので、それらを説明したいと思います。

## 本記事のキーワード ##

- OpenAPIの開発環境
- OpenAPI関連のツール
- 数十個のAPIを長期間保守するのポイント

## VSCodeの設定 ##

```json:extensions.json
{
    "recommendations": [
        // OpenAPI (Swagger) Editor
        "42crunch.vscode-openapi",
        // OpenApi Designer
        "philosowaffle.openapi-designer",
        // Prettier
        "esbenp.prettier-vscode"
    ]
}
```

### OpenAPI (Swagger) Editor ###

拡張ID: `42crunch.vscode-openapi`

- インストールしておくと`$ref`の参照先YAMLにコードジャンプできます。
- APIの数が増えるとファイルが数十・数百と増えていくのコードジャンプできるのは非常に便利です

### OpenApi Designer ###

拡張のID: `philosowaffle.openapi-designer`

- プレビュー表示する拡張機能
- ファイル分割したopenapi.ymlにも対応している
- yamlファイルを編集して保存すると、プレビューも自動更新される

### Prettier: "esbenp.prettier-vscode" ###

拡張のID: `esbenp.prettier-vscode`

- 言わずとしれたフォーマッタ

## 便利なツール ###

### ファイル結合 swagger-cli ###

OpenAPIには分割されたYAMLに非対応なツールもあります。
次に説明する静的HTML生成ツールReDocもその一つです。
またエンジニア間で共有する場合も分割されていると不便なのでswagger-cliを用いて結合しています。

```sh
swagger-cli bundle -r openapi/openapi.yml -o build/openapi.json
```

出力形式はyamlとjsonどちらも対応していますがjsonのほうが汎用的かなと思いデフォルトのjsonにしています。

### 静的HTML生成 ![ReDoc](https://github.com/Redocly/redoc) ###

openapi.jsonから静的なHTMLを生成するツールの中でもっともキレイで見やすいとおもったのがReDocです。
成果物が単一のHTMLファイルというのも魅力です。
納品物としてドキュメントを提出したり、社内で情報共有する際にとても重宝します。

```sh
redoc-cli bundle build/openapi.json --options.menuToggle --options.pathInMiddlePanel  -o build/redoc-static.html
```

自分はオプションで以下の2つをONにしています

- `--options.menuToggle`:  左のmenuをトグルできるようにする
- `--options.pathInMiddlePanel`: 中央にAPIのエンドポイントパスを表示する

### Mockサーバー prism ###

```sh
prism mock openapi/openapi.yml
```
