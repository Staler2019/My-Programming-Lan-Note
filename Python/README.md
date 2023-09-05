# Python

## 環境

- 簡稱
    eg. Python 3.11 常簡稱 python311

- 體系與套件管理工具
  1. Python3 & pip (from Pypl) (這裡不談 Python2)
     - 執行檔名稱: `python`、`python3`、`python311`
     - 套件管理工具執行檔名稱: `pip`、`pip3`

  2. Anaconda & conda
     - 套件穩定、比較舊(通常是 Pypl 的前一兩個版本)
     - 有 miniconda 等，較不占空間的選擇

> 下文以 pip 體系作為主軸
---

### 虛擬環境

> 每個專案都建議建立虛擬環境，這樣每個專案的套件版本才不會因為新舊時間差異而產生版本衝突，會很難解決

1. 舊 Python 版本需安裝 virtualenv 套件 
2. `python -m venv $虛擬環境資料夾名稱`，eg.

    ```.ps1
    python -m venv venv
    ```

3. activation
   - `windows`

        ```.bat
        .\venv\Scripts\activate.bat
        ```

        or

        ```.ps1
        .\venv\Scripts\Activate.ps1
        ```

   - `linux` `mac`

        ```.sh
        source ./venv/bin/activate
        ```

4. deactivation

    ```.sh
    deactivate
    ```

### 套件安裝

- `pip install $套件名稱`，可加上判斷確保套件版本，eg.

    ```.sh
    pip install PySide6==6.5.2
    ```

- `requirements.txt`
  - 描述所有需求套件
  - 安裝套件方式 `pip install -r requirements.txt`
  - 產生方式
      1. 自己手刻
      2. 由目前環境所有套件產生 `pip freeze > requirements.txt`

## Pythonic: Python 特有寫法

### 先備知識，基礎資料結構

#### list

- [$值, $值, ...]
- 可以當作 stack 使用 (pop、append)
- 可以當作 queue 使用 (popleft、append)
- 可以在任何地方進行 O(N) cudr

#### dict

- {$key: $value, $key: $value, ...}
- key & value
- value 可以是任何資料結構
- key 只能是 Number, str

#### tuple

- ($值, $值, ...)
- 值與順序不可調換，想更改只能重建tuple (immutable object)

### Pythonic

指 Python 可執行的特殊寫法，由於效率較佳，所以多人使用。但也導致其他語言看 Python 會看不懂的主要原因

- 三元運算子

  ```.py
  def isTrue():
    return True

  check_bool = $真值 if isTrue() else $假值
  ```

- list 迭代 `list_obj[起始位置:終點位置:間隔]`

  ```.py
  a = [1, 2, 3, 4]

  print(a[0])       # 1
  print(a[-1])      # 4
  print(a[::-1])    # [4, 3, 2, 1]
  print(a[1:3:-1])  # []
  print(a[1::2])    # [2, 4]
  ```

- 從陣列製造基礎資料結構

  ```.py
  class ExObj:
    def __init__(self, num: int):
      self.num = num
      self.text = ''
  
  a = [1, 2, 3, 4]

  obj_list = [ExObj(i) for i in a]
  
  for o in obj_list:
    print(o.num)

  # 1
  # 2
  # 3
  # 4
  ```

  - 從陣列篩選製造基礎資料結構

    ```.py
    class ExObj:
      def __init__(self, num: int):
        self.num = num
        self.text = ''
  
    a = [1, 2, 3, 4]

    obj_list = [ExObj(i) for i in a if i > 3]

    new_a = [o.num for o in obj_list]
    # [4]
    ```
  
  - 須注意用法差異

    ```.py
    a = [1, 2, 3, 4, 5, 6, 7, 8, 9]

    print([i for i in a if i > 5])
    # [6, 7, 8, 9]

    print([i if i > 5 else 99 for i in a])
    # [99, 99, 99, 99, 99, 6, 7, 8, 9]

    ```

#### 延伸閱讀

- `for` + `enumerate`
- `for` + `zip`
- `is` vs. `==`

## 推薦 IDE

- PyCharm (JetBrain): has better refactor and better VCS integration
- VS Code: power saving, fluent, and large extension market
  - `isort` sort imports
  - `jupyter` help you test language grammars
  - `Pylance` basis
  - `Python` basis
  - `Python Environment Management` view installed packages
  - `Python Indent` correct Python Indent
