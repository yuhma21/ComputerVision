======================
Intro
======================

:著者: yuhma21

PILを利用する
============================================


PIL(Python Imaging Library)は画像に関する操作が自在に可能。
もっとも重要なモジュールはImageモジュール。

**画像を読み込む**

画像を
:download:`ダウンロード</download/empire.jpg>`

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-

   from PIL import Image

   #画像を読み込む
   pil_im = Image.open('empire.jpg')

   #画像を読み込んでグレースケールに変換する
   im_gray =Image.open('empire.jpg').convert('L')

   #画像を表示する
   import matplotlib.pyplot as plt
   plt.figure()
   plt.subplot(121)
   plt.imshow(pil_im)
   plt.subplot(122)
   plt.imshow(im_gray)
   plt.show()

コードを実行すると下図のように表示されるはずです

.. image:: /cv/pilread.png

jpgファイルリストを取得
------------------------------------

osモジュールを利用してファイルのリストを取得する。

imtools.pyというファイルを作成し下記の命令文を加える

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-

   import os

   def get_imlist(path):
       return [os.path.join(path, f) for f in os.listdir(path) if f.endswith('.jpg')]

ここで、利用されているosモジュールの関数などを説明。

.. function:: join(path1[, path2[, ...]])
   :module: os.path

   パスを結合する

   .. code-block:: python

      >>> import os
      >>> p = 'c:\'
      >>> f = 'foo.jpg'
      >>> os.path.join(p, f)
      'c:\foo.jpg'

.. function:: endswith(suffix)
   :module: str

   文字列strでsuffixの文字列で終了している場合、Trueを返す。そうでない場合はFalse

   .. code-block:: python

      >>> 'foo.png'.endswith('png')
      True
      >>> 'foo.png'.endswith()
      False

サムネイルの作成
-------------------------

.. code-block:: python

   pil_im.thumbnail((128, 128))

指定したサイズにサムネイルを作成してくれる

領域のコピーと貼り付け
--------------------------------

画像を切り抜くにはcrop()メソッドを利用する

.. code-block:: python

   box = (100, 100, 400, 400)
   region = pil_im.crop(box)

切り抜く領域は(左, 上, 右, 下)の座標を指定する。

画像を貼り付けるにはpaste()メソッドを利用する

.. code-block:: python

   pil_im.paste(region, box)

拡大縮小と回転
-------------------------------

画像のサイズを変更するには、resizeメソッドを利用する。その際、サイズをタプルで指定する

画像を回転するには、rotate()メソッドを利用する。引数は時計回りの角度(degree)で指定する

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-

   from PIL import Image
   import matplotlib.pyplot as plt

   #画像を読み込む
   pil_im = Image.open('empire.jpg')

   #画像をコピーする領域をタプルに格納
   region = (100, 100, 400, 400)
   #画像をコピーして格納
   cpy_im = pil_im.crop(region)
   #画像を回転して格納
   cpy_im = cpy_im.rotate(180)
   #コピーした位置と同じ位置に貼り付け
   pil_im.paste(cpy_im, region)

   #画像を表示
   plt.figure()
   plt.imshow(pil_im)
   plt.show()

.. image:: /cv/rotePaste.png

matplotlib
=======================

matplotlibはpythonでグラフを書くためのモジュール

通常、下記のように宣言する

.. code-block:: python

   import matplotlib.pyplot as plt

画像の表示や点の表示は下のソースの通り

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-
   
   from PIL import Image
   import matplotlib.pyplot as plt
   import numpy as np
   
   im = np.array(Image.open('empire.jpg').convert('L'))
   
   #画像を表示する
   plt.subplot(121)
   plt.imshow(im)
   
   #グラフに点を表示する
   plt.plot([100, 200, 300], [200, 500, 200], 'r*')
   
   #グラフに線を描画する
   plt.plot([100, 200], [200, 500])
   
   #画像のヒストグラムを表示する
   plt.subplot(122)
   #ヒストグラムはビンの数も指定する
   plt.hist(im.flatten(), 128)
   
   #結果を表示
   plt.show()

.. image:: /cv/matplottest.png

