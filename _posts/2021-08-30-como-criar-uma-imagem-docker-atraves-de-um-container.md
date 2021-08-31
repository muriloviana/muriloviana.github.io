---
layout: post
title: Como criar uma imagem docker através de um container
slug: como-criar-uma-imagem-docker-atraves-de-um-container
date: 2021-08-30 19:20 -0300
categories: Docker
---

Normalmente quando pensamos na criação de um container Docker, o fluxo padrão que nos vem a mente é: obtenho uma imagem, seja ela compartilhada pela comunidade através do [Docker Hub](https://hub.docker.com){:target="_blank"} ou criada através de um _Dockerfile_, e com ela gero um container. Resumindo, com uma imagem Docker é possível gerar um container Docker. E se eu te disser que o contrário também é possível?

Sei que parece estranho mas sim, é possível criarmos uma imagem Docker através de um container já existente. Como este não é um fluxo "padrão" no desenvolvimento com Docker, decidi compartilhar esta curiosidade que descobri recentemente.

## Criando um container como base para a imagem

A ideia é fazer algo simples, apenas para demostrar que é possível gerar uma nova imagem Docker através de um container. Por tanto, iremos criar uma imagem que simule um servidor do [Redis](https://redis.io){:target="_blank"}.

Vamos lá, primeiro iremos criar um container [Alpine](https://hub.docker.com/_/alpine){:target="_blank}, que é, a grosso modo, uma distribuição Linux super reduzida. E nele, iremos executar o _shell_.

```bash
$ docker run -it alpine sh
```

Uma vez que estamos habilitados a interar no container, graças a flag `-it`, iremos instalar o Redis.

```shell
apk add --update redis
```

Redis instalado com sucesso, partiremos para o próximo passo. Iremos abrir uma nova aba, ou janela, do terminal e iremos verificar se o container continua de pé, utilizando o comando `docker ps`. O output que aparecerá deve ser parecido com este:

```bash
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
b6f2056808f1   alpine    "sh"      21 seconds ago   Up 21 seconds             priceless_wiles
```

## Docker commit

Ok, o que temos até o momento? Temos um container, em execução no atual momento, com Redis instalado nele. Nesta mesma aba, ou janela, do terminal que acabamos de rodar o `docker ps`, iremos copiar o ID do container em execução e rodaremos o servidor do Redis nele.

```bash
$ docker commit -c 'CMD ["redis-server"]' b6f2056808f1
```

O output será algo semelhante a:

```bash
sha256:ad8437cf7924d7c2e831ae1f560ee61695e75bcbfbd687beb10891aa9b10a34c
```

Este é o pulo do gato! O comando `docker commit` nos permite salvar o estado atual de um container, em execução, em uma imagem. Logo, este ID gigante que obtivemos no passo anterior, nada mais é, que o identificador de uma imagem Docker. Se executarmos o comando `docker image ls`, para listar as imagens salvas no seu computador, veremos este mesmo ID entre elas. Desta forma, podemos gerar um novo container com base nesta nova imagem criada através do comando: `docker run <IMAGEM_ID>`.

## Conclusão

Provavelmente, serão raros os casos que precisaremos gerar uma nova imagem através do comando `docker commit`. Todavia, é sempre bem-vindo conhechermos as ferramentas que usamos no nosso dia-a-dia, para quando a necessidade surgir estarmos preparados.
