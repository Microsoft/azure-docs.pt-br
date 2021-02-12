---
title: Perguntas frequentes sobre a taxa de transferência provisionada de dimensionamento automático no Azure Cosmos DB
description: Perguntas frequentes sobre a taxa de transferência provisionada de dimensionamento automático para bancos de dados e contêineres do Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 31b96f03a8519b068eaa816443be0a0f374a4a8c
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247022"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Perguntas frequentes sobre a taxa de transferência provisionada de dimensionamento automático no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Com a taxa de transferência provisionada de dimensionamento automático, o Azure Cosmos DB automaticamente gerencia e dimensiona as RU/s do banco de dados ou contêiner com base no uso. Este artigo apresenta as respostas das perguntas frequentes sobre o dimensionamento automático.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Qual é a diferença entre "piloto automático" e "dimensionamento automático" no Azure Cosmos DB?
"Dimensionamento automático" ou "taxa de transferência provisionada de dimensionamento automático" é o nome atualizado para o recurso conhecido anteriormente como "piloto automático". Com a versão atual do dimensionamento automático, adicionamos novos recursos, incluindo a capacidade de definir o máximo personalizado de RU/s e o suporte programático. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>O que acontece com os bancos de dados ou contêineres criados no modelo de camada do piloto automático anterior?
Os recursos criados com o modelo de camada anterior são automaticamente compatíveis com o novo modelo de RU/s máximo personalizado de dimensionamento automático. O limite superior da camada torna-se o novo máximo de RU/s, que resulta no mesmo intervalo de escala. 

