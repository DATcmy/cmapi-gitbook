# API Document



## \#define

### limit

```cpp
#define limit(x,y) ;limitStart(x);y;limitEnd();
```

`limitStart`, `limitEnd`的綜合使用, 開啟執行時間限制, 執行指令y, 然後關閉限制 例如:

```cpp
limit(3600,
  forward(120) await;
  turnLeft(90) await;
);
```

此代碼表示**向前直行**和**轉左90度**需要在3600毫秒內完成, 否則將會停止並直接跳到外面

### lamdba

```cpp
#define lamdba []()
```

匿名函數的語法糖

### await

```cpp
#define await ;_lockthread();
```

為下一個指令提出鎖起線程的要求  
例如:

```cpp
lift(40);
forward(120) await;
turnLeft(90);
```

此代碼表示**升降台升起**和**向前直行**一起進行 , 等待**向前直行**指令完成後進行**轉左90度**, 無論**升降台升起**是否已經完成

### redonly

```cpp
#define redonly ;_redonly();
```

聲明下一個指令只適用於紅方區域的自動程序, 如果執行區域為藍區, 指令將不會被執行  
例如:

```cpp
redonly turnLeft(90) blueonly turnRight(90);
```

此代碼表示**轉左90度**只適用於紅色區域, 如果執行區域為藍區, 則會跳到**轉右90度**

### blueonly

```cpp
#define redonly ;_blueonly();
```

聲明下一個指令只適用於藍方區域的自動程序, 如果執行區域為紅區, 指令將不會被執行  
例如:

```cpp
redonly turnLeft(90) blueonly turnRight(90);
```

此代碼表示**轉左90度**只適用於紅色區域, 如果執行區域為藍區, 則會跳到**轉右90度**

### waiting

```cpp
#define waiting(x) ;waitUntil([]()->bool{return x;});
```

`waitUntil`的語法糖, 暫停線程並等待直至條件成立  
例如:

```cpp
lift(40);
forward(120);
waiting(LiftTarget == 0 && MoveTargetL == 0);
...
```

此代碼表示**升降台升起**和**向前直行**一起進行, 等待兩個指令都完成後才繼續以下部分的指令

## Arm

### 配置

#### 馬達與感應器

| type | name | detail |
| :--- | :--- | :--- |
| Motor | mtrArm | 手臂 |

### arm

```cpp
action arm(const short speed, const int waitms = 0);
```

指定手臂馬達速度

* **參數**: 
  * `speed`  

       手臂的馬達速度, 範圍 -127 ~ +127

  * `waitms`   

       持續毫秒, 可選, 預設為 0  

       不填寫 : 持續  

       填寫   : 持續ms毫秒, 然後停止
* **返回**: action結構以控制指令

## Config

### loadConfig

```cpp
bool loadConfig();
```

載入設定檔, 此動作會覆蓋部分受設定檔影響的參數

* **返回**: True 如果成功載入設定檔, 或者 False 如果不能打開或格式錯誤

### saveConfig

```cpp
bool saveConfig();
```

儲存設定檔, 此動作會將上次選擇的 自動程序, 顏色區域 以及 除錯模式閥門 記錄到設定檔中

* **返回**: True 如果成功儲存設定檔, 或者 False 如果不能打開

## Core

### action\\_brake\\_mode\\_e

此枚舉定義了不同類型的制動模式  
可使用於移動指令

| Enum |  |
| :--- | :--- |
| BRAKE | 0 |
| HOLD | 1 |
| RELEASE | 2 |

### action\_component\_type\_e

此枚舉定義了不同類型的制動模式

1. 讓機械人移動的部件，例如四輪或全向輪底架  
2. 以角度為單位的部件，例如升降台  
3. 開關狀態的部件，例如轉盤或夾 
4. 以力量為單位的部件，例如無限循環的滾輪或忽略角度的手臂  
5. 能維持轉動速度的部件，例如轉輪發射器  
6. 步驟性的部件，例如位能發射器  

