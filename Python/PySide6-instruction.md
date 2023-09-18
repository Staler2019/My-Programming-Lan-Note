# PySide6

## What is PySide6

API 幾乎跟 PyQt6 相同，差別在 License
PySide6 為 LGPL、PyQt6 則是 GPL/commercial

### Requirements

> 預設在 windows 上建置

```shell
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

          ```shell
          pyside6-uic mainwindow.ui -o ui_main_window.py
          ```

          ```python
          #!/usr/bin/env python

          # file: uipy_main.py
          
          import sys
          from PySide6.QtWidgets import QApplication

          from controllers.main_window_controller import MainWindowController


          if __name__ == '__main__':
            app = QApplication(sys.argv)
            
            window = MainWindowController()
            window.show()

            sys.exit(app.exit())
          ```

          ```python
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

            ```python
            #!/usr/bin/env python
            
            # file: ui_main.py

            import sys
            from PySide6 import QtWidgets
            from PySide6.QtUiTools import QUiLoader

            loader = QUiLoader()

            app = QtWidgets.QApplication(sys.argv)
            window = loader.load("mainwindow.ui", None)
            window.show()
            app.exec()
            ```

    3. 手刻: 範例 `main.py` 可見下方 `Widgets 之間溝通: Signal & Slot` 處

## Thread 管理

### 寫 APP 的核心概念，UI 只處理 UI 的事

1. UI 如果拿來處理資料，會造成 UI 當機，小資料沒事，不過大資料就嚴重了，所以一定要開 thread
2. 非 UI thread 處理 UI updates 會造成生命周期未被觸發，導致 UI 不會更新

### Example Worker Template

[pyside6_worker_template.py](./pyside6_worker_template.py)

## Widgets

### Widgets 之間溝通: Signal & Slot

> 只能存在 Qt 週期中

- Signal (tx, 發送)
- Slot (rx, 接收)

