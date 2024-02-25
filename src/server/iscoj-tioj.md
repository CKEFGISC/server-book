# TIOJ 使用說明

TIOJ 位在的資料夾：`/home/fgisc/tioj-new`aka`~/tioj-new`
由於此 oj 歷經多次嘗試才架設成功
目前 oj 所在的資料夾名稱為`tioj-new`

## 文件

- GitHub: https://github.com/TIOJ-INFOR-Online-Judge/tioj

## 架設

我不知道 請找 AaW

## 啟動 OJ

```bash=
# cd path/to/tioj-new

# 啟動OJ
docker-compose up -d
sudo systemctl restart nginx
```

啟動完之後可以去 `localhost:4000` 查看網頁

## 雜七雜八的設定

## UI 修改方式

在~/tioj-new 下修改原始碼

- 圖像儲存在`public/image`
- 一些網頁內容與 css 儲存在`app`
  `app/assets/stylesheets/custom_styles.css`可以修改眾多 css 類別
- `app/views`則多為純 html
  若副檔名為.html.erb 則為 html 與 ruby 配合的文件寫法
  詳細語法請自行搜尋

在此改完所有欲修改、新增的東西後

- 前往`/srv/tioj/public/assets/`找到自己新增的東西並刪掉
<div style="background-color:rgba(255, 0, 0, 0.6); text-align:center; vertical-align: middle; padding:40px 0; margin-top:30px">
sudo rm -r /srv/tioj/public/assets/<你要刪的東西>/
</div>

- 上面那個很危 不懂不要亂搞
- 不會的請到下面的 build --no-cache 只是會慢個億點點而已
- 之後再回到`/home/fgisc/tioj-new/`

```
docker-compose build
sudo systemctl restart nginx
```

- 此時連回網站，如果發現東西沒有變
  到`/home/fgisc/tioj-new/`

```
docker-compose build --no-cache
sudo systemctl restart nginx
```

可能是因為你在 srv 刪錯東西的原因

## 題目新增方式

### 新增測資

tioj 不像 iscoj
測資與題序是分開上傳的
在新增題目後點入該題目 若有管理員權限
應會看到一個叫 manage testdata 的地方
按下去就可以上傳測資了
但只能一筆一筆慢慢上傳
至於測資來源一樣從 iscoj 上下載
每一題後面都會有個 download testcase 的按鈕
記得解壓縮

### 批量上傳測資

使用本文件底端的那份扣
就照著他說的做
問你文件路徑如果此.py 跟那個在同一個資料夾請輸入`./`
很方便你只要有給到正確的路徑 他就會讀取所有的`.in`, `.out`並上傳
time limit 與 memory limit 請照 iscoj 原本大小

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import requests
import random
import string
from bs4 import BeautifulSoup
from getpass import *
from os import listdir
from os.path import join, isfile

judge_site = 'https://iscoj.fg.tp.edu.tw'

session = requests.Session()
def login():
	TIOJusername = input("username: ")
	TIOJpassword = input("password: ")
	print('logging in...')
	global session
	rel = session.get(judge_site + '/users/sign_in')
	soup = BeautifulSoup(rel.text, "html.parser")
	inputs = soup.find('form').find_all('input')
	rel = session.post(judge_site + '/users/sign_in', data = {
		inputs[0].attrs['name']: inputs[0].attrs['value'],
		#inputs[1].attrs['name']: inputs[1].attrs['value'],
		'user[username]': TIOJusername,
		'user[password]': TIOJpassword,
		'user[remember_me]': '1',
		'commit': 'Sign in'
	})

login()
print('Successful log in')

problem_id = input('Problem ID: ')
tdpath = input('Testdata folder path: ')
files = [join(tdpath, f[:-3]) for f in listdir(tdpath) if isfile(join(tdpath, f)) and f[-3:] == '.in']
files.sort()

time_limit = input('Time limit: ')
memory_limit = input('Memory limit: ')

sign_up_get_url = judge_site + '/problems/%s/testdata/new' % problem_id
sign_up_post_url = judge_site + '/problems/%s/testdata' % problem_id
for f in files:
	print(f'processing {f}...')
	rel = session.get(sign_up_get_url)
	soup = BeautifulSoup(rel.text, "html.parser")
	inputs = soup.find('form').find_all('input')
	rel = session.post(sign_up_post_url, data = {
		inputs[0].attrs['name']: inputs[0].attrs['value'],
		#inputs[1].attrs['name']: inputs[1].attrs['value'],
		'testdatum[limit_attributes][time]': time_limit,
		'testdatum[limit_attributes][memory]': memory_limit,
		'testdatum[limit_attributes][output]': '65536',
		'testdatum[problem_id]': problem_id,
		'commit': 'Create Testdatum'
	}, files = {
		'testdatum[test_input]': open(f'{f}.in', 'rb'),
		'testdatum[test_output]': open(f'{f}.out', 'rb')
	})
	print(f'Create {f}.')

```

### 不按照 pid 順序新增題目

#### 連進 db

不要刪東西 不要刪東西

```bash
docker exec -it tioj-new_db_1 bash #進入tioj的db所在的docker
mysql -u root -p #進入該docker中的sql
55555333 #這是sql密碼 不要問為甚麼這麼爛 ~~問AaW~~
use tioj_production #進入sql的tioj_production這個表
# 在problems這張表新增題目 schema隨附與文件末
INSERT INTO problems (id, specjudge_type, interlib_type, verdict_ignore_td_list) VALUES(**THE ID TO INSERT**, 0, 0, "A");
```

## 附

sql-tioj_production-problems-schema

````sql
# == Schema Information
#
# Table name: problems
#
#  id                     :bigint           not null, primary key
#  name                   :string(255)
#  description            :text(16777215)
#  source                 :text(16777215)
#  created_at             :datetime
#  updated_at             :datetime
#  input                  :text(16777215)
#  output                 :text(16777215)
#  hint                   :text(16777215)
#  visible_state          :integer          default("public")
#  sjcode                 :text(4294967295)
#  interlib               :text(4294967295)
#  specjudge_type         :integer          not null
#  interlib_type          :integer          not null
#  specjudge_compiler_id  :bigint
#  discussion_visibility  :integer          default("enabled")
#  interlib_impl          :text(4294967295)
#  score_precision        :integer          default(2)
#  verdict_ignore_td_list :string(255)      not null
#  num_stages             :integer          default(1)
#  judge_between_stages   :boolean          default(FALSE)
#  default_scoring_args   :string(255)
#  strict_mode            :boolean          default(FALSE)
#  skip_group             :boolean          default(FALSE)
#  ranklist_display_score :boolean          default(FALSE)
#  code_length_limit      :integer          default(5000000)
#  specjudge_compile_args :string(255)
#
# Indexes
#
#  index_problems_on_name                   (name)
#  index_problems_on_specjudge_compiler_id  (specjudge_compiler_id)
#  index_problems_on_visible_state          (visible_state)
#
# Foreign Keys
#
#  fk_rails_...  (specjudge_compiler_id => compilers.id)
#```
````
