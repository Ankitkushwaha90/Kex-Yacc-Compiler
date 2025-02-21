---
title: "Lex & Yacc Notes"
description: "In-depth notes on Lex (Flex) and Yacc (Bison) with code examples."
---

import { CodeBlock } from "@/components/CodeBlock";

# Lex & Yacc Notes

Lex and Yacc (or Flex and Bison) are tools used for building lexical analyzers and parsers, respectively.

## 1. Introduction to Lex & Yacc

- **Lex** (or **Flex**) is a lexical analyzer generator that converts a sequence of input characters into tokens.
- **Yacc** (or **Bison**) is a parser generator that converts tokens into an abstract syntax tree (AST) based on grammar rules.

## 2. Lex (Flex) - Tokenizer

Lex is responsible for **tokenizing** the input text. It uses regular expressions to define patterns for different types of tokens.

### 2.1 Lex Structure

A Lex file has three main sections:

```lex
%%
[0-9]+     { printf("NUMBER token: %s\n", yytext); }
[a-zA-Z]+  { printf("WORD token: %s\n", yytext); }
"+"        { printf("PLUS token\n"); }
%%

int main() {
    yylex();
    return 0;
}

int yywrap() { return 1; }
```

### 2.2 Explanation

- The `%%` marks separate sections.
- `[0-9]+` matches numbers.
- `[a-zA-Z]+` matches words.
- `"+"` matches the `+` operator.
- `yytext` contains the matched text.
- `yylex()` is called in `main()` to start tokenizing.

## 3. Yacc (Bison) - Parser

Yacc processes tokens from Lex and applies grammar rules to construct structured output.

### 3.1 Yacc Structure

A Yacc file has three main sections:

```yacc
%{
    #include <stdio.h>
    #include <stdlib.h>
%}

%token NUMBER PLUS

%%
expr: expr PLUS term   { printf("Expression: Addition\n"); }
    | term            { printf("Expression: Term\n"); }
    ;

term: NUMBER         { printf("Term: NUMBER\n"); }
    ;
%%

int main() {
    yyparse();
    return 0;
}

int yyerror(char *s) {
    fprintf(stderr, "Error: %s\n", s);
    return 0;
}
```

### 3.2 Explanation

- `%token` defines tokens (e.g., `NUMBER`, `PLUS`).
- The `expr` rule defines expressions.
- The `term` rule matches `NUMBER`.
- `yyparse()` starts parsing.

## 4. Compiling & Running

To compile and run:

```sh
lex lexer.l   # Generates lex.yy.c
gcc lex.yy.c -o lexer -ll
./lexer < input.txt

yacc -d parser.y   # Generates y.tab.c and y.tab.h
gcc y.tab.c -o parser -ly
./parser < input.txt
```

## 5. Conclusion

Lex & Yacc are powerful tools for building compilers, interpreters, and DSLs. Understanding how they work helps in designing language parsers efficiently.

---

> **Next Steps:** Explore AST generation using Yacc and advanced parsing techniques.

