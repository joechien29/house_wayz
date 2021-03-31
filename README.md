# 資料清洗注意事項及常用操作
###### tags:`筆記`



### 讀檔常用參數

- header: 
Row number(s) to use as the column names,預設是0
- index_col:
預設是=None，也就是沒有index的欄位



### 常用來觀察df的func
- 看整張df的資料型態等
> df.info()
- 看前幾筆資料
> df.head()
- 總共有幾筆資料，幾個欄位
> df.shape

- 看整張df有什麼欄位
> df.columns
> 
配合reindex(columns=[]), 可將欄位順序快速調整
ex：原column順序=['a','b','c','d','e','f']

> df = df.reindex(columns=['a','f','d','b','c','e'])

增加欄位的方式，可參考：https://www.delftstack.com/zh-tw/howto/python-pandas/how-to-create-an-empty-column-in-pandas-dataframe/

- 改變column_name
ex：將"Address"換成"土地區段位置建物區段門牌"

> df = df.rename(columns={"Address":"土地區段位置建物區段門牌"})

- 看整張df的統計數值
> df.describe()

- 看每個欄位的dtype
> df.dtypes 



- 看欄位中有那些值
> df["column_name"].unique()
看

透過設定index跟column來觀看資料。(關於iloc用法，官網範例很完整), 一個簡單的sample如下，
> df.iloc[[0,10]][["交易年月日","交易標的"]]

交易年月日	交易標的
0	1010629	房地(土地+建物)
10	1010718	車位


iloc內也可接受func。下面例子為取出index為偶數的row
> df.iloc[lambda x: x.index % 2 == 0]

參考：https://medium.com/@b89202027_37759/%E5%AF%A6%E7%94%A8%E4%BD%86%E5%B8%B8%E5%BF%98%E8%A8%98%E7%9A%84pandas-dataframe%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4-1-976f48eb2bd5



### 讓提醒文字不要出現：
> import warnings
> warnings.filterwarnings('ignore')

### 當有drop的行為時，最好配合reset_index(drop=True)：
reset_index可維持index順序的正確性，不然會缺少被移除列的index。
後面有使用for loop時會錯誤
參數drop=True，讓舊index被移除而不是生成一個新的欄位

### 而想要讓drop後的新df保留並替代舊的df，方法有二：
一、	可用參數：inplace=True
二、	使用一個變數去接，
ex：df = df.drop([0]).rest_index(drop=True)

drop欄的時候要記得參數axis=1 ,預設是axis=0
tips：沒有使用的話就只是show出來，但原始的df並不會改變，可以先不使用，確認好處理後是想要的結果再去接，可節省時間

### 設定觀看欄位中某些值的資料

ex：
> df = df[~df["交易標的"].isin(["土地","建物","車位"])]
> 
這句是表示我要將df改成
欄位[交易標的]的值沒有[土地、車位、建物]，`~` 是表示反向，

### 對於df or df[columns]值的操作可參考.apply()的使用

參考：https://zhuanlan.zhihu.com/p/100064394
ex：

> import re
> 
> df['交易筆棟數_土地'] = df['交易筆棟數'].apply(lambda x:int(re.findall(r'\d+',x)[0]))

原始資料：欄位[交易筆棟數]的值[土地3建物1車位1]，使用以上方式將
此轉換成：欄位[交易筆棟數_土地]值[3]，這當中也使用了正規表示式, 很方便的作法



### 多條件的篩選，並調整篩選後某欄位的值
當我們想做多條件的篩選，並且將此條件下的某欄位的值做調整。提供以下作法給各位參考
ex:
[建物型態]是套房或其它，且[總樓層數]在1的物件。將[建物型態]改為透天厝

>con1 = df["建物型態"].isin(["套房(1房1廳1衛)","其他"])
>con2 = df["總樓層數"].isin([1])
>df["建物型態"][con1 & con2] = "透天厝"

參考：https://ithelp.ithome.com.tw/articles/10194003


