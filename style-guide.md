# Style Guide



## 編程風格規範

在編寫CMAPI代碼的時候，無論是系統代碼抑或自動程式，都應該嘗試符合這些標準所指定的格式進行修改及增加程式碼，遵循特定的編程風格將有助於程序員閱讀和理解符合樣式的源代碼，並有助於避免引入錯誤。

本規範著重在最小的區域閱讀更多代碼，以確保在比賽時能夠快速閱讀，因此代碼使用較小的縮進空隙以及空格，以便在同一視窗內顯示更多的代碼。

### 空白

沒有Tab，一行末尾沒有空格。  
Windows風格的換行符（'\r\n'）。

### 線長

80個字符或更少，單行註解或區域註解超越1至2個字符是可以接受的。

### 縮進

每個邏輯級別有兩個空格。

請注意，類可見性和`goto`標籤不會消耗邏輯級別，但`switch` `case`標籤會消耗邏輯級別。見下面的例子。

### 控制結構

使用 K&R支撐樣式，但是控制關鍵字`if`、`for`、`while`和`switch`後面不允許有空格，括號和花括號之間沒有多餘的空格。

```cpp
if(...){
}else if(...){
}else{
}

while(...){
}

do{
}while (...);

for(...; ...; ...){
}

switch(...){
  case 1:{
    // When you need to declare a variable in a switch, put the block in braces.
    int var;
    break;
  }
  case 2:
    ...
    break;
  default:
    break;
}
```

### 方法、枚舉和結構

在C / C ++中，公開方法名稱應使用小駝峰式命名法 - camelCase，系統方法名稱應放置底線在開頭並使用下劃線命名法 - \_under\_score。類型名稱和參數名稱應使用單個空格字符分隔。

```cpp
template <typename T>  // Templates on own line.
static int myFunction(const firstString& str,
                      second::SomeThing<const char*>&& charBuffer,
                      third* optionalThing = nullptr){
  ...
}

int _system_method(const firstString& str,
                   second::SomeThing<const char*>&& charBuffer,
                   third* optionalThing = nullptr){
  ...
}
```

枚舉和結構名稱應使用全小寫，以及使用下劃線命名法。

```cpp
typedef enum cool_type_e{
  COOL_THING_1,
  COOL_THING_2,
  COOL_THING_3
} cool_type_e_t;

struct amazing_struct{
  firstString _child_str_;
  ...
}
```

枚舉名稱後面需附加 `_e` ，如果需要定義枚舉變數，則需要再加上 `_t`。枚舉標識符應使用大寫以及使用下底線命名法 - UNDER\_SCORE。結構子變數後面需要增加下劃線。

### 指示詞、常數與變數命名

特別類型包括**馬達插線位置**、**馬達**、**馬達組**及**PID控制**4個類型，分別擁有以下四個命名前綴`port` 、`mtr`、`group`及`pid`。

```cpp
// port number
#define portGoodMotor 1

// motor variable
const pros::Motor mtrGoodMotor(...);

// motor group variable
const std::vector<pros::Motor> groupNewSet = {...};

// PID variable
PID pidVeryFast = PID(...);

const int CONST_VAR = 0;// normal read, no write access
int _system_variable = 1; // system read, system write
int ReadOnly = 2; // normal read, system write
int freeToUse = 3; // normal read, normal write
```



### 註解

```cpp
int variable = 0; // a prefect comment

// a ver good and very long comment
// some time we need two lines
int useful = 1;

/**
 * A short description is very helpful
 *
 * \param str
 *        the first string to import
 * \param charBuffer
 *        put many chars on it.
 *        two lines of description are also allowed
 * \param optionalThing
 *        optional object
 *        if do: very good
 *        else : nuulptr
 * 
 * \return an integer that you want.
 */
static int myFunction(const firstString& str,
                      second::SomeThing<const char*>&& charBuffer,
                      third* optionalThing = nullptr){
  ...
}
```

