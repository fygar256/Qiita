


**************************************************


[Utility]Qiitaの自分のMarkdownの全記事をダウンロードするスクリプト　おまけ付き。 ver 1.3


**************************************************


Qiitaの自分の全記事をダウンロードするスクリプトです。

記事の中にGitHub Gistのリンクがあれば、そこからダウンロードして、記事の中に埋め込みます。（これがおまけ）

タイトルがファイルネームになりますが、タイトルの中に`/`が含まれていると,`-`に、スペースはアンダースコアに、全角スペースは全角アンダースコアに変換します。(これもおまけ)

タイトルに`'`または、`"`が含まれていると、エスケープキャラクタ`\`を付けます。GUI環境などで、これがいらない方は、スクリプトから`# @@@`の行を削除してください。

僕は、タイトルにバックスラッシュを付ける癖がないので、これで問題なく動作しますが、バックスラッシュを付ける人は`# @@@`周辺を自分で書き換えてください。

OSの環境変数QIITA_USERにユーザーネームを、QIITA_TOKENにアクセストークンを指定するか、スクリプトを直接書き換えて、ACCESS_TOKENにアクセストークンを、USER_IDにユーザーIDを指定してください。

`python qiitadl.py`で動かしてください。

pythonが動作する環境なら、どこでも動作すると思います。

僕の環境はArchlinuxです。

AIを使って書きました。動作します。お世話になっているので、Qiitaに還元です。

```qiitadl.py
#!/usr/bin/python
import os
import subprocess
import requests
import re
import json
from bs4 import BeautifulSoup
ACCESS_TOKEN = os.environ['QIITA_TOKEN']
USER_ID =  os.environ['QIITA_USER']
DIR='qiita_articles'
TEXT_MARK="blob-code blob-code-inner js-file-line" # 変わるらしいので、適宜調整が必要

# Qiita APIのエンドポイント
API_URL = f'https://qiita.com/api/v2/users/{USER_ID}/items'

# ヘッダーにアクセストークンを設定
headers = {
    'Authorization': f'Bearer {ACCESS_TOKEN}'
}

def get_gist_code(gist_url):
    response = requests.get(gist_url)
    soup = BeautifulSoup(response.text, 'html.parser')
    code_blocks = soup.find_all(class_=TEXT_MARK)
    extracted_code = "" 
    for block in code_blocks:
        code = block.get_text()
        extracted_code+=code+"\n"
    return extracted_code

def include_gist_content_in_markdown(markdown_file):
    with open(markdown_file, 'r') as file:
        content = file.read()

    gist_urls = re.findall(r'https://gist\.github\.com/[^\s]+', content)
    for gist_url in gist_urls:
        text = get_gist_code(gist_url)
        content = content.replace(gist_url, text)

    with open(markdown_file, 'w') as file:
        file.write(content)

def download_articles(n):
    url=f"{API_URL}?per_page=100&page={n}"
    response = requests.get(url, headers=headers)
    if response.status_code == 200:
        articles = response.json()
        if not os.path.exists(DIR):
            os.makedirs(DIR)
        for article in articles:
            title = article['title']
            body = article['body']
            file_name = f"{DIR}/{title.replace('/', '-')}.md"
            file_name = file_name.replace(' ', '_')
            file_name = file_name.replace('　', '＿')
            file_name = file_name.replace('"', '\\"') # @@@
            file_name = file_name.replace("'", "\\'") # @@@
            with open(file_name, 'w', encoding='utf-8') as file:
                file.write("\n\n\n**************************************************\n\n\n")
                file.write(title)
                file.write("\n\n\n**************************************************\n\n\n")
                file.write(body)
            print(file_name)
            include_gist_content_in_markdown(file_name)
            
        print(f"{len(articles)} articles downloaded successfully.")
        if len(articles)<100:
            return False
        return True
    else:
        print(f"Failed to fetch articles: {response.status_code}")
        return False

def main():
    i=0
    while True:
        i+=1
        if download_articles(i)==False:
            break

if __name__=='__main__':
    main()


```