```python
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

### Widget 組成

Widget 可包含或被包含於任一 Widget 或是 Layout 中

- Qt 提供的 QTableView, QListView, QTreeView, QColumnView 或是其 Widget 都可以以自己的資料型態定義 model，以繼承及重寫(override)方式，讓 model 底層實作有了彈性，當然，有時候沒有必要，本來的就很好用

  ```python
  # file: controllers/table_view_example.py

  from PySide6 import QtWidgets

  from models.table_model_example import MyTableModel


  class Widget3(QtWidgets.QWidget):
    def __init__(self, parent=None):
        super().__init__(parent)

        #...

        self.tableView = QTableView()

        # 不顯示 vertical header
        self.tableView.verticalHeader().setVisible(False)

        self.tableView.horizontalHeader().setSectionResizeMode(
            # # 根據內容大小伸縮欄寬
            # QtWidgets.QHeaderView.ResizeToContents
            
            # 忘記了
            QtWidgets.QHeaderView.Stretch
        )
        # 伸縮最後一欄至填滿 QTableView 橫向長度
        self.tableView.horizontalHeader().setStretchLastSection(True)

        # 設定 QTableView 點按後選擇 Row，預設為格子
        self.tableView.setSelectionBehavior(QtWidgets.QAbstractItemView.SelectRows)
        
        # 設定 QTableView 一次只能選擇一項
        self.tableView.setSelectionMode(QtWidgets.QAbstractItemView.SingleSelection)

        # ... layout add widget
  ```

  ```python
  # file: models/table_model_example.py

  from PySide6 import QtCore
  from PySide6.QtCore import Qt


  class MyTableModel(QtCore.QAbstractTableModel):
    def __init__(self, col_names):
        super().__init__()

        self._data = []
        self._col_names = col_names

    ######## necessary functions ########
    def flags(self, index):
        '''
        資料的可處裡形式
        '''
        # return Qt.ItemIsSelectable | Qt.ItemIsEnabled | Qt.ItemIsEditable
        return Qt.ItemIsSelectable | Qt.ItemIsEnabled

    def setData(self, index, value, role):
        '''
        應該是 QAbstractTableModel 更改資料的方式
        '''
        if role == Qt.EditRole:
            self._data[index.row()][index.column()] = value
            return True

    def data(self, index, role=Qt.DisplayRole):
        '''
        應該是 QTableView 顯示 QAbstractTableModel 資料的方式
        '''
        if index.isValid():
            if role == Qt.DisplayRole or role == Qt.EditRole:
                value = self._data[index.row()][index.column()]
                return str(value)

    def rowCount(self, index):
        return len(self._data)

    def columnCount(self, index):
        return len(self._colsName)

    def headerData(self, section, orientation, role):
        """
        QTableView vertical header & horizontal header 應該顯示什麼

        input: section start from 0
        """
        if role == Qt.DisplayRole:
            if orientation == Qt.Horizontal:
                return str(self._colsName[section])

            if orientation == Qt.Vertical:
                return str(section + 1)  # 沒有設定 row name 所以我 return index
    ######## necessary functions ########

    # insert, remove, modify...
    def insertRow(self, rowId: int, rowData: list):
        self.beginInsertRows(QtCore.QModelIndex(), rowId, rowId)

        self._data.insert(rowId, rowData)
        self.idSet.add(rowData[0])

        self.endInsertRows()

        self.layoutChanged.emit()

        return self.length()
  ```

- Qt 如果要實作上述 View 或是 Widget 資料的顯示方式可以參考 ItemDelegate 相關實作，更改 paint()。
  - 例如在 QListWidget 中顯示 Card View，我們需要更改 item 的實作。藉由從 QStyledItemDelegate 繼承而來的 CardView，需要重寫(override)其 sizeHint() 及 paint()

  ```python
  # file: constants/role.py

  from PySide6 import QtCore

  TitleRole = QtCore.Qt.UserRole + 1
  TitlePicRole = QtCore.Qt.UserRole + 2
  SummaryRole = QtCore.Qt.UserRole + 3
  TagsRole = QtCore.Qt.UserRole + 4 
  ```

  ```python
  # file: models/cart_item_model.py

  from PySide6.QtGui import QPixmap
  from PySide6.QtWidgets import QListWidgetItem

  from constants.roles import *


  class CardItemModel(QListWidgetItem):
    def __init__(self, title: str, tags: list, title_pic: QPixmap, summary: str):
        super().__init__()

        # QListWidgetItem with roles -> cannot be used like model
        self.title = title
        self.title_pic = title_pic
        self.tags = tags
        self.summary = summary

    @property
    def title(self):
        return self.title(TitleRole)

    @title.setter
    def title(self, title):
        self.setData(TitleRole, title)

    @property
    def summary(self):
        return self.summary(SummaryRole)

    @summary.setter
    def summary(self, summary):
        self.setData(SummaryRole, summary)

    @property
    def tags(self):
        return self.tags(TagsRole)

    @tags.setter
    def tags(self, tags):
        self.setData(TagsRole, tags)

    @property
    def title_pic(self):
        return self.title_pic(TitlePicRole)

    @title_pic.setter
    def title_pic(self, title_pic):
        self.setData(TitlePicRole, title_pic) 
  ```

  ```python
  # file: views/card_view.py

  from PySide6.QtCore import QSize, Qt, QRect
  from PySide6.QtGui import QPen, QFont
  from PySide6.QtWidgets import QStyledItemDelegate

  from constants.roles import *


  class CardView(QStyledItemDelegate):
    """
    not RWD implemented
    """
    width = 320
    height = 470

    def sizeHint(self, option, index):
        return QSize(self.width, self.height)

    def paint(self, painter, option, index) -> None:
        '''
        記得 painter.save() 及 painter.restore()，由於 painter 是 Qt 類似全域變數的實作，所以要避免影響其他物件的 painter
        '''
        if index.isValid():
            painter.save()

            title = index.data(TitleRole)
            title_pic = index.data(TitlePicRole)
            summary = index.data(SummaryRole)
            tags = index.data(TagsRole)

            rect = option.rect

            painter.setPen(QPen(Qt.black))
            painter.setBrush(Qt.NoBrush)  # background color
            painter.drawRoundedRect(rect, 7, 7, Qt.AbsoluteSize)

            pic_width = 300
            pic_height = 132
            title_height = 125
            tags_height = 45
            summary_height = self.height - pic_height - title_height - tags_height - 25
            pic_rect = QRect(option.rect.left() + 10, option.rect.top() + 7, pic_width, pic_height)
            title_rect = QRect(option.rect.left() + 10, option.rect.top() + pic_height + 5 * 2 + 2,
                               pic_width,
                               title_height)
            tags_rect = QRect(option.rect.left() + 10,
                              option.rect.top() + pic_height + title_height + 5 * 2 + 2,
                              pic_width, tags_height)
            summary_rect = QRect(option.rect.left() + 10,
                                 option.rect.top() + pic_height + title_height + tags_height + 5 * 3 + 2,
                                 pic_width,
                                 summary_height)

            painter.drawPixmap(pic_rect, title_pic)

            painter.setPen(QPen(Qt.black))
            painter.setFont(QFont('Arial', 14, QFont.Bold))
            painter.drawText(title_rect, Qt.AlignCenter | Qt.TextWordWrap, title)

            painter.setPen(QPen(Qt.blue))
            painter.setFont(QFont('Arial', 9, QFont.Bold, ))
            painter.drawText(tags_rect, Qt.AlignHCenter | Qt.AlignTop | Qt.TextWordWrap,
                             ' | '.join(tags))

            painter.setPen(QPen(Qt.black))
            painter.setFont(QFont('Arial', 12))
            painter.drawText(summary_rect, Qt.AlignLeft | Qt.AlignTop | Qt.TextWordWrap, summary)

            painter.restore()
  ```

  ```python
  # file: controllers/card_list_view_widget_example.py

  from PySide6 import QtWidgets

  from views.card_view import CardView


  class Widget4(QtWidgets.QWidget):
    def __init__(self, parent=None):
        super().__init__(parent)

        # ...

        self.list_widget = QtWidgets.QListWidget()
        self.list_widget.setItemDelegate(CardView(self))
        # 設定 item 間距
        self.list_widget.setSpacing(10)
        # 這個不懂
        self.list_widget.setViewMode(QListWidget.IconMode)
        # 禁止拖曳
        self.list_widget.setDragEnabled(False)

        # ... layout add widget
  ```

- 若要建立由某些 Widget 更改而來的顯示方式，可以替換該 QObject 的 paintEvent()
- 更改 painEvent() 及 paint() 中 QRect 幾乎要手動實作邊界確認，且常常調半天仍重疊顯示於其他已顯示的畫筆筆跡上，導致<span style="color:red">此項實作十分困難</span>

## MVC 學習參考

<https://stackoverflow.com/questions/26698628/mvc-design-with-qt-designer-and-pyqt-pyside>

## Reference

1. My experiences
2. <https://juejin.cn/post/7166899345185308702>
3. <https://blog.csdn.net/baidu_36499789/article/details/119486355>
4. <https://stackoverflow.com/questions/73179661/unable-remove-row-in-proper-order-from-qtableview>
5. <https://blog.csdn.net/zhouzhiwengang/article/details/119741602>
6. <https://stackoverflow.com/questions/72951147/pyside-tableview-and-pandas-add-row>
