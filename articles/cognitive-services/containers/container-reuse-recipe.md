---
title: Receitas para contêineres do Docker
titleSuffix: Azure Cognitive Services
description: Use essas receitas de contêiner para criar contêineres de serviços Cognitivos que podem ser reutilizados. Contêineres podem ser criados com algumas ou todas as definições de configuração para que eles não são necessários quando o contêiner é iniciado. Depois que essa nova camada de contêiner (com configurações), e você o testou localmente, você pode armazenar o contêiner em um registro de contêiner. Quando o contêiner for iniciado, ele precisará somente as configurações que não estão atualmente armazenadas no contêiner.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 37fa972d52c564c4d61e5923c2b3dc48bde9d2ee
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445800"
---
# <a name="create-containers-for-reuse"></a>Criar contêineres para reutilização

Use essas receitas de contêiner para criar contêineres de serviços Cognitivos que podem ser reutilizados. Contêineres podem ser criados com algumas ou todas as definições de configuração para que fiquem _não_ necessários quando o contêiner é iniciado.

Depois que essa nova camada de contêiner (com configurações), e você o testou localmente, você pode armazenar o contêiner em um registro de contêiner. Quando o contêiner for iniciado, ele precisará somente as configurações que não estão atualmente armazenadas no contêiner. O contêiner do registro privado fornece espaço de configuração para que você possa passar essas configurações no.

## <a name="docker-run-syntax"></a>Sintaxe de execução do docker

Qualquer `docker run` exemplos neste documento pressupõem que um console do Windows com um `^` caractere de continuação de linha. Considere o seguinte para seu próprio uso:

* não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.
* Se você estiver usando um sistema operacional diferente do Windows ou um console que não seja o console do Windows, use o console/terminal correto, a sintaxe de pasta para montagens e o caractere de continuação de linha para o seu console e do sistema.  Como o contêiner de serviços Cognitivos é um sistema operacional Linux, a montagem de destino usa uma sintaxe de pasta de estilo do Linux.
* `docker run` exemplos usam o diretório de desativar o `c:` unidade para evitar conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder ao Docker permissão de serviço.

## <a name="store-no-configuration-settings-in-image"></a>Não Store nenhuma definição de configuração na imagem

O exemplo `docker run` comandos para cada serviço não armazenam as definições de configuração no contêiner. Quando você inicia o contêiner de um serviço de registro ou de console, essas definições de configuração necessário passar. O contêiner do registro privado fornece espaço de configuração para que você possa passar essas configurações no.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Reutilizar receita: armazenar todas as definições de configuração com o contêiner

Para armazenar todas as definições de configuração, crie um `Dockerfile` com essas configurações.

Problemas com essa abordagem:

* O novo contêiner tem um nome separado e a marca do contêiner original.
* Para alterar essas configurações, você precisará alterar os valores do Dockerfile, recompilar a imagem e republicar seu registro.
* Se alguém obtiver acesso a seu registro de contêiner ou seu host local, eles podem executar o contêiner e usar os pontos de extremidade de serviços Cognitivos.
* Se seu serviço cognitivo não requer entradas montagens, não adicione o `COPY` linhas ao seu Dockerfile.

Criar um Dockerfile, puxando do contêiner de serviços Cognitivos existente que você deseja usar, em seguida, usar comandos do docker no Dockerfile para definir ou reunir as informações de contêiner precisa.

Neste exemplo:

* Define o ponto de extremidade cobrança `{BILLING_ENDPOINT}` do host da chave de ambiente usando `ENV`.
* Define a cobrança-chave de API, `{ENDPOINT_KEY}` do host da chave de ambiente usando ' ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Reutilizar receita: armazenar as configurações de cobrança com o contêiner

Este exemplo mostra como criar um contêiner de sentimento da análise de texto de um Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Compilar e executar o contêiner [localmente](#how-to-use-container-on-your-local-host) ou de seu [contêiner do registro privado](#how-to-add-container-to-private-registry) conforme necessário.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Reutilizar receita: armazenar a cobrança e configurações com o contêiner de montagem

Este exemplo mostra como usar o reconhecimento vocal, economizando a cobrança e modelos do Dockerfile.

* Copia o modelo Luis (reconhecimento vocal a Language) de arquivos do host do usando o sistema de arquivos `COPY`.
* O contêiner de LUIS dá suporte a mais de um modelo. Se todos os modelos são armazenados na mesma pasta, todos os você precisa de um `COPY` instrução.
* Execute o arquivo do docker do pai relativo do diretório de entrada do modelo. Para o exemplo a seguir, execute as `docker build` e `docker run` comandos do pai relativo de `/input`. A primeira `/input` sobre o `COPY` comando é a pasta do computador host. O segundo `/input` é o diretório do contêiner.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Compilar e executar o contêiner [localmente](#how-to-use-container-on-your-local-host) ou de seu [contêiner do registro privado](#how-to-add-container-to-private-registry) conforme necessário.

## <a name="how-to-use-container-on-your-local-host"></a>Como usar o contêiner em seu host local

Para criar o arquivo do Docker, substitua `<your-image-name>` com o novo nome da imagem, em seguida, use:

```console
docker build -t <your-image-name> .
```

Para executar a imagem e removê-lo quando o contêiner é interrompido (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Como adicionar o contêiner ao registro privado

Siga estas etapas para usar o Dockerfile e colocar a nova imagem no registro de contêiner privado.  

1. Criar um `Dockerfile` com o texto da receita de reutilização. Um `Dockerfile` não tem uma extensão.

1. Substitua quaisquer valores nos chevrons pelos seus próprios valores.

1. Crie o arquivo em uma imagem em linha de comando ou terminal, usando o comando a seguir. Substitua os valores entre colchetes angulares, `<>`, com seu próprio nome do contêiner e marca.  

    A opção de marca, `-t`, é uma maneira de adicionar informações sobre o que você tiver alterado para o contêiner. Por exemplo, um nome de contêiner de `modified-LUIS` indica o contêiner original foram colocadas em camadas. Um nome de marca de `with-billing-and-model` indica como o contêiner de compreensão de idioma (LUIS) foi modificado.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Entrar CLI do Azure em um console. Esse comando abre um navegador e exige autenticação. Uma vez autenticado, você pode fechar o navegador e continuar trabalhando no console.

    ```azure-cli
    az login
    ```

1. Entrar no seu registro privado com CLI do Azure em um console.

    Substitua os valores entre colchetes angulares, `<my-registry>`, com seu próprio nome de registro.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    Você também pode entrar com o logon do docker se você for atribuído a uma entidade de serviço.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Marca o contêiner com o local do registro privado. Substitua os valores entre colchetes angulares, `<my-registry>`, com seu próprio nome de registro. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se você não usar um nome de marca `latest` é implícita.

1. Enviar por push a nova imagem ao registro de contêiner privado. Quando você exibe o registro de contêiner privado, o nome do contêiner usado no comando CLI a seguir será o nome do repositório.

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