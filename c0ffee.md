


**************************************************


c0ffee


**************************************************


joke for linux terminal.
辞書ファイルから、16進数で表せる単語を探します。
例えば、CAFE C0DE BEEF C0FFEE DEADBEEF等です。

私のC++の腕が未熟なため、Cと、C++の混交したプログラムになっています。
（因みに、CとC++の混交したコードは、Better Cと言うそうです。）

辞書ファイルは、英辞郎1446以下のバージョンのファイル、
シンプルな、英単語で始まって0x0aで終わる行の並びで成り立つファイルなどです。

See:
https://qiita.com/fygar256/items/7b4f090489eb1563238c

```c0ffee.cpp
/*
	16進数で表せる英単語を返す。
	Usage: c0ffee [-d=dicfile]
	to compile: c++ c0ffee.cpp -o c0ffee
*/
#include	<stdio.h>
#include	<stdlib.h>
#include	<string.h>
#include	<ctype.h>
#include	<iostream>
#include	<algorithm>
#include	<vector>
#include	<string>

using namespace std;

char hdc[]="O*********ABCDEF";

char *getword(FILE *fp,char *w) {
	int idx;
	int c;

	idx=0;
	c=fgetc(fp);
	if (c==EOF) return(NULL);
	if (c==0x81) fgetc(fp);
		else ungetc(c,fp);

	while(1) {
		c=fgetc(fp);
		if (c==EOF) return(NULL);
		w[idx]=(unsigned char)c;

		if (c==' ') {
			int	c2;
			c2=fgetc(fp);
			if (c2==EOF) return(NULL);
			if (c2==' ') c='\t';
			else ungetc(c2,fp);
			}
		if (c=='(') {
			while(1) {
				c=fgetc(fp);
				if (c==EOF) return(NULL);
				if (c=='\n') break;
				if (c==')') continue;
				}
			}
		if (c=='\n'||c=='\t'||
			c==','||c==':'||c=='{') break;
		idx++;
		}
		w[idx]='\0';
		ungetc(c,fp);
		while(1) {
			c=fgetc(fp);
			if (c==EOF) return(NULL);
			if (c=='\n') break;
			}
		if (w[idx-1]==' ')
			w[idx-1]='\0';
		return(w);
}

int isnullstring(char *s) {
	while(*s!='\0') {
		if (isalpha(*s)) return(0);
		if (*s!=' ') return(1);
		s++;
		}
	return(1);
}
		
int ishdc(char c) {
	int	i;
	for(i=0;i<=0xf;i++)
		if (toupper(c)==hdc[i]) return(1);
	return(0);
}

int ishdw(char *s) {
	while(1) {
		if (*s=='\0') return(1);
		if (ishdc(*s)==0) return(0);
		s++;
		}
}

int	main(int argc,char *argv[])
{
	FILE	*fp;
	char	w[1000];
	char	defaultdic[]="EIJIRO-1446.TXT";
	char	*dicfile=defaultdic;
  vector<string> data;
	string	str;

	if (argc==2&&(strncmp(*(argv+1),"-d=",3)==0))
			dicfile=&((*(++argv))[3]);

	fp=fopen(dicfile,"r");
	if (fp==NULL) exit(1);
	data.clear();
	while((getword(fp,w))!=NULL) {
		if (ishdw(w)&&isnullstring(w)==0) {
			str=w;
			for(auto & c:str) c=toupper(c);
			data.push_back(str); 
			}
		}
	fclose(fp);

  sort(data.begin(), data.end());
  data.erase(unique(data.begin(), data.end()), data.end());

	for(auto i:data) {
		replace(i.begin(),i.end(),'O','0');
		cout << "0x" << i <<endl;
		}

	exit(0);
}
```