| Enum | Value |
| :--- | :--- |
| COMPONENT\_MOVE | 0 |
| COMPONENT\_FIXED\_RANGE | 1 |
| COMPONENT\_POLARITY | 2 |
| COMPONENT\_POWER\_DRIVE | 3 |
| COMPONENT\_SPINNER | 4 |
| COMPONENT\_SHOOTER | 5 |

### handler

此結構包含一個動作生命週期所使用的參數, 包括數據更新,狀態檢查,結束停止的處理函式 及 開始數值和時間 及 使用者後續設定的自定義事件 等等. 使用 startHandler 函式激活, 系統會透過文字組成的金匙儲存在 \_handler\_map 字典中

#### fnc_update_

```cpp
std::function<int(handler)> fnc_update_;
```

生命週期中數據更新的處理函式, 用於取得動作進度以啟動 to 事件

#### fnc_loop_

```cpp
std::function<bool(handler,int)> fnc_loop_;
```

生命週期中狀態檢查的處理函式, 用於檢查動作是否完成以及更新馬達速度

#### fnc_stop_

```cpp
std::function<void(handler)> fnc_stop_;
```

生命週期中結束停止的處理函式, 用於停止動作並且進行制動

#### compare_operator_

```cpp
short compare_operator_;
```

原始感應器數據在動作進行時呈現上升 1 還是 下降 -1  
1 : target &gt; now, -1 : target &lt; now

#### start_value_

```cpp
int start_value_;
```

原始感應器數據在動作開始前的數值

#### start_time_

```cpp
uint32_t start_time_;
```

在動作開始前的時間值

#### waituntil_value_

```cpp
int waituntil_value_ =- 1;
```

使用者設定, 同步等待到此進度,  
-1 : 沒有設定, &gt;= 0 : 目前進度高於此值時將會跳出同步等待

#### waituntil_time_

```cpp
uint32_t waituntil_time_  = -1;
```

在動作開始前的時間值 -1 : 沒有設定, &gt;= 0 : 目前時間值高於此值時將會跳出同步等待

#### thread_run_

```cpp
bool thread_run_ = true;
```

生命週期是否繼續運行

#### fnc_event\_to_

```cpp
std::map<int,std::function<void()>> fnc_event_to_;
```

儲存自定義 to 事件  
int : 進度\(使用者單位\), std::function : 事件函式

#### fnc_event\_finish_

```cpp
std::function<void()> fnc_event_finish_ = []{};
```

儲存自定義完結事件, std::function : 事件函式

### action

此結構包含三個函式用於操作動作處理器, 作為動作處理器的介面, 可以讓使用者使用預設的函式進行操作, 包括設定同步等待限制以及自定義事件等等

#### id\_

```cpp
std::string id_ = "*";
```

動作處理器的唯一金匙

#### enable_wait\_until_

```cpp
bool enable_wait_until_ = true;
```

是否啟用同步等待的限制條件

#### awaitUntil

```cpp
action& awaitUntil(const int value = -1, const int time = -1);
```

設定同步等待的限制條件, 只需要進度值和時間其中一方完成, 便會跳過同步等待, 兩種限制必須填寫其中一種

#### to

```cpp
action& to(const int value, const std::function<void()> event);
```

設定觸發事件, 當進度值超過 value 數值時將會呼叫 event 函式, 事件處理函式會在動作的處理線程中進行, 運行阻塞性指令會阻塞檢測循環, 也不能進行同步的動作式函數

#### finish

```cpp
action& finish(const std::function<void()> event);
```

設定結束事件, 函式會在動作停止後呼叫, 並且可以運行動作函式

#### forceStop

```cpp
action& forceStop();
```

立即停止動作處理器並結束動作生命週期

### startHandler

```cpp
action startHandler(handler h, std::string key);
```

高級函數, 啟動動作處理器並設定處理器的唯一金匙

