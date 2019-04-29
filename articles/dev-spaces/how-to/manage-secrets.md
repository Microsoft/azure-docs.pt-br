---
title: Como gerenciar segredos ao trabalhar com um Azure Dev Space
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: 9fe29e8717c76c353f3e95d4693011f3925c4e1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686418"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerenciar segredos ao trabalhar com um Azure Dev Space

Os serviços podem exigir determinadas senhas, cadeias de conexão e outros segredos, como bancos de dados, ou outros serviços seguros do Azure. Ao definir os valores desses segredos nos arquivos de configuração, será possível disponibilizá-los no código como variáveis de ambiente.  Estes devem ser manipulados com cuidado para evitar comprometer a segurança dos segredos.

O Azure Dev Spaces fornece duas opções recomendadas para armazenar segredos: no arquivo values.dev.yaml e em linha diretamente no azds.yaml. Não é recomendável armazenar segredos em values.yaml.
 
## <a name="method-1-valuesdevyaml"></a>Método 1: values.dev.yaml
1. Abra o VS Code com o projeto que está habilitado para o Azure Dev Spaces.
2. Adicione um arquivo nomeado _values.dev.yaml_ na mesma pasta que os _values.yaml_ existentes e defina a chave secreta e os valores, como no exemplo a seguir:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Atualize _azds.yaml_ para informar ao Azure Dev Spaces para usar o novo arquivo _values.dev.yaml_. Para fazer isso, adicione essa configuração na seção configurations.develop.container:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. Modifique o código de serviço para referir-se a esses segredos como variáveis de ambiente, como no exemplo a seguir:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

    ```
    azds up
    ```
 
6. (Opcional) Na linha de comando, verifique se estes segredos foram criados:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Certifique-se de adicionar _values.dev.yaml_ ao arquivo _.gitignore_ para evitar confirmar segredos no controle de origem.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Método 2: embutido diretamente no azds.yaml
1.  Em _azds.yaml_, configure os segredos na seção de yaml configurações/desenvolver/instalar. Embora não seja possível inserir valores secretos diretamente, isso não é recomendável porque _azds.yaml_ está selecionado no controle do código-fonte. Em vez disso, adicione espaços reservados usando a sintaxe "$PLACEHOLDER".

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT_DEV"
                host: "$REDIS_HOST_DEV"
                key: "$REDIS_KEY_DEV"
    ```
     
2.  Crie um arquivo _.env_ na mesma pasta que _azds.yaml_. Insira segredos usando a notação chave-valor padrão. Não confirme o arquivo _.env_ para o controle do código-fonte. (Para omitir do controle do código-fonte em sistemas de controle de versão baseados em git, adicione-o no arquivo _.gitignore_.) O exemplo a seguir mostra um arquivo _.env_:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  Modifique o código-fonte de serviço para referenciar esses segredos no código, como no exemplo a seguir:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

    ```
    azds up
    ```

4.  (opcional) Ver segredos do kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Próximas etapas

Com esses métodos, é possível se conectar com segurança a um banco de dados, um Cache do Azure para Redis ou acessar serviços seguros do Azure.
 
