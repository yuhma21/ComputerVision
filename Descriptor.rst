=================================
Descriptor
=================================

著者:yuhma21

画像間の対応付けの方法

#. Harrisコーナー検出
#. SIFT

パノラマ画像の作成、拡張現実、3次元復元などに利用される

Harrisコーナー検出器
===============================================

* 周辺に複数の方向のエッジ、コーナーを求める

x方向、y方向の各点の傾きを計算し、下記に定義した式に代入する

.. math::

   M_I = \nabla I \nabla I^{T} = \left[ \begin{array}{cc} I_x^{2} & I_x I_y \\

   I_x I_y & I_y^{2} \end{array} \right]

   \overline{M_I} = W \times M_I

そして、定義した式にW（ガウス分布）等をかけ、重み行列とする。 :math:`\overline{M_I}` はHarris行列と呼ばれる。
Harris行列の固有値でコーナーを判断する。

**Harris行列の判別方法**

===================  ===================  ============================
:math:`\lambda _1`   :math:`\lambda _1`    判定
===================  ===================  ============================
大きな正の値            大きな正の値             xにコーナーが存在する
大きな値               0に近い値               xにエッジがある
0に近い値              0に近い値               何もない
===================  ===================  ============================

通常は固有値を計算しないで済むように下記の判別関数を使う

.. math::
   
   \frac{det(\overline{M_I})}{trace(\overline{M_I})}


detは行列の判別式、traceは対角成分の和を示す

値が大きい場合はコーナーがあり、中位はエッジで、0に近い箇所は平坦といったところでしょうか。

.. function:: gaussian_filter(input, sigma, order=0, output=None, mode='reflect', cval=0.0)
   :module: scipy.ndimage.filters

   多次元のガウシアンフィルタ

   Parameters :

      input : フィルタを×配列を指定する
      
      sigma : 各次元のシグマの値をリストで代入する(x,y,z,......)

      ortder : 出力を行う次元のリストを指定する(1,0,0,...)xだけ出力

      output : フィルタをかけた配列を出力する配列を指定する

      mode : {‘reflect’, ‘constant’, ‘nearest’, ‘mirror’, ‘wrap’}, optional

      cval : scalar, optional

   Returns :	
      gaussian_filter : ndarray

      インプットと同じ形の配列を返す

**ハリスのコーナー検出を行うコード**

.. code-block:: python
   :linenos:

   def compute_harris_response(im, sigma=3):
       """ グレースケール画像の各ピクセルについて
           Harrisコーナー検出器の応答関数を定義する
           im:numpy配列形式の画像
       """
       #微分係数
       imx = np.zeros(im.shape)
       filters.gaussian_filter(im, (sigma, sigma), (0, 1), imx)
       imy = np.zeros(im.shape)
       filters.gaussian_filter(im, (sigma, sigma), (1, 0), imy)
       #Harrisの行列成分を計算する
       Wxx = filters.gaussian_filter(imx * imx, sigma)
       Wxy = filters.gaussian_filter(imx * imy, sigma)
       Wyy = filters.gaussian_filter(imy * imy, sigma)
       #判別式と対角成分
       Wdet = Wxx * Wyy - Wxy ** 2 #判別式
       Wtr = Wxx + Wyy #トレース
   
       return Wdet / Wtr


x,y方向にそれぞれ微分した様子

左側はx方向なので、縦線のコーナーに強く陰影が出ています。

.. image:: /cv/harrisGauss.png

コーナー検出の様子（左：元画像、右：コーナー）

.. image:: /cv/harrisC.png

