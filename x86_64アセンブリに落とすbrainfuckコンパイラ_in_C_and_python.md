


**************************************************


x86_64アセンブリに落とすbrainfuckコンパイラ in C and python


**************************************************


Brainfuckソースをアセンブリに変換するコンパイラbfs

cc bfs.c -o bfsとしてコンパイルしてください。

./bfs file.bfとすると、file.bfからbrainfuckのソースを読み込み、x86_64アセンブリソースに変換して標準出力に出力します。リダイレクトしてご使用ください。
for ex.
    ./bfs file.bf >file.s
出力されたソースはccの出力する.sファイルと同様ですので、
cc file.s
とするとa.outに実行コードが出力されます。

```bfs.c
#include    <stdio.h>
#include    <stdlib.h>
static int loopstack[1000];
static int lsp=0;

void lsout() {
    for (int i=0;i<lsp;i++)
        printf("%d_",loopstack[i]);
}

void main(int argc,char *argv[]) {
    FILE *fp;
    int  c,lf;
    if (argc!=2) {
        fprintf(stderr,"Usage: bfs <file>\n");
        exit(1);
    }
    fp=fopen(argv[1],"rt");
    if (fp==NULL) {
        fprintf(stderr,"File open error.\n");
        exit(1);
    }

    printf("\t.file\t\"%s\"\n",argv[1]);
	printf("\t.text\n");
	printf("\t.globl\tmain\n");
	printf("\t.type\tmain,@function\n");
    printf("main:\n");
    printf(".LFB0:\n");
    printf("\t.cfi_startproc\n");
	printf("\tpushq\t%%rbp\n");
	printf("\t.cfi_def_cfa_offset 16\n");
	printf("\t.cfi_offset 6, -16\n");
	printf("\tmovq\t%%rsp, %%rbp\n");
	printf("\t.cfi_def_cfa_register 6\n");
	printf("\tsubq\t$30032, %%rsp\n");
	printf("\tmovq\t%%fs:40, %%rax\n");
	printf("\tmovq\t%%rax, -8(%%rbp)\n");
	printf("\txorl\t%%eax, %%eax\n");
	printf("\tleaq\t-30016(%%rbp), %%rax\n");
	printf("\tmovl\t$30000, %%edx\n");
	printf("\tmovl\t$0, %%esi\n");
	printf("\tmovq\t%%rax, %%rdi\n");
	printf("\tcall\tmemset@PLT\n");
	printf("\tleaq\t-30016(%%rbp), %%rax\n");
	printf("\tmovq\t%%rax, -30024(%%rbp)\n");

    lsp=0;
    lf='[';
    while((c=fgetc(fp))!=EOF) {
        switch (c) {
            case '>':
                printf("\taddq\t$1, -30024(%%rbp)\n");
                break;
            case '<':
                printf("\tsubq\t$1, -30024(%%rbp)\n");
                break;
            case '+':
				printf("\tmovq\t-30024(%%rbp), %%rax\n");
				printf("\tmovzbl\t(%%rax), %%eax\n");
				printf("\taddl\t$1, %%eax\n");
				printf("\tmovl\t%%eax, %%edx\n");
				printf("\tmovq\t-30024(%%rbp), %%rax\n");
				printf("\tmovb\t%%dl, (%%rax)\n");
                break;
            case '-':
				printf("\tmovq\t-30024(%%rbp), %%rax\n");
				printf("\tmovzbl\t(%%rax), %%eax\n");
				printf("\tsubl\t$1, %%eax\n");
				printf("\tmovl\t%%eax, %%edx\n");
				printf("\tmovq\t-30024(%%rbp), %%rax\n");
				printf("\tmovb\t%%dl, (%%rax)\n");
                break;
            case '.':
				printf("\tmovq\t-30024(%%rbp), %%rax\n");
				printf("\tmovzbl\t(%%rax), %%eax\n");
				printf("\tmovsbl\t%%al, %%eax\n");
				printf("\tmovl\t%%eax, %%edi\n");
				printf("\tcall\tputchar@PLT\n");
                break;
            case ',':
				printf("\tcall	getchar@PLT\n");
				printf("\tmovl	%%eax, %%edx\n");
				printf("\tmovq	-30024(%%rbp), %%rax\n");
				printf("\tmovb	%%dl, (%%rax)\n");
                break;
            case '[':
                if (lf==']')
                    loopstack[(lsp-1)]++;
                else
                    loopstack[lsp++]=1;
                lf='[';
                printf("\tjmp LE"); lsout(); printf("\n");
                printf("LB"); lsout(); printf(":\n");
                break;
            case ']':
                if (lf==']')
                    lsp--;
                lf=']';
                printf("LE"); lsout(); printf(":\n");
                printf("\tmovq\t-30024(%%rbp), %%rax\n");
	            printf("\tmovzbl\t(%%rax), %%eax\n");
	            printf("\ttestb\t%%al, %%al\n");
                printf("\tjne\tLB"); lsout(); printf("\n");
                break;
            default:
            }
    }
    fclose(fp);
	printf("\tnop\n");
	printf("\tmovq\t-8(%%rbp), %%rax\n");
	printf("\tsubq\t%%fs:40, %%rax\n");
	printf("\tje\t.LFE1\n");
	printf("\tcall\t__stack_chk_fail@PLT\n");
	printf(".LFE1:\n");
	printf("\tleave\n");
	printf("\t.cfi_def_cfa 7, 8\n");
	printf("\tret\n");
	printf("\t.cfi_endproc\n");
	printf(".LFE0:\n");
	printf("\t.size\tmain, .-main\n");
	printf("\t.section\t.note.GNU-stack,\"\",@progbits\n");
    exit(0);
}

```