Por exemplo, se você selecionou anteriormente a camada dimensionada entre 400 a 4.000 RU/s, o banco de dados ou o contêiner agora será mostrado como se tivesse no máximo 4.000 RU/s, o que é dimensionado entre 400 e 4.000 RU/s. A partir deste ponto, você pode alterar o máximo de RU/s para um valor personalizado para adequar-se à carga de trabalho. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Com que rapidez o dimensionamento automático será dimensionado com base nos picos de tráfego?
Com o dimensionamento automático, o sistema escala ou reduz verticalmente a taxa de transferência (RU/s) `T` dentro do intervalo de `0.1 * Tmax` e `Tmax`, com base no tráfego de entrada. Como o dimensionamento é automático e instantâneo, a qualquer momento, é possível consumir até o `Tmax` provisionado sem atraso. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Como posso determinar para quais RU/s o sistema está dimensionado no momento?
Use as [métricas do Azure Monitor](how-to-choose-offer.md#measure-and-monitor-your-usage) para monitorar o máximo de RU/s e a taxa de transferência (RU/s) provisionada para a qual o sistema é dimensionado. 

### <a name="what-is-the-pricing-for-autoscale"></a>Qual é o preço do dimensionamento automático?
A cada hora, você será cobrado pela maior taxa de transferência `T` para a qual o sistema é dimensionado dentro da hora. Se seu recurso não recebeu solicitações durante a hora ou não foi dimensionado além de `0.1 * Tmax`, você será cobrado pelo mínimo de `0.1 * Tmax`. Veja a [página de preço](https://azure.microsoft.com/pricing/details/cosmos-db/) do Azure Cosmos DB para obter detalhes. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Como o dimensionamento automático é mostrado na minha fatura?
Em contas de região de gravação única, a taxa de dimensionamento automático por 100 RU/s é 1,5 x a taxa de taxa de transferência padrão (manual) provisionada. Na sua fatura, você verá o medidor da taxa de transferência provisionada padrão atual. A quantidade desse medidor será multiplicada por 1,5. Por exemplo, se as RU/s mais altas para as quais o sistema dimensionou dentro de uma hora foram 6.000 RU/s, você receberá uma cobrança de 60 * 1,5 = 90 unidades do medidor para essa hora.

Em contas com várias regiões de gravação, a taxa de dimensionamento automático por 100 RU/s é a mesma que a taxa da taxa de transferência de região de gravação múltipla padrão (manual) provisionada. Em sua conta, você verá o medidor de várias regiões de gravação existentes. Como as taxas são as mesmas, se você usar o dimensionamento automático, verá a mesma quantidade da taxa de transferência padrão.

### <a name="does-autoscale-work-with-reserved-capacity"></a>O dimensionamento automático funciona com capacidade reservada?
Sim. Quando você adquire capacidade reservada para contas com regiões de gravação única, o desconto de reserva para recursos de dimensionamento automático é aplicado ao seu uso de medidor a uma taxa de 1,5 * a [taxa da região específica](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region). 

A capacidade reservada da região de várias gravações funciona da mesma para a taxa de transferência de dimensionamento automático e padrão (manual). Confira [Capacidade reservada do Azure Cosmos DB](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>O dimensionamento automático funciona com a camada gratuita?
Sim. Na camada gratuita, você pode usar a taxa de transferência do dimensionamento automático em um contêiner. O suporte para bancos de dados de taxa de transferência compartilhada de dimensionamento automático com o máximo personalizado de RU/s ainda não está disponível. Confira como [a cobrança da camada gratuita funciona com o dimensionamento automático](understand-your-bill.md#azure-free-tier).

### <a name="is-autoscale-supported-for-all-apis"></a>O dimensionamento automático tem suporte para todas as APIs?
Sim, o dimensionamento automático tem suporte para todas as APIs: Core (SQL), Gremlin, Table, Cassandra e API para MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>O dimensionamento automático tem suporte para contas de gravação de várias regiões?
Sim. O máximo de RU/s está disponível em cada região adicionada à conta do Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Como posso habilitar o dimensionamento automático em novos bancos de dados ou contêineres?
Confira este artigo em [como habilitar o dimensionamento automático](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Posso habilitar o dimensionamento automático em um banco de dados ou contêiner existente?
Sim. Você também pode alternar entre o dimensionamento automático e a taxa de transferência padrão (manual) provisionada, conforme necessário. No momento, para todas as APIs, você só pode usar o [portal do Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) para realizar essas operações.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Como funciona a migração entre o dimensionamento automático e a taxa de transferência padrão (manual) provisionada?
Conceitualmente, a alteração do tipo de taxa de transferência é um processo de duas etapas. Primeiro, você envia uma solicitação para alterar as configurações de taxa de transferência para usar o dimensionamento automático ou a taxa de transferência manual provisionada. Em ambos os casos, o sistema automaticamente determina e define um valor inicial de RU/s, com base nas configurações de taxa de transferência atuais e no armazenamento. Durante essa etapa, os valores de RU/s fornecidos pelo usuário não são aceitos. Depois que a atualização for concluída, você pode [alterar as RU/s](#can-i-change-the-max-rus-on-the-database-or-container) para adequar-se à carga de trabalho. 

**Migração da taxa de transferência padrão (manual) provisionada para o dimensionamento automático**

Para um contêiner, use a fórmula a seguir para estimar o máximo de dimensionamento automático inicial de RU/s: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)``, arredondado para as 1.000 RU/s mais próximas. O máximo de RU/s de dimensionamento automático inicial real pode variar dependendo da configuração da conta.

Exemplo nº 1: Suponha que você tenha um contêiner com uma taxa de transferência manual provisionada de 10.000 RU/s e 25 GB de armazenamento. Quando você habilitar o dimensionamento automático, o máximo de RU/s de dimensionamento automático inicial será: 10.000 RU/s, o que será dimensionado entre 1.000 e 10.000 RU/s. 

Exemplo nº 2: Suponha que você tenha um contêiner com uma taxa de transferência manual provisionada de 50.000 RU/s e 2.500 GB de armazenamento. Quando você habilitar o dimensionamento automático, o máximo de RU/s de dimensionamento automático inicial será: 250.000 RU/s, o que será dimensionado entre 25.000 e 250.000 RU/s. 

**Migração do dimensionamento automático para a taxa de transferência padrão (manual) provisionada**

A taxa de transferência manual provisionada inicial será igual ao máximo de RU/s de dimensionamento automático atual. 

Exemplo: Suponha que você tenha um banco de dados ou contêiner de dimensionamento automático com o máximo de 20.000 RU/s (o que é dimensionado entre 2.000 e 20.000 RU/s). Quando você atualizar para usar a taxa de transferência manual provisionada, a taxa de transferência inicial será 20.000 RU/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Há suporte da CLI do Azure ou do PowerShell para gerenciar bancos de dados ou contêineres com dimensionamento automático?
No momento, você só pode criar e gerenciar recursos com o dimensionamento automático no portal do Azure, SDK do .NET v3, SDK do Java v4 e Azure Resource Manager. O suporte na CLI do Azure, no PowerShell e em outros SDKs ainda não está disponível.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>O dimensionamento automático é compatível com os bancos de dados de taxa de transferência compartilhada?
Sim, o dimensionamento automático é compatível com os bancos de dados de taxa de transferência compartilhada. Para habilitar esse recurso, selecione dimensionamento automático e a opção **Provisionar taxa de transferência** ao criar o banco de dados. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Qual é o número de contêineres permitidos por banco de dados de taxa de transferência compartilhada, quando o dimensionamento automático está habilitado?
O Azure Cosmos DB impõe no máximo 25 contêineres em um banco de dados de taxa de transferência compartilhada, o que se aplica a bancos de dados com dimensionamento automático ou taxa de transferência padrão (manual). 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Qual é o impacto do dimensionamento automático no nível de consistência do banco de dados?
Não há nenhum impacto sobre o dimensionamento automático no nível de consistência do banco de dados.
Consulte o artigo [níveis de consistência](consistency-levels.md) para obter mais informações sobre os níveis de consistência disponíveis.

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual é o limite de armazenamento associado a cada opção do máximo de RU/s?  
O limite de armazenamento em GB para cada máximo de RU/s é: Máximo de RU/s do banco de dados ou contêiner / 100. Por exemplo, se o máximo de RU/s for 20.000 RU/s, o recurso pode dar suporte a 200 GB de armazenamento. Confira o artigo [limites de dimensionamento automático](provision-throughput-autoscale.md#autoscale-limits) para obter o máximo de RU/s e as opções de armazenamento disponíveis. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>O que acontece se eu exceder o limite de armazenamento associado à minha taxa de transferência máxima?
Se o limite de armazenamento associado à taxa de transferência máxima do banco de dados ou contêiner for excedido, o Azure Cosmos DB aumentará automaticamente a taxa de transferência máxima para as próximas RU/s mais altas que podem dar suporte a esse nível de armazenamento.

Por exemplo, se você começar com o máximo de 50.000 RU/s (o que é dimensionado entre 5.000 e 50.000 RU/s), pode armazenar até 500 GB de dados. Se você exceder 500 GB - por exemplo, o armazenamento agora é de 600 GB, o novo máximo de RU/s será 60.000 RU/s (o que é dimensionado entre 6.000 e 60.000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Posso alterar o máximo de RU/s no banco de dados ou contêiner? 
Sim. Confira este [artigo](how-to-provision-autoscale-throughput.md) sobre como alterar o máximo de RU/s. Quando você altera o máximo de RU/s, dependendo do valor solicitado, essa pode ser uma operação assíncrona que pode demorar um pouco para ser concluída (pode ser de até 4-6 horas, dependendo das RU/s selecionadas)

#### <a name="increasing-the-max-rus"></a>Aumento do máximo de RU/s
Quando você envia uma solicitação para aumentar o máximo de RU/s `Tmax`, dependendo do máximo de RU/s selecionado, o serviço provisiona mais recursos para dar suporte ao máximo de RU/s mais altas. Durante essa ação, a carga de trabalho e as operações atuais não serão afetadas. O sistema continuará a dimensionar o banco de dados ou contêiner entre o `0.1*Tmax` a `Tmax` anterior, até que o novo intervalo de escala de `0.1*Tmax_new` a `Tmax_new` esteja pronto.

#### <a name="lowering-the-max-rus"></a>Redução do máximo de RU/s
Quando você reduz o máximo de RU/s, o valor mínimo para o qual você pode defini-lo é: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`, arredondado para as 1.000 RU/s mais próximas. 

Exemplo nº 1: Suponha que você tenha um contêiner de dimensionamento automático com o máximo de 20.000 RU/s (o que é dimensionado entre 2.000 e 20.000 RU/s) e 50 GB de armazenamento. O menor valor mínimo possível para definir o máximo de RU/s é: MAX(4.000, 20.000 / 10, **50 * 100**) = 5.000 RU/s (o que é dimensionado entre 500 e 5.000 RU/s).

Exemplo nº 2: Suponha que você tenha um contêiner de dimensionamento automático com o máximo de 100.000 RU/s e 100 GB de armazenamento. Agora, você dimensiona o máximo de RU/s até 150.000 (o que é dimensionado entre 15.000 e 150.000 RU/s). Agora, o menor valor mínimo possível para definir o máximo de RU/s é: MAX(4.000, **150.000 / 10**, 100 * 100) = 15.000 RU/s (o que é dimensionado entre 1.500 e 15.000 RU/s). 

Para um banco de dados de taxa de transferência compartilhada, quando você reduz o máximo de RU/s, o valor mínimo para o qual você pode defini-lo é: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, arredondado para as 1.000 RU/s mais próximas.  

As fórmulas e os exemplos acima estão relacionados ao máximo de RU/s de dimensionamento automático mínimo que você pode definir, e é diferente do intervalo de `0.1 * Tmax` a `Tmax` em que o sistema é dimensionado automaticamente. Independentemente do máximo de RU/s, o sistema sempre será dimensionado entre `0.1 * Tmax` e `Tmax`. 

### <a name="how-does-ttl-work-with-autoscale"></a>Como o TTL funciona com o dimensionamento automático?
Com o dimensionamento automático, as operações de TTL não afetam o dimensionamento de RU/s. As RUs consumidas devido ao TTL não fazem parte das RU/s cobradas do contêiner de dimensionamento automático. 

Por exemplo, suponha que você tenha um contêiner de dimensionamento automático com 400 - 4.000 RU/s:
- Hora 1: T=0: O contêiner não tem utilização (nenhuma solicitação de TTL ou de carga de trabalho). As RU/s faturáveis são 400 RU/s.
- Hora 1: T=1: O TTL está habilitado.
- Hora 1: T=2: O contêiner começa a receber solicitações que consomem 1.000 RU/s em 1 segundo. Também existem 200 RUs que valem o TTL que precisam acontecer. As RU/s faturáveis ainda são 1.000 RU/s. Independentemente de quando ocorrem os TTLs, eles não afetam a lógica de dimensionamento automático.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual é o mapeamento entre o máximo de RU/s e as partições físicas?
Quando você selecionar o máximo de RU/s pela primeira vez, o Azure Cosmos DB provisionará: Máximo de RU/s / 10.000 RU/s = nº de partições físicas. Cada [partição física](partitioning-overview.md#physical-partitions) pode dar suporte a até 10.000 RU/s e 50 GB de armazenamento. À medida que o tamanho do armazenamento aumenta, o Azure Cosmos DB divide automaticamente as partições para adicionar mais partições físicas, a fim de lidar com o aumento de armazenamento, ou aumenta o máximo de RU/s, se o armazenamento [exceder o limite associado](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

O máximo de RU/s do banco de dados ou contêiner é dividido igualmente em todas as partições físicas. Portanto, a taxa de transferência total em que qualquer partição física única pode ser dimensionada é: Máximo de RU/s do banco de dados ou contêiner / nº de partições físicas. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>O que acontece se as solicitações de entrada excederem o máximo de RU/s do banco de dados ou contêiner?
Se as RU/s gerais consumidas excederem o máximo de RU/s do banco de dados ou contêiner, as solicitações que excederem o máximo de RU/s serão limitadas e retornarão um código de status 429. As solicitações que resultam em mais de 100% de utilização normalizada também serão limitadas. A utilização normalizada é definida como o máximo da utilização de RU/s em todas as partições físicas. Por exemplo, suponha que a taxa de transferência máxima seja 20.000 RU/s e que você tenha duas partições físicas, P_1 e P_2, cada uma com capacidade de dimensionamento para 10.000 RU/s. Em determinado segundo, se P_1 tiver usado 6.000 RUs e P_2 8.000 RUs, a utilização normalizada será MAX(6.000 RUs / 10.000 RUs, 8.000 RUs/ 10.000 RUs) = 0,8.

> [!NOTE]
> Os SDKs do cliente e as ferramentas de importação de dados (Azure Data Factory, biblioteca de executores em massa) do Azure Cosmos DB repetem automaticamente em 429s, portanto, 429s ocasionais são adequados. Um número alto constante de 429s pode indicar que você precisa aumentar o máximo de RU/s ou examinar sua estratégia de particionamento para uma [partição ativa](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Ainda é possível ver 429s (limitação/limitação de taxa) quando o dimensionamento automático está habilitado? 
Sim. É possível ver 429s em dois cenários. Primeiro, quando as RU/s gerais consumidas excedem o máximo de RU/s do banco de dados ou contêiner, o serviço limita as solicitações de acordo. 

Segundo, se houver uma partição ativa, ou seja, um valor de chave de partição lógica que tenha uma quantidade desproporcionalmente maior de solicitações em comparação a outros valores de chave de partição, é possível que a partição física subjacente exceda seu orçamento de RU/s. Como melhor prática, para evitar partições ativas, [escolha uma chave de partição adequada](partitioning-overview.md#choose-partitionkey) que resulte em uma distribuição uniforme do armazenamento e da taxa de transferência. 

Por exemplo, se você selecionar a opção de taxa de transferência máxima de 20.000 RU/s e tiver 200 GB de armazenamento, com quatro partições físicas, cada partição física pode ser dimensionada para até 5.000 RU/s. Se houver uma partição ativa em uma chave de partição lógica específica, você verá 429s quando a partição física subjacente na qual ela reside exceder 5.000 RU/s, ou seja, exceder 100% da utilização normalizada.


## <a name="next-steps"></a>Próximas etapas

* Saiba como [habilitar o dimensionamento automático em um banco de dados ou contêiner do Azure Cosmos DB](how-to-provision-autoscale-throughput.md).
* Saiba mais sobre as [benefícios da taxa de transferência provisionada com o dimensionamento automático](provision-throughput-autoscale.md#benefits-of-autoscale).
* Saiba mais sobre as [partições lógicas e físicas](partitioning-overview.md).
                        
