# nand2tetris 學習筆記

NAND 是 functionally complete（功能完備）的邏輯閘。
只要有 NAND，就可以做出所有基本邏輯閘。

Online IDE:
https://nand2tetris.github.io/web-ide/chip

## 邏輯閘
### NOT
NOT A = NAND(A,A)

```
CHIP Not {
    IN in;
    OUT out;

    PARTS:
    Nand(a=in , b=in , out=out );
}
```
### AND
AND：

AND(A,B) = NOT(NAND(A,B))
= NAND(NAND(A,B), NAND(A,B))

```
CHIP And {
    IN a, b;
    OUT out;
    
    PARTS:
    Nand(a=a , b=b , out=nandOut );
    Nand(a=nandOut , b=nandOut , out=out );
}
```
### OR

(一開始稍微走歪XD)

OR(A, B) = NOT(NOT A AND NOT B)

= NOT(NAND(A, A) AND NAND(B, B))

= NOT(NAND(NAND(A, A), NAND(B, B)))

= NAND(NAND(NAND(A, A), NAND(B, B)), NAND(NAND(A, A), NAND(B, B)))

```
CHIP Or {
    IN a, b;
    OUT out;

    PARTS:

    Nand(a=a , b=a , out=notA );
    Nand(a=b , b=b , out=notB );

    Nand(a=notA , b=notB , out=nandAB );
    Nand(a=nandAB , b=nandAB , out=notAB );

    Nand(a=notAB , b=notAB , out=out );
}
```

OR(A,B) = NOT(NOT A AND NOT B)？
= NOT(NAND(A, A) AND NAND(B, B))？？所以應該是
= NAND(NAND(A,A), NAND(B,B))

```
CHIP Or {
    IN a, b;
    OUT out;

    PARTS:
    Nand(a=a , b=a , out=notA );
    Nand(a=b , b=b , out=notB );
    Nand(a=notA , b=notB , out=out );
}
```
### XOR

(一樣一開始直接展開XD)
XOR(A, B) = (A OR B) AND NOT(A AND B)

```
CHIP Xor {
    IN a, b;
    OUT out;

    PARTS:
//or
    Nand(a=a , b=a , out=notA );
    Nand(a=b , b=b , out=notB );
    Nand(a=notA , b=notB , out=outOrAB );

//and
    Nand(a=a , b=b , out=nandOut );
    Nand(a=nandOut , b=nandOut , out=andAB );

//not
    Nand(a=andAB , b=andAB , out=notAndAB );


//and
    Nand(a=outOrAB , b=notAndAB , out=orNotAndOut );
    Nand(a=orNotAndOut , b=orNotAndOut , out=out );

}
```
最佳化：

XOR(A,B) = 4 × NAND
(A OR B) AND NOT(A AND B)

```
CHIP Xor {
    IN a, b;
    OUT out;

    PARTS:
    Nand(a=a, b=b, out=x);
    Nand(a=a, b=x, out=y);
    Nand(a=b, b=x, out=z);
    Nand(a=y, b=z, out=out);
}
```
Nand(A, B) = NOT(A AND B)

### Mux(Multiplexer)


a：資料 A
b：資料 B
sel：選擇哪一個

sel = 0 → 選 a
sel = 1 → 選 b

```
CHIP Mux {
    IN a, b, sel;
    OUT out;

    PARTS:
    //out = (a AND NOT sel) OR (b AND sel)

    Not(in=sel , out=notSel );

    And(a=a, b=notSel, out= out1 );

    And(a=b , b=sel , out=out2 );

    Or(a=out1 , b=out2 , out=out );

}
```

### DMux(Demultiplexer)

我有一個輸入，幫我決定要送到哪一個輸出

sel = 0 → a = in
sel = 1 → b = in

```
CHIP DMux {
    IN in, sel;
    OUT a, b;

    PARTS:
    Not(in=sel , out=selNot );
    And(a=in, b=selNot, out=a);
    And(a=in, b=sel, out=b);

}
```