* **參數**: 
  * `h`   

       即將執行的處理器, 必須提供完整的生命週期, 詳情請查看處理器的建構及使用

  * `key`  

       處理器的唯一識別金匙,以文字而組成,多數為四個字
* **返回**: action控制介面如果成功開始動作的生命週期

### limitStart

```cpp
void limitStart(int ms);
```

開始執行時間限制, 此動作會清空上一次的時間限制記錄

* **參數**: 
  * `ms`   

       限制時間

### limitEnd

```cpp
void limitEnd();
```

結束指令時間限制

### isTimeup

```cpp
bool isTimeup();
```

取得現在或上一次指令是否超過執行時間限制

* **返回**: True 如果超過限制時間, 否則 False

### wait

```cpp
void wait(int ms);
```

暫停線程並等待所設定的時間\(以毫秒為單位\)  
指令與pros::delay不同, wait指令會配合限時控制

* **參數**: 
  * `ms`   

       等待的時間

### waitUntil

```cpp
void waitUntil(const std::function<bool()> condition);
```

暫停線程並等待直至條件成立

* **參數**: 
  * `condition`   

       等待條件建構及使用

### waitUntilAboveSettled

```cpp
void waitUntilAboveSettled();
```

暫停線程並等待所有指令完成

## LCD

### format

```cpp
char* format(const char* f, ...);
```

格式化字符串

* **參數**: 
  * `f`   

       格式化的字符串

  * `...`   

       格式化字符串的可選參數列表
* **返回**: 格式化的字符串

### log

```cpp
template <typename... Params>
void log(std::int16_t line, const char* fmt, Params... args);
```

在LCD屏幕上顯示格式化的字符串

* **參數**: 
  * `line`   

       顯示文字的行\[0-7\]

  * `fmt`   

       格式化的字符串

  * `...`   

       格式化字符串的可選參數列表

### clearLog

```cpp
void clearLog();
```

清空LCD屏幕上的所有內容

### waitScreenBtnPress

```cpp
uint8_t waitScreenBtnPress();
```

等待直至 LCD屏幕的按鈕狀態發生變化

* **返回**: 新的按鈕狀態位掩碼

### isLeftBtnPress

```cpp
uint8_t isLeftBtnPress();
```

返回屏幕左側按鈕是否被按下

* **返回**: 位掩碼 &gt; 0 如果正在按下

### isCenterBtnPress

```cpp
uint8_t isCenterBtnPress();
```

返回屏幕中間按鈕是否被按下

* **返回**: 位掩碼 &gt; 0 如果正在按下

### isRightBtnPress

```cpp
uint8_t isRightBtnPress();
```

返回屏幕右側按鈕是否被按下

* **返回**: 位掩碼 &gt; 0 如果正在按下

### initLCD

```cpp
void initLCD();
```

初始化LCD屏幕

## Move

### 配置

#### 變數初始化

| type | name | detail |
| :--- | :--- | :--- |
| PID | pidMove | 直行指令使用 平衡輪差距 PID 控制類 |
| PID | pidLF | 直行指令使用 左前輪 PID 控制類 |
| PID | pidRF | 直行指令使用 右前輪 PID 控制類 |
| PID | pidTurnNear | 轉彎指令短距離 PID 控制類 |
| PID | pidTurnFar | 轉彎指令長距離 PID 控制類 |
| double | leftMargin | 向左轉偏移加成 |
| double | rightMargin | 向右轉偏移加成 |

#### 馬達與感應器

| type | name | detail |
| :--- | :--- | :--- |
| Motor | mtrLeftF | 左前輪 |
| Motor | mtrLeftR | 左後輪 |
| Motor | mtrRightF | 右前輪 |
| Motor | mtrRightR | 右後輪 |
| int | portGyro | 陀螺儀 |

### initMove

```cpp
void initMove();
```

初始化移動指令

### resetMove

```cpp
void resetMove();
```

重設移動指令

### drive

