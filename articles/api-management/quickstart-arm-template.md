---
title: Início Rápido – Criar uma instância do Gerenciamento de API do Azure usando o modelo do ARM
description: Saiba como criar uma instância do Gerenciamento de API do Azure na camada Desenvolvedor usando um modelo do ARM (Azure Resource Manager).
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792235"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Início Rápido: Criar uma instância de serviço do Gerenciamento de API do Azure usando um modelo do ARM

Este início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para criar uma instância de serviço do APIM (Gerenciamento de API do Azure). O APIM ajuda organizações a publicar APIs para desenvolvedores externos, parceiros e internos de modo a revelar o potencial de seus dados e serviços. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O APIM permite que você crie e gerencie gateways de API modernos para serviços de back-end hospedados em qualquer lugar. Para obter mais informações, confira a [Visão geral](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/).

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

O seguinte recurso é definido no modelo:

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

Encontre mais amostras de modelo do Gerenciamento de API do Azure nos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria uma instância de serviço de Gerenciamento de API com um nome gerado automaticamente.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    Neste exemplo, a instância é configurada na camada Desenvolvedor, uma opção econômica para avaliar o Gerenciamento de API do Azure. Essa camada não é para uso em produção. Para obter mais informações sobre a colocação em escala das camadas de Gerenciamento de API, confira [atualizar e dimensionar](upgrade-and-scale.md).

1. Selecione ou insira os seguintes valores.
    - **Assinatura**: selecione uma assinatura do Azure.
    - **Grupo de recursos**: selecione **Criar**, insira um nome exclusivo para o grupo de recursos e, em seguida, selecione **OK**.
    - **Região**: selecione uma localização para o grupo de recursos. Exemplo: **EUA Central**.
    - **Email do Publicador**: insira um endereço de email para receber notificações.
    - **Nome do Editor**: insira um nome escolhido para o editor de API.
    - **Sku**: aceite o valor padrão de **Desenvolvedor**.
    - **Contagem do Sku**: aceite o valor padrão.
    - **Local**: aceite o local gerado para o serviço de Gerenciamento de API.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="Propriedades do modelo do Gerenciamento de API":::

1. Selecione **Examinar + Criar** e, em seguida, examine os termos e condições. Se você concorda, selecione **Criar**.

    > [!TIP]
    >  A criação e a ativação de um serviço de Gerenciamento de API nessa camada Desenvolvedor leva de 30 a 40 minutos.

1. Depois que a instância tiver sido criada com êxito, você receberá uma notificação:

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Notificação de implantação":::

 O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Use o portal do Azure para verificar os recursos implantados ou use ferramentas, como a CLI do Azure ou o Azure PowerShell, para listar os recursos implantados.

1. No [portal do Azure](https://portal.azure.com), procure e selecione **serviços de Gerenciamento de API** e selecione a instância de serviço que você criou.
1. Examine as propriedades do seu serviço na página **Visão geral**.

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Página de visão geral do serviço":::

Quando sua instância de serviço de Gerenciamento de API estiver online, você estará pronto para usá-la. Comece com o tutorial para [importar e publicar](import-and-publish.md) sua primeira API.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja continuar trabalhando com tutoriais subsequentes, é interessante manter a instância do Gerenciamento de API onde está. Quando não for mais necessário, exclua o grupo de recursos, que excluirá os recursos no grupo de recursos.

1. No [portal do Azure](https://portal.azure.com) pesquise e selecione **Grupos de recursos**. Você também pode selecionar **Grupos de recursos** na **Página Inicial**.
1. Na página **Grupos de recursos**, selecione seu grupo de recursos.
1. Na página Grupo de recursos, selecione **Excluir grupo de recursos**.

    Excluir grupo de recursos
1. Digite o nome do seu grupo de recursos e depois selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Importar e publicar sua primeira API](import-and-publish.md)
