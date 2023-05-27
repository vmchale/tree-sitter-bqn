# tree-sitter-bqn
Structual editing and highlighting [BQN](https://mlochbaum.github.io/BQN) programs with editors powered by [tree-sitter](https://tree-sitter.github.io/)

- [Specification BQN grammar](https://mlochbaum.github.io/BQN/spec/grammar.html)
- [tree-sitter tutorial](https://tree-sitter.github.io/tree-sitter/creating-parsers)
- [target color schema](https://github.com/helix-editor/helix/blob/53f47bc47771c94dab51626ca025be28e62eba0c/runtime/themes/solarized_light.toml#L1-L23)
- [textobjects](https://docs.helix-editor.com/guides/textobject.html)

`tree-sitter-bqn` provides a parser for a simplified BQN grammar, in which blocks (`subExpr`, `FuncExpr`, `_m1Expr` and `_m2_Expr`) are unified as a typeless `block `type.
So you get the following:

```
$ cat check.bqn
(((+))(((⌜))))((˜))((↕)10)
$ tree-sitter parse check.bqn
(source_file [0, 0] - [0, 31]
  (STMT [0, 0] - [0, 31]
    (EXPR [0, 0] - [0, 31]
      (subExpr [0, 0] - [0, 31]
        (arg [0, 0] - [0, 31]
          (Derv [0, 0] - [0, 22]
            (Operand [0, 0] - [0, 16]
              (Derv [0, 0] - [0, 16]
                (Func [0, 0] - [0, 16]
                  (FuncExpr [0, 1] - [0, 15]
                    (Train [0, 1] - [0, 15]
                      (Fork [0, 1] - [0, 15]
                        (Derv [0, 1] - [0, 15]
                          (Operand [0, 1] - [0, 6]
                            (Derv [0, 1] - [0, 6]
                              (Func [0, 1] - [0, 6]
                                (FuncExpr [0, 2] - [0, 5]
                                  (Train [0, 2] - [0, 5]
                                    (Fork [0, 2] - [0, 5]
                                      (Derv [0, 2] - [0, 5]
                                        (Func [0, 2] - [0, 5]
                                          (FuncExpr [0, 3] - [0, 4]
                                            (Train [0, 3] - [0, 4]
                                              (Fork [0, 3] - [0, 4]
                                                (Derv [0, 3] - [0, 4]
                                                  (Func [0, 3] - [0, 4]
                                                    (symbol_Fl [0, 3] - [0, 4]))))))))))))))
                          (mod_1 [0, 6] - [0, 15]
                            (m1_Expr [0, 7] - [0, 14]
                              (mod_1 [0, 7] - [0, 14]
                                (m1_Expr [0, 8] - [0, 13]
                                  (mod_1 [0, 8] - [0, 13]
                                    (m1_Expr [0, 9] - [0, 12]
                                      (mod_1 [0, 9] - [0, 12]
                                        (symbol__ml [0, 9] - [0, 12])))))))))))))))
            (mod_1 [0, 16] - [0, 22]
              (m1_Expr [0, 17] - [0, 21]
                (mod_1 [0, 17] - [0, 21]
                  (m1_Expr [0, 18] - [0, 20]
                    (mod_1 [0, 18] - [0, 20]
                      (symbol__ml [0, 18] - [0, 20])))))))
          (subExpr [0, 22] - [0, 31]
            (arg [0, 22] - [0, 31]
              (subject [0, 22] - [0, 31]
                (atom [0, 22] - [0, 31]
                  (subExpr [0, 23] - [0, 30]
                    (arg [0, 23] - [0, 30]
                      (Derv [0, 23] - [0, 28]
                        (Func [0, 23] - [0, 28]
                          (FuncExpr [0, 24] - [0, 27]
                            (Train [0, 24] - [0, 27]
                              (Fork [0, 24] - [0, 27]
                                (Derv [0, 24] - [0, 27]
                                  (Func [0, 24] - [0, 27]
                                    (symbol_Fl [0, 24] - [0, 27]))))))))
                      (subExpr [0, 28] - [0, 30]
                        (arg [0, 28] - [0, 30]
                          (subject [0, 28] - [0, 30]
                            (atom [0, 28] - [0, 30]
                              (symbol_sl [0, 28] - [0, 30]
                                (number [0, 28] - [0, 30])))))))))))))))))
```

# Loadmap

1. Phase1: quite simplified -- basic expressions
2. Phase2: simplified -- statements
3. Phase3: shaping -- modifiers
4. Phase4: tedious -- untyped blocks
5. Phase5: 'Quite tedious' -- fix errors, give up a better grammar
6. Release 0.1.0 -- provide misc queries
7. Release 0.2.0 -- better textobjects and auto indents

# A configuration for Helix

Are you a [Helix](https://helix-editor.com/) user? Then try:

1. Add the following to your $CONFIG/helix/languages.toml

```toml
[[language]]
name = "bqn"
scope = "source.bqn"

[[grammar]]
name = "bqn"
source.git = "https://github.com/shnarazk/tree-sitter-bqn"
source.rev = "20fbe4cc2b4f6d398421f570fa80dd383a6ef956" or something new
```

2. Build up on shell:

```
$ hx -g fetch
$ hx -g build
```

3. Copy query files:

```
$ cp -r queries $HELIX/runtime/queries/bqn
```

```apl
# What you can do now in Helix:
#   - traverse function blocks by `]f` and `[f`
#   - traverse function headers by `]a` and `[a`
#   - traverse namespaces by `]t` and `[t`
#   - traverse comments by `]c` and `[c`

J ⇐ { F x: 2⋆x; w F x: x + w }
K ← (⌽⋈∨)       # no block here
l ⇐ { w ⇐ 10, Y ⇐ J }
_m ⇐ { 𝔽 _mod: 𝔽´⁼ }
ns ⇐ { w ⇐ 3, k ⇐ 10 }
```
