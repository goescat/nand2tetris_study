# nand2tetris 學習筆記

## Project 1
Project 1 用 Primitive Gate （這裡是 NAND） 組出各種邏輯閘。

NAND 是 functionally complete（功能完備）的邏輯閘。
只要有 NAND，就可以做出所有基本邏輯閘。

--

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
### DMux4Way

```
CHIP DMux4Way {
    IN in, sel[2];
    OUT a, b, c, d;

    PARTS:
    DMux(in=in , sel=sel[1] , a=ab , b=cd );
    DMux(in=ab , sel=sel[0] , a=a , b=b );
    DMux(in=cd , sel=sel[0] , a=c , b=d );

}
```

### DMux8Way
```
CHIP DMux8Way {
    IN in, sel[3];
    OUT a, b, c, d, e, f, g, h;

    PARTS:
    DMux(in=in , sel=sel[2] , a=abcd , b=efgh );
    DMux(in=abcd , sel=sel[1] , a=ab , b=cd );
    DMux(in=efgh , sel=sel[1] , a=ef , b=gh );
    DMux(in=ab , sel=sel[0] , a=a , b=b );
    DMux(in=cd , sel=sel[0] , a=c , b=d );
    DMux(in=ef , sel=sel[0] , a=e , b=f );
    DMux(in=gh , sel=sel[0] , a=g , b=h );

}
```

--

額外筆記：

NOR 也是功能完備邏輯閘，所以我好奇為什麼 nand2tetris 不是 nor2tetris XDD

下面是查到的資料：

CMOS 電路由 NMOS（利用電子導電）和 PMOS（利用電洞導電）組成。

NMOS 的導通速度比 PMOS 快，因為電子的遷移率大約是電洞的 2 到 3 倍。

為了讓電路的上升時間（Rise time）和下降時間（Fall time）對稱，PMOS 的尺寸通常必須設計得比 NMOS 大 2 到 3 倍。這直接影響了兩者的電路佈局：

NAND 閘：
* PMOS 銜接方式：並聯（Parallel）
* NMOS 銜接方式，串聯（Series）
* 尺寸設計需求：串聯的 NMOS 速度雖變慢，但因為電子本來就快，尺寸不需放大太多。

NOR 閘：
* PMOS 銜接方式：串聯（Series）
* NMOS 銜接方式，並聯（Parallel）
* 尺寸設計需求：串聯的 PMOS 速度更慢，為了彌補速度，必須把 PMOS 的尺寸放得非常大。

NOR 閘因為有巨大的 PMOS 晶體，會產生很大的輸入電容，這會導致訊號傳播延遲變大。在相同的驅動能力下，NAND 閘的切換速度明顯快於 NOR 閘。

總之 NOR 閘需要做得比較大，代表它會比較慢，而且成本比較高。

    
## Project 2
用 Project 1 的邏輯閘和電路組出加法器與 ALU，讓 0 1 開始具備計算的能力。

### HalfAdder

最小的加法器，輸入 a b ，輸出 sum 和 carry。

例如：  1 + 1 = 10

sum = 0 

carry = 1

```
CHIP HalfAdder {
    IN a, b;    // 1-bit inputs
    OUT sum,    // Right bit of a + b 
        carry;  // Left bit of a + b

    PARTS:
    Xor(a=a , b=b , out=sum );
    And(a=a , b=b , out=carry );
}
```

### FullAdder
多位數加法還需要處理上一位的進位，所以 FullAdder 有三個輸入：a b c

c 為 carry-in。

```
CHIP FullAdder {
    IN a, b, c;  // 1-bit inputs
    OUT sum,     // Right bit of a + b + c
        carry;   // Left bit of a + b + c

    PARTS:
    HalfAdder(a=a , b=b , sum=sum1 , carry=carry1 );
    HalfAdder(a=sum1 , b=c , sum=sum , carry=carry2 );
    Or(a=carry1 , b=carry2 , out=carry );
}
```

### Add16
16-bit 加法器。

