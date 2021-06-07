---
title: Estimar custos
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre eventos faturáveis, o modelo de preços e dicas para gerenciar o custo da execução de um serviço de Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539559"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Como estimar e gerenciar os custos de um serviço de Pesquisa Cognitiva do Azure

Neste artigo, saiba mais sobre o modelo de preços, eventos faturáveis e dicas para gerenciar o custo da execução de um serviço de Pesquisa Cognitiva do Azure.

## <a name="pricing-model"></a>Modelo de preços

A arquitetura de escalabilidade no Azure Pesquisa Cognitiva baseia-se em combinações flexíveis de réplicas e partições para que você possa variar a capacidade dependendo se precisa de mais poder de consulta ou de indexação e paga apenas pelo que precisa.

A quantidade de recursos usados pelo serviço de pesquisa, multiplicada pela taxa de cobrança estabelecida pela camada de serviço, determina o custo da execução do serviço. Os custos e a capacidade estão ligados rigidamente. Ao estimar os custos, compreender a capacidade necessária para executar a indexação e as cargas de trabalho de consulta oferece a melhor ideia do que os custos projetados serão.

Para fins de cobrança, há duas fórmulas simples a serem consideradas:

| Fórmula | Descrição |
|---------|-------------|
| **R x P = SU** | O número de réplicas usadas, multiplicado pelo número de partições usadas, é igual à quantidade de *unidades de pesquisa* (Su) usada por um serviço. Um SU é uma unidade de recurso e pode ser uma partição ou uma réplica. |
| **Taxa de cobrança do SU * = gasto mensal** | O número de SUs multiplicado pela taxa de cobrança da camada na qual você provisionou o serviço é o principal determinante da sua fatura mensal geral. Alguns recursos ou cargas de trabalho têm dependências em outros serviços do Azure, o que pode aumentar o custo de sua solução no nível da assinatura. A seção eventos faturáveis abaixo identifica os recursos que podem ser adicionados à sua fatura. |

