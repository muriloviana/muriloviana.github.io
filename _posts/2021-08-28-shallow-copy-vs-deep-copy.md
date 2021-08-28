---
layout: post
title: Shallow copy vs Deep copy
slug: shallow-copy-vs-deep-copy
date: 2021-08-28 11:00 -0300
categories: Python
---

Em Python, temos dois tipos de objetos ou coleções (objects or collections), os mutáveis e os imutáveis. Dito isto, quando precisamos fazer uma cópia de algo imutável, basta realizarmos uma nova atribuição. Por exemplo:

```python
>>> name = "Ash"
>>> full_name = name
>>> full_name += " Ketchum"
>>> print(name)
Ash
>>> print(full_name)
Ash Ketchum
```

Bem tranquilo, concorda? No entanto, isto não se aplica a objetos ou coleções mútavies.

```python
>>> original_list = [1, 2, 3]
>>> copy_list = my_list
>>> copy_list.append(4)
>>> print(copy_list)
[1, 2, 3, 4]
>>> print(original_list)
[1, 2, 3, 4]
>>> id(original_list) == id(copy_list)
True
```

Isto acontece pois, em Python, não criamos uma cópia do objeto e sim vinculamos um nome de váriavel a uma alocação de memória, no caso, ao mesmo registro de memória.

Sendo assim, quando se trata de algo mútavel e temos a necessidade de gerar uma cópia de forma que uma não altere o outro, temos duas opções: podemos fazer uma "cópia rasa" ou uma "cópia profunda" do objeto ou coleção de origem. Em Python, estas cópias são conhecidas, respectivamente, como _shallow copy_ e _deep copy_, na própria documentação da linguagem tem uma explicação da diferença entre elas:

> -   Um _shallow copy_ constrói um novo objeto composto e então (na medida do possível) insere nele _referências_ aos objetos encontrados no original.
> -   Um _deep copy_ constrói um novo objeto composto e então, recursivamente, insere nele _cópias_ dos objetos encontrados no original.

Talvez isso não seja muito claro só com esta definição. Por isto, decidi criar este post com o intuito de complementar a explicação já existente, da diferença entre o _shallow copy_ e o _deep copy_, através de alguns exemplos.

## Shallow copy

Vamos começar por um cenário simples onde temos uma lista de números e precisamos fazer uma cópia desta lista. Em python, podemos fazer uma cópia de uma lista através da sua _factory function_.

```python
>>> original_list = [1, 2, 3]
>>> copy_list = list(original_list)
>>> id(original_list) == id(copy_list)
False
```

> __Nota__: esse mesmo conceito é apicado para dicts e sets.

Perceba que a lista `original_list` não mais se referencia ao mesmo registro de mémoria que a lista `copy_list`. Logo, agora podemos alterar a lista de cópia sem modificar a original.

```python
>>> copy_list.append(4)
>>> print(copy_list)
[1, 2, 3, 4]
>>> print(original_list)
[1, 2, 3]
```

### Navegando em águas profundas

Vamos partir para um segundo cenário. Neste caso, temos uma lista de listas representando uma matriz 2x2, e iremos fazer uma cópia da mesma forma que fizemos no exemplo anterior.

```python
>>> original_matrix = [[1, 2], [3, 4]]
>>> copy_matrix = list(original_matrix)
>>> id(original_matrix) == id(copy_matrix)
False
```

Até o momento, tudo certo. Fizemos uma cópia da matrix original e de fato ela aponta para um registro de memória diferente da original. Agora, vamos adicionar uma nova lista na nossa cópia.

```python
>>> copy_matrix.append([5, 6])
>>> print(copy_matrix)
[[1, 2], [3, 4], [5, 6]]
>>> print(original_matrix)
[[1, 2], [3, 4]]

```

Perfeito, a matriz original mateve-se intacta ao modificarmos sua cópia. No entanto, o que aconteceria se alterássemos uma das listas filhas? Por exemplo, se modificarmos o segundo elemento da primeira lista da matriz de `2` para `0`.

```python
>>> copy_matrix[0][1] = 0
>>> print(copy_matrix)
[[1, 0], [3, 4], [5, 6]]
>>> print(original_matrix)
[[1, 0], [3, 4]]
```

Ops, o segundo elemento da primeira lista tanto da cópia quanto da matriz original foram alteradas. Isto aconteceu pois fizemos um _shallow copy_, ou seja, apenas a lista pai, que engloba as outras listas da representação da matriz, foi copiada. Enquanto as listas filhas, que representam as linhas da matriz, continuam sendo referenciadas para o mesmo registro de memória da matriz original.

```python
>>> id(original_matrix) == id(copy_matrix)
False
>>> id(original_matrix[0]) == id(copy_matrix[0])
True
```

## Deep copy

Como vimos no exemplo anterior, o _shallow copy_ não faz uma cópia em todos os níveis do objeto em questão. Para estes casos precisamos fazer um _deep copy_. Exemplificando, vamos repetir o cenário anterior, porém desta vez, utilizaremos o `copy.deepcopy()`.

```python
>>> from copy import deepcopy
>>> original_matrix = [[1, 2], [3, 4]]
>>> copy_matrix = deepcopy(original_matrix)
>>> id(original_matrix) == id(copy_matrix)
False
```

Matriz copiada, iremos repetir a ação que anteriormente não resultou no comportamento esperado, no caso, vamos alterar o segundo elemento da primeira lista da nossa matriz de cópia e observar o resultado.

```python
>>> copy_matrix[0][1] = 0
>>> print(copy_matrix)
[[1, 0], [3, 4]]
>>> print(original_matrix)
[[1, 2], [3, 4]]
```

Uhull! Agora obtivemos o resultado esperado, pois a matriz original foi alocada em novo registro de memória, tanto para a lista pai quanto para as listas filhas.

```python
>>> id(original_matrix) == id(copy_matrix)
False
>>> id(original_matrix[0]) == id(copy_matrix[0])
False
```

> __Nota__: também é possível realizar um _shallow copy_ através do `copy.copy()`, assim como fizemos um _deep copy_ com o `copy.deepcopy()`.

## Conclusão

Quando quisermos realizar uma cópia em todos os níveis do objeto de origem, incluindo possíveis objetos filhos, utilizamos o _deep copy_. E quando quisermos realizar uma cópia apenas do nível atual do objeto de origem, alterando apenas a referência de memória do objeto pai e mantendo a referência de memória dos objetos filhos, utilizamos o _shallow copy_.

__Fontes de inspiração:__
* [Python Docs: shallow vs deep copy](https://docs.python.org/3/library/copy.html#shallow-vs-deep-copy){:target="_blank"}
* [Real Python: copying python objects](https://realpython.com/copying-python-objects/){:target="_blank"}
