---
title: Receitas para contêineres do Docker
titleSuffix: Azure Cognitive Services
description: Saiba como criar, testar e armazenar contêineres com algumas ou todas as suas definições de configuração para implantação e reutilização.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80875071"
---
# <a name="create-containers-for-reuse"></a>Criar contêineres para reutilização

Use essas receitas de contêiner para criar contêineres de serviços cognitivas que podem ser reutilizados. Os contêineres podem ser criados com algumas ou todas as definições de configuração para que _não_ sejam necessárias quando o contêiner for iniciado.

Assim que tiver essa nova camada de contêiner (com configurações) e você a tiver testado localmente, você poderá armazenar o contêiner em um registro de contêiner. Quando o contêiner é iniciado, ele só precisará dessas configurações que não estão armazenadas atualmente no contêiner. O contêiner do registro privado fornece espaço de configuração para você passar essas configurações.

## <a name="docker-run-syntax"></a>Sintaxe de execução do Docker

Todos os `docker run` exemplos neste documento pressupõem um console do Windows com um `^` caractere de continuação de linha. Considere o seguinte para seu próprio uso:

* não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.
* Se você estiver usando um sistema operacional diferente do Windows, ou um console diferente do console do Windows, use o console/terminal, a sintaxe de pasta para montagens e o caractere de continuação de linha corretos para seu console e sistema.  Como o contêiner de serviços cognitivas é um sistema operacional Linux, a montagem de destino usa uma sintaxe de pasta em estilo Linux.
* `docker run` Os exemplos usam o diretório fora da `c:` unidade para evitar conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder ao Docker permissão de serviço.

## <a name="store-no-configuration-settings-in-image"></a>Não armazenar definições de configuração na imagem

Os `docker run` comandos de exemplo para cada serviço não armazenam nenhuma definição de configuração no contêiner. Quando você inicia o contêiner a partir de um console ou serviço de registro, essas definições de configuração precisam ser passadas. O contêiner do registro privado fornece espaço de configuração para você passar essas configurações.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Reutilizar receita: armazenar todas as definições de configuração com o contêiner

Para armazenar todas as definições de configuração, crie uma `Dockerfile` com essas configurações.

Problemas com essa abordagem:

* O novo contêiner tem um nome e uma marca separados do contêiner original.
* Para alterar essas configurações, você precisará alterar os valores de Dockerfile, recriar a imagem e republicar no registro.
* Se alguém obtiver acesso ao registro de contêiner ou ao host local, ele poderá executar o contêiner e usar os pontos de extremidade de serviços cognitivas.
* Se seu serviço cognitiva não exigir montagens de entrada, não adicione as `COPY` linhas ao seu Dockerfile.

Crie Dockerfile, extraindo do contêiner de serviços cognitivas existente que você deseja usar e, em seguida, use os comandos do Docker no Dockerfile para definir ou extrair as informações necessárias para o contêiner.

Neste exemplo:

* Define o ponto de extremidade de cobrança, `{BILLING_ENDPOINT}` da chave de ambiente do host usando `ENV` .
* Define a chave de API de cobrança `{ENDPOINT_KEY}` da chave de ambiente do host usando ' env.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Reutilizar receita: armazenar configurações de cobrança com o contêiner

Este exemplo mostra como criar o contêiner de sentimentos Análise de Texto ' de um Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Compile e execute o contêiner [localmente](#how-to-use-container-on-your-local-host) ou do seu [contêiner de registro privado](#how-to-add-container-to-private-registry) , conforme necessário.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Reutilizar receita: armazenar as configurações de cobrança e montagem com o contêiner

Este exemplo mostra como usar Reconhecimento vocal, salvando a cobrança e os modelos do Dockerfile.

* Copia o arquivo de modelo Reconhecimento vocal (LUIS) do sistema de arquivos do host usando o `COPY` .
* O contêiner LUIS dá suporte a mais de um modelo. Se todos os modelos estiverem armazenados na mesma pasta, todas as necessidades de uma instrução serão necessárias `COPY` .
* Execute o arquivo do Docker do pai relativo do diretório de entrada do modelo. Para o exemplo a seguir, execute `docker build` os `docker run` comandos e do pai relativo de `/input` . O primeiro `/input` no `COPY` comando é o diretório do computador host. O segundo `/input` é o diretório do contêiner.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Compile e execute o contêiner [localmente](#how-to-use-container-on-your-local-host) ou do seu [contêiner de registro privado](#how-to-add-container-to-private-registry) , conforme necessário.

## <a name="how-to-use-container-on-your-local-host"></a>Como usar o contêiner em seu host local

Para criar o arquivo do Docker, substitua `<your-image-name>` pelo novo nome da imagem e, em seguida, use:

```console
docker build -t <your-image-name> .
```

Para executar a imagem e removê-la quando o contêiner for interrompido ( `--rm` ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Como adicionar contêiner ao registro privado

Siga estas etapas para usar o Dockerfile e colocar a nova imagem em seu registro de contêiner privado.  

1. Crie um `Dockerfile` com o texto da reutilização da receita. Um `Dockerfile` não tem uma extensão.

1. Substitua quaisquer valores nos colchetes angulares pelos seus próprios valores.

1. Crie o arquivo em uma imagem na linha de comando ou no terminal, usando o comando a seguir. Substitua os valores nos colchetes angulares, `<>` , com seu próprio nome de contêiner e marca.  

    A opção de marca, `-t` , é uma maneira de adicionar informações sobre o que você alterou para o contêiner. Por exemplo, um nome de contêiner `modified-LUIS` indica que o contêiner original foi colocado em camadas. Um nome de marca `with-billing-and-model` indica como o contêiner de reconhecimento vocal (Luis) foi modificado.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Entre no CLI do Azure em um console do. Esse comando abre um navegador e requer autenticação. Uma vez autenticado, você pode fechar o navegador e continuar trabalhando no console do.

    ```azurecli
    az login
    ```

1. Entre em seu registro privado com o CLI do Azure de um console do.

    Substitua os valores nos colchetes angulares, `<my-registry>` , pelo seu próprio nome de registro.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Você também pode entrar com o logon do Docker se você tiver atribuído uma entidade de serviço.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Marque o contêiner com o local do registro privado. Substitua os valores nos colchetes angulares, `<my-registry>` , pelo seu próprio nome de registro. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se você não usar um nome de marca, o `latest` será implícito.

1. Envie por push a nova imagem para o registro de contêiner privado. Quando você exibir o registro de contêiner privado, o nome do contêiner usado no comando da CLI a seguir será o nome do repositório.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e usar a instância de contêiner do Azure](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->