```
CHIP Add16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
    FullAdder(a=a[0] , b=b[0] , c=false , sum=out[0] , carry=carry0 );
    FullAdder(a=a[1] , b=b[1] , c=carry0 , sum=out[1] , carry=carry1 );
    FullAdder(a=a[2] , b=b[2] , c=carry1 , sum=out[2] , carry=carry2 );
    FullAdder(a=a[3] , b=b[3] , c=carry2 , sum=out[3] , carry=carry3 );
    FullAdder(a=a[4] , b=b[4] , c=carry3 , sum=out[4] , carry=carry4 );
    FullAdder(a=a[5] , b=b[5] , c=carry4 , sum=out[5] , carry=carry5 );
    FullAdder(a=a[6] , b=b[6] , c=carry5 , sum=out[6] , carry=carry6 );
    FullAdder(a=a[7] , b=b[7] , c=carry6 , sum=out[7] , carry=carry7 );
    FullAdder(a=a[8] , b=b[8] , c=carry7 , sum=out[8] , carry=carry8 );
    FullAdder(a=a[9] , b=b[9] , c=carry8 , sum=out[9] , carry=carry9 );
    FullAdder(a=a[10] , b=b[10] , c=carry9 , sum=out[10] , carry=carry10 );
    FullAdder(a=a[11] , b=b[11] , c=carry10 , sum=out[11] , carry=carry11 );
    FullAdder(a=a[12] , b=b[12] , c=carry11 , sum=out[12] , carry=carry12 );
    FullAdder(a=a[13] , b=b[13] , c=carry12 , sum=out[13] , carry=carry13 );
    FullAdder(a=a[14] , b=b[14] , c=carry13 , sum=out[14] , carry=carry14 );
    FullAdder(a=a[15] , b=b[15] , c=carry14 , sum=out[15] , carry=carry15 );
}
```
### Inc16
16-bit 數字 + 1。

```
CHIP Inc16 {
    IN in[16];
    OUT out[16];

    PARTS:
    Add16(a=in, b[0]=true, b[1..15]=false, out=out);
}
```

### ALU

Arithmetic Logic Unit。
ALU 其實就是一連串 Mux，不斷選擇要用哪一個輸入。

--

* out：計算結果
* zr：結果是否為 0
* ng：結果為負

為什麼 ALU 要多給 zr 和 ng？

因為 CPU 很常需要做結果是不是 0、結果是不是負數。

例如 Assembly 裡面想做條件跳轉：如果結果 == 0：跳；如果結果 < 0：跳。

--

out[15]？怎麼表示負數？

這裡使用二補數，16 個 bit 一共有 2^16 = 65536 種組合。

如果全部拿來表示正整數就是 0 ~ 65535，但這裡選擇把其中一半拿來表示負數：-32768 ~ 32767。

最高位 bit 15 是 1 時就是負數。

--

假設我們要表示 -1

先拿 1：0000 0000 0000 0001

全部反轉：1111 1111 1111 1110

再加 1：1111 1111 1111 1111

1111 1111 1111 1111 = -1


```
CHIP ALU {
    IN  
        x[16], y[16],  // 16-bit inputs        
        zx, // zero the x input?
        nx, // negate the x input?
        zy, // zero the y input?
        ny, // negate the y input?
        f,  // compute (out = x + y) or (out = x & y)?
        no; // negate the out output?
    OUT 
        out[16], // 16-bit output
        zr,      // if (out == 0) equals 1, else 0
        ng;      // if (out < 0)  equals 1, else 0

    PARTS:
    // zero the x input
    Mux16(a=x , b=false , sel=zx , out=outZx );

    // negate the x input
    Not16(in=outZx, out=notX);
    Mux16(a=outZx , b=notX , sel=nx , out=outNx );

    // zero the y input
    Mux16(a=y , b=false , sel=zy , out=outZy );

    // negate the y input
    Not16(in=outZy, out=notY);
    Mux16(a=outZy , b=notY , sel=ny , out=outNy );

    // compute (out = x + y) or (out = x & y)
    Add16(a=outNx , b=outNy , out=xyAdd );
    And16(a=outNx , b=outNy , out=xyAnd );
    Mux16(a=xyAnd , b=xyAdd , sel=f , out=outf );

    // negate the out output
    // ng
    Not16(in=outf , out=outfN );
    Mux16(
        a=outf,
        b=outfN,
        sel=no,
        out=out,
        out[0..7]=outL,
        out[8..15]=outH,
        out[15]=ng
    );

    //zr
    Or8Way(in=outL, out=lOne);
    Or8Way(in=outH, out=hOne);

    Or(a=lOne, b=hOne, out=hasOne);

    Not(in=hasOne, out=zr);
}
```
## Project 3