```cpp
action drive(const float cm,
             const short lfspeed, const short lrspeed,
             const short rfspeed, const short rrspeed,
             const action_brake_mode_e_t brakemode = HOLD,
             int tick = 0);
```

向前直行一定距離, 然後停止

* **參數**: 
  * `cm`   

       移動距離, 厘米, 不能負數

  * `lfspeed`  

       左前輪子的馬達速度, 範圍 -127 ~ +127

  * `lrspeed`  

       左後輪子的馬達速度, 範圍 -127 ~ +127

  * `rfspeed`  

       右前輪子的馬達速度, 範圍 -127 ~ +127

  * `rrspeed`  

       右後輪子的馬達速度, 範圍 -127 ~ +127

  * `brakemode`   

       設定馬達停止後的制動方式, 可選, 預設為 HOLD

  * `tick`   

       移動距離\(單位tick\), 可選, 預設為 0  

       不填寫 : 距離跟從cm, 並由cm計算出實際移動多少tick  

       填寫   : 覆蓋cm, 不能負數
* **返回**: action結構以控制指令

### forward

```cpp
action forward(const float cm,
               const short speed = 0,
               const action_brake_mode_e_t brakemode = HOLD,
               int tick = 0);
```

向前直行一定距離, 然後停止

* **參數**: 
  * `cm`   

       移動距離\(單位厘米\), 不能負數

  * `speed`  

       輪子的馬達速度, 可選, 預設為 0  

       不填寫 : 由PID運算控制  

       填寫   : 範圍 +1 ~ +127

  * `brakemode`   

       設定馬達停止後的制動方式, 可選, 預設為 HOLD

  * `tick`   

       移動距離\(單位tick\), 可選, 預設為 0  

       不填寫 : 距離跟從cm, 並由cm計算出實際移動多少tick  

       填寫   : 覆蓋cm, 不能負數
* **返回**: action結構以控制指令

### backward

```cpp
action backward(const float cm,
                const short speed = 0,
                const action_brake_mode_e_t brakemode = HOLD,
                int tick = 0);
```

向後直行一定距離, 然後停止

* **參數**: 
  * `cm`   

       移動距離\(單位厘米\), 不能負數

  * `speed`  

       輪子的馬達速度, 可選, 預設為 0  

       不填寫 : 由PID運算控制  

       填寫   : 範圍 +1 ~ +127

  * `brakemode`   

       設定馬達停止後的制動方式, 可選, 預設為 HOLD

  * `tick`   

       移動距離\(單位tick\), 可選, 預設為 0  

       不填寫 : 距離跟從cm, 並由cm計算出實際移動多少tick  

       填寫   : 覆蓋cm, 不能負數
* **返回**: action結構以控制指令

### turnLeftToAzimuth

```cpp
action turnLeftToAzimuth(const float angle, const short speed = 0, const double error = 1);
```

轉左到指定的絕對方位角並停下, 無論轉右到達目標是否近於轉左到達  
絕對方位角是指 自動程式開始時機械人面向角 與 現時機械人面向角 之間的角度差

* **參數**: 
  * `angle`   

       絕對方位角\(單位角度\)

  * `speed`  

       轉向的馬達速度, 可選, 預設為 0

       不填寫 : 由PID運算控制  

       填寫   : 範圍 +1 ~ +127

  * `error`  

       判斷動作完成的誤差的容許範圍\(單位角度\)
* **返回**: action結構以控制指令

### turnRightToAzimuth

```cpp
action turnRightToAzimuth(const float angle, const short speed = 0, const double error = 1);
```

轉右到指定的絕對方位角並停下, 無論轉左到達目標是否近於轉右到達  
絕對方位角是指 自動程式開始時機械人面向角 與 現時機械人面向角 之間的角度差

* **參數**: 
  * `angle`   

       絕對方位角\(單位角度\)

  * `speed`  

       轉向的馬達速度, 可選, 預設為 0

       不填寫 : 由PID運算控制  

       填寫   : 範圍 +1 ~ +127

  * `error`  

       判斷動作完成的誤差的容許範圍\(單位角度\)
