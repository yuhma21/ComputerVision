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

Numpy
==============================

Numpyを利用して配列を扱う