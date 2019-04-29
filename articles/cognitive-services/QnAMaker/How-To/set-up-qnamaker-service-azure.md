---
title: Configurar um serviço do QnA Maker – QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 90e7abf229e0ee1ca396150fee0c1f0970d257aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372165"
---
# <a name="create-a-qna-maker-service"></a>Criar um serviço do QnA Maker

Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.

## <a name="create-a-new-service"></a>Criar um novo serviço

Esse procedimento implanta alguns recursos do Azure. Juntos, esses recursos gerenciam o conteúdo da base de conhecimento e fornecem recursos de respostas à perguntas por um ponto de extremidade.

1. Entre no [Portal do Azure](<https://portal.azure.com>).

1. Selecione **adicionar novo recurso**, digite "qna maker" na pesquisa e selecione o recurso do QnA Maker

    ![Criar um serviço do QnA Maker – Adicionar novo recurso](../media/qnamaker-how-to-setup-service/create-new-resource.png)

1. Selecione **criar** depois de ler os termos e condições.

    ![Criar um novo serviço do QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Em **QnA Maker**, selecione os preços e regiões adequados.

    ![Criar um serviço do QnA Maker – tipo de preço e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Preencha o **Nome** com um nome exclusivo para identificar este serviço QnA Maker. Esse nome também identifica o ponto de extremidade do QnA Maker ao qual suas bases de conhecimento serão associadas.
    * Escolha a **Assinatura** na qual o recurso QnA Maker será implantado.
    * Selecione **Gerenciamento de tipo de preço**  os serviços de gerenciamento QnA Maker (portal e APIs de gerenciamento). Clique [aqui](https://aka.ms/qnamaker-pricing) para obter detalhes sobre os preços de SKUs.
    * Crie um novo **Grupo de Recursos** (recomendado) ou use um existente no qual implantar esse recurso QnA Maker. O QnA Maker cria vários recursos do Azure; Quando você cria um grupo de recursos para manter esses recursos, você pode facilmente encontrar, gerenciar e excluir esses recursos pelo nome do grupo de recursos.
    * Escolha o **Tipo de preço de pesquisa** do serviço de Azure Search. Se você vir a opção Camada gratuita esmaecida, significa que você já tem uma Camada gratuita do Azure Search implantada em sua assinatura. Nesse caso, você precisará iniciar com a Camada Básica do Azure Search. Consulte os detalhes de preços do Azure Search [aqui](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **Local de Pesquisa** onde você deseja implantar os dados do Azure Search. As restrições de armazenamento dos dados informarão o local escolhido para o Azure Search.
    * Dê um nome para o serviço de Aplicativo em **Nome do aplicativo**.
    * Por padrão, o serviço de Aplicativo assume o padrão da camada (S1) padrão. Você pode alterar o plano após a criação. Confira mais detalhes sobre preços do Serviço de Aplicativo [aqui](https://azure.microsoft.com/pricing/details/app-service/).
    * Escolha o **Local do site** onde o Serviço de Aplicativo será implantado.

        > [!NOTE]
        > O Local da pesquisa pode ser diferente do Site local.

    * Escolha se deseja habilitar **Application Insights** ou não. Se o **Application Insights** estiver habilitado, o QnA Maker coletará a telemetria em tráfego, logs de chat e erros.
    * Escolha o **local do App insights** onde o recurso do Application Insights será implantado.
    * Para obter medidas de economia de custo, você pode [compartilhar](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) algumas, mas nem todos os recursos do Azure criados para o QnA Maker. 

1. Depois que todos os campos são validados, você pode selecionar **criar** para iniciar a implantação desses serviços em sua assinatura. Isso levará alguns minutos para ser concluído.

1. Após a conclusão da implantação, você verá os seguintes recursos criados em sua assinatura.

    ![O recurso criou um serviço do QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Região do serviço de gerenciamento

O serviço de gerenciamento do QnA Maker, só é usado para o portal de & inicial para processamento de dados, está disponível apenas no Oeste dos EUA. Nenhum dado de cliente é armazenado nesse serviço Oeste dos EUA.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e publicar uma base de conhecimento](../Quickstarts/create-publish-knowledge-base.md)
