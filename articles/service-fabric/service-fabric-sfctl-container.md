---
title: CLI do Azure Service Fabric-contêiner sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando Service Fabric do Azure. Inclui uma lista de comandos para contêineres.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f82883b68ab911fb0b89fc117d9a9d77e05a781a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245884"
---
# <a name="sfctl-container"></a>sfctl container
Execute comandos relacionados ao contêiner em um nó de cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| invocar api | Invoca a API de contêiner em um contêiner implantado em um nó do Service Fabric para o pacote de código fornecido. |
| logs | Obtém os logs de contêiner para um contêiner implantado em um nó do Service Fabric. |

## <a name="sfctl-container-invoke-api"></a>contêiner de sfctl invoke-api
Invoca a API de contêiner em um contêiner implantado em um nó do Service Fabric para o pacote de código fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id do aplicativo [requerido] | A identidade do aplicativo. <br><br> Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -código de pacote-id-instância-[requerido] | ID que identifica exclusivamente uma instância de pacote de código implantada em um nó do Service Fabric. <br><br> Pode ser recuperada usando "service code-package-list". |
| -código de-nome do pacote [requerido] | O nome do pacote de código especificado no manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| – contêiner-api-uri-path [requerido] | Caminho de URI de API REST do contêiner, use '{ID}' no lugar de nome/id do contêiner. |
| --node-name                [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| – corpo da api de contêiner | Corpo da solicitação HTTP para API REST do contêiner. |
| – contêiner-api-content-type | Tipo de conteúdo para a API REST, o padrão é 'application/json' de contêiner. |
| --container-api-http-verb | Verbo HTTP para o contêiner de API REST, o padrão é GET. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-container-logs"></a>logs de contêiner sfctl
Obtém os logs de contêiner para um contêiner implantado em um nó do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id do aplicativo [requerido] | A identidade do aplicativo. <br><br> Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| -código de pacote-id-instância-[requerido] | ID de instância do pacote de código, que pode ser recuperada por 'serviço código--lista de pacotes'. |
| -código de-nome do pacote [requerido] | O nome do pacote de código especificado no manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| --node-name                [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | O nome de um manifesto de serviço registrado como parte de um tipo de aplicativo em um cluster do Service Fabric. |
| -final | Número de linhas a serem mostradas do final dos logs. O padrão é 100. 'todos' para mostrar os logs de conclusão. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configure](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](./scripts/sfctl-upgrade-application.md).
