---
layout: post
title: Left e right shift, operadores de bitwise em Python
slug: left-e-right-shift-operadores-de-bitwise-em-python
date: 2024-04-10 20:16 -0300
categories: [Python, Programação]
---

Semana passada navegando no X (Twitter), me deparei com o seguinte <a href="https://twitter.com/Python_Dv/status/1774488075537936425" target="blank">quiz</a>:

<p style="text-align: center;">
    <img src="/assets/right-shift-bitwise-quiz.png" alt="Quiz. a = 4. print(a >> 2). Options: 1, 2, True, Erro" style="max-width: 500px">
</p>

Pra você, qual seria a resposta correta? Sabe o que esse sinal `>>` significa?

Este sinal significa que a linguagem executará uma **operação de bitwise**. Mas afinal de contas, o que é isso?

Não se preocupe caso você não conheça essa operação, na hora, ao ver os comentário do tweet, a maior parte das pessoas haviam respondido *True*. Segundo elas a operação a ser executada seria "4 maior que 2". Como 280 caracteres não são suficientes para abordar tudo que gostaria na resolução desse quiz, decidi escrever esse texto para introduzir um conceito, aparentemente, pouco conhecido.

## Operações de bitwise: Left e Right Shift

Vamos começar pelo básico. Como o próprio nome da operação sugere, bitwise é uma operação feita diretamente no bit, ou seja, uma operação diretamente compreendida e suportada pelo processador, tornando-a extremamente rápida.

Existem diversos operadores de bitwise, mas vamos abordar apenas dois deles com foco na resolução do quiz: **left shift** e **right shift**. A ideia dessas operações é mover os dígitos de um determinado conjunto de bits para direita ou para a esquerda. Simples assim!

Vamos supor que temos o seguinte binário: '0b100111' ('0b' no começo é apenas uma representação binária informando que todo bit a esquerda é 0). Ao realizarmos left shift nele iremos mover os bits uma casa para a esquerda, adicionando um novo bit à direita do binário. Esse novo bit será sempre 0 para essa operação, resultando em um novo binário.

<p style="text-align: center;">
    <img src="/assets/left-shift-example.png" alt="Binário 0 1 0 0 1 1 1 com setas apontando os bits movidos à esquerda resultando em 1 0 0 1 1 1 0" style="max-width: 500px">
</p>

Para right shift, a ideia é bem parecida, a diferença é que iremos mover os bits uma casa para a direita. Desta forma, o bit mais à direita do antigo binário será removido do novo binário.

<p style="text-align: center;">
    <img src="/assets/right-shift-example.png" alt="Binário 1 0 0 1 1 1 com setas apontando os bits movidos à direita resultando em 0 1 0 0 1 1 1" style="max-width: 500px">
</p>

## Sintaxe em Python

Maravilha, agora que sabemos o que são os operadores de shift, vamos a sintaxe em Python. O binário utilizado nos exemplos anteriores ('0b100111') é a representação do número 39, portanto, para realizar left shift de apenas um bit deste decimal utilizamos o seguinte comando:

```python
>>> 39 << 1
78
>>> bin(78)
'0b1001110'
```

O primeiro elemento da operação é o número que queremos mover os bits (39), o operador `<<` representa left shift, e o segundo elemento (1) é a quantidade de bits que serão movidos na direção do operador (left << esquerda).

*"Calma lá, quer dizer então que podemos fazer mais de um shift ao mesmo tempo?"*

Exatamente! Por exemplo:

```python
>>> 39 << 2
156
>>> bin(156)
'0b10011100'
```

A mesma sintaxe se aplica para right shift: o primeiro elemento é o número que queremos mover os bits, o operador `>>` representa right shift, e o segundo elemento é a quantidade de bits que serão movidos na direção do operador (right >> direita).

```python
>>> 39 >> 1
19
>>> bin(19)
'0b10011'
>>> 39 >> 2
9
>>> bin(9)
'0b1001'
```

Perceba, sempre que realizamos uma operação de left shift o binário aumenta. Logo, sua representação decimal também aumenta. Por outro lado, ao realizarmos uma operação de right shift o binário sempre diminui, assim como sua representação decimal.

## Voltando ao quiz

Agora que os conceitos de left e right shift estão mais claros, ao retornar para o quiz percebemos que ele não é tão difícil assim. A operação a ser feita é um right shift da representação binária do número 4 ('0b100') movendo os bits duas vezes à direita.

```python
>>> 4 >> 2
1
>>> bin(1)
'0b1'
```

Portanto, a resposta correta para o quiz é 1.

E aí? Conseguiu acertar de primeira? Espero que agora você esteja mais familiarizado com esse tipo de operação.
