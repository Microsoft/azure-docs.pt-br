---
title: "Criar um serviço do Azure Search no portal | Microsoft Azure"
description: "Provisionar um serviço do Azure Search no portal."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: 58f4eab190e40e16ed261c165ffdfc8155eeb434
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Criar um serviço de Pesquisa do Azure no portal

Este artigo explica como criar ou provisionar um serviço do Azure Search no portal. Para obter instruções do PowerShell, consulte [Gerenciar o Azure Search com o PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Assinar (gratuito ou pago)

[Abra uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e use créditos gratuitos para experimentar serviços pagos do Azure. Depois que os créditos forem usados, mantenha a conta e continue a usar os serviços do Azure gratuitos, como os sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.

Alternativamente, você pode [ativar os benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Todos os meses, uma assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure. 

## <a name="find-azure-search"></a>Encontrar o Azure Search
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique no sinal de adição ("+") no canto superior esquerdo.
3. Selecione **Web + Móvel** > **Azure Search**.

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>Nome do serviço e o ponto de extremidade da URL

Um nome de serviço é parte do ponto de extremidade da URL na qual as chamadas à API são emitidas. Digite o nome do serviço no campo **URL** . 

Requisitos de nome de serviço:
   * Dois a 60 caracteres de comprimento
   * letras minúsculas, dígitos ou traços ("-")
   * sem traços ("-") como os dois primeiros caracteres ou o último caractere
   * sem traços consecutivos ("--")

## <a name="select-a-subscription"></a>Selecionar uma assinatura
Se você tiver mais de uma assinatura, escolha uma que também tenha serviços de armazenamento de arquivos ou dados. O Azure Search pode detectar automaticamente o armazenamento de Tabelas e Blobs do Azure, o Banco de Dados SQL e o Azure Cosmos DB para indexação por meio de *indexadores*, mas apenas para os serviços na mesma assinatura.

## <a name="select-a-resource-group"></a>Selecionar um grupo de recursos
Um grupo de recursos é uma coleção de serviços e recursos do Azure que são usados juntos. Por exemplo, se você estiver usando a Azure Search para indexar um banco de dados SQL, esses dois serviços deverão fazer parte do mesmo grupo de recursos.

> [!TIP]
> Excluir um grupo de recursos também exclui os serviços dentro dele. Para projetos de protótipo utilizando vários serviços, colocar todos eles no mesmo grupo de recursos facilita a limpeza depois da conclusão do projeto. 

## <a name="select-a-hosting-location"></a>Selecione um local de hospedagem 
Como um serviço do Azure, a Azure Search pode ser hospedado em datacenters em todo o mundo. Observe que os [preços podem variar](https://azure.microsoft.com/pricing/details/search/) de acordo com a geografia.

## <a name="select-a-pricing-tier-sku"></a>Selecionar um tipo de preço (SKU)
[A Azure Search é oferecida atualmente em vários tipos de preço](https://azure.microsoft.com/pricing/details/search/): Gratuito, Básico ou Standard. Cada tipo tem sua própria [capacidade e limites](search-limits-quotas-capacity.md). Confira [Escolher um tipo de preço ou SKU](search-sku-tier.md) para obter orientações.

Neste passo a passo, escolhemos o tipo Standard para nosso serviço.

## <a name="create-your-service"></a>Criar seu serviço

Lembre-se de fixar o seu serviço no painel para acesso fácil sempre que você entrar.

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>Dimensione seu serviço
Pode levar alguns minutos para criar um serviço (15 minutos ou mais dependendo da camada). Depois que o serviço é fornecido, você pode dimensioná-lo para atender às suas necessidades. Com a escolha do tipo Standard para o serviço do Azure Search, você pode dimensionar o serviço em duas dimensões: réplicas e partições. Com a escolha do tipo Básico, você pode apenas adicionar réplicas. Se você provisionou o serviço gratuito, o dimensionamento não estará disponível.

As ***partições*** permitem que o seu serviço armazene e pesquise mais documentos.

***Réplicas*** permitem que seu serviço lide com uma carga maior de consultas de pesquisa.

> [!Important]
> Um serviço deve ter [duas réplicas para o SLA somente leitura e três réplicas para o SLA de leitura/gravação](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Vá até a folha de serviço de pesquisa no Portal do Azure.
2. No painel de navegação esquerdo, selecione **Configurações** > **Escala**.
3. Use a barra deslizante para adicionar réplicas ou partições.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Cada camada tem diferentes [limites](search-limits-quotas-capacity.md) do número total de Unidades de Pesquisa permitidas em um único serviço (Réplicas * Partições = Total de Unidades de Pesquisa).

## <a name="when-to-add-a-second-service"></a>Quando adicionar um segundo serviço

A grande maioria dos clientes usa apenas um serviço provisionado em uma camada que fornece o [equilíbrio certo de recursos](search-sku-tier.md). Um serviço pode hospedar vários índices, sujeito aos [limites máximos na camada selecionada](search-capacity-planning.md), com cada índice isolado do outro. No Azure Search, as solicitações podem ser direcionadas somente para um índice, minimizando a possibilidade de recuperação de dados acidental ou intencional de outros índices no mesmo serviço.

Embora a maioria dos clientes use apenas um serviço, a redundância de serviço poderá ser necessária se os requisitos operacionais incluírem o seguinte:

+ Recuperação de desastre (interrupção do datacenter). O Azure Search não fornece failover instantâneo caso ocorra uma interrupção. Para obter recomendações e diretrizes, consulte [Administração de serviço](search-manage.md).
+ Sua investigação de modelagem de multilocação determinou que serviços adicionais são o design ideal. Para obter mais informações, consulte [Design para multilocação](search-modeling-multitenant-saas-applications.md).
+ Para aplicativos implantados globalmente, é possível exigir uma instância do Azure Search em várias regiões para minimizar a latência de tráfego internacional do aplicativo.

> [!NOTE]
> No Azure Search, não é possível segregar cargas de trabalho de indexação e de consulta; portanto, você nunca criará vários serviços para cargas de trabalho segregadas. Um índice sempre é consultado no serviço em que foi criado (não é possível criar um índice em um serviço e copiá-lo para outro).
>

Um segundo serviço não é necessário para alta disponibilidade. A alta disponibilidade para consultas é obtida ao usar duas ou mais réplicas no mesmo serviço. Atualizações de réplica são sequenciais, o que significa que, pelo menos, uma está operacional quando uma atualização de serviço é distribuída. Para obter mais informações sobre tempo de atividade, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Próximas etapas
Após o provisionamento de um serviço de Azure Search, você está pronto para [definir um índice](search-what-is-an-index.md) para que possa carregar e pesquisar os dados.

Para acessar o serviço de código ou script, forneça a URL (*nome-do-serviço*.search.windows.net) e uma chave. Chaves de administração concedem acesso completo; chaves de consulta concedem acesso somente leitura. Veja [Como usar o Azure Search no .NET](search-howto-dotnet-sdk.md) para começar.

Consulte [Criar e consultar seu primeiro índice](search-get-started-portal.md) para obter um tutorial rápido baseado no portal.

