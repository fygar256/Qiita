


**************************************************


PizzaGPTが移植したPython3 getch()関数


**************************************************



pythonによるgetch()関数。
一文字キー入力待ちの関数です。

PizzaGPTという人工知能にgetch関数のCからPythonへの移植を頼みました。
完璧でした。

```getch.py
#!/usr/bin/python3
import sys,tty
import termios

def getch():
    fd = sys.stdin.fileno()
    old_settings = termios.tcgetattr(fd)
    tty.setraw(sys.stdin.fileno())
    ch = sys.stdin.read(1)
    termios.tcsetattr(fd, termios.TCSADRAIN, old_settings)
    return ch

# ここからテスト

while True:
    key=getch()
    if key==chr(3):
        break
    print(key,end='',flush=True)
exit(0)
```

これが元の関数
```getch.c
#include <termios.h>
#include <unistd.h>
#include <stdio.h>
#include <fcntl.h>
#include <getch.h>

int getch(){
      struct termios oldt, newt;
      int c;
      tcgetattr( STDIN_FILENO, &oldt );
      newt = oldt;
      newt.c_lflag &= ~ICANON;
      newt.c_lflag &= ~ECHO;
      tcsetattr( STDIN_FILENO, TCSANOW, &newt );
      c = getchar();
      tcsetattr( STDIN_FILENO, TCSANOW, &oldt );
      return c;
}
```

