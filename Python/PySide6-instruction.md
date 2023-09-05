# PySide6

## What is PySide6

API 幾乎跟 PyQt6 相同，差別在 License
PySide6 為 LGPL、PyQt6 則是 GPL/commercial

### Requirements

> 預設在 windows 上建置

- MS C++ (底層 LIB)

- ```.ps1
  pip install PySide6
  ```

## 建立 UI 的方法

1. QML: 像 CSS 一樣一個個建立屬性及條件判斷
   - Qt Creator: 要錢
2. Qt Designer

    > [click to download page](https://build-system.fman.io/qt-designer-download)

    1. create .ui file
    2. 兩種 ui 互動方式
       >
       > - Pros
       >    1. 有視覺化對新手來說比較容易上手
       > - Cons
       >    1. 換成 py 檔，view 檔案不易閱讀
       >    2. 使用 ui loader，控制 button 等 widgets 時不知道名字，須一直開著 Qt Designer 且 IDE 會一直跳紅字
       >
       1. 轉成 py 檔，eg.

          ```.ps1
          pyside6-uic mainwindow.ui -o ui_main_window.py
          ```

          ```.py
          #!/usr/bin/env python

          # file: main.py
          
          import sys
          from PySide6.QtWidgets import QApplication

          from controllers.main_window_controller import MainWindowController


          if __name__ == '__main__':
            app = QApplication(sys.argv)
            
            window = MainWindowController()
            window.show()

            sys.exit(app.exit())
          ```

          ```.py
          #!/usr/bin/env python

          # file: controllers/main_window_controller.py

          from PySide6 import QtWidgets

          from views import ui_main_window


          class MainWindowController(QtWidgets.QWidget, ui_main_window.Ui_Form):
            def __init__(self):
                super().__init__()

                self.setupUi(self)
          
          ```

       2. loader.load('$UI_FILE')，eg.

            ```.py
            #!/usr/bin/env python
            
            # file: main.py

            import sys
            from PySide6 import QtWidgets
            from PySide6.QtUiTools import QUiLoader

            loader = QUiLoader()

            app = QtWidgets.QApplication(sys.argv)
            window = loader.load("mainwindow.ui", None)
            window.show()
            app.exec()
            ```

    3. 手刻

## Widget 之間溝通: Signal & Slot

> 只能存在 Qt 週期中

- Signal (tx, 發送)
- Slot (rx, 接收)

```.py
#!/usr/bin/env python
            
# file: controllers/signal_example.py

import sys
from PySide6 import QtCore, QtWidgets


class Widget1(QtWidgets.QWidget):
    helloWorldSignal = QtCore.Signal(str)
    finishSignal = QtCore.Signal()

    def __init__(self, parent=None):
        super().__init__(parent)
        # ...

        self.helloWorldSignal.emit('HelloWorld!')

        self.finishSignal.emit()


class Widget2(QtWidgets.QWidget):
    def __init__(self, parent=None):
        super().__init__(parent)
        # ...

        self.lineEdit = QtWidgets.QLineEdit(self)

        self.widget1 = Widget1(parent=self)
        self.widget1.helloWorldSignal.connect(self.printStr)

    @Slot(str)
    def printStr(self, helloStr: str):
        self.lineEdit.setText(helloStr)

    @Slot()
    def widget1Fin(self):
        print('widget1 finished')


if __name__ == '__main__':
    app = QtWidgets.QApplication(sys.argv)
    window = Widget2()
    window.show()
    app.exec()
```

## Thread 管理

### 寫 APP 的核心概念，UI 只處理 UI 的事

1. UI 如果拿來處理資料，會造成 UI 當機，小資料沒事，不過大資料就嚴重了，所以一定要開 thread
2. 非 UI thread 處理 UI updates 會造成生命周期未被觸發，導致 UI 不會更新

### Example Worker Template

[pyside6_worker_template.py](./pyside6_worker_template.py)

## Reference

1. <https://juejin.cn/post/7166899345185308702>
2. <https://blog.csdn.net/baidu_36499789/article/details/119486355>
3. My experiences
