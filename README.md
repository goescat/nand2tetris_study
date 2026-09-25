# nand2tetris 學習筆記

NAND 是 functionally complete（功能完備）的邏輯閘。
只要有 NAND，就可以做出所有基本邏輯閘。

Online IDE:
https://nand2tetris.github.io/web-ide/chip

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
### Not16
16-bit 版本的 Not
CPU 裡面的 16-bit 資料，不是什麼神奇的「16-bit 電路」，而是很多個 1-bit 電路並行工作。

```
CHIP Not16 {
    IN in[16];
    OUT out[16];

    PARTS:
    Not(in=in[0], out=out[0]);
    Not(in=in[1], out=out[1]);
    Not(in=in[2], out=out[2]);
    Not(in=in[3], out=out[3]);
    Not(in=in[4], out=out[4]);
    Not(in=in[5], out=out[5]);
    Not(in=in[6], out=out[6]);
    Not(in=in[7], out=out[7]);
    Not(in=in[8], out=out[8]);
    Not(in=in[9], out=out[9]);
    Not(in=in[10], out=out[10]);
    Not(in=in[11], out=out[11]);
    Not(in=in[12], out=out[12]);
    Not(in=in[13], out=out[13]);
    Not(in=in[14], out=out[14]);
    Not(in=in[15], out=out[15]);
}
```

### And16

```
CHIP And16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
    And(a=a[0] , b=b[0] , out=out[0] );
    And(a=a[1] , b=b[1] , out=out[1] );
    And(a=a[2] , b=b[2] , out=out[2] );
    And(a=a[3] , b=b[3] , out=out[3] );
    And(a=a[4] , b=b[4] , out=out[4] );
    And(a=a[5] , b=b[5] , out=out[5] );
    And(a=a[6] , b=b[6] , out=out[6] );
    And(a=a[7] , b=b[7] , out=out[7] );
    And(a=a[8] , b=b[8] , out=out[8] );
    And(a=a[9] , b=b[9] , out=out[9] );
    And(a=a[10] , b=b[10] , out=out[10] );
    And(a=a[11] , b=b[11] , out=out[11] );
    And(a=a[12] , b=b[12] , out=out[12] );
    And(a=a[13] , b=b[13] , out=out[13] );
    And(a=a[14] , b=b[14] , out=out[14] );
    And(a=a[15] , b=b[15] , out=out[15] );
}
```
### Or16
```
CHIP Or16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
    Or(a=a[0] , b=b[0] , out=out[0] );
    Or(a=a[1] , b=b[1] , out=out[1] );
    Or(a=a[2] , b=b[2] , out=out[2] );
    Or(a=a[3] , b=b[3] , out=out[3] );
    Or(a=a[4] , b=b[4] , out=out[4] );
    Or(a=a[5] , b=b[5] , out=out[5] );
    Or(a=a[6] , b=b[6] , out=out[6] );
    Or(a=a[7] , b=b[7] , out=out[7] );
    Or(a=a[8] , b=b[8] , out=out[8] );
    Or(a=a[9] , b=b[9] , out=out[9] );
    Or(a=a[10] , b=b[10] , out=out[10] );
    Or(a=a[11] , b=b[11] , out=out[11] );
    Or(a=a[12] , b=b[12] , out=out[12] );
    Or(a=a[13] , b=b[13] , out=out[13] );
    Or(a=a[14] , b=b[14] , out=out[14] );
    Or(a=a[15] , b=b[15] , out=out[15] );
}
```

### Mux16