### 將欄位中的值做調整
>df["主要建材"].replace(["土木造", "土造", "土磚石混合造", "木造", "加強磚造", "石造", "竹造"], "磚造", inplace = True) 

### 將欄位的值取出成list
ex：
> com_list = df["建築完成年月"].tolist()

如要配合for loop 操作記得要先df.rest_index()
tips：
list.index(list中的物件)，可返回此物件的index，有時蠻好用的


### 將操作後的list再放回insert回df
ex:

>df.insert(2,"建築年", com_year_list)

參數
1. loc：int
Insertion index. Must verify 0 <= loc <= len(columns).

2. column：str, number, or hashable object
Label of the inserted column.

3. value：int, Series, or array-like
4. allow_duplicatesbool, optional
如果没有設定allow_duplicates = True，此時如果添加的列已經存在，則會報錯，(我覺得這個使用狀況很少，不然有重覆名也不好去操作)


### 在加入嫌惡特徵的操作，合併報表：concat、join、merge
直接附上我覺得蠻詳細的url
參考：https://zhuanlan.zhihu.com/p/76476957

將資料夾中的檔案名寫入list，方便後續讀檔作業
> dir_list = os.listdir("./新加入特徵/tempOut/")

### 轉換型態：建議在一開始拿到資料時就可以先做一次，操作時會減少報錯，縮短除錯時間

有些時候astype()函數執行成功了也並不一定代表著執行結果符合預期，建議寫一個轉換的function先處理欄位的值
參考：https://zhuanlan.zhihu.com/p/35287822

轉換時間型態：
參考：https://ithelp.ithome.com.tw/articles/10235251


### 去除重複, 及缺失值處理
需要去重複時，可drop_duplicates方法完成：
drop_duplicates方法還可以按照某列去重，例如去除id列重複的所有記錄
填入平均數的code
> df.score.fillna(sample.score.mean())

參考：
1. https://www.mdeditor.tw/pl/pmLR/zh-tw
2. https://medium.com/@zector1030/pandas-fillna-%E7%AF%84%E4%BE%8B-5d33819fb7b8


### 結束前的檢查
可以寫個func檢查還有沒有空值，以及各欄位值的合理區間檢查(也可讓後續離群值處理)，以及型態檢查。
檢查之後再來刪除欄位，可以減少很多再重複比對錯誤資料跟原始資料的不同。




### 補充參考：
1. pandas實用技巧
https://leemeng.tw/practical-pandas-tutorial-for-aspiring-data-scientists.html
2. pandas實用技巧2
https://medium.com/@b89202027_37759/%E5%AF%A6%E7%94%A8%E4%BD%86%E5%B8%B8%E5%BF%98%E8%A8%98%E7%9A%84pandas-dataframe%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4-2-aa99d4eb949e
3. pandas實用技巧3
https://www.mdeditor.tw/pl/g2IG/zh-tw
4. numpy入門
https://blog.techbridge.cc/2017/07/28/data-science-101-numpy-tutorial/ 
5. DataFrame的新增、迴圈與刪除：https://ithelp.ithome.com.tw/articles/10191774
6. 利用單行迴圈產生list：https://sites.google.com/site/ezpythoncolorcourse/listandsingleforloop
7. 利用groupby串接字串：http://hk.uwenku.com/question/p-mdarcfkf-qc.htm
8. 用Pandas分析資料— 資料的聚合：https://seanyeh.medium.com/%E7%94%A8pandas%E5%88%86%E6%9E%90%E8%B3%87%E6%96%99-%E8%B3%87%E6%96%99%E7%9A%84%E8%81%9A%E5%90%88-e08f1b1504ed
9. 資料前處理( Label encoding、 One hot encoding)：https://medium.com/@PatHuang/%E5%88%9D%E5%AD%B8python%E6%89%8B%E8%A8%98-3-%E8%B3%87%E6%96%99%E5%89%8D%E8%99%95%E7%90%86-label-encoding-one-hot-encoding-85c983d63f87

