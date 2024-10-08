


**************************************************


golangで、brainfuckソースをx86_64アセンブリに落とすコンパイラを書いてみた。［習作］


**************************************************



go言語を取り掛かるにしたら、何を書けばよいかを思案しているうちに、brainfuck compilerを書くことにした。やってることは難しそうに見えても、実際の処理は簡単だし、すぐにできるだろうと思って書いたが、変数の扱いでちょっと引っ掛かった。

### 実行

goはシバンをつけるのがややこしいので、`go run bfs.go <file.bf> >out.s`としてコンパイルして下さい。
out.sはccでアセンブルできるので、`cc out.s`として、アセンブリファイルをアセンブル、
`$./a.out` で実行して下さい。

### コメント

標準出力に結果が出力されます。やってることは、拙作のbfs.c,bfs.pyと一緒です。

https://qiita.com/fygar256/items/3274f1dfc14378645350

相変わらずエラー処理がプアです。人間は何をするかわからないから、エラー処理はいくらやってもキリがないので。ガベージイン・ガベージアウトです。

pythonもそこそこに、これからgoをやるぞ、と思っていましたが、リストが使えないことが大分不便で、pythonは手放せそうにないです。ネイティブコンパイルは魅力ですね。goはsyntaxがきれいで、pythonよりエラーハンドリングが楽なんですけど。どの言語も一長一短があり、完全なプログラミング言語の登場を待っていますが、完全なプログラミング言語が出来たときは、プログラミング言語やデータ構造の知識が完成したときであり、そこから進歩はなくなってしまうでしょうが、完成した方がいいです。手続き型言語の完成を待っています。とりあえず、pythonが一番じゃのう。

### 本体

```bfs.go
package main

import (
    "fmt"
    "os"
    "io"
)

var (
    lf int ='['
    loopstack=[]int{}
)

func lsout(loopstack [] int) {
    for i:=0;i<len(loopstack);i++ {
        fmt.Printf("%d_",loopstack[i])
    }
}

func main() {
    if len(os.Args)!=2 {
        fmt.Println("Usage: go run bfs.go file.bf >out.s")
        return
    }
    fmt.Println("    .text")
    fmt.Println("    .globl  main")
    fmt.Println("    .type   main,@function")
    fmt.Println("main:")
    fmt.Println(".LFB0:")
    fmt.Println("    .cfi_startproc")
    fmt.Println("    pushq    %rbp")
    fmt.Println("    .cfi_def_cfa_offset 16")
    fmt.Println("    .cfi_offset 6, -16")
    fmt.Println("    movq    %rsp, %rbp")
    fmt.Println("    .cfi_def_cfa_register 6")
    fmt.Println("    subq    $30032, %rsp")
    fmt.Println("    movq    %fs:40, %rax")
    fmt.Println("    movq    %rax, -8(%rbp)")
    fmt.Println("    xorl    %eax, %eax")
    fmt.Println("    leaq    -30016(%rbp), %rax")
    fmt.Println("    movl    $30000, %edx")
    fmt.Println("    movl    $0, %esi")
    fmt.Println("    movq    %rax, %rdi")
    fmt.Println("    call    memset@PLT")
    fmt.Println("    leaq    -30016(%rbp), %rax")
    fmt.Println("    movq    %rax, -30024(%rbp)")

    file,err := os.Open(os.Args[1])
    byteData,err:=io.ReadAll(file)
    _ =err

    for idx:=0;idx<len(byteData);idx++ {
        switch byteData[idx] {
        case '>':
            fmt.Println("    addq    $1, -30024(%rbp)")
        case '<':
            fmt.Println("    subq    $1, -30024(%rbp)")
        case '+':
            fmt.Println("    movq    -30024(%rbp), %rax")
            fmt.Println("    movzbl  (%rax), %eax")
            fmt.Println("    addl    $1, %eax")
            fmt.Println("    movl    %eax, %edx")
            fmt.Println("    movq    -30024(%rbp), %rax")
            fmt.Println("    movb    %dl, (%rax)")
        case '-':
            fmt.Println("    movq    -30024(%rbp), %rax")
            fmt.Println("    movzbl  (%rax), %eax")
            fmt.Println("    subl    $1, %eax")
            fmt.Println("    movl    %eax, %edx")
            fmt.Println("    movq    -30024(%rbp), %rax")
            fmt.Println("    movb    %dl, (%rax)")
        case '.':
            fmt.Println("    movq    -30024(%rbp), %rax")
            fmt.Println("    movzbl  (%rax), %eax")
            fmt.Println("    movsbl  %al, %eax")
            fmt.Println("    movl    %eax, %edi")
            fmt.Println("    call    putchar@PLT")
        case ',':
            fmt.Println("    call    getchar@PLT")
            fmt.Println("    movl    %eax, %edx")
            fmt.Println("    movq    -30024(%rbp), %rax")
            fmt.Println("    movb    %dl, (%rax)")
        case '[':
            if lf==']' {
                loopstack[len(loopstack)-1]+=1
            } else {
                loopstack = append(loopstack,1)
            }
            lf='['
            fmt.Print("    jmp     LE")
            lsout(loopstack)
            fmt.Println("")
            fmt.Print("LB")
            lsout(loopstack)
            fmt.Println(":")
        case ']':
            if lf==']' {
                loopstack=loopstack[:len(loopstack)-1]
            }
            lf=']'
            fmt.Print("LE")
            lsout(loopstack)
            fmt.Println(":")
            fmt.Println("    movq    -30024(%rbp), %rax")
            fmt.Println("    movzbl  (%rax), %eax")
            fmt.Println("    testb   %al, %al")
            fmt.Print("    jne     LB")
            lsout(loopstack)
            fmt.Println("")
        default:
        }
    }

    fmt.Println("    nop")
    fmt.Println("    movq    -8(%rbp), %rax")
    fmt.Println("    subq    %fs:40, %rax")
    fmt.Println("    je      .LFE1")
    fmt.Println("    call    __stack_chk_fail@PLT")
    fmt.Println(".LFE1:")
    fmt.Println("    leave")
    fmt.Println("    .cfi_def_cfa 7, 8")
    fmt.Println("    ret")
    fmt.Println("    .cfi_endproc")
    fmt.Println(".LFE0:")
    fmt.Println("    .size   main, .-main")
    fmt.Println("    .section .note.GNU-stack,\"\",@progbits")
}
```
