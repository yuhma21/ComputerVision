======================
OpenCV
======================

:著者: yuhma21

マウスイベントを利用する
============================================

**マウスイベントを利用する流れ**

#. マウスイベントの通知を受ける関数を定義する

   関数には必ずイベント内容を受け取る(event,x,y,flags,param)の5変数を用意する

#. ウィンドウの名前を決めるcv2.namedWindow('')を実行する
#. イベント通知を定義するcv2.setMouseCallback('',通知先関数)を実行する

*マウスイベント一覧*

==========================  =============================
イベント                        説明
==========================  =============================
cv2.EVENT_LBUTTONDBLCLK      左ボタンをダブルクリック
cv2.EVENT_LBUTTONDOWN        左ボタンを押した時
cv2.EVENT_LBUTTONUP          左ボタンを離した時
cv2.EVENT_RBUTTONDBLCLK      右ボタンをダブルクリック
cv2.EVENT_RBUTTONDOWN        右ボタンを押した時
cv2.EVENT_RBUTTONUP          右ボタンを離した時
cv2.EVENT_MBUTTONDBLCLK      中ボタンをダブルクリック
cv2.EVENT_MBUTTONDOWN        中ボタンを押した時
cv2.EVENT_MBUTTONUP          中ボタンを離した時
cv2.EVENT_MOUSEMOVE          マウスを動かした時
==========================  =============================

イベントの種類は次のプログラムでも見れる

.. code-block:: python

   >>> import cv2
   >>> events = [i for i in dir(cv2) if 'EVENT' in i]
   >>> print events
   ['EVENT_FLAG_ALTKEY', 'EVENT_FLAG_CTRLKEY', 'EVENT_FLAG_LBUTTON', 'EVENT_FLAG_MBUTTON', 
   'EVENT_FLAG_RBUTTON', 'EVENT_FLAG_SHIFTKEY', 'EVENT_LBUTTONDBLCLK', 'EVENT_LBUTTONDOWN', 
   'EVENT_LBUTTONUP', 'EVENT_MBUTTONDBLCLK', 'EVENT_MBUTTONDOWN',
   'EVENT_MBUTTONUP', 'EVENT_MOUSEMOVE', 'EVENT_RBUTTONDBLCLK', 'EVENT_RBUTTONDOWN', 
   'EVENT_RBUTTONUP']

*サンプルコード*

.. code-block:: python

   import cv2
   import numpy as np
   
   drawing = False # true if mouse is pressed
   mode = True # if True, draw rectangle. Press 'm' to toggle to curve
   ix,iy = -1,-1
   
   # マウスが押された時に発動するコールバック関数
   # 必ず5つの決まった引数を取る
   def draw_circle(event,x,y,flags,param):
       # 他の変数が取りたいときにはグローバル変数で受け取る
       global ix,iy,drawing,mode
   
       if event == cv2.EVENT_LBUTTONDOWN:
           drawing = True
           ix,iy = x,y
   
       elif event == cv2.EVENT_MOUSEMOVE:
           if drawing == True:
               if mode == True:
                   cv2.rectangle(img,(ix,iy),(x,y),(0,255,0),-1)
               else:
                   cv2.circle(img,(x,y),5,(0,0,255),-1)
   
       elif event == cv2.EVENT_LBUTTONUP:
           drawing = False
           if mode == True:
               cv2.rectangle(img,(ix,iy),(x,y),(0,255,0),-1)
           else:
               cv2.circle(img,(x,y),5,(0,0,255),-1)

   img = np.zeros((512,512,3), np.uint8)
   # ウィンドウの名前を決定する
   cv2.namedWindow('image')
   # 名前をつけたウィンドウにマウスイベントを通知する関数を定義する
   cv2.setMouseCallback('image',draw_circle)
   
   while(1):
       cv2.imshow('image',img)
       k = cv2.waitKey(1) & 0xFF
       if k == ord('m'):
           mode = not mode
       elif k == 27:
           break
   
   cv2.destroyAllWindows()

