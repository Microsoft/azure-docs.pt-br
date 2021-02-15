---
title: Como solicitar um aumento de cota para recursos de Azure Cosmos DB
description: Saiba como solicitar um aumento de cota para Azure Cosmos DB recursos. Você também aprenderá como habilitar uma assinatura para acessar uma região.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090028"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Como solicitar um aumento de cota para recursos de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Os recursos em Azure Cosmos DB têm [cotas/limites padrão](concepts-limits.md). No entanto, pode haver um caso em que sua carga de trabalho precisa de mais cota do que o valor padrão. Nesse caso, você deve entrar em contato com a equipe de Azure Cosmos DB para solicitar um aumento de cota. Este artigo explica como solicitar um aumento de cota para Azure Cosmos DB recursos. Você também aprenderá como habilitar uma assinatura para acessar uma região.

## <a name="create-a-new-support-request"></a>Criar uma solicitação de suporte

Para solicitar um aumento de cota, você deve criar uma nova solicitação de suporte com os detalhes da carga de trabalho. A equipe de Azure Cosmos DB avaliará sua solicitação e a aprovará. Use as etapas a seguir para criar uma nova solicitação de suporte do portal do Azure:

1. Entre no portal do Azure.

1. No menu à esquerda, selecione **ajuda + suporte** e, em seguida, selecione **nova solicitação de suporte** .

1. Na guia **noções básicas** , preencha os seguintes detalhes:

   * Em **tipo de problema** , selecione **Limites (cotas) de serviço e assinatura**
   * Para **assinatura** , selecione a assinatura para a qual você deseja aumentar a cota.
   * Para **tipo de cota** , selecione **Cosmos DB**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Criar uma nova solicitação de suporte Cosmos DB para aumento de cota":::

1. Na guia **detalhes** , insira os detalhes correspondentes à sua solicitação de cota. As informações fornecidas nesta guia serão usadas para avaliar melhor seu problema e ajudar o engenheiro de suporte a solucionar o problema.

1. Preencha os seguintes detalhes neste formulário:

   * **Descrição** : forneça uma breve descrição de sua solicitação, como sua carga de trabalho, por que os valores padrão não são suficientes. Com base no tipo de recurso para o qual você deseja aumentar a cota, é obrigatório que você forneça os seguintes detalhes no campo **Descrição** :

     **Solicitações de regiões** Se sua solicitação corresponder à adição de uma região à lista de permissões, certifique-se de fornecer os seguintes valores:

        * Nome da região
        * ID da assinatura

     **Solicitações de limite de taxa de transferência** Se sua solicitação corresponder ao aumento da cota da taxa de transferência, certifique-se de fornecer os seguintes valores:

        * Nome do banco de dados
        * ID da assinatura
        * Novo limite de taxa de transferência

     **Solicitações de limite de conta de banco de dados** Se sua solicitação corresponder ao aumento da cota para o número de contas de banco de dados em uma assinatura, certifique-se de fornecer os seguintes valores:

       * ID da assinatura
       * Novo limite de conta de banco de dados

   * **Upload de arquivo** : carregue os arquivos de diagnóstico ou quaisquer outros arquivos que você ache que sejam relevantes para a solicitação de suporte. Para saber mais sobre as diretrizes de carregamento de arquivo, consulte o artigo de [suporte do Azure]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) .

   * **Severidade** : escolha um dos níveis de severidade disponíveis com base no impacto nos negócios.

   * **Método de contato preferencial** : você pode optar por ser contatado por **email** ou por **telefone** .

1. Preencha os detalhes restantes, como disponibilidade, idioma de suporte, informações de contato, email e número de telefone no formulário.

1. Selecione **Avançar: revisar + criar** . Valide as informações fornecidas e selecione **criar** para criar uma solicitação de suporte.

Dentro de 24 horas, a equipe de suporte do Azure Cosmos DB avaliará sua solicitação e retornará para você.

## <a name="next-steps"></a>Próximas etapas

* Consulte as [Azure Cosmos DB cotas de serviço padrão](concepts-limits.md)