```
CHIP Mux16 {
    IN a[16], b[16], sel;
    OUT out[16];

    PARTS:
    Mux(a=a[0] , b=b[0] , sel=sel , out=out[0] );
    Mux(a=a[1] , b=b[1] , sel=sel , out=out[1] );
    Mux(a=a[2] , b=b[2] , sel=sel , out=out[2] );
    Mux(a=a[3] , b=b[3] , sel=sel , out=out[3] );
    Mux(a=a[4] , b=b[4] , sel=sel , out=out[4] );
    Mux(a=a[5] , b=b[5] , sel=sel , out=out[5] );
    Mux(a=a[6] , b=b[6] , sel=sel , out=out[6] );
    Mux(a=a[7] , b=b[7] , sel=sel , out=out[7] );
    Mux(a=a[8] , b=b[8] , sel=sel , out=out[8] );
    Mux(a=a[9] , b=b[9] , sel=sel , out=out[9] );
    Mux(a=a[10] , b=b[10] , sel=sel , out=out[10] );
    Mux(a=a[11] , b=b[11] , sel=sel , out=out[11] );
    Mux(a=a[12] , b=b[12] , sel=sel , out=out[12] );
    Mux(a=a[13] , b=b[13] , sel=sel , out=out[13] );
    Mux(a=a[14] , b=b[14] , sel=sel , out=out[14] );
    Mux(a=a[15] , b=b[15] , sel=sel , out=out[15] );
}
```

### Or8Way
這 8 個 bit 有沒有任何一個是 1？

```
CHIP Or8Way {
    IN in[8];
    OUT out;

    PARTS:
    Or(a=in[0] , b=in[1] , out=out1 );
    Or(a=out1 , b=in[2] , out=out2 );
    Or(a=out2 , b=in[3] , out=out3 );
    Or(a=out3 , b=in[4] , out=out4 );
    Or(a=out4 , b=in[5] , out=out5 );
    Or(a=out5 , b=in[6] , out=out6 );
    Or(a=out6 , b=in[7] , out=out );
}
```

```
CHIP Or8Way {
    IN in[8];
    OUT out;

    PARTS:
    Or(a=in[0], b=in[1], out=a);
    Or(a=in[2], b=in[3], out=b);
    Or(a=in[4], b=in[5], out=c);
    Or(a=in[6], b=in[7], out=d);

    Or(a=a, b=b, out=e);
    Or(a=c, b=d, out=f);

    Or(a=e, b=f, out=out);
}
```

上面差異主要在「電路結構」。

原本寫法：串接
```
in0 ─┐
     OR ──┐
in1 ─┘    │
          OR ──┐
in2 ──────┘    │
               OR ──┐
in3 ────────────┘   │
                    ...
```

最壞情況要經過 7 層 OR 才到 out。

另一種：樹狀
```
in0 ─┐
     OR ──┐
in1 ─┘    │
in2 ─┐    OR ──┐
     OR ──┘    │
in3 ─┘         │
               OR ── out
in4 ─┐         │
     OR ──┐    │
in5 ─┘    │    │
in6 ─┐    OR ─┘
     OR ──┘
in7 ─┘
```
最多只需要經過 3 層 OR。

假設每個 OR gate 都需要 1 單位時間，原先需要最深 7 個 gate。

樹狀最深 3 個 gate。

### Mux4Way16
4 組 16-bit 輸入，選 1 組 16-bit 輸出。

```
CHIP Mux4Way16 {
    IN a[16], b[16], c[16], d[16], sel[2];
    OUT out[16];
    
    PARTS:
    Mux16(a=a , b=b , sel=sel[0] , out=ab );
    Mux16(a=c , b=d , sel=sel[0] , out=cd );
    Mux16(a=ab , b=cd , sel=sel[1] , out=out );


}
```
### Mux8Way16
```
CHIP Mux8Way16 {
    IN a[16], b[16], c[16], d[16],
       e[16], f[16], g[16], h[16],
       sel[3];
    OUT out[16];

    PARTS:
    Mux16(a=a , b=b , sel=sel[0] , out=ab );
    Mux16(a=c , b=d , sel=sel[0] , out=cd );
    Mux16(a=e , b=f , sel=sel[0] , out=ef );
    Mux16(a=g , b=h , sel=sel[0] , out=gh );
    Mux16(a=ab , b=cd , sel=sel[1] , out=out1 );
    Mux16(a=ef , b=gh , sel=sel[1] , out=out2 );
    Mux16(a=out1 , b=out2 , sel=sel[2] , out=out );
}
```
