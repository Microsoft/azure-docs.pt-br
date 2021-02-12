---
title: Recuperar pares de chave-valor de um ponto no tempo
titleSuffix: Azure App Configuration
description: Recuperar pares de chave-valor antigos usando instantâneos de ponto no tempo na configuração do Azure App, que mantém um registro das alterações nos valores de chave.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929890"
---
# <a name="point-in-time-snapshot"></a>Instantâneo pontual

Azure App configuração mantém um registro das alterações feitas nos valores de chave. Esse registro fornece uma linha do tempo das alterações de chave-valor. Você pode reconstruir o histórico de qualquer valor-chave e fornecer seu valor passado a qualquer momento dentro do período do histórico de chaves (7 dias para armazenamentos de camada gratuita ou 30 dias para repositórios de camada Standard). Usando esse recurso, você pode retroceder "tempo-de-viagem" e recuperar um valor de chave antigo. Por exemplo, você pode recuperar as definições de configuração usadas antes da implantação mais recente a fim de reverter o aplicativo para a configuração anterior.

## <a name="key-value-retrieval"></a>Recuperação de par chave-valor

Você pode usar portal do Azure ou CLI para recuperar valores de chave antigos. Em CLI do Azure, use `az appconfig revision list` , adicionando os parâmetros apropriados para recuperar os valores necessários.  Especifique a instância de configuração de Azure App fornecendo o nome do repositório ( `--name <app-config-store-name>` ) ou usando uma cadeia de conexão ( `--connection-string <your-connection-string>` ). Restrinja a saída especificando um ponto no tempo específico ( `--datetime` ) e especificando o número máximo de itens a serem retornados ( `--top` ).

Se você não tiver CLI do Azure instalado localmente, você pode, opcionalmente, usar [Azure cloud Shell](../cloud-shell/overview.md).

Recupere todas as alterações gravadas para seus valores de chave.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

Recupere todas as alterações gravadas para a chave `environment` e os rótulos `test` e `prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Recupere todas as alterações gravadas no espaço de chave hierárquica `environment:prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Recupere todas as alterações gravadas para a chave `color` em um momento específico.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Recupere as 10 últimas alterações gravadas em seus valores de chave e retorne apenas os valores para `key` , `label` e `last_modified` carimbo de data/hora.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)