### python版

こちらはpython3で書いたbfsです。
C版と同じコードを出力します。
使い方：
./bfs.py sample.bf >sample.s

```bfs.py
#!/usr/bin/python3
import sys

def lsout(loopstack):
    return str(loopstack).replace(', ','_').replace('[','').replace(']','')

def main():
    if len(sys.argv)!=2:
        print(f"Usage: {sys.argv[0]} <file>")
        return

    print(f"\t.file\t\"{sys.argv[1]}\"")
    print("    .text")
    print("    .globl  main")
    print("    .type   main,@function")
    print("main:")
    print(".LFB0:")
    print("    .cfi_startproc")
    print("    pushq    %rbp")
    print("    .cfi_def_cfa_offset 16")
    print("    .cfi_offset 6, -16")
    print("    movq    %rsp, %rbp")
    print("    .cfi_def_cfa_register 6")
    print("    subq    $30032, %rsp")
    print("    movq    %fs:40, %rax")
    print("    movq    %rax, -8(%rbp)")
    print("    xorl    %eax, %eax")
    print("    leaq    -30016(%rbp), %rax")
    print("    movl    $30000, %edx")
    print("    movl    $0, %esi")
    print("    movq    %rax, %rdi")
    print("    call    memset@PLT")
    print("    leaq    -30016(%rbp), %rax")
    print("    movq    %rax, -30024(%rbp)")

    f=open(sys.argv[1],"rt")
    loopstack=[]
    lf='['
    for s in f:
        for c in s:
            if c=='>':
                print("    addq    $1, -30024(%rbp)")
            elif c=='<':
                print("    subq    $1, -30024(%rbp)")
            elif c=='+':
                print("    movq    -30024(%rbp), %rax")
                print("    movzbl  (%rax), %eax")
                print("    addl    $1, %eax")
                print("    movl    %eax, %edx")
                print("    movq    -30024(%rbp), %rax")
                print("    movb    %dl, (%rax)")
            elif c=='-':
                print("    movq    -30024(%rbp), %rax")
                print("    movzbl  (%rax), %eax")
                print("    subl    $1, %eax")
                print("    movl    %eax, %edx")
                print("    movq    -30024(%rbp), %rax")
                print("    movb    %dl, (%rax)")
            elif c=='.':
                print("    movq    -30024(%rbp), %rax")
                print("    movzbl  (%rax), %eax")
                print("    movsbl  %al, %eax")
                print("    movl    %eax, %edi")
                print("    call    putchar@PLT")
            elif c==',':
                print("    call    getchar@PLT")
                print("    movl    %eax, %edx")
                print("    movq    -30024(%rbp), %rax")
                print("    movb    %dl, (%rax)")
            elif c=='[':
                if lf==']':
                    loopstack[len(loopstack)-1]+=1
                else:
                    loopstack.append(1)
                lf='['
                print(f"    jmp     LE{lsout(loopstack)}")
                print(f"LB{lsout(loopstack)}:")
            elif c==']':
                if lf==']':
                    loopstack.pop()
                lf=']'
                print(f"LE{lsout(loopstack)}:")
                print("    movq    -30024(%rbp), %rax")
                print("    movzbl  (%rax), %eax")
                print("    testb   %al, %al")
                print(f"    jne     LB{lsout(loopstack)}")
    f.close()
    print("    nop")
    print("    movq    -8(%rbp), %rax")
    print("    subq    %fs:40, %rax")
    print("    je      .LFE1")
    print("    call    __stack_chk_fail@PLT")
    print(".LFE1:")
    print("    leave")
    print("    .cfi_def_cfa 7, 8")
    print("    ret")
    print("    .cfi_endproc")
    print(".LFE0:")
    print("    .size   main, .-main")
    print("    .section .note.GNU-stack,\"\",@progbits")

if __name__=='__main__':
    main()
    exit(0)


```
