---
title: Criar um serviço de pesquisa no portal
titleSuffix: Azure Cognitive Search
description: Neste guia de início rápido do portal, saiba como configurar um recurso do Azure Cognitive Search no portal do Azure. Escolha os grupos de recursos, as regiões, o SKU ou o tipo de preço.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/15/2021
ms.openlocfilehash: e183d81355d4db81e677f34b02330ddb9b631957
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651979"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Início Rápido: Criar um serviço da Pesquisa Cognitiva do Azure no portal

O [Azure Cognitive Search](search-what-is-azure-search.md) é um recurso do Azure usado para adicionar uma experiência de pesquisa de texto completo a aplicativos personalizados. Você pode integrá-lo a outros serviços do Azure que fornecem dados ou processamento adicional, a aplicativos em servidores de rede ou a software em execução em outras plataformas de nuvem.

Você pode criar o serviço de pesquisa usando o [portal do Azure](https://portal.azure.com/), que é abordado neste artigo. Você também pode usar o [Azure PowerShell](search-manage-powershell.md), a [CLI do Azure](/cli/azure/search), a [API REST de Gerenciamento](/rest/api/searchmanagement/) ou um [modelo de serviço do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

[![GIF animado](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## <a name="before-you-start"></a>Antes de começar

As propriedades de serviço a seguir são corrigidas durante o tempo de vida do serviço; a alteração de cada uma delas exige um novo serviço. Como elas são corrigidas, considere as implicações de uso conforme você preenche cada propriedade:

+ o nome do serviço passa a fazer parte do ponto de extremidade da URL ([examine as dicas](#name-the-service) para obter nomes de serviço úteis).
+ A [camada de serviço](search-sku-tier.md) afeta a cobrança e define um limite ascendente da capacidade. Alguns recursos não estão disponíveis na Camada gratuita.
+ A região do serviço pode determinar a disponibilidade de alguns cenários. Caso precise de [recursos de alta segurança](search-security-overview.md) ou de [enriquecimento de IA](cognitive-search-concept-intro.md), crie o Azure Cognitive Search na mesma região de outros serviços ou em regiões que fornecem o recurso em questão. 

## <a name="subscribe-free-or-paid"></a>Assinar (gratuito ou pago)

[Abra uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e use créditos gratuitos para experimentar serviços pagos do Azure. Depois que os créditos forem usados, mantenha a conta e continue a usar os serviços do Azure gratuitos, como os sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.

Alternativamente, você pode [ativar os benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Todos os meses, uma assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure. 

## <a name="find-azure-cognitive-search"></a>Localizar a Pesquisa Cognitiva do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Clique no sinal de adição ( **"+ Criar Recurso"** ) no canto superior esquerdo.

1. Use a barra de pesquisa para localizar a "Pesquisa Cognitiva do Azure" ou navegue para o recurso por meio de **Web** > **Pesquisa Cognitiva do Azure**.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Criar um recurso no portal" border="false":::

## <a name="choose-a-subscription"></a>Escolha uma assinatura

Se você tiver mais de uma assinatura, escolha uma para o serviço de pesquisa. Se você estiver implementando a [criptografia dupla](search-security-overview.md#double-encryption) ou outros recursos que dependam das identidades de serviço gerenciadas, escolha a mesma assinatura usada para o Azure Key Vault ou outros serviços nos quais as identidades gerenciadas são usadas.

## <a name="set-a-resource-group"></a>Definir um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados da sua solução do Azure. Ele é necessário para o serviço de pesquisa. Também é útil para gerenciar recursos por completo, incluindo os custos. Um grupo de recursos pode consistir em um ou em vários serviços usados juntos. Por exemplo, se estiver usando a Pesquisa Cognitiva do Azure para indexar um banco de dados do Azure Cosmos DB, você poderá fazer com que ambos os serviços façam parte do mesmo grupo de recursos para fins de gerenciamento. 

Se você não estiver combinando recursos em um único grupo ou se os grupos de recursos existentes estiverem preenchidos com recursos usados em soluções não relacionadas, crie um grupo de recursos apenas para o recurso da Pesquisa Cognitiva do Azure. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Criar um novo grupo de recursos" border="false":::

Ao longo do tempo, você pode acompanhar os custos atuais e projetados por completo ou ver os preços de recursos individuais. A captura de tela a seguir mostra o tipo de informação de custo que você pode esperar ver ao combinar vários recursos em um grupo.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Gerenciar custos no nível do grupo de recursos" border="false":::

> [!TIP]
> Os grupos de recursos simplificam a limpeza, porque a exclusão de um grupo exclui todos os serviços dentro dele. Para projetos de protótipo utilizando vários serviços, colocar todos eles no mesmo grupo de recursos facilita a limpeza depois da conclusão do projeto.

## <a name="name-the-service"></a>Dê um nome ao serviço

Em Detalhes da Instância, dê um nome ao serviço no campo **URL**. O nome faz parte do ponto de extremidade da URL na qual as chamadas à API são emitidas: `https://your-service-name.search.windows.net`. Por exemplo, caso deseje que o ponto de extremidade seja `https://myservice.search.windows.net`, insira `myservice`.

Requisitos de nome de serviço:

+ Ele deve ser exclusivo dentro do namespace search.windows.net
+ Ele precisa ter entre 2 e 60 caracteres
+ Use letras minúsculas, dígitos ou traços ("-")
+ Não use traços ("-") nos dois primeiros caracteres ou como o último caractere
+ Você não deve usar traços consecutivos ("--") em nenhum lugar

> [!TIP]
> Se você acredita que vai usar vários serviços, recomendamos incluir a região (ou o local) no nome do serviço como uma convenção de nomenclatura. Os serviços na mesma região podem trocar dados sem custos, portanto, se a Pesquisa Cognitiva do Azure estiver no Oeste dos EUA e tiver outros serviços também no Leste dos EUA, um nome como `mysearchservice-westus` poderá poupar uma viagem à página de propriedades ao decidir como combinar ou anexar recursos.

## <a name="choose-a-location"></a>Escolher um local

O Azure Cognitive Search está disponível na maioria das regiões, conforme documentado nos [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=search). Geralmente, se você estiver usando vários serviços do Azure, escolha uma região que também está hospedando seu serviço de aplicativo ou de dados. Fazer isso minimiza ou anula os preços de largura de banda para dados de saída (não há preços para dados de saída quando os serviços estão na mesma região).

+ O [enriquecimento de IA](cognitive-search-concept-intro.md) exige que os Serviços Cognitivos estejam na mesma região física que o Azure Cognitive Search. Há apenas algumas regiões que não fornecem ambos. A página [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=search) indica disponibilidade dupla mostrando duas marcas de seleção empilhadas. Uma combinação não disponível tem uma marca de seleção ausente:

  :::image type="content" source="media/search-create-service-portal/region-availability.png" alt-text="Disponibilidade regional" border="true":::

+ Os requisitos de BCDR (continuidade dos negócios e recuperação de desastres) devem ser atendidos pela criação de vários serviços de pesquisa em [pares regionais](../best-practices-availability-paired-regions.md#azure-regional-pairs). Por exemplo, se você estiver operando na América do Norte, poderá escolher Leste dos EUA e Oeste dos EUA ou Centro-Norte dos EUA e Centro-Sul dos EUA para cada serviço de pesquisa.

Alguns recursos têm disponibilidade limitada com base em regiões. As restrições são descritas na documentação do recurso:

+ [Criptografia dupla](search-security-overview.md#double-encryption)

+ ["Zonas de Disponibilidade" em Escala para desempenho](search-performance-optimization.md#availability-zones).

## <a name="choose-a-pricing-tier"></a>Escolher um tipo de preço

Atualmente, o Azure Cognitive Search é oferecido em [vários tipos de preço](https://azure.microsoft.com/pricing/details/search/): Gratuito, Básico, Standard ou Otimizado para Armazenamento. Cada tipo tem sua própria [capacidade e limites](search-limits-quotas-capacity.md). Confira [Escolher um tipo de preço](search-sku-tier.md) para obter diretrizes.

Básico e Standard são as opções mais comuns para cargas de trabalho de produção, mas a maioria dos clientes começa com o serviço Gratuito. As principais diferenças entre as camadas são o tamanho e a velocidade da partição e os limites do número de objetos que você pode criar.

Lembre-se de que o tipo de preço não pode ser alterado após a criação do serviço. Caso você precise de um nível superior ou inferior, precisará recriar o serviço.

## <a name="create-your-service"></a>Criar seu serviço

Depois de fornecer as entradas necessárias, crie o serviço. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Revisar e criar o serviço" border="false":::

O serviço é implantado em minutos. Monitore o progresso por meio de notificações do Azure. Considere a possibilidade de fixar o serviço no painel para facilitar o acesso no futuro.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Monitorar e fixar o serviço" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>Obter uma chave e um ponto de extremidade de URL

A menos que você esteja usando o portal, o acesso ao novo serviço exigirá o fornecimento do ponto de extremidade de URL e de uma chave de API de autenticação.

1. Na página **Visão Geral**, localize e copie o ponto de extremidade de URL do lado direito da página.

1. Na página **Chaves**, copie uma das chaves administrativas (elas são equivalentes). As chaves de API do administrador são necessárias para criar, atualizar e excluir objetos em seu serviço. Por outro lado, as chaves de consulta fornecem acesso de leitura ao conteúdo do índice.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Página de visão geral do serviço com ponto de extremidade de URL" border="false":::

Um ponto de extremidade e uma chave não são necessários para tarefas baseadas no portal. O portal já está vinculado ao recuso da Pesquisa Cognitiva do Azure com direitos de administrador. Para obter um passo a passo do portal, comece com [Início rápido: Criar um índice da Pesquisa Cognitiva do Azure no portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Dimensione seu serviço

Depois que o serviço é fornecido, você pode dimensioná-lo para atender às suas necessidades. Se escolher o nível Standard para o serviço da Pesquisa Cognitiva do Azure, você poderá dimensionar o serviço em duas dimensões: réplicas e partições. Com a escolha do tipo Básico, você pode apenas adicionar réplicas. Se você provisionou o serviço gratuito, o dimensionamento não estará disponível.

As ***partições*** permitem que o seu serviço armazene e pesquise mais documentos.

***Réplicas*** permitem que seu serviço lide com uma carga maior de consultas de pesquisa.

A adição de recursos aumenta sua fatura mensal. A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode ajudá-lo a entender as implicações de cobrança de adição de recursos. Lembre-se de que você pode ajustar os recursos com base na carga. Por exemplo, você pode aumentar os recursos para criar um índice inicial completo e reduzir recursos posteriormente para um nível mais adequado para indexação incremental.

> [!Important]
> Um serviço deve ter [duas réplicas para o SLA somente leitura e três réplicas para o SLA de leitura/gravação](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Vá até a folha de serviço de pesquisa no Portal do Azure.
1. No painel de navegação esquerdo, selecione **Configurações** > **Escala**.
1. Use a barra deslizante para adicionar recursos de qualquer tipo.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Adicionar capacidade por meio de réplicas e partições" border="false":::

> [!Note]
> A velocidade e o armazenamento por partição aumentam em camadas superiores. Para obter mais informações, confira [Capacidade e limites](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Quando adicionar um segundo serviço

A maioria dos clientes usa apenas um serviço provisionado em uma camada que fornece o [equilíbrio certo de recursos](search-sku-tier.md). Um serviço pode hospedar vários índices, sujeito aos [limites máximos na camada selecionada](search-capacity-planning.md), com cada índice isolado do outro. Na Pesquisa Cognitiva do Azure, as solicitações podem ser direcionadas somente para um índice, minimizando a possibilidade de recuperação de dados acidental ou intencional de outros índices no mesmo serviço.

Embora a maioria dos clientes use apenas um serviço, a redundância de serviço poderá ser necessária se os requisitos operacionais incluírem o seguinte:

+ [BCDR (continuidade dos negócios e recuperação de desastres)](../best-practices-availability-paired-regions.md). A Pesquisa Cognitiva do Azure não fornece failover instantâneo caso ocorra uma interrupção.

+ [Arquiteturas multilocatários](search-modeling-multitenant-saas-applications.md), às vezes, chamam dois ou mais serviços.

+ Aplicativos implantados globalmente podem exigir serviços de pesquisa em cada geografia para minimizar a latência.

> [!NOTE]
> Na Pesquisa Cognitiva do Azure, não é possível segregar operações de indexação e de consulta; portanto, você nunca criará vários serviços para cargas de trabalho segregadas. Um índice sempre é consultado no serviço em que foi criado (não é possível criar um índice em um serviço e copiá-lo para outro).

Um segundo serviço não é necessário para alta disponibilidade. A alta disponibilidade para consultas é obtida ao usar duas ou mais réplicas no mesmo serviço. Atualizações de réplica são sequenciais, o que significa que, pelo menos, uma está operacional quando uma atualização de serviço é distribuída. Para obter mais informações sobre tempo de atividade, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Próximas etapas

Depois de provisionar um serviço, continue no portal para criar seu primeiro índice.

> [!div class="nextstepaction"]
> [Início Rápido: Criar um índice da Pesquisa Cognitiva do Azure no portal](search-get-started-portal.md)

Deseja otimizar e reduzir seus gastos com a nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar os custos com o Gerenciamento de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)