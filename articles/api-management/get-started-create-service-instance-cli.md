---
title: Guia de início rápido – Criar uma instância de Gerenciamento de API do Azure usando a CLI
description: Crie uma instância de serviço do Gerenciamento de API do Azure usando a CLI do Azure.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 19fc2e1629e7f67063e3cc3eec8cb3707b6dd2e4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775809"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Guia de início rápido: criar uma instância de serviço do Gerenciamento de API do Azure usando a CLI do Azure

O APIM (Gerenciamento de API) do Azure ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de liberar o potencial de seus dados e serviços. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O APIM permite que você crie e gerencie gateways de API modernos para serviços de back-end hospedados em qualquer lugar. Para obter mais informações, confira a [Visão geral](api-management-key-concepts.md).

Este guia de início rápido descreve as etapas para criar uma instância de Gerenciamento de API usando os comandos [az apim](/cli/azure/apim) na CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.11.1 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

As instâncias de Gerenciamento de API do Azure, assim como todos os recursos do Azure, precisam ser implantadas em um grupo de recursos. Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.

Primeiramente, crie um grupo de recursos denominado *myResourceGroup* no local EUA Central com o seguinte comando [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Criar um novo serviço

Agora que você tem um grupo de recursos, você pode criar uma instância de serviço do Gerenciamento de API. Crie uma usando o comando [az apim create](/cli/azure/apim#az_apim_create) e forneça um nome de serviço e detalhes do editor. O nome do serviço precisa ser único no Azure. 

No exemplo a seguir, *myapim* é usado para o nome do serviço. Atualize o nome para um valor exclusivo. Também atualize o nome da organização do editor da API e o endereço de email para receber notificações. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Por padrão, o comando cria a instância na camada Desenvolvedor, uma opção econômica para avaliar o Gerenciamento de API do Azure. Essa camada não é para uso em produção. Para obter mais informações sobre a colocação em escala das camadas de Gerenciamento de API, confira [atualizar e dimensionar](upgrade-and-scale.md). 

> [!TIP]
> A criação e a ativação de um serviço de Gerenciamento de API nessa camada leva de 30 a 40 minutos. O comando anterior usa a opção `--no-wait` para que o comando seja retornado imediatamente enquanto o serviço é criado.

Verifique o status da implantação executando o comando [az apim show](/cli/azure/apim#az_apim_show):

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Inicialmente, a saída se assemelha ao seguinte, mostrando o status `Activating`:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Após a ativação, o status será `Online` e a instância de serviço tem um endereço de gateway e um endereço IP público. Por enquanto, esses endereços não expõem nenhum conteúdo. Por exemplo:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Quando sua instância de serviço de Gerenciamento de API estiver online, você estará pronto para usá-la. Comece com o tutorial para [importar e publicar sua primeira API](import-and-publish.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e a instância de serviço do Gerenciamento de API.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar e publicar sua primeira API](import-and-publish.md)
