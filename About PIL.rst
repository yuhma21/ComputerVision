======================
About PIL
======================

:著者: yuhma21

.. contents:: コンピュータビジョンの導入
   :depth: 2

PILモジュールについて
======================

PIL(Python Imaging Library)はPythonで画像を処理するライブラリです。


PIL.Imageモジュール
===============================

PIL.Imageモジュールは、ファイルから画像を開く、新しい画像を作成するなどの様々な関数を提供している。

関数
-----------------

.. function:: new(mode, size)
              new(mode, size, color)
   :module: Image

   与えられたサイズで新しく画像を作成する。サイズは(幅, 高さ)のタプルで指定する。
   色は、通常のHTML color nameで指定する。もしくは、rgb()関数を利用。
   modeは"RGB"等を指定する

   **Mode**\ の一覧

   ==========  ====================================
   Mode        説明
   ==========  ====================================
   1           1bit画像。黒か白
   L           8bit画像。グレースケール
   P           8bit画像。カラー
   RGB         3x8bit画像。RGB
   RGBA        4x8bit画像。RGB+Alpha(透明度)
   CMYK        4x8bit画像。CMYK
   YCbCr       3x8bit画像。ビデオフォーマット
   I           32bit画像。整数型
   F           32bit画像。浮動小数点型
   ==========  ====================================

.. function:: open(file)
              open(file, mode)
   :module: PIL.Image

   画像を開く。モードを指定するのであれば、必ず"r"を指定する。

.. function:: blend(image1, image2, alpha)
   :module: Image

   2つの画像をブレンドする。イメージとアルファは下記の関係

   out = image1 * (1.0 - alpha) + image2 * alpha

   サンプル画像のダウンロード
   :download:`ダウンロード</download/AquaTermi_lowcontrast.JPG>`
   ,
   :download:`ダウンロード</download/flower32_t0.png>`

   .. code-block:: python

      from PIL import Image

      im1 = Image.open('AquaTermi_lowcontrast.JPG')
      im2 = Image.open('flower32_t0.png')
      im2 = im2.resize(im1.size)

      out = Image.blend(im1, im2, 0.5)

      out.show()
　　　
   .. image:: /cv/blend.jpg

.. function:: composite(image1, image2, mask)
   :module: Image

   image1を下地にimage2をかぶせる。ただし、maskのイメージを割合をAlpha値
   にして、image2に透明度を加える。maskに利用する画像は、"1"、"L"、"RGBA"
   でなければならない。また、画像は全て同じサイズにする

   サンプル画像のダウンロード
   :download:`ダウンロード</download/AquaTermi_lowcontrast.JPG>`
   ,
   :download:`ダウンロード</download/flower32_t0.png>`
   ,
   :download:`ダウンロード</download/fisherman.jpg>`

   .. code-block:: python

      from PIL import Image

      im1 = Image.open('AquaTermi_lowcontrast.JPG')
      im2 = Image.open('flower32_t0.png')
      im3 = Image.open("fisherman.jpg").convert("1")
      im2 = im2.resize(im1.size)
      im3 = im3.resize(im1.size)

      out = Image.composite(im1, im2, im3)
      out = out.resize((im1.size[0]/3, im1.size[1]/3))

      out.show()

   .. image:: /cv/composite.jpg

.. function:: eval(image, function)
   :module: Image

   imageの各ピクセルに対してfunctionの計算をかけて出力

   .. code-block:: python

      #!/usr/bin/env python
      # -*- coding: utf-8 -*-

      from PIL import Image

      #evalメソッド用の関数を用意する
      #２値化反転する
      def efunc(x):
          if x > 128:
              a = 0
          else:
              a = 255
          return a

      im1 = Image.open('AquaTermi_lowcontrast.JPG').convert('L')

      out = Image.eval(im1, efunc)

      out.show()

   .. image:: /cv/eval.jpg

メソッド
----------------

読み込んだイメージオブジェクトに対して利用するメソッド

.. function:: convert(mode)
              convert("P", **options)
              convert(mode, matrix)
   :module: im

   イメージオブジェクトを指定の方法で変換する

   ============  ====================================================
   options       パラメータ
   ============  ====================================================
   dither=       ディザリング。デフォルトはFLOYDSTEINBERG
   palette=      パレットの設定。デフォルトはWEB
   colors=       使用する色数。
   ============  ====================================================

.. function:: crop(box)
   :module: im

   boxでタプル型の領域指定を行い、コピーする。(x1, y1, x2, y2)

.. function:: filter(filter)
   :module: im

   フィルタで得られた画像を返す。scipyのファイル他の方が良い？

.. function:: histgram()
              histgram(mask)
   :module: im

   画像のヒストグラムをリストで返す。maskを利用すると0出ないところだけのヒストグラムを返す

.. function:: paste(image, box)
   :module: im

   コピーした画像をはりつける。boxは(x1, y1, x2, y2)を指定

.. function:: resize(size)
   :module: im

   sizeタプル(幅, 高さ)で指定した大きさに変更した画像を返す。

.. function:: rotate(angle)
   :module: im

   指定したangle(度)に時計回りに回転させる

   サンプル画像のダウンロード
   :download:`ダウンロード</download/flower32_t0.png>`

   .. code-block:: python

      #!/usr/bin/env python
      # -*- coding: utf-8 -*-

      from PIL import Image

      im = Image.open('flower32_t0.PNG')

      out = im.rotate(45)

      out.show()

   .. image:: /cv/rotate.jpg

.. function:: save(outfile, options...)
              save(outfile, format, options...)
   :module: im

   ファイル名を指定して画像を保存する

.. function:: show()
   :module: im

   画像を表示する。表示の際は通常画像を開くプログラムで開く

.. function:: thumbnail(size)
   :module: im

   sizeタプルで指定した大きさのサムネイルを作成する

.. function:: tobitmap()
   :module: im

   画像をビットマップに変換して返す

.. function:: transform(size, method, data)
              transform(size, method, data, filter)
   :module:

   何やら変換して返す

属性
---------------------

.. function:: format
   :module: im

   画像のフォーマットを返す

   .. code-block:: python

      >>> from PIL import Image
      >>> im = Image.open('foo.png')
      >>> im.format
      'PNG'

.. function:: mode
   :module: im

   画像のmodeを返す。convertなどで指定するmodeの事。

   .. code-block:: python

      >>> from PIL import Image
      >>> im1 = Image.open('foo.png')
      >>> im1.mode
      'RGB'
      >>> im2 = im1.convert('L')
      >>> im2.mode
      'L'

.. function:: size
   :module: im

   画像のサイズをタプル(幅, 高さ)で返す
