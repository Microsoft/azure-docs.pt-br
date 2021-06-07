---
title: Guia de Início Rápido – Criar um registro com replicação geográfica – modelo do Azure Resource Manager
description: Saiba como criar um registro de contêiner do Azure com replicação geográfica usando um modelo do Azure Resource Manager.
services: azure-resource-manager
author: dlepow
ms.author: danlep
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: c59c7897054b2ad65a76353e6d886af46cac91e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537443"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Início Rápido: Criar um registro de contêiner com replicação geográfica usando um modelo do Resource Manager

Este guia de início rápido mostra como criar uma instância do Registro de Contêiner do Azure usando um modelo do Azure Resource Manager. O modelo configura um Registro [com replicação geográfica](container-registry-geo-replication.md), que sincroniza automaticamente o conteúdo do Registro em mais de uma região do Azure. A replicação geográfica permite acesso de fechamento de rede a imagens de implantações regionais, fornecendo, ao mesmo tempo, uma única experiência de gerenciamento. É um recurso da camada de serviço do Registro [Premium](container-registry-skus.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). O modelo configura um Registro e uma réplica regional adicional.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

Os seguintes recursos do Azure estão definidos no modelo:

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : criar um registro de contêiner do Azure
* **[Microsoft.ContainerRegistry/registries/replications](/azure/templates/microsoft.containerregistry/registries/replications)** : criar uma réplica do registro de contêiner

Mais exemplos de modelo do Registro de Contêiner do Azure podem ser encontrados na [galeria de modelo de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

 1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Selecione ou insira os seguintes valores.

    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: selecione **Criar**, insira um nome exclusivo para o grupo de recursos e, em seguida, selecione **OK**.
    * **Região**: selecione uma localização para o grupo de recursos. Exemplo: **EUA Central**.
    * **Nome do Acr**: aceite o nome gerado para o Registro ou insira um nome. Deve ser globalmente exclusivo.
    * **Usuário Administrador do Acr Habilitado**: aceite o valor padrão.
    * **Local**: aceite o local gerado para a réplica inicial do Registro ou insira um local como **EUA Central**. 
    * **SKU do Acr**: aceite o valor padrão.
    * **Local da Réplica do ACR**: insira um local para a réplica de Registro usando o nome curto da região. Ele deve ser diferente do local do Registro inicial. Exemplo: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Propriedades do modelo":::

1. Selecione **Examinar + Criar** e, em seguida, examine os termos e condições. Se você concorda, selecione **Criar**.

1. Depois que o Registro tiver sido criado com êxito, você receberá uma notificação:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Notificação do portal":::

 O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Use o portal do Azure ou uma ferramenta como a CLI do Azure para examinar as propriedades do registro de contêiner.

1. No portal, pesquise Registros de Contêiner e selecione o registro de contêiner que você criou.

1. Na página **Visão geral**, observe o **Servidor de logon** do Registro. Use esse URI ao usar o Docker para marcar e enviar imagens por push para o Registro. Para obter informações, confira [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Visão geral do Registro":::

1. Na página **Replicações**, confirme os locais da réplica inicial e da réplica adicionada por meio do modelo. Se desejar, adicione mais réplicas nesta página.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Replicações do Registro":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais deles, exclua o grupo de recursos, o Registro e a réplica do Registro. Para isso, acesse o portal do Azure, selecione o grupo de recursos que contém o Registro e, em seguida, selecione **Excluir grupo de recursos**.

Excluir grupo de recursos

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um Registro de Contêiner do Azure com um modelo do Resource Manager e configurou uma réplica do registro em outra localização. Prossiga para os tutoriais de Registro de Contêiner do Azure para uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure](container-registry-tutorial-prepare-registry.md)

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, confira:

> [!div class="nextstepaction"]
> [Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
