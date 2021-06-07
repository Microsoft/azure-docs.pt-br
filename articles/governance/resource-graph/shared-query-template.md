---
title: 'Início Rápido: Criar uma consulta compartilhada com modelos'
description: Neste guia de início rápido, você usa um modelo do ARM (modelo do Azure Resource Manager) para criar uma consulta compartilhada do Resource Graph que conta máquinas virtuais por SO.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: ac736d4371531bb38a8cd2cf095acbdfbc7c08a1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535789"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Início Rápido: Criar uma consulta compartilhada usando um modelo do ARM

As consultas do Resource Graph podem ser salvas como uma _consulta privada_ ou uma _consulta compartilhada_. Uma consulta privada é salva no perfil do portal dos indivíduos e não fica visível a outras pessoas. Uma consulta compartilhada é um objeto do Resource Manager que pode ser compartilhado com outras pessoas por meio de permissões e acesso baseado em função. Uma consulta compartilhada fornece execução consistente e comum da descoberta de recursos. Este guia de início rápido usa um modelo do ARM (modelo do Azure Resource Manager) para criar uma consulta compartilhada.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar o modelo ARM para criar uma consulta compartilhada para o Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

Neste guia de início rápido, você cria uma consulta compartilhada chamada _Contar VMs por SO_. Para testar essa consulta no SDK ou no portal com o Resource Graph Explorer, confira [Exemplos – Contar máquinas virtuais por tipo SO](./samples/starter.md#count-os).

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

O recurso definido no modelo inclui:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>Implantar o modelo

> [!NOTE]
> O serviço do Azure Resource Graph é gratuito. Para saber mais, confira [Visão geral do Azure Resource Graph](./overview.md).

1. Selecione a imagem a seguir para entrar no portal do Azure e abrir o modelo:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar o modelo ARM para criar uma consulta compartilhada para o Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Selecione ou insira os valores a seguir:

   | Nome | Valor |
   |------|-------|
   | Subscription | Selecione sua assinatura do Azure. |
   | Resource group | Selecione **Criar**, especifique um nome e, em seguida, selecione **OK**. |
   | Location | Selecione uma região. Por exemplo, **Centro dos EUA**. |
   | Nome da consulta | Deixar o valor padrão: **Contar VMs por sistema operacional**. |
   | Código da consulta | Deixar o valor padrão: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Descrição da consulta | Deixar o valor padrão: **Esta consulta compartilhada conta todos os recursos de máquina virtual e os resume por tipo de sistema operacional.** |
   | Concordo com os termos e condições acima | (Selecionar) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- Para encontrar mais modelos de exemplo, confira o [Modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência de modelo, acesse [Referência de modelo do Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Para saber como desenvolver modelos do ARM, confira a [documentação do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Para conhecer a implantação de nível de assinatura, confira [Create resource groups and resources at the subscription level](../../azure-resource-manager/templates/deploy-to-subscription.md) (Criar grupos de recursos e recursos no nível da assinatura).

## <a name="validate-the-deployment"></a>Validar a implantação

Para executar a nova consulta compartilhada, siga estas etapas:

1. Na barra de pesquisa do portal, pesquise **Consultas do Resource Graph** e selecione.

1. Selecione a consulta compartilhada denominada **Contar VMs por SO** e selecione a guia **Resultados** na página **Visão geral**.

A consulta compartilhada também pode ser aberta no Resource Graph Explorer:

1. Na barra de pesquisa do portal, pesquise **Consultas do Resource Graph Explorer** e selecione.

1. Selecione o botão **Abrir uma consulta**.

1. Altere **Tipo** para _Consultas compartilhadas_. Se você não vir **Contar VMs por SO** na lista, use a caixa de filtro para limitar os resultados. Depois que a consulta compartilhada **Contar VMs por SO** estiver visível, selecione seu nome.

1. Depois que a consulta for carregada, selecione o botão **Executar consulta**. Os resultados são exibidos na guia **Resultados**.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a consulta compartilhada criada, siga estas etapas:

1. Na barra de pesquisa do portal, pesquise **Consultas do Resource Graph** e selecione.

1. Marque a caixa de seleção ao lado da consulta compartilhada chamada **Contar VMs por SO**.

1. Selecione o botão **Excluir** na parte superior da página.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma consulta compartilhada do Resource Graph.

Para saber mais sobre consultas compartilhadas, continue no tutorial para:

> [!div class="nextstepaction"]
> [Gerenciar consultas no portal do Azure](./tutorials/create-share-query.md)
