---
title: Atualizar e dimensionar uma instância de Gerenciamento de API do Azure | Microsoft Docs
description: Este tópico descreve como atualizar e dimensionar uma instância de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: bd36434bbfe435a53567c46728610627f99f987f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127780"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Fazer upgrade e dimensionar uma instância de Gerenciamento de API do Azure  

Os clientes podem dimensionar uma instância de gerenciamento de API do Azure adicionando e removendo unidades. Uma **unidade** é composta por recursos do Azure dedicados e tem uma determinada capacidade de carga expressa como um número de chamadas à API por mês. Esse número não representa um limite de chamada; ele é mais um valor de produtividade máxima para permitir o planejamento da capacidade aproximada. A taxa de transferência e a latência reais variam muito dependendo de fatores como o número e a taxa de conexões simultâneas, o tipo e o número de políticas configuradas, os tamanhos de solicitação e resposta e a latência de back-end.

A capacidade e o preço de cada unidade dependem da **camada** na qual a unidade existe. Você pode escolher quatro camadas: **Desenvolvedor**, **Básico**, **Padrão**, **Premium**. Se você precisar aumentar a capacidade de um serviço dentro de uma camada, deverá adicionar uma unidade. Se a camada selecionada atualmente em sua instância de gerenciamento de API não permitir a adição de mais unidades, você precisará atualizar para uma camada de nível superior.

O preço de cada unidade e os recursos disponíveis (por exemplo, implantação em várias regiões) depende da camada que você escolheu para sua instância de gerenciamento de API. O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) explica quais preços por unidade e recursos obtidos em cada camada. 

>[!NOTE]
>O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mostra o número aproximado de capacidade da unidade em cada camada. Para obter números mais precisos, você precisará examinar um cenário realista para suas APIs. Consulte o artigo [Capacidade de uma instância de Gerenciamento de API do Azure](api-management-capacity.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas deste artigo, é necessário:

+ Ter uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ter uma instância de gerenciamento de API. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

+ Entender o conceito de [Capacidade de uma instância do Gerenciamento de API do Azure](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Atualizar e dimensionar  

Você pode escolher entre quatro camadas: **Developer**, **Basic**,  **Standard** e **Premium**. A camada de **Desenvolvedor** deve ser usada para avaliar o serviço; ela não deve ser usada para a produção. A camada de **Desenvolvedor** não tem um SLA e não é possível dimensioná-la (adicionar/remover unidades). 

**Basic**, **Standard** e **Premium** são as camadas de produção que têm SLA e podem ser dimensionadas. A camada **básica** é a camada mais barata com um SLA e pode ser dimensionada para até duas unidades, a camada **Standard** pode ser dimensionada para até quatro unidades. Você pode adicionar qualquer número de unidades para a camada **Premium**.

A camada **Premium** permite que você distribua uma única instância de Gerenciamento de API do Azure em qualquer número de regiões do Azure desejadas. Quando você cria inicialmente um serviço de Gerenciamento de API do Azure, a instância contém apenas uma unidade e reside em uma única região do Azure. A região inicial é designada como região **primária**. Regiões adicionais podem ser facilmente incluídas. Ao adicionar uma região, você pode especificar o número de unidades que deseja alocar. Por exemplo, você pode ter uma unidade na região **primária** e cinco unidades em alguma outra região. Você pode personalizar o número de unidades para o tráfego existente em cada região. Para obter mais informações, consulte [Como implantar uma instância do serviço de Gerenciamento de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md).

Você pode atualizar e fazer downgrade de e para qualquer camada. A atualização ou o downgrade pode remover alguns recursos, por exemplo, VNETs ou implantação de várias regiões, ao fazer downgrade para Standard ou Basic da camada Premium.

> [!NOTE]
> O processo de atualização ou escala pode levar de 15 a 45 minutos para ser aplicado. Você será notificado quando terminar.

> [!NOTE]
> O serviço de gerenciamento de API na camada de **consumo** é dimensionado automaticamente com base no tráfego.

## <a name="scale-your-api-management-service"></a>Dimensionar seu serviço de gerenciamento de API

![Dimensionar o serviço de gerenciamento de API no portal do Azure](./media/upgrade-and-scale/portal-scale.png)

1. Navegue até o serviço de gerenciamento de API no [portal do Azure](https://portal.azure.com/).
2. Selecione **locais** no menu.
3. Clique na linha com o local que você deseja dimensionar.
4. Especificar o novo número de **unidades** – use o controle deslizante ou digite o número.
5. Clique em **Aplicar**.

## <a name="change-your-api-management-service-tier"></a>Alterar a camada de serviço de gerenciamento de API

1. Navegue até o serviço de gerenciamento de API no [portal do Azure](https://portal.azure.com/).
2. Clique no **tipo de preço** no menu.
3. Selecione a camada de serviço desejada na lista suspensa. Use o controle deslizante para especificar a escala do serviço de gerenciamento de API após a alteração.
4. Clique em **Save** (Salvar).

## <a name="downtime-during-scaling-up-and-down"></a>Tempo de inatividade durante a expansão e a redução
Se você estiver dimensionando de ou para a camada de desenvolvedor, haverá tempo de inatividade. Caso contrário, não há nenhum tempo de inatividade. 

## <a name="compute-isolation"></a>Isolamento de computação
Se seus requisitos de segurança incluírem o [isolamento de computação](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), você poderá usar o tipo de preço **isolado** . Essa camada garante que os recursos de computação de uma instância de serviço de gerenciamento de API consumam todo o host físico e forneçam o nível necessário de isolamento necessário para dar suporte, por exemplo, cargas de trabalho de impacto de nível 5 (IL5) do departamento de defesa dos EUA. Para obter acesso à camada isolada, [crie um tíquete de suporte](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Próximas etapas

- [Como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure](api-management-howto-deploy-multi-region.md)
- [Como dimensionar automaticamente uma instância do serviço de gerenciamento de API do Azure](api-management-howto-autoscale.md)
- [Planejar e gerenciar custos para o gerenciamento de API](plan-manage-costs.md)
- [Limites de Gerenciamento de API](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)