


**************************************************


Arch linuxにvscodeをインストールする恣意的ノート


**************************************************


軽量テキストエディタであるplumaのハイフネーションがどうしても取れなかったため、この際リッチなテキストエディタであるvscodeに乗り換えることに決め、インストールをした次第でございます。
```
yay -S code
```
あとは、
・VScodeVimのプラグインをインストールして、キーバインドをvimライクにする。
・.vimrcを読み込むようにする。"vim.vimrc.enable": true,	"vim.vimrc.path": "/home/gar/.config/nvim/init.vim",
・固定幅フォントは"Myrica M"と、"源ノ角ゴシック","BIZ UDゴシック"がずれません。あとのは、どうしてだかずれるようです。どうも、"BIZ UDゴシック"が一番いいようです。
・設定ファイルに"editor.stickyScroll.enabled": false,を追加。スティッキースクロールは邪魔なので。
・設定ファイルに"editor.wordWrap": "on",を追加。テキストを右端で折り返す。
・信頼するディレクトリに/home/$USER/を入れる。

設定ファイルはこんな感じでいいかな。

```~/.config/Code - OSS/User/settings.json
{
	"workbench.settings.useSplitJSON": true,
	"editor.formatOnSave": true,
	"emmet.variables": {
		"lang": "ja",
	},
	"window.zoomLevel": 0,
	"workbench.startupEditor": "newUntitledFile",
	"editor.fontSize": 20,
	"editor.tabSize": 4,
	"editor.fontFamily": "BIZ UDゴシック",
	"editor.fontLigatures": true,
	"editor.detectIndentation": false,
	"editor.insertSpaces": false,
	"editor.wordWrap": "on",
	"editor.stickyScroll.enabled": false,
	"html.format.extraLiners": "",
	"html.format.preserveNewLines": false,
	"editor.renderWhitespace": "boundary",
	"editor.minimap.showSlider": "always",
	"editor.quickSuggestions": {
		"strings": true,
	},
	"editor.suggest.insertMode": "replace",
	"files.eol": "\n",
	"vim.vimrc.enable": true,
	"vim.vimrc.path": "/home/gar/.config/nvim/init.vim",
	"window.newWindowDimensions": "inherit",
	"workbench.editor.empty.hint": "hidden"
}
```

VScodeはかっこいいけど、慣れるのは大変そう。とりあえず。これでOK。
