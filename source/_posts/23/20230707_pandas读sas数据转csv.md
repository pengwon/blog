---
title: pandas读sas数据转csv
date: 2023-07-07 16:28:47
categories:
tags:
    - python
---

1999年以后的nhanes数据都是以xpt文件格式发布的，而更早的数据有很多是以dat原始数据格式发布，需要将原始数据转为数据表以做进一步统计分析。

以 [NHANES III](https://wwwn.cdc.gov/nchs/nhanes/nhanes3/datafiles.aspx#core) 为例，其中有一个数据`Household Youth File`，包含3个数据文件`youth.dat`, `youth.sas`, `YOUTH-acc.pdf`。`YOUTH-acc.pdf`是这个数据的概述报告，可以从这个文件找到数据的概况信息，比如样本数量，以及一些代码的含义，比如`DMARACER`这个变量标识种族，在原始数据中的位置是`13`，只有1个字符，就是用代码来表示不同种族的：`1`表示'White', `2`表示'Black', `3`表示'Other', `8`表示'Mexican-American of unknown race'。`youth.sas`是数据解析的重点，这个文件描述了原始数据的格式，这是一个文本文件，内容为：

<!-- more -->

```
 FILENAME YOUTH "D:\Questionnaire\DAT\YOUTH.DAT" LRECL=2103;
    *** LRECL includes 2 positions for CRLF, assuming use of PC SAS;

    DATA WORK;
      INFILE YOUTH MISSOVER;

      LENGTH
        SEQN      7
        DMPFSEQ   5
        DMPSTAT   3
        DMARETHN  3
        DMARACER  3
        DMAETHNR  3
        HSSEX     3
        HSDOIMO   3
        HSAGEIR   3
        HSAGEU    3
        HSAITMOR  4
        HSFSIZER  3
        HSHSIZER  3
        DMPCNTYR  3
        DMPFIPSR  3
        DMPMETRO  3
        DMPCREGN  3
        DMPPIR    8
        ...
        HYK9EO    4
        HYK9FO    4
        HYK11AO  $6
        HYK12SO   4
        HYM1      3
        HYM2      3
        HYM3      3
      ;

      FORMAT
        DMPPIR   Z6.3
        WTPFQX6  Z9.2
        WTPFEX6  Z9.2
        WTPFHX6  Z9.2
        WTPFALG6 Z9.2
        WTPFCNS6 Z9.2
        WTPFSD6  Z9.2
        WTPFMD6  Z9.2
        WTPFHSD6 Z9.2
        WTPFHMD6 Z9.2
        WTPFQX1  Z9.2
        WTPFEX1  Z9.2
        WTPFHX1  Z9.2
        WTPFALG1 Z9.2
        WTPFCNS1 Z9.2
        WTPFSD1  Z9.2
        WTPFMD1  Z9.2
        WTPFHSD1 Z9.2
        WTPFHMD1 Z9.2
        WTPFQX2  Z9.2
        WTPFEX2  Z9.2
        ...
        WTPXRP43 Z9.2
        WTPXRP44 Z9.2
        WTPXRP45 Z9.2
        WTPXRP46 Z9.2
        WTPXRP47 Z9.2
        WTPXRP48 Z9.2
        WTPXRP49 Z9.2
        WTPXRP50 Z9.2
        WTPXRP51 Z9.2
        WTPXRP52 Z9.2
      ;

     INPUT
        SEQN     1-5
        DMPFSEQ  6-10
        DMPSTAT  11
        DMARETHN 12
        DMARACER 13
        DMAETHNR 14
        HSSEX    15
        HSDOIMO  16-17
        HSAGEIR  18-19
        HSAGEU   20
        HSAITMOR 21-24
        HSFSIZER 25-26
        HSHSIZER 27-28
        DMPCNTYR 29-31
        DMPFIPSR 32-33
        DMPMETRO 34
        DMPCREGN 35
        DMPPIR   36-41
        ...
        HYK9DO   2075-2078
        HYK9EO   2079-2082
        HYK9FO   2083-2086
        HYK11AO  2087-2092
        HYK12SO  2093-2097
        HYM1     2098-2099
        HYM2     2100
        HYM3     2101
     ;

      LABEL
        SEQN     = "Sequence number"
        DMPFSEQ  = "Family sequence number"
        DMPSTAT  = "Examination/interview status"
        DMARETHN = "Race-ethnicity"
        DMARACER = "Race"
        DMAETHNR = "Ethnicity"
        HSSEX    = "Sex"
        HSDOIMO  = "Date of screener: month"
        HSAGEIR  = "Age at interview (screener) - qty"
        HSAGEU   = "Age at interview (screener) - unit"
        HSAITMOR = "Age in months at interview (screener)"
        HSFSIZER = "Family size (persons in family)"
        HSHSIZER = "Household size (persons in dwelling)"
        DMPCNTYR = "County code"
        DMPFIPSR = "FIPS code for State"
        DMPMETRO = "Rural/urban code based on USDA code"
        DMPCREGN = "Census region, weighting(Texas in south)"
        DMPPIR   = "Poverty Income Ratio (unimputed income)"
        ...
        HYK9DO   = "Primary drug class code-9th"
        HYK9EO   = "Secondary drug class code-9th"
        HYK9FO   = "Tertiary drug class code-9th"
        HYK11AO  = "ICD-9-CM code-9th"
        HYK12SO  = "For how long been taking (days)-9th"
        HYM1     = "Main respondents relationship to SP"
        HYM2     = "Was SP present during any of interview"
        HYM3     = "Quality of interview"
      ;
```

可见每一个区段是用`区段名`和`;`包裹的，我们可以根据`INPUT`段的内容对原始数据进行处理。`youth.dat`就是原始数据文件。

定义一个sas解析函数：

```python
# 定义工具函数，解析 sas 文件数据格式
import re

def parse_sas_file(filename):
    with open(filename, 'r') as file:
        sas_content = file.read()

    # 使用正则表达式提取所需内容
    pattern = r'INPUT(.*?)\;'
    match = re.search(pattern, sas_content, re.DOTALL)

    if match:
        input_lines = match.group(1)
    else:
        input_lines = ""

    input_lines = input_lines.strip().splitlines()
    columns_info = {}

    for line in input_lines:
        name, position_range = line.split()
        
        if '-' in position_range:
            start, end = position_range.split('-')
            start, end = int(start) - 1, int(end)
        else:
            start = int(position_range) - 1
            end = start + 1
        
        columns_info[name] = {
            'start': start,
            'end': end
        }

    return columns_info
```

运行一下：

```python
sas_file_name = 'nhanes3/youth.sas'
dat_file_name = 'nhanes3/youth.dat'

columns_info = parse_sas_file(sas_file_name)
columns_info
```

输出为：

```python
{'SEQN': {'start': 0, 'end': 5},
 'DMPFSEQ': {'start': 5, 'end': 10},
 'DMPSTAT': {'start': 10, 'end': 11},
 'DMARETHN': {'start': 11, 'end': 12},
 'DMARACER': {'start': 12, 'end': 13},
 'DMAETHNR': {'start': 13, 'end': 14},
 'HSSEX': {'start': 14, 'end': 15},
 'HSDOIMO': {'start': 15, 'end': 17},
 'HSAGEIR': {'start': 17, 'end': 19},
 'HSAGEU': {'start': 19, 'end': 20},
 'HSAITMOR': {'start': 20, 'end': 24},
 'HSFSIZER': {'start': 24, 'end': 26},
 'HSHSIZER': {'start': 26, 'end': 28},
 'DMPCNTYR': {'start': 28, 'end': 31},
 'DMPFIPSR': {'start': 31, 'end': 33},
 'DMPMETRO': {'start': 33, 'end': 34},
 'DMPCREGN': {'start': 34, 'end': 35},
 'DMPPIR': {'start': 35, 'end': 41},
 'SDPPHASE': {'start': 41, 'end': 42},
 'SDPPSU6': {'start': 42, 'end': 43},
 'SDPSTRA6': {'start': 43, 'end': 45},
 'SDPPSU1': {'start': 45, 'end': 46},
 'SDPSTRA1': {'start': 46, 'end': 48},
 'SDPPSU2': {'start': 48, 'end': 49},
 'SDPSTRA2': {'start': 49, 'end': 51},
...
 'HYK11AO': {'start': 2086, 'end': 2092},
 'HYK12SO': {'start': 2092, 'end': 2097},
 'HYM1': {'start': 2097, 'end': 2099},
 'HYM2': {'start': 2099, 'end': 2100},
 'HYM3': {'start': 2100, 'end': 2101}}
```

再定义一个转为pandas数据帧并存为csv的函数：

```python
import pandas as pd

def dat2csv(dat_file_name, columns_info, csv_file_name):
    # 读取并解析dat文件
    rows = []
    with open(dat_file_name, 'r') as file:
        for line in file:
            temp_dict = {}
            for col, positions in columns_info.items():
                temp_dict[col] = line[positions["start"]: positions["end"]].strip()
            rows.append(temp_dict)

    # 转换为 Pandas DataFrame
    data = pd.DataFrame(rows)
    data.to_csv(csv_file_name)
```

运行一下：

```python
# 将 dat 文件转存为 csv 文件
dat2csv(dat_file_name, columns_info, 'nhanes3/youth.csv')
df = pd.read_csv('nhanes3/youth.csv')
df.head()
```

输出为：

![运行结果](https://imgs.boringhex.top/blog/20230705151256.png)
