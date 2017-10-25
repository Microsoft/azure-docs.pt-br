---
title: "Atualizar e dimensionar uma instância de Gerenciamento de API do Azure | Microsoft Docs"
description: "Este tópico descreve como atualizar e dimensionar uma instância de Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: cf5cd03bd9147ac81b07dc453a919a3c74de44fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Atualizar e dimensionar uma instância de Gerenciamento de API 

Os clientes podem dimensionar uma instância de APIM (Gerenciamento de API) adicionando e removendo unidades. Uma **unidade** é composta por recursos do Azure dedicados e tem uma determinada capacidade de carga expressa como um número de chamadas à API por mês. A taxa de transferência e a latência reais variarão muito dependendo de fatores como o número e a taxa de conexões simultâneas, o tipo e o número de políticas configuradas, tamanhos de solicitação e resposta e latência de back-end. 

A capacidade e o preço de cada unidade dependem de uma **camada** na qual a unidade existe. Você pode escolher entre três camadas: **Desenvolvedor**, **Standard**, **Premium**. Se você precisar aumentar a capacidade de um serviço dentro de uma camada, deverá adicionar uma unidade. Se a camada selecionada no momento em sua instância de APIM não permitir a adição de mais unidades, será preciso atualizar para uma camada de nível superior. 

O preço de cada unidade, a capacidade de adicionar/remover unidades e se você tem ou não alguns recursos (por exemplo, implantação em várias regiões) dependem da camada que você escolheu para sua instância APIM. O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/) explica quais preços por unidade e recursos você obtém em cada camada. 

>[!NOTE]
>O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/) mostra o número aproximado de capacidade da unidade em cada camada. Para obter números mais precisos, você precisará examinar um cenário realista para suas APIs. Consulte a seção "Como planejar a capacidade" que segue.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Como planejar a capacidade?

Para saber se você tem unidades suficientes para manipular o tráfego, teste nas cargas de trabalho que você espera. 

Como mencionado acima, o número de solicitações por segundo que o APIM pode processar depende de muitas variáveis. Por exemplo, o padrão de conexão, o tamanho da solicitação e resposta, as políticas que são configuradas em cada API, o número de clientes que estão enviando solicitações.

Use **Métricas** (usa capacidades do Azure Monitor) para compreender quanta capacidade é usada em um determinado momento.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Use o portal do Azure para examinar métricas 

1. Navegue para sua instância do APIM no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Métricas**.
3. Selecione a métrica **Capacidade** em **Métricas disponíveis**. 

    A métrica de capacidade dá alguma ideia de quanta capacidade está sendo usada em seu locatário. Você pode testar colocando cada vez mais carga e vendo qual é sua carga de coleta. Você pode definir um alerta de métrica para avisá-lo quando algo inesperado estiver acontecendo. Por exemplo, sua instância de APIM excede a capacidade para mais de 5 minutos. 

    >[!TIP]
    > Você pode configurar um alerta para saber quando seu serviço está ficando com pouca capacidade ou fazê-lo chamar um aplicativo lógico que dimensionará automaticamente adicionando uma unidade.

## <a name="upgrade-and-scale"></a>Atualizar e dimensionar 

Conforme mencionado anteriormente, você pode escolher entre três camadas: **Desenvolvedor**, **Standard**, **Premium**. A camada de **Desenvolvedor** deve ser usada para avaliar o serviço; ela não deve ser usada para a produção. A camada de **Desenvolvedor** não tem um SLA e não é possível dimensioná-la (adicionar/remover unidades). 

**Standard** e **Premium** são camadas de produção que têm SLA e podem ser dimensionadas. A camada **Standard** pode ser dimensionada para até quatro unidades. Você pode adicionar qualquer número de unidades para a camada **Premium**. 

A camada **Premium** permite que você distribua uma única instância de gerenciamento de API em qualquer número de regiões do Azure desejadas. Quando você cria inicialmente um serviço de gerenciamento de API, a instância contém apenas uma unidade e reside em uma única região do Azure. A região inicial é designada como região **primária**. Regiões adicionais podem ser facilmente incluídas. Ao adicionar uma região, você pode especificar o número de unidades que deseja alocar. Por exemplo, você pode ter uma unidade na região **primária** e cinco unidades em alguma outra região. Você pode personalizar para qualquer tráfego que você tenha em cada região. Para obter mais informações, consulte [Como implantar uma instância do serviço de Gerenciamento de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md).

Você pode atualizar e fazer downgrade de e para qualquer camada. Observe que fazer downgrade pode remover alguns recursos, por exemplo, VNETs ou implantação de várias regiões, ao fazer o downgrade da camada Standard para a Premium.

>[!NOTE]
>O processo de atualização ou escala pode levar de 15 a 30 minutos para ser aplicado. Você receberá uma notificação quando tiver terminado.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Use o portal do Azure para atualizar e dimensionar

1. Navegue para sua instância do APIM no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Escala e preços**.
3. Escolha a camada desejada.
4. Especifique o número de **unidades** que você deseja adicionar. Você pode usar o controle deslizante ou digitar o número de unidades.<br/>
    Se você escolher a camada **Premium**, primeiro precisará selecionar uma região.
5. Pressione **Salvar**

## <a name="next-steps"></a>Próximas etapas

[Como implantar uma instância do serviço de Gerenciamento de API do Azure para várias regiões do Azure](api-management-howto-deploy-multi-region.md)