Cada serviço começa com uma UA (uma réplica multiplicada por uma partição) como o mínimo. O máximo de qualquer serviço é o SUs 36. Esse máximo pode ser alcançado de várias maneiras: 6 partições x 6 réplicas ou 3 partições x 12 réplicas, por exemplo. É comum usar a capacidade menor que o total (por exemplo, um serviço de 3 réplicas, de três partições cobrado como 9 SUs). Consulte o gráfico de [combinações de partição e réplica](search-capacity-planning.md#chart) para obter combinações válidas.

A taxa de cobrança é por hora por SU. Cada camada tem uma taxa progressivamente maior. As camadas mais altas vêm com partições maiores e speediers, e isso contribui para uma taxa geral maior por hora para essa camada. Você pode exibir as taxas de cada camada na página de [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) .

A maioria dos clientes coloca apenas uma parte da capacidade total online, mantendo o restante em reserva. Para cobrança, o número de partições e réplicas que você coloca online, calculadas pela fórmula SU, determina o que você paga por hora. 

## <a name="billable-events"></a>Eventos faturáveis

Uma solução criada no Azure Pesquisa Cognitiva pode gerar custos das seguintes maneiras:

+ [Custo do próprio serviço](#service-costs) , executando 24x7, na configuração mínima (uma partição e réplica), na taxa base. Você pode considerar isso como o custo fixo da execução do serviço.

+ Adição de capacidade (réplicas ou partições), em que os custos aumentam em incrementos da taxa faturável. Se a alta disponibilidade for um requisito de negócios, você precisará de 3 réplicas.

+ Encargos de largura de banda (transferência de dados de saída)

+ Serviços de complemento necessários para recursos ou recursos específicos:

  + Enriquecimento de ia (requer [Serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + loja de conhecimento (requer o [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/))
  + enriquecimento incremental (requer o [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/), aplica-se ao enriquecimento de ia)
  + chaves gerenciadas pelo cliente e criptografia dupla (requer [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + pontos de extremidade privados para um modelo de acesso sem Internet (requer o [link privado do Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Custos de serviço

Ao contrário das máquinas virtuais ou outros recursos que podem ser "pausados" para evitar cobranças, um serviço de Pesquisa Cognitiva do Azure está sempre disponível em hardware dedicado para seu uso exclusivo. Dessa forma, a criação de um serviço é um evento cobrável que começa quando você cria o serviço e termina quando você exclui o serviço. 

O encargo mínimo é a primeira unidade de pesquisa (uma réplica x uma partição) na taxa faturável. Esse mínimo é fixo durante o tempo de vida do serviço, pois o serviço não pode ser executado em nada menor que essa configuração. 

Além do mínimo, você pode adicionar réplicas e partições independentemente umas das outras. Aumentos incrementais na capacidade por meio de réplicas e partições aumentarão sua fatura com base na fórmula a seguir: **(réplicas x partições x taxa de cobrança)**, em que a taxa cobrada depende do tipo de preço selecionado.

Quando estiver estimando o custo de uma solução de pesquisa, tenha em mente que os preços e a capacidade não são lineares (dobrar a capacidade mais do que o dobro do custo). Para obter um exemplo de como a fórmula funciona, consulte [como alocar réplicas e partições](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Cobranças de largura de banda

O uso de [indexadores](search-indexer-overview.md) pode afetar a cobrança se a fonte de dados do Azure estiver em uma região diferente da pesquisa cognitiva do Azure. Nesse cenário, pode haver um custo para mover dados de saída da fonte de dados do Azure para o Azure Pesquisa Cognitiva. Para obter detalhes, consulte as páginas de preços da plataforma de dados do Azure em questão.

Você pode eliminar os encargos de saída de dados inteiramente se criar o serviço de Pesquisa Cognitiva do Azure na mesma região que seus dados. Aqui estão algumas informações da [página de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Dados de entrada: a Microsoft não cobra por nenhum dado de entrada para qualquer serviço no Azure. 

+ Dados de saída: dados de saída referem-se aos resultados da consulta. Pesquisa Cognitiva não cobra pelos dados de saída, mas os encargos de saída do Azure serão possíveis se os serviços estiverem em regiões diferentes.

  Esses encargos não são, na verdade, parte da sua conta de Pesquisa Cognitiva do Azure. Eles são mencionados aqui porque, se você estiver enviando resultados para outras regiões ou aplicativos não Azure, poderá ver esses custos refletidos na sua fatura geral.

### <a name="ai-enrichment-with-cognitive-services"></a>Enriquecimento de ia com serviços cognitivas

Para [aprimorar o ia](cognitive-search-concept-intro.md), você deve planejar [anexar um recurso de serviços cognitivas do Azure cobrável](cognitive-search-attach-cognitive-services.md), na mesma região que o Azure pesquisa cognitiva, no tipo de preço S0 para processamento pago conforme o uso. Não há nenhum custo fixo associado à anexação de serviços cognitivas. Você paga apenas pelo processamento de que precisa.

| Operação | Impacto de cobrança |
|-----------|----------------|
| Quebra de documentos, extração de texto | Gratuita |
| Quebra de documento, extração de imagem | Cobrado de acordo com o número de imagens extraídas dos seus documentos. Em uma [configuração de indexador](/rest/api/searchservice/create-indexer#indexer-parameters), **imageaction** é o parâmetro que dispara a extração de imagem. Se **imageaction** for definido como "None" (o padrão), você não será cobrado pela extração de imagem. A taxa de extração de imagem está documentada na página de [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) do Azure pesquisa cognitiva.|
| [Habilidades cognitivas internas](cognitive-search-predefined-skills.md) | Cobrado na mesma taxa que se você executou a tarefa usando serviços cognitivas diretamente. |
| Habilidades personalizadas | Uma habilidade personalizada é A funcionalidade que você fornece. O custo de usar uma habilidade personalizada depende totalmente de se o código personalizado está chamando outros serviços medidos. |

O recurso de [enriquecimento incremental (versão prévia)](cognitive-search-incremental-indexing-conceptual.md) permite que você forneça um cache que permite que o indexador seja mais eficiente na execução apenas das habilidades cognitivas necessárias se você modificar o seu Skill no futuro, economizando tempo e dinheiro.

## <a name="tips-for-managing-costs"></a>Dicas para gerenciar custos

As diretrizes a seguir podem ajudá-lo a reduzir os custos ou gerenciar custos com mais eficiência:

+ Crie todos os recursos na mesma região, ou no menor número de regiões possível, para minimizar ou eliminar encargos de largura de banda.

+ Consolide todos os serviços em um grupo de recursos, como o Azure Pesquisa Cognitiva, serviços cognitivas e quaisquer outros serviços do Azure usados em sua solução. No portal do Azure, encontre o grupo de recursos e use os comandos de **Gerenciamento de custos** para obter informações sobre gastos reais e projetados.

+ Considere o aplicativo Web do Azure para seu aplicativo front-end para que as solicitações e respostas permaneçam dentro do limite de data center.

+ Escalar verticalmente para operações com uso intensivo de recursos, como indexação, e reajustar verticalmente para cargas de trabalho de consulta regular. Comece com a configuração mínima do Azure Pesquisa Cognitiva (uma SU composta de uma partição e uma réplica) e, em seguida, monitore a atividade do usuário para identificar os padrões de uso que indicariam a necessidade de mais capacidade. Se houver um padrão previsível, você poderá sincronizar a escala com a atividade (você precisaria escrever código para automatizar isso).

+ O gerenciamento de custos é integrado à infraestrutura do Azure. Revise o [Gerenciamento de custos e cobrança](../cost-management-billing/cost-management-billing-overview.md) para obter mais informações sobre como controlar custos, ferramentas e APIs.

Não é possível desligar um serviço de pesquisa em uma base temporária. Os recursos dedicados estão sempre operacionais, alocados para seu uso exclusivo durante o tempo de vida do seu serviço. Excluir um serviço é permanente e também exclui seus dados associados.

Em termos do próprio serviço, a única maneira de reduzir sua conta é reduzir as réplicas e partições para um nível que ainda fornece desempenho aceitável e [conformidade de SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ou criar um serviço em uma camada mais baixa (as tarifas de S1 por hora são menores do que as taxas S2 ou S3). Supondo que você provisione seu serviço na extremidade inferior de suas projeções de carga, se você ultrapassar o serviço, poderá criar um segundo serviço em camadas maior, recriar os índices no segundo serviço e, em seguida, excluir o primeiro.

## <a name="next-steps"></a>Próximas etapas

Saiba como monitorar e gerenciar custos em sua assinatura do Azure.

> [!div class="nextstepaction"]
> [Documentação de Cobrança e Gerenciamento de Custos do Azure](../cost-management-billing/cost-management-billing-overview.md)