これは何？
=====

Pycgettbは帳票ベースのエクセルファイルをテキストデータに変換します。特別なプログラミングは必要なく同じ形のデータとテンプレートを用意し、jinja2ベースのテンプレートを用いてテキストデータとして出力します。

このコマンド,ライブラリはα版です
=================

α版のため仮想環境での利用をおすすめします。

利用ライブラリ
=======

- `openpyxl <https://openpyxl.readthedocs.io/en/default/>`_
- `Jinja2 <http://jinja.pocoo.org/docs/2.9/>`_
- `Click <http://click.pocoo.org/5/>`_


インストール
======

pipでインストール
----------

::

    # 仮想環境でセットアップします
    $ python3 -m venv env
    $ source env/bin/activate
    (env)$ pip install pycgettb

Github経由でインストール
---------------

::

    $ git clone https://github.com/hrsano645/pycgettb.git
    $ cd pycgettb

    # 仮想環境でセットアップします
    $ python3 -m venv env
    $ source env/bin/activate

    (env)$ pip install -r requirements.txt

コマンドの利用方法
=========

::

    (env)$ pycgettbcli --help
    Usage: pycgettbcli [OPTIONS] SRC_TEMPLATE SRC_DATA EXPORT_TEMPLATE

    Options:
      --export_filename TEXT  Set export filename
      --help                  Show this message and exit.


- `--export_filename` はエクスポートしたファイルの名前を指定します。指定しない場合は exported_data.txtというファイル名を作成します。

利用方法
====

まず３種類のファイルを用意することで利用できます。サンプルファイルは `./tests/testfiles/` 内にあります。

ソースデータ(source data): 変換元のデータが入ったxlsxファイル

.. image:: https://github.com/hrsano645/pycgettb/blob/master/docs/img/example_data_img.png?raw=true
    :alt: source data file image

ソーステンプレート(source template): ソースデータと同じワークシートやセルのレイアウトにしたものにテンプレート変数を埋め込んだxlsxファイル

.. image:: https://github.com/hrsano645/pycgettb/blob/master/docs/img/example_template_img.png?raw=true
    :alt:  source template file image

エクスポートテンプレート(export template):jinja2のテンプレート変数を埋め込んだテキストファイル(htmlやjson, csvなど)

※:jinja2のテンプレートは現在は変数のみ対応です。ドットによるネストやフィルターなどの対応は未確認です。

::

    <!DOCTYPE html>
    <html lang="ja">
    <head>
        <meta charset="UTF-8">
        <title>{{ title }}</title>
    </head>
    <body>

    <h1>{{ title }}</h1>

    <table>
        <tr>
            <td>date</td>
            <td>name</td>
            <td>note</td>
        </tr>
        <tr>
            <td>{{ date_1 }}</td>
            <td>{{ name_1 }}</td>
            <td>{{ note_1 }}</td>
        </tr>
        <tr>
            <td>{{ date_2 }}</td>
            <td>{{ name_2 }}</td>
            <td>{{ note_2 }}</td>
        </tr>
    </table>
    </body>
    </html>

ソーステンプレートとエクスポートテンプレートで利用するテンプレート変数名は同じにすることでマッピングを行い、jinja2テンプレートを用いてテキスト形式に変換されます。

::

    (env)$ pycgettbcli ./tests/testfiles/template.xlsx ./tests/testfiles/data.xlsx ./tests/testfiles/export_template.html


変換された exported_data.txt は以下となります。

::

    <!DOCTYPE html>
    <html lang="ja">
    <head>
        <meta charset="UTF-8">
        <title>Example Title</title>
    </head>
    <body>

    <h1>Example Title</h1>

    <table>
        <tr>
            <td>date</td>
            <td>name</td>
            <td>note</td>
        </tr>
        <tr>
            <td>2017-01-01 00:00:00</td>
            <td>suzuki</td>
            <td>日本語テスト</td>
        </tr>
        <tr>
            <td>2017-01-02 00:00:00</td>
            <td>sato</td>
            <td>None</td>
        </tr>
    </table>
    </body>
    </html>

ライブラリの利用方法
==========

pycgettbはライブラリとしても利用できます。以下に簡単なサンプルを記載します。

::

    from pycgettb import Source
    from pycgettb import TextRender

    # 各種ファイルパスを指定
    src_template = "[source template file path]"
    src_data = "[source data file path]"
    export_template = "[export template file path]"

    # 書き出すファイル名を指定
    export_filename = "exported_file.txt"

    source = Source(src_template, src_data)
    source_data_map = source.parse()

    textrender = TextRender(export_template, source_data_map)

    # 変換した結果を書き出す
    with open(export_filename, "w") as export_file:
        export_file.write(textrender.render())


今後の予定
=====

- 各OS向けのバイナリを用意
- データの羅列（リスト形式）に対応できるソーステンプレートの変数の追加
- （何かしらの方法で）GUIフロントエンド
- APIのドキュメントを用意

ライセンス
=====

MIT License


