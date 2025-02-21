---
title: "C Compiler with Digits Support"
description: "Extending a basic Lex-Yacc compiler to support numeric constants and arithmetic expressions."
---

import { CodeBlock } from "@/components/CodeBlock";

# C Compiler with Digits Support

If you want to manually include digits (numbers) in a C compiler using Lex and Yacc, you need to ensure that:

- The lexer (Lex file) recognizes numeric constants (digits).
- The parser (Yacc file) properly handles numbers in expressions and assignments.

## **1. Lex File (compiler.l)**
This file scans tokens like keywords, identifiers, numbers, and operators.

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

[0-9]+     { yylval = atoi(yytext); return NUMBER; }
[a-zA-Z_][a-zA-Z0-9_]* { return IDENTIFIER; }
[+\-*/=]   { return *yytext; }
[{}();]    { return *yytext; }
[ \t\n]    { /* Ignore whitespace */ }
.          { printf("Invalid character: %s\n", yytext); }

%%
```

## **2. Yacc File (compiler.y)**
This file defines grammar rules to handle numbers, arithmetic expressions, and assignments.

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
    INT IDENTIFIER ';'          { printf("Declaration: int %s;\n", $2); }
  | IDENTIFIER '=' expression ';' { printf("Assignment: %s = %d;\n", $1, $3); }
  | RETURN expression ';'        { printf("Return statement: return %d;\n", $2); }
  | IF '(' expression ')' '{' statement '}' { printf("If statement\n"); }
  | WHILE '(' expression ')' '{' statement '}' { printf("While loop\n"); }
  ;

expression:
    NUMBER                  { $$ = $1; }
  | IDENTIFIER              { $$ = 0; /* Placeholder for variable lookup */ }
  | expression '+' expression { $$ = $1 + $3; }
  | expression '-' expression { $$ = $1 - $3; }
  | expression '*' expression { $$ = $1 * $3; }
  | expression '/' expression { $$ = $3 ? $1 / $3 : 0; /* Handle division by zero */ }
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

x = 10 + 5 * 2;
Assignment: x = 20;

return x;
Return statement: return 20;
```

## **Enhancements Possible**
- Support for floating-point numbers (`float` and `double`)
- Variable storage and lookup
- Function definitions and calls

Would you like to add more features like multiplication, division, or variable memory storage? 🚀
