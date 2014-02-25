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

.. function:: cumsum(a, axis=None, dtype=None, out=None)
   :module: numpy

   累積度数分布を計算する

   a:対象の配列

   axis:累積度数分布を行う方向を決める

   dtype:出力の型を決める

   .. code-block:: python
   
      >>> a = np.array([[3,2,1], [4,5,6]])
      >>> a
      array([[3, 2, 1],
             [4, 5, 6]])
      >>> np.cumsum(a)
      array([ 3,  5,  6, 10, 15, 21])

.. function:: interp(x, xp, fp)
   :module: numpy

   一次元の線形保管を行う。xpの写像fpの対応関係をxに適用する

   x:変換を適用する配列

   .. code-block:: python
   
      >>> import numpy as np
      >>> xp = [1, 3, 5]
      >>> fp = [2, 6, 10]
      >>> x = [2, 4]
      >>> np.interp(x, xp, fp)
      array([4., 8.])
      >>> #外挿はできない
      >>> x = [7, 8]
      >>> np.interp(x, xp, fp)
      array([10., 10.])
      >>> 当然、非線形な写像は扱えない
      >>> fp = [1, 9, 25]
      >>> x = [2, 4]
      >>> np.interp(x, xp, fp)
      array([5, 17])

.. code-block:: python

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

pickleモジュール
===========================

オブジェクトの状態を保存できる。学習済みのオブジェクトを一旦保存し、
再度、呼び出すときなどに非常に有効。

.. code-block:: python

  import pickle
  from sklern import svm
  clf = svm.SVC('linear')
  f = open('test.pkl', 'wb')
  plckle.dump(clf, f)
  f.close()

再度、呼び出すときは下記の通り

.. code-block:: python

   import pickle
   from sklern import svm
   f = open('test.pkl', 'rb')
   clf = plckle.dump(f)
   f.close()

withディレクティプを使って、closeの処理を省略してもよい

SciPy
=================================

SciPyはNumPy上に構築された、数値積分、最適化、統計、信号処理を行うパッケージ

画像をぼかす
-----------------------------

ガウシアンカーネルを利用してぼかす。

.. math::
   
   I_\sigma = I \times G_\sigma

   G_\sigma = \frac{1}{2\pi\sigma}e^{-(x^{2}+y^{2})/2\sigma^{2}}

画像の微分
--------------------

.. math::
   
   |\nabla |=\sqrt{I_x^{2}+I_y^{2}}

モルフォロジー
-----------------------

* モルフォロジーは基本図形を計測したり解析したりする画像処理手法
* 通常、2値画像に利用されるが、グレースケールでも良い
* 物体の数を数えたり、大きさを測定したりする。

モルフォロジーはscipy.ndimageモジュールのmorphogyにまとめてある

2値画像用の計数や測定関数はscipy.ndimageモジュールのmeasurementsにある

**画像の物体を数える**

画像を
:download:`ダウンロード</download/houses.png>`

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-
   
   from PIL import Image
   from scipy.ndimage import measurements, morphology
   import numpy as np
   import matplotlib.pyplot as plt
   
   #画像を読み込んで2値化
   im = np.array(Image.open('houses.png').convert('L'))
   im = 1 * (im < 128)
   
   labels, nbr_objects = measurements.label(im)
   print('number of objects:',nbr_objects)
   
   plt.imshow(labels)
   plt.show()

.. image:: /cv/morpho.png

コード中のmeasuments.labelメソッドが返すlabelは物体を認識した画像のNoを
ラベリングした配列を返す

例えば

.. code-block:: python

   [1, 0, 0, 1]
   [1, 0, 0, 0]
   [0, 0, 0, 0]
   [0, 0, 0, 1]

という画像があった場合、オブジェクト数は3となる。そしてそれをラベリングしたものが下記の通り

.. code-block:: python

   [1, 0, 0, 2]
   [1, 0, 0, 0]
   [0, 0, 0, 0]
   [0, 0, 0, 3]

上記の例では物体がつながっている箇所があるのでOpening演算を実施し、分離する

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-
   
   from PIL import Image
   from scipy.ndimage import measurements, morphology
   import numpy as np
   import matplotlib.pyplot as plt
   
   #画像を読み込んで2値化
   im = np.array(Image.open('houses.png').convert('L'))
   im = 1 * (im < 128)
   
   labels, nbr_objects = measurements.label(im)
   print('number of objects:',nbr_objects)
   
   im_open = morphology.binary_opening(im, np.ones((9, 5)), iterations=2)
   labels_open, nbr_objects_open = measurements.label(im_open)
   print('number of opening_objects:',nbr_objects_open)
   
   plt.subplot(221)
   plt.imshow(im)
   plt.subplot(222)
   plt.imshow(labels)
   plt.subplot(223)
   plt.imshow(im_open)
   plt.subplot(224)
   plt.imshow(labels_open)
   plt.show()

iterationは演算回数

.. image:: /cv/morphoopen.png

便利なSciPyモジュール
------------------------------------

.matファイルを読み書きする
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Matlabの.matファイルを読み書きできる

.. code-block:: python

  import scipy.io
  data = scipy.io.loadmat('test.mat')

配列を画像として保存する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

配列を画像として保存できる。miscにはlenaもいる

.. code-block:: python

   from scipy import misc
   import numpy as np

   lena = np.array(misc.lena())
   misc.imsave('lena.jpg', lena)

高度な例
============================
