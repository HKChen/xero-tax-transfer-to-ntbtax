# xero-tax-transfer-to-ntbtax

<div>

[![CircleCI](https://dl.circleci.com/status-badge/img/gh/HKChen/xero-tax-transfer-to-ntbtax/tree/main.svg?style=shield)](https://dl.circleci.com/status-badge/redirect/gh/HKChen/xero-tax-transfer-to-ntbtax/tree/main)
[![LICENSE](https://badgen.net/github/license/hkchen/xero-tax-transfer-to-ntbtax)](LICENSE)

</div>

由中小企業常用之 Xero 雲端會計軟體產出之報表，
再依據公司稅籍及統編轉出台灣國稅局報稅上傳格式。

## System Required Node.js 15+

## Project setup
```
npm install
```

### Compiler
```
npm run serve
```

### Build with Win 64 exe
```
npm run electron:build:win 
```

### Build with Linux
```
npm run electron:build
```

## User Guide
依據 Xero 所匯出的會計報表內容
```
發票字軌/發票號碼/未稅金額/稅額/廠商統編/發票年月/發票格式/ 摘要
```
轉換並插入所選之公司的稅籍及統編最後輸出***報稅碼*** txt 國稅局報稅格式。

![image](https://github.com/HKChen/xero-tax-transfer-to-ntbtax/blob/main/test_file/descrption_img/info.png)
