---
title: Estimar custos usando o planejador de capacidade Azure Cosmos DB
description: O planejador de capacidade Azure Cosmos DB permite estimar a taxa de transferência (RU/s) necessária e o custo da sua carga de trabalho. Este artigo descreve como usar a nova versão do planejador de capacidade para estimar a taxa de transferência e o custo necessário.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 829fb28577fbb89c031a3b972d3ee5e04c935e9f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434994"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Estimar RU/s usando o planejador de capacidade Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Configurar seus bancos de dados do Azure Cosmos e contêineres com a quantidade certa de taxa de transferência provisionada ou de [unidades de solicitação (ru/s)](request-units.md)para sua carga de trabalho é essencial para otimizar o custo e o desempenho. Este artigo descreve como usar o [planejador de capacidade](https://cosmos.azure.com/capacitycalculator/) Azure Cosmos DB para obter uma estimativa dos ru/s necessários e do custo de sua carga de trabalho. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Como estimar a taxa de transferência e o custo com o planejador de capacidade Azure Cosmos DB

O planejador de capacidade pode ser usado em dois modos.

|**Modo**  |**Descrição**  |
|---------|---------|
|Basic|Fornece uma estimativa rápida, de alto nível de RU/s e de custo. Esse modo assume as configurações de Azure Cosmos DB padrão para política de indexação, consistência e outros parâmetros. <br/><br/>Use o modo básico para uma estimativa rápida e de alto nível quando estiver avaliando uma carga de trabalho em potencial para ser executada em Azure Cosmos DB.|
|Avançado|Fornece uma estimativa mais detalhada de RU/s e custo, com a capacidade de ajustar configurações adicionais — política de indexação, nível de consistência e outros parâmetros que afetam o custo e a taxa de transferência. <br/><br/>Use o modo avançado quando estiver estimando RU/s para um novo projeto ou se quiser uma estimativa mais detalhada. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Estimar a taxa de transferência e o custo provisionados usando o modo básico
Para obter uma estimativa rápida para sua carga de trabalho usando o modo básico, navegue até o [planejador de capacidade](https://cosmos.azure.com/capacitycalculator/). Insira os seguintes parâmetros com base em sua carga de trabalho: 

|**Entrada**  |**Descrição**  |
|---------|---------|
|Número de regiões|O Microsoft Azure Cosmos DB está disponível em todas as regiões do Azure por padrão. Selecione o número de regiões necessárias para sua carga de trabalho. Você pode associar qualquer número de regiões à sua conta do cosmos. Consulte [distribuição global](distribute-data-globally.md) em Azure Cosmos DB para obter mais detalhes.|
|Gravações de várias regiões|Se você habilitar [gravações de várias regiões](distribute-data-globally.md#key-benefits-of-global-distribution), seu aplicativo poderá ler e gravar em qualquer região do Azure. Se você desabilitar gravações de várias regiões, seu aplicativo poderá gravar dados em uma única região. <br/><br/> Habilite gravações de várias regiões se você espera ter uma carga de trabalho ativo-ativo que exija gravações de baixa latência em regiões diferentes. Por exemplo, uma carga de trabalho IOT que grava dados no banco de dados em grandes volumes em regiões diferentes. <br/><br/> As gravações de várias regiões garantem 99,999% de disponibilidade de leitura e gravação. As gravações de várias regiões exigem mais taxa de transferência quando comparadas às regiões de gravação única. Para saber mais, confira [como o RUs é diferente para o artigo de regiões com uma e várias gravações](optimize-cost-regions.md) .|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB em uma única região.|
|Tamanho do item|O tamanho estimado do item de dados (por exemplo, documento), variando de 1 KB a 2 MB. |
|Leituras/s por região|Número de leituras esperadas por segundo. |
|Gravações/s por região|Número de gravações esperadas por segundo. |

Depois de preencher os detalhes necessários, selecione **calcular**. A guia **estimativa de custo** mostra o custo total para armazenamento e taxa de transferência provisionada. Você pode expandir o link **Mostrar detalhes** nesta guia para obter a análise da taxa de transferência necessária para solicitações de leitura e gravação. Cada vez que você alterar o valor de qualquer campo, selecione **calcular** para calcular novamente o custo estimado. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Modo básico do planejador de capacidade":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Estimar a taxa de transferência e o custo provisionados usando o modo avançado

O modo avançado permite que você forneça mais configurações que afetam a estimativa de RU/s. Para usar essa opção, navegue até o [planejador de capacidade](https://cosmos.azure.com/capacitycalculator/) e entre na ferramenta com uma conta que você usa para o Azure. A opção de entrada está disponível no canto direito. 

Depois de entrar, você pode ver campos adicionais em comparação com os campos no modo básico. Insira os parâmetros adicionais com base em sua carga de trabalho. 

|**Entrada**  |**Descrição**  |
|---------|---------|
|API|Azure Cosmos DB é um serviço multimodelo e várias APIs. Para novas cargas de trabalho, selecione API do SQL (núcleo). |
|Número de regiões|O Microsoft Azure Cosmos DB está disponível em todas as regiões do Azure por padrão. Selecione o número de regiões necessárias para sua carga de trabalho. Você pode associar qualquer número de regiões à sua conta do cosmos. Consulte [distribuição global](distribute-data-globally.md) em Azure Cosmos DB para obter mais detalhes.|
|Gravações de várias regiões|Se você habilitar [gravações de várias regiões](distribute-data-globally.md#key-benefits-of-global-distribution), seu aplicativo poderá ler e gravar em qualquer região do Azure. Se você desabilitar gravações de várias regiões, seu aplicativo poderá gravar dados em uma única região. <br/><br/> Habilite gravações de várias regiões se você espera ter uma carga de trabalho ativo-ativo que exija gravações de baixa latência em regiões diferentes. Por exemplo, uma carga de trabalho IOT que grava dados no banco de dados em grandes volumes em regiões diferentes. <br/><br/> As gravações de várias regiões garantem 99,999% de disponibilidade de leitura e gravação. As gravações de várias regiões exigem mais taxa de transferência quando comparadas às regiões de gravação única. Para saber mais, confira [como o RUs é diferente para o artigo de regiões com uma e várias gravações](optimize-cost-regions.md) .|
|Consistência padrão|O Azure Cosmos DB dá suporte a 5 níveis de consistência, para permitir que os desenvolvedores equilibrem a compensação entre a consistência, a disponibilidade e as compensações de latência. Para saber mais, confira o artigo [níveis de consistência](consistency-levels.md) . <br/><br/> Por padrão, Azure Cosmos DB usa a consistência de sessão, o que garante a capacidade de ler suas próprias gravações em uma sessão. <br/><br/> A escolha de uma forte ou desatualização limitada exigirá o dobro de RU/s necessários para leituras, quando comparado à sessão, ao prefixo consistente e à consistência eventual. Não há suporte para a consistência forte com gravações de várias regiões e o padrão será automaticamente para gravações de região única com consistência forte. |
|Política de indexação|Por padrão, Azure Cosmos DB [indexa todas as propriedades](index-policy.md) em todos os itens para consultas flexíveis e eficientes (mapeia para a política de indexação **automática** ). <br/><br/> Se você escolher **desativado**, nenhuma das propriedades será indexada. Isso resulta na menor taxa de RU para gravações. Selecione **desativado** política se você pretende apenas fazer [leituras de ponto](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (pesquisas de valor de chave) e/ou gravações e nenhuma consulta. <br/><br/> A política de indexação personalizada permite que você inclua ou exclua propriedades específicas do índice para reduzir a taxa de transferência e o armazenamento de gravação. Para saber mais, consulte a [política de indexação](index-overview.md) e os artigos de [políticas de indexação de exemplo](how-to-manage-indexing-policy.md#indexing-policy-examples) .|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB em uma única região.|
|Modo de carga de trabalho|Selecione **estável** se o volume de carga de trabalho for constante. <br/><br/> Selecione **variável** se o volume de carga de trabalho for alterado ao longo do tempo.  Por exemplo, durante um dia ou mês específico. <br/><br/> As configurações a seguir estarão disponíveis se você escolher a opção de carga de trabalho variável:<ul><li>Percentual de tempo no pico: a porcentagem de tempo em um mês em que sua carga de trabalho requer a taxa de transferência de pico (mais alta). <br/><br/> Por exemplo, se você tiver uma carga de trabalho que tenha alta atividade durante 9h – 18:00 horário comercial do dia da semana, a porcentagem de tempo no pico será: 45 horas no pico/730 horas/mês = ~ 6%.<br/><br/></li><li>Leituras/s por região em pico-número de leituras esperadas por segundo no pico.</li><li>Gravações/s por região no pico – número de gravações esperadas por segundo no pico.</li><li>Leituras/s por região fora do pico – número de leituras esperadas por segundo durante o pico.</li><li>Gravações/s por região fora do pico – número de gravações esperadas por segundo durante o pico.</li></ul>Com os intervalos de pico e fora do pico, você pode otimizar seu custo ao [dimensionar programaticamente sua taxa de transferência provisionada](set-throughput.md#update-throughput-on-a-database-or-a-container) para cima e para baixo de forma adequada.|
|Tamanho do item|O tamanho do item de dados (por exemplo, documento), variando de 1 KB a 2 MB. <br/><br/>Você também pode carregar o documento de **exemplo (JSON)** para obter uma estimativa mais precisa.<br/><br/>Se sua carga de trabalho tiver vários tipos de itens (com conteúdo JSON diferente) no mesmo contêiner, você poderá carregar vários documentos JSON e obter a estimativa. Use o botão **Adicionar novo item** para adicionar vários documentos JSON de exemplo.|

Você também pode usar o botão **salvar estimativa** para baixar um arquivo CSV que contém a estimativa atual. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Modo avançado do planejador de capacidade":::

Os preços mostrados no planejador de capacidade Azure Cosmos DB são estimativas com base nas taxas de preços públicas para taxa de transferência e armazenamento. Todos os preços são mostrados em dólares americanos. Consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para ver todas as taxas por região.  

## <a name="estimating-throughput-for-queries"></a>Estimando a taxa de transferência para consultas

A calculadora de capacidade do Azure Cosmos pressupõe leituras de ponto (uma leitura de um único item, por exemplo, Document, por ID e valor de chave de partição) e gravações para a carga de trabalho. Para estimar a taxa de transferência necessária para consultas, execute sua consulta em um conjunto de dados representativos em um contêiner Cosmos e [obtenha a cobrança de ru](find-request-unit-charge.md). Multiplique a cobrança de RU pelo número de consultas que você prevê executar por segundo para obter o total de RU/s necessário. 

Por exemplo, se sua carga de trabalho exigir uma consulta, ``SELECT * FROM c WHERE c.id = 'Alice'`` que é executada 100 vezes por segundo, e a carga de ru da consulta for 10 RUs, você precisará de 100 consulta/s * 10 ru/Query = 1000 ru/s no total para atender a essas solicitações. Adicione esses RU/s aos RU/s necessários para que as leituras ou gravações ocorram em sua carga de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [modelo de preços de Azure Cosmos DB](how-pricing-works.md).
* Crie uma nova [conta, banco de dados e contêiner do cosmos](create-cosmosdb-resources-portal.md).
* Saiba como [otimizar o custo de taxa de transferência provisionada](optimize-cost-throughput.md).
* Saiba como [otimizar o custo com capacidade reservada](cosmos-db-reserved-capacity.md).