**matplotlibの色コマンド**

================  ========================
コマンド              色
================  ========================
'b'                青
'g'                緑
'r'                赤
'c'                シアン
'm'                マゼンタ
'y'                黄
'k'                黒
'w'                白
================  ========================

**線のスタイル**

================  ========================
コマンド              線スタイル
================  ========================
'-'                実線
'- -'              破線
':'                点線
================  ========================

**マーカースタイル**

================  ========================
コマンド              マーカースタイル
================  ========================
'.'                点
'o'                丸
's'                四角
'*'                星
'+'                十字
'x'                バツ
================  ========================

表示された画像をクリックしたポイントを取得するには、plt.ginput(クリック数)を利用する

Numpy
==============================

PILモジュールを利用して読み込んだ画像は、Numpyモジュールのndarrayオブジェクトで
キャストすることにより、配列に変換することが出来る。

ndarrayを画像に戻すときは、PILのImage.fromarray(obj)メソッドを活用する。
Image.frombufferでもいいらしい。

配列による画像表現
--------------------------

* PIL形式の画像をarrayオブジェクトでキャストすることで配列に変換できる
* 配列へのアクセス方法は通常のリスト型と同じ
* 配列から画像へ変換するにはImage.fromarray(np.uint8(im))を利用

念のため配列をキャストしたほうがよい

画面のサイズ変更
--------------------------

PILの画像変換関数:im.resize(size)を利用すると簡単

.. code-block:: python

   def imresize(im, sz):
       """ array形式のimを受け取りサイズ(タプル)にサイズ変更する
       """
       pil_im = Image.fromarray(np.uint8(im))
       return np.array(pil_im.resize(sz))

ヒストグラム平坦化
----------------------------

画像全体の明暗のレベルを均一化するために、ヒストグラムの平坦化を行う。

* 画像の明度が同じようになるために利用する
* コントラストを向上させる

.. code-block:: python

   def histeq(im, nbr_bins=256):
       """ グレースケール画像のヒストグラム平坦化
           array形式のグレースケール画像を受け取る
       """
       #画像のヒストグラムを取得する
       imhist, bins = np.histogram(im.flatten(), nbr_bins, normed=True)
       cdf = imhist.cumsum()
       cdf = 255 * cdf / cdf[-1]
       #cdfを線形保管し、新しいピクセル値にする
       im2 = np.interp(im.flatten(),bins[:-1], cdf)
   
       return im2.reshape(im.shape), cdf

.. function:: histgram(a, bins=10, range=None, normed=False)
   :module: numpy

   ヒストグラムのデータを計算する

   a:配列

   normed:正規化するかどうか

.. function:: cumsum()
   :module: numpy

   累積度数分布に並び替える

.. function:: interp(x, xp, fp)
   :module: numpy

   一次元の線形保管を行う

.. code-block:: pyhon

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-
   
   from PIL import Image
   import numpy as np
   import matplotlib.pyplot as plt
   
   im = np.array(Image.open('empire.jpg').convert('L')).flatten()
   #ヒストグラムを取得する
   imhist, bins = np.histogram(im, 256, normed=True)
   cdf = imhist.cumsum()
   #正規化する
   cdf_norm = 255 * cdf / cdf[-1]
   #cdf_normを線形補完する
   im2 = np.interp(im.flatten(), bins[:-1], cdf_norm)
   
   plt.figure()
   plt.subplot(121)
   plt.plot(imhist)
   plt.subplot(122)
   plt.plot(cdf)
   plt.figure()
   plt.plot(im2,'.')
   plt.show()

.. image:: /cv/cdf.png

.. image:: /cv/interp.png

平均画像
--------------

PILモジュールのImage.composite(image1, image2, mask)を利用するのが吉

画像の主成分分析
---------------------

* 主成分分析（Principal Component Analysis）は次元削減を行うのに有用な方法。
* 訓練データの次元削減をする
* 重要度順に並んだ座標軸に値を変換

**numpyではSVD(Singular Value Decomposition)を利用**

.. function:: svd(a)
   :module: numpy.linalg

   U:

   S:分散を返す

   V:写像行列を返す