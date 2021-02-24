---
title: 快速撸出一个Brainfuck语言解释器
date: 2017-11-30 22:27:24
tags: Brainfuck
categories: Brainfuck React-Native
---

一直都知道一个很有趣的语言，Brainfuck，只是一直尚未研究，今天晚上看了一下，竟然不是那么难，而且还撸出来了一个解释器。这是一种极小化的计算机语言，它是由Urban Müller在1993年创建的。

就象它的名字所暗示的，brainfuck程序很难读懂。尽管如此，brainfuck图灵机一样可以完成任何计算任务。虽然brainfuck的计算方式如此与众不同，但它确实能够正确运行。

这种语言基于一个简单的机器模型，除了指令，这个机器还包括：一个以字节为单位、被初始化为零的数组、一个指向该数组的指针（初始时指向数组的第一个字节）、以及用于输入输出的两个字节流。

下面是这八种状态的描述，其中每个状态由一个字符标识：

| 字符   | 含义                                |
| ---- | --------------------------------- |
| `>`  | 指针加一                              |
| `<`  | 指针减一                              |
| `+`  | 指针指向的字节的值加一                       |
| `-`  | 指针指向的字节的值减一                       |
| `.`  | 输出指针指向的单元内容（ASCII码）               |
| `,`  | 输入内容到指针指向的单元（ASCII码）              |
| `[`  | 如果指针指向的单元值为零，向后跳转到对应的`]`指令的次一指令处  |
| `]`  | 如果指针指向的单元值不为零，向前跳转到对应的`[`指令的次一指令处 |

举个例子，hello world

<script src="https://gist.github.com/kidk/44b8cd699c5879f1084f.js"></script>

自己看下原理并不难，于是自己撸了一个解释器，话不多说直接上代码，用es6写的。

```javascript
/**
 * Brainfuck Interpreter
 * author: yfgeek
 * https://github.com/yfgeek
 */
'use strict';

class Interpreter{
    constructor(code,input) {
        this.code = code.trim().replace(/ /g, "").replace(/(\r\n|\n|\r)/gm,"").split("");
        this.input = input || [];
        this.pointer = 0;
        this.codePointer = 0 ;
        this.dataset = [];
        this.bracketStack = [];
        this.output = [];
    }

    operation(str){

        switch(str){
            case '+':  {
                this.dataset[this.pointer] = this.dataset[this.pointer] || 0;
                ++this.dataset[this.pointer];
                break;
            }

            case  '-':  {
                this.dataset[this.pointer] = this.dataset[this.pointer] || 0;
                --this.dataset[this.pointer];
                break;
            }

            case  '<':{
                this.pointer = --this.pointer<0 ? 0: this.pointer;
                break;
            }

            case '>': {
                this.pointer++;
                break;
            }

            case '.':{
                this.output.push(String.fromCharCode(this.dataset[this.pointer]));
                break;
            }

            case ',':{
                let c = this.input.shift();
                if (typeof c === "string") {
                    this.dataset[this.pointer] = c.charCodeAt(0);
                }
                break;
            }

            case '[': {
                this.leftBracket();
                break;
            }

            case ']': {
                this.rightBracket();
                break;
            }
        }

    };

    leftBracket(){
        let openBrackets = 1;
        if (this.dataset[this.pointer]) {
            this.bracketStack.push(this.codePointer);
        } else {
            while (openBrackets && this.code[++this.codePointer]) {
                if (this.code[this.codePointer] === ']') {
                    openBrackets--;
                } else if (this.code[this.codePointer] === '[') {
                    openBrackets++;
                }
            }
        }
    }

    rightBracket(){
        this.codePointer =  this.bracketStack.pop() - 1;
    }

    run(){
        let list = ['+','-','<','>','.',',','[',']'];
        do{
           let c = this.code[this.codePointer];
           if(list.indexOf(c) >= 0)  this.operation(c);
        }while(++this.codePointer < this.code.length);
        return this.output;
    }

    toString(){
        return this.run().join('');
    }

}

let code = '++++++++++[>+++++++>++++++++++>+++>+<<<<-]>++.>+.+++++++..+++.>++.<<+++++++++++++++.>.+++.------.--------.>+.>.';
let i = new Interpreter(code,[]);
console.log(i.toString());
```

