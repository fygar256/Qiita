


**************************************************


素因数分解をするrubyスクリプト


**************************************************



素因数分解をするrubyスクリプトです。
`chmod +x factor.rb`
`./factor.rb <n>`
として動かしてください。

```factor.rb
#!/usr/bin/ruby
def primefactorization(n)
  if n==0 || n==1
    return []
  end
  i=2
  f=[]
  while n!=1 do
    if n%i==0
      f+=[i]
      n/=i
    else
      i=i+1
    end
  end
  f
end

n=ARGV[0].to_i
f=primefactorization(n)
print n,":",f,"\n"
```
