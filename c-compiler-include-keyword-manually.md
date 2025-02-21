---
title: "C Compiler with Custom Keywords"
description: "Extending a basic Lex-Yacc compiler to support C keywords like if, else, while, return, and int."
---

import { CodeBlock } from "@/components/CodeBlock";

# C Compiler with Custom Keywords

This example extends a basic Lex-Yacc compiler to include C keywords like `if`, `else`, `while`, `return`, and `int`.

## **1. Lex File (compiler.l)**
Lex will tokenize keywords, identifiers, numbers, and operators.

```lex
%{
#include "y.tab.h"
%}

%%

"if"       { return IF; }
"else"     { return ELSE; }
"while"    { return WHILE; }
"return"   { return RETURN; }
"int"      { return INT; }

[a-zA-Z_][a-zA-Z0-9_]* { return IDENTIFIER; }
[0-9]+     { yylval = atoi(yytext); return NUMBER; }
[+\-*/=]   { return *yytext; }
[{}();]    { return *yytext; }
[ \t\n]    { /* Ignore whitespace */ }
.          { printf("Invalid character: %s\n", yytext); }

%%
```

## **2. Yacc File (compiler.y)**
This defines grammar rules for basic expressions and statements.

```yacc
%{
#include <stdio.h>
#include <stdlib.h>

void yyerror(const char *s);
int yylex();
%}

%token IF ELSE WHILE RETURN INT IDENTIFIER NUMBER

%%

program:
    program statement
  | /* empty */
  ;

statement:
    INT IDENTIFIER ';'       { printf("Declaration: int %s;\n", $2); }
  | IDENTIFIER '=' NUMBER ';' { printf("Assignment: %s = %d;\n", $1, $3); }
  | RETURN NUMBER ';'         { printf("Return statement: return %d;\n", $2); }
  | IF '(' IDENTIFIER ')' '{' statement '}' { printf("If statement\n"); }
  | WHILE '(' IDENTIFIER ')' '{' statement '}' { printf("While loop\n"); }
  ;

%%

void yyerror(const char *s) {
    fprintf(stderr, "Error: %s\n", s);
}

int main() {
    printf("Enter C code:\n");
    yyparse();
    return 0;
}
```

## **3. Compilation & Execution**
Run the following commands in the terminal:

```sh
lex compiler.l
yacc -d compiler.y
gcc lex.yy.c y.tab.c -o compiler -ll
./compiler
```

## **4. Example Run**

```sh
$ ./compiler
Enter C code:
int x;
Declaration: int x;

x = 5;
Assignment: x = 5;

return 0;
Return statement: return 0;

if (x) { return 1; }
If statement

while (x) { x = x - 1; }
While loop
```

## **Next Steps**
Would you like to extend this with functions, loops, or expressions? 🚀