Sequential Logic。

DFF（D Flip-Flop）：在 clock 到來的時候，把 in 記住，然後從 out 輸出。

### Bit
最基本的記憶單位，可以記住 0 或 1。
核心元件是 DFF。

```
CHIP Bit {
    IN in, load;
    OUT out;

    PARTS:
    Mux(a=t, b=in, sel=load, out=ot);
    DFF(in=ot , out=t, out=out );
    
}
```

### Register
把 16 個 Bit 放在一起。

```
CHIP Register {
    IN in[16], load;
    OUT out[16];

    PARTS:
    Bit(in=in[0] , load=load , out=out[0] );
    Bit(in=in[1] , load=load , out=out[1] );
    Bit(in=in[2] , load=load , out=out[2] );
    Bit(in=in[3] , load=load , out=out[3] );
    Bit(in=in[4] , load=load , out=out[4] );
    Bit(in=in[5] , load=load , out=out[5] );
    Bit(in=in[6] , load=load , out=out[6] );
    Bit(in=in[7] , load=load , out=out[7] );
    Bit(in=in[8] , load=load , out=out[8] );
    Bit(in=in[9] , load=load , out=out[9] );
    Bit(in=in[10] , load=load , out=out[10] );
    Bit(in=in[11] , load=load , out=out[11] );
    Bit(in=in[12] , load=load , out=out[12] );
    Bit(in=in[13] , load=load , out=out[13] );
    Bit(in=in[14] , load=load , out=out[14] );
    Bit(in=in[15] , load=load , out=out[15] );
}
```

### RAM8

8 × Register。

剛剛 Register 本身只有一個位置，不需要問 Register 裡面的哪一格，但 RAM 有很多格，所以需要記憶體位址（address）。

```
CHIP RAM8 {
    IN in[16], load, address[3];
    OUT out[16];

    PARTS:
    DMux8Way(in=load , sel=address , a=a , b=b , c=c , d=d , e=e , f=f , g=g , h=h );
    Register(in=in , load=a , out=r0 );
    Register(in=in , load=b , out=r1 );
    Register(in=in , load=c , out=r2 );
    Register(in=in , load=d , out=r3 );
    Register(in=in , load=e , out=r4 );
    Register(in=in , load=f , out=r5 );
    Register(in=in , load=g , out=r6 );
    Register(in=in , load=h , out=r7 );
    Mux8Way16(a=r0 , b=r1 , c=r2 , d=r3 , e=r4 , f=r5 , g=r6 , h=r7 , sel=address , out=out );
}
```

### RAM64

8 × RAM8。
RAM64 有 64 個 16-bit 儲存位置。
因為 8 × 8 = 64

需要 2⁶ = 64

所以 address[6]

這時候 address 可以拆成：
高 3 bits → 選 RAM8，低 3 bits → RAM8 裡面的 Register。

```
CHIP RAM64 {
    IN in[16], load, address[6];
    OUT out[16];

    PARTS:
    DMux8Way(in=load , sel=address[3..5] , a=a , b=b , c=c , d=d , e=e , f=f , g=g , h=h );
    RAM8(in=in , load=a , address=address[0..2] , out=o0 );
    RAM8(in=in , load=b , address=address[0..2] , out=o1 );
    RAM8(in=in , load=c , address=address[0..2] , out=o2 );
    RAM8(in=in , load=d , address=address[0..2] , out=o3 );
    RAM8(in=in , load=e , address=address[0..2] , out=o4 );
    RAM8(in=in , load=f , address=address[0..2] , out=o5 );
    RAM8(in=in , load=g , address=address[0..2] , out=o6 );
    RAM8(in=in , load=h , address=address[0..2] , out=o7 );

    Mux8Way16(a=o0 , b=o1 , c=o2 , d=o3 , e=o4 , f=o5 , g=o6 , h=o7 , sel=address[3..5] , out=out );

}
```

### RAM512

8 × RAM64。