* **返回**: action結構以控制指令

### turnLeft

```cpp
action turnLeft(const float angle, const short speed = 0, const double error = 1);
```

轉左指定角度並停下

* **參數**: 
  * `angle`   

       轉左的角度\(單位角度\)

  * `speed`  

       轉向的馬達速度, 可選, 預設為 0

       不填寫 : 由PID運算控制  

       填寫   : 範圍 +1 ~ +127

  * `error`  

       判斷動作完成的誤差的容許範圍\(單位角度\)
* **返回**: action結構以控制指令

### turnRight

```cpp
action turnRight(const float angle, const short speed = 0, const double error = 1);
```

轉右指定角度並停下

* **參數**: 
  * `angle`   

       轉右的角度\(單位角度\)

  * `speed`  

       轉向的馬達速度, 可選, 預設為 0

       不填寫 : 由PID運算控制  

       填寫   : 範圍 +1 ~ +127

  * `error`  

       判斷動作完成的誤差的容許範圍\(單位角度\)
* **返回**: action結構以控制指令

### keepMove

```cpp
action keepMove(const short lfspeed, const short lrspeed,
                const short rfspeed, const short rrspeed,
                const int waitms = 0);
```

指定不同馬達速度

* **參數**: 
  * `lfspeed`  

       左前輪子的馬達速度, 範圍 -127 ~ +127

  * `lrspeed`  

       左後輪子的馬達速度, 範圍 -127 ~ +127

  * `rfspeed`  

       右前輪子的馬達速度, 範圍 -127 ~ +127

  * `rrspeed`  

       右後輪子的馬達速度, 範圍 -127 ~ +127

  * `waitms`   

       持續毫秒, 可選, 預設為 0  

       不填寫 : 持續  

       填寫   : 持續ms毫秒, 然後停止
* **返回**: action結構以控制指令

### keepMove

```cpp
action keepMove(const short lspeed, const short rspeed, const int waitms = 0);
```

指定左方及右方馬達速度

* **參數**: 
  * `lspeed`  

       左輪子的馬達速度, 範圍 -127 ~ +127

  * `rspeed`  

       右輪子的馬達速度, 範圍 -127 ~ +127

  * `waitms`   

       持續毫秒, 可選, 預設為 0  

       不填寫 : 持續  

       填寫   : 持續ms毫秒, 然後停止
* **返回**: action結構以控制指令

### holdMove

```cpp
action holdMove(const bool isenable = true);
```

保持輪子馬達位置, 此動作不阻塞線程

* **參數**: 
  * `isenable`  

       是否保持, 可選, 預設為 True
* **返回**: action結構以控制指令

## Roller

### 配置

#### 馬達與感應器

| type | name | detail |
| :--- | :--- | :--- |
| Motor | mtrRoller | 滾筒 |

### roller

```cpp
action roller(const short speed, const int waitms = 0);
```

指定滾筒馬達速度

* **參數**: 
  * `speed`  

       滾筒的馬達速度, 範圍 -127 ~ +127

  * `waitms`   

       持續毫秒, 可選, 預設為 0  

       不填寫 : 持續  

       填寫   : 持續ms毫秒, 然後停止
* **返回**: action結構以控制指令

## Shooter

### 配置

#### 變數初始化

| type | name | detail |
| :--- | :--- | :--- |
| int | msShootTime | 保持/基準位置到釋放位置所需時間 |
| int | msBackTime | 釋放位置到基準位置所需時間 |
| int | msHoldTime | 基準位置到保持位置所需時間 |

#### 馬達與感應器

| type | name | detail |
| :--- | :--- | :--- |
| Motor | mtrShooter | 發射器 |

### arm

```cpp
action shooter();
```

射擊一次並回到準備點

* **返回**: action結構以控制指令

