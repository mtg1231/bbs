#!/usr/bin/python3
# coding: "utf8"


import io,sys
sys.stdout = io.TextIOWrapper(sys.stdout.buffer, encoding='utf-8')

import cgi

form = cgi.FieldStorage( keep_blank_values = True )



import cgitb

cgitb.enable()

print ('Content-type: text/html\n')
print('<!DOCTYPE html>')
print("""<html lang="ja">""")
print("""<head>""")
print("""<meta charset="utf8">""")
print("""<link href="bbs.css" rel="stylesheet" type="text/css" media="all">""")
print('<title>掲示板</title>')
print('</head>')
print('<body>')
print('<h1>掲示板</h1>')
print('<section>')
print('<h2>新規投稿</h2>')
print('<form action="" method="post">')
print('<div class="name"><span class="label">お名前:</span><input type="text" name="name" value=""></div>')
print('<div class="honbun"><span class="label">本文:</span>')
print('<textarea name="comment" cols="30" rows="3" maxlength="80" wrap="hard" placeholder="80字以内で入力してください。"></textarea></div>')
print('<input type="submit" value="投稿">')
print(' </form>')
print('</section>')
print('<section class="toukou">')
print('<h2>投稿一覧</h2>')
print('<ul>')

import MySQLdb
con = None

cur = None

# 接続する
con = MySQLdb.connect(
        user='root',
        passwd='yourpw',
        host='localhost',
        db='yourdb',
        charset="utf8")

# カーソルを取得する
cur= con.cursor()

name = form.getvalue('name')
honbun = form.getvalue('comment')

#入力を挿入
sql= 'INSERT INTO Post (name, honbun) VALUES (%s, %s)'

cur.execute(sql,(name, honbun))
con.commit()



# クエリを実行する
sql = "select * from Post";
cur.execute(sql)
# 実行結果をすべて取得する
rows = cur.fetchall()

import datetime
date = datetime.datetime.now()
for row in rows:
    print('<li>' + row[0] + '</li>')
    print(date)
    print('<a>' + row[1] + '</a>')

print('</ul>')
print('</section>')
print('</body>')
print('</html>')


cur.close()
con.close()
