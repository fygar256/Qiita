


**************************************************


vimのカスタマイズ


**************************************************


vimのカスタマイズをする.vimrcのサンプルです。

viとvimは違いますが、僕は

```
alias vi='vim'
```

として使っています。

vimは起動時に~/.vimrcを読み込んで動作します。neovimの設定ファイルにするには、ファイル名を~/.config/nvim/init.vimとします。

qをquitにして、qqをqの代わりにしてますが、コメントアウトしてあります。

```.vimrc
" エンコードはUTF-8
set fenc=utf-8
" バックアップファイルを作らない
set nobackup
" スワップファイルを作らない
set noswapfile
" オートインデント
set autoindent
" 対応する括弧を表示
set showmatch
" シンタックスハイライト
syntax on
" ステータスを表示
set laststatus=2
" タブをスペースにする
set expandtab
" タブをスペース4つ分にする
set tabstop=4
" シフトをスペース4つ分にする
set shiftwidth=4
" サーチ時に大文字小文字を区別しない
set ignorecase
" 小文字で検索すると大文字小文字を区別しない
set smartcase
" 検索がファイルの終わりまで行ったら先頭に戻る
set wrapscan
" 検索結果をハイライト表示
set hlsearch
" オートインデント
set smartindent
" beepもビジュアルベルも無効
set vb t_vb=
" Clipboardからペースト可能
vmap <C-c> "+y
" qでquit
"nnoremap q :q<CR>
" qqでqの代わりをする
"nnoremap qq q
" ESCの2回押しでハイライト消去
nnoremap <ESC><ESC> :nohl<CR>
" 画面行の移動をする。論理行の移動はgj,gkでする。
nnoremap k gk
nnoremap gk k
nnoremap j gj
nnoremap gj j
```
