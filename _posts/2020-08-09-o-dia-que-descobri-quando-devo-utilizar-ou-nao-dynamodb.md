---
layout: post
title: O dia que descobri quando devo utilizar, ou não, DynamoDB
slug: o-dia-que-descobri-quando-devo-utilizar-ou-nao-dynamodb
date: 2020-08-09 14:00 -0300
---

Junto com meus colegas de time, em grande parte do tempo trabalhamos na criação de aplicações serverless. Ou seja, aplicações que precisam ter uma alta disponibilidade além de serem extremamente escaláveis.

Logo no princípio decidimos utilziar o [DynamoDB](https://aws.amazon.com/dynamodb/?nc2=h_ql_prod_db_ddb){:target="_blank"} para todas as aplicações que seguissem este modelo serverless. Pensamos que seria interessante ter uma única ferramenta de banco de dados para todas as aplicações a fim diminuir a curva de aprendizado do time. Apesar de ser uma ferramenta bem interessante, assim como nenhuma outra ferramenta ou tecnologia, ela não é uma bala de prata que solucionará todos os seus problemas.

Dito isso, venho compartilhar com você algumas experiências e aprendizados obtidos no dia que decidimos discutir sobre *"quais aplicações fazem sentido, ou não, utilizar DynamoDB"*.

### O que é DynamoDB, de forma resumida

DynamoDB é um serviço de banco de dados NoSQL disponibilizado pela AWS (Amazon Web Services) que fornece uma rápida e previsível performance de escalabilidade. Com essa ferramenta você não precisa se preocupar com provisionamento de hardware, instalação, configuração, replicação ou escalonamento de cluster para escalar seu banco de dados.

Os principais componentes do DynamoDB são as tabelas, itens e atributos. De forma simplória, uma tabela contém uma coleção de itens e cada item contém uma coleção de atributos. O DynamoDB usa chaves primárias para identificar exclusivamente cada item em uma tabela e índices secundários para fornecer mais flexibilidade de consulta.

### Nossa primeira aplicação usando DynamoDB

Nossa primeira aplicação tinha uma função, tokenizar cartões de crédito. Basicamente ela receberia dados sensíveis dos nossos clientes e converteria em um código identificador digital exclusivo, aleatório e temporário para que fosse utilizado de forma segura. Ou seja, precisávamos de um banco de dados capaz de receber diversas queries de escrita, com baixo tempo de resposta, criptografia nativa dos dados confidenciais e fácil escalabilidade sem dor de cabeça. E o DynamoDB é capaz de fazer tudo isso. Tinha apenas um detalhe que nos encomodava, como o DynamoDB trabalha.

Diferentemente dos bancos de dados relacionais que utilizam SQL, ou até mesmo de outros bancos NoSQL, o DynamoDB tem uma forma peculiar de trabalhar. Ele utiliza uma API.

![Wat?](/assets/wat.jpg)

Porém quando parei para refletir, ele funciona como um SaaS (Software as a Service ou software como um serviço). Logo, devemos pensar no DynamoDB como um serviço e não um banco de dados (apesar dele ser um, confuso né rs), e a melhor forma de fazer isso é através de uma API.

Legal, diferente, mas isso vai ficar meio estranho na hora de desenvolver nossa aplicação. Neste momento fomos apresentados ao [PynamoDB](https://pynamodb.readthedocs.io/en/latest/){:target="_blank"}. O PynamoDB é uma interface feita em Python que cria uma abstração em cima do DynamoDB API permitindo usar todo o poder do banco de dados através da simplicidade do Python. Funciona parecido a um ORM (Object-Relational Mapping ou mapeamento de objeto relacional).

Com todas as ferramentas e tecnologias em mãos, criamos nossa primeira aplicação utilizando DynamoDB e funcionou perfeitamente. Tudo muito seguro, simples, rápido e escalável. Foi então que veio a necessidade de criar uma outra aplicação, uma API REST, e assim as coisas começaram a mudar.

### Queries complexas e o começo dos questionamentos

As primeiras versões dessa nova aplicação foram tranquilas, até o momento que surgiu a necessidade de fazer queries cada vez mais complexas. O primeiro problema identificado foi a modelagem das tabelas. Como o time tinha pouca experiência com NoSQL, muitas tabelas haviam sido criadas com um pensamento de banco de dados relacional. Devido ao projeto ainda não ter nenhuma versão em produção, decidimos então fazer uma reestruturação nesses modelos seguindo o [design NoSQL](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-general-nosql-design.html){:target="_blank"}.

Foi nesse momento que percebemos o quão trabalhoso seria criar uma estrutura de tabelas composta por diversos relacionamentos entre suas entidades, sendo que o DynamoDB oferece poucas possibilidades de indexação e queries. Como o DynamoDB utiliza apenas a Primary Key e os Secondary Indexes para realizar buscas dos itens em uma determinada tabela, estes recursos não eram suficientes para alcançar os resultados desejados em queries mais complexas.

Outro ponto que dificultou o uso do DynamoDB foi a necessidade de realizar BI (Business Intelligence). Sistemas desse tipo realizão diversas ad-hoc queries (consultas não planejadas), uma vez que o DynamoDB precisa ter seus índices previamente definidos, essas queries se tornam altamente custosas do ponto de recursos, e consequentemente, financeiro.

Com todas essas cartas na mesa, decidimos não utilizar o DynamoDB para está aplicação em específico. Não vou entrar no mérito de qual ferramenta acabamos usando, pois não é esse o intuito deste post. Porém, para não te deixar sem resposta alguma, tanto o MySQL, PostgreSQL e MongoDB evitariam os problemas citados. Lembre-se que cada projeto tem sua especificidade e não existe uma stack Exodia para resolver tudo.

![Exodia](https://media.giphy.com/media/Snd51fjjX6s0M/giphy.gif)

### Conclusão

DynamoDB é uma ferramenta muito poderosa e não tem tantos recursos extras como outros bancos de dados. Porém, acredito que ele não tenha esses recursos extamente para focar em simplicidade e escalabilidade. É realmente impressionante como ele escala bem e com praticamente nada de configuração. Mas fique sempre atento ao uso de recursos, as queries precisam estar otimizadas para valer a pena utilizá-lo. Nunca esqueça de que não é possível realizar algumas queries tradicionais do SQL, como por exemplo search com texto livre e ad-hoc queries, pois isso pode te custar uma alta fatura na AWS.

**Inspirado após a leitura do post: <https://medium.com/better-programming/5-real-life-use-cases-for-dynamodb-a152a9d152e2>**
