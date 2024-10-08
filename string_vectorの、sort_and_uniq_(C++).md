


**************************************************


string vectorの、sort and uniq (C++)


**************************************************


string のソート、ユニークをしたい時は多々あります。
ここに、メモを兼ねて、基本中の基本のコードを上げておきます。
このコードは、`$ cat - | sort | uniq`と、同じ働きをします。

```sortuniq.cpp
//
// String vector の、sort | uniq
//
#include	<iostream>
#include	<algorithm>
#include	<vector>
#include	<string>

using namespace std;

int	main()
{
  vector<string> data;
  string  str;

	data.clear();   // データのクリア

  while(1) {      // EOF (Ctrl-D)まで読み込み
    cin >> str;
    if (cin.eof()) break;
    data.push_back(str); 
    }

  sort(data.begin(), data.end());                            // sort

  data.erase(unique(data.begin(), data.end()), data.end());  // uniq

	for(auto i:data) cout << i <<endl;   // 表示

	exit(0);
}
```
