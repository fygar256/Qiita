


**************************************************


ubuntu 20.04 remove persistent desktop home and trash icons on gnome-flashback.


**************************************************


gnome-flashbackで、色々と試行錯誤しても消えないデスクトップアイコンを消す方法。

```
gsettings set org.gnome.gnome-flashback.desktop.icons show-home false
gsettings set org.gnome.gnome-flashback.desktop.icons show-trash false
```

また、戻す方法。私の場合はパネルのTrashに不具合が出た時、戻さなければなりませんでした。

```
gsettings set org.gnome.gnome-flashback.desktop.icons show-trash true
```
