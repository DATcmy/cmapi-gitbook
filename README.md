# Home

## CMAPI

CMAPI是基於PROS開發的 VEX V5機械人應用程序接口, 有助程式員更快製作、易於修改、除錯和編寫更複雜的自動程序，而不需要處理複雜的邏輯。

### 目錄

{% page-ref page="./" %}

{% page-ref page="beginning.md" %}

{% page-ref page="tutorials.md" %}

{% page-ref page="api-document.md" %}

{% page-ref page="collaboration.md" %}

{% page-ref page="style-guide.md" %}

### 入門

入門教學將為您提供在本地電腦上啟動和運行的項目副本，以進行開發和測試。 有關如何在實際機械人上部署項目的說明，請參閱**部署**。

#### 開發環境

你必須具備以下設備環境才能編寫程式及有效地工作:

```text
PROS Atom IDE
PROS CLI
Git 版本控制系統
```

**建議安裝:**

PROS [官網](https://pros.cs.purdue.edu/)  
完整安裝PROS 可以包含一切編寫程式所需要的的工具, 包括 IDE 及 CLI

GitKraken[官網](https://www.gitkraken.com/)  
Git 的界面控制軟件, 推薦給初學者

#### 安裝

以下步驟將會教你如何開啟一個項目

開啟一個新的資料夾 /cmapi-basic，然後通過運行以下命令克隆CMAPI git repo的副本：

```text
git clone https://bitbucket.org/vex7984/cmapi.git
```

然後切換到支線 /basic

```text
git checkout -t origin/basic
```

Basic 支線下的檔案是運行CMAPI的基本檔案，將所有檔案及資料夾\(除了.git\)複製到機器人的項目資料夾裏，完成後可以開始編寫代碼，而不會影響CMAPI的源代碼

### 運行測試

如果要確保編譯後的的程式為最新版本，可以跟從以下步驟:

#### 清除所有編譯快取

在PROS Atom IDE 執行:

```text
PROS: Build Project (clean)
```

#### **重新編譯所有程序**

在PROS Atom IDE 執行:

```text
PROS: Build Project
```

### 部署

將電腦連接機械人  
在PROS Atom IDE 執行:

```text
PROS: Quick Action
```

程式成功下載後便會自動運行

### 使用工具

* [PROS](https://pros.cs.purdue.edu/) - 包含一切編寫Vex V5程式所需要的的工具
* [Visual Studio Code](https://code.visualstudio.com/) - 另一種好用的編輯器
* [GitKraken](https://www.gitkraken.com/) - Git 界面控制軟件
* [StackEdit](https://stackedit.io) - 編寫說明文件

### 貢獻

請閱讀另一篇[貢獻](Collaboration.html)以及[風格規範](Style_Guide.html)以了解編寫CMAPI核心代碼時的規範及注意事項

### 版本管理

我們使用Git版本控制系統管理CMAPI核心代碼，詳情請參考[BitBucket管理頁](https://bitbucket.org/vex7984/cmapi/)

### 作者

* **Jerry Lum** - **核心編寫和製作** - [BitBucket](https://bitbucket.org/jerrylum/)

### 許可證

此項目的任意部分未經許可不得轉載，版權由 CMASS機器人製作團隊 所有，保留所有權利