2⁹ = 512

address[9]

高 3 bits → 選 RAM64，低 6 bits → RAM64 裡的位置。

```
CHIP RAM512 {
    IN in[16], load, address[9];
    OUT out[16];

    PARTS:
    DMux8Way(in=load , sel=address[6..8] , a=a , b=b , c=c , d=d , e=e , f=f , g=g , h=h );
    RAM64(in=in , load=a , address=address[0..5] , out=o0 );
    RAM64(in=in , load=b , address=address[0..5] , out=o1 );
    RAM64(in=in , load=c , address=address[0..5] , out=o2 );
    RAM64(in=in , load=d , address=address[0..5] , out=o3 );
    RAM64(in=in , load=e , address=address[0..5] , out=o4 );
    RAM64(in=in , load=f , address=address[0..5] , out=o5 );
    RAM64(in=in , load=g , address=address[0..5] , out=o6 );
    RAM64(in=in , load=h , address=address[0..5] , out=o7 );

    Mux8Way16(a=o0 , b=o1 , c=o2 , d=o3 , e=o4 , f=o5 , g=o6 , h=o7 , sel=address[6..8] , out=out );

}
```

### RAM4K
8 × RAM512。

8 × 512 = 4096

2¹² = 4096

高 3 bits → 選 RAM512，低 9 bits → RAM512 裡的位置。

```
CHIP RAM4K {
    IN in[16], load, address[12];
    OUT out[16];

    PARTS:
    DMux8Way(in=load , sel=address[9..11] , a=a , b=b , c=c , d=d , e=e , f=f , g=g , h=h );
    
    RAM512(in=in , load=a , address=address[0..8] , out=o0 );
    RAM512(in=in , load=b , address=address[0..8] , out=o1 );
    RAM512(in=in , load=c , address=address[0..8] , out=o2 );
    RAM512(in=in , load=d , address=address[0..8] , out=o3 );
    RAM512(in=in , load=e , address=address[0..8] , out=o4 );
    RAM512(in=in , load=f , address=address[0..8] , out=o5 );
    RAM512(in=in , load=g , address=address[0..8] , out=o6 );
    RAM512(in=in , load=h , address=address[0..8] , out=o7 );

    Mux8Way16(a=o0 , b=o1 , c=o2 , d=o3 , e=o4 , f=o5 , g=o6 , h=o7 , sel=address[9..11] , out=out );

}
```

### RAM16K
4 × RAM4K。
4 × 4096
= 16384
= 16K

2¹⁴ = 16384

14-bit address

高 2 bits → 選 RAM4K，低 12 bits → RAM4K 裡的位置。

```
CHIP RAM16K {
    IN in[16], load, address[14];
    OUT out[16];

    PARTS:
    DMux4Way(in=load , sel=address[12..13], a=a , b=b , c=c , d=d );
    
    RAM4K(in=in , load=a , address=address[0..11] , out=o0 );
    RAM4K(in=in , load=b , address=address[0..11] , out=o1 );
    RAM4K(in=in , load=c , address=address[0..11] , out=o2 );
    RAM4K(in=in , load=d , address=address[0..11] , out=o3 );

    Mux4Way16(a=o0 , b=o1 , c=o2 , d=o3 , sel=address[12..13] , out=out );

}
```

### PC
Program Counter，根據控制訊號更新的 16-bit Register。

```
CHIP PC {
    IN in[16], reset, load, inc;
    OUT out[16];
    
    PARTS:
    
    //out +1
    Inc16(in=prev, out=incOut);

    //if inc
    Mux16(a=prev, b=incOut, sel=inc, out=incResult);

    //if load in
    Mux16(a=incResult, b=in, sel=load, out=loadResult);

    //if reset
    Mux16(a=loadResult, b=false, sel=reset, out=next);

    Register(in=next, load=true, out=prev, out=out);
}
```

## Project 4

Mult.asm 
```
// R0 * R1 -> R2

@i
M=1

@res
M=0

(LOOP)

@i
D=M

@R1
D=D-M

@END
D;JGT

@R0
D=M

@res
M=D+M

@i
M=M+1

@LOOP
0;JMP

(END)

@res
D=M

@R2
M=D

@END
0;JMP
```
