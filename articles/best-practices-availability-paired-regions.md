---
title: Garantir a continuidade dos negócios & recuperação de desastre usando regiões emparelhadas do Azure
description: Garantir a resiliência do aplicativo usando o emparelhamento regional do Azure
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 3310d4a7d86db9dee7d5f71fc9410545817886f3
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511222"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Emparelhadas do Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

Uma região do Azure consiste em um conjunto de data centers implantados em um perímetro definido por latência e conectados por meio de uma rede dedicada de baixa latência.  Isso garante que os serviços do Azure em uma região do Azure ofereçam o melhor desempenho e segurança possíveis.  

Uma geografia do Azure define uma área do mundo que contém pelo menos uma região do Azure. As regiões geográficas definem um mercado discreto, normalmente contendo duas ou mais regiões, que preservam os limites de conformidade e residência de dados.  Encontre mais informações sobre a infraestrutura global do Azure [aqui](https://azure.microsoft.com/global-infrastructure/regions/)

Um par regional consiste em duas regiões na mesma geografia. O Azure serializa atualizações de plataforma (manutenção planejada) entre pares regionais, garantindo que apenas uma região em cada par seja atualizada de cada vez. Se uma interrupção afetar várias regiões, pelo menos uma região em cada par será priorizada para recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Alguns serviços do Azure aproveitam ainda mais as regiões emparelhadas para garantir a continuidade dos negócios e proteger contra perda de dados.  O Azure fornece várias [soluções de armazenamento](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) que aproveitam regiões emparelhadas para garantir a disponibilidade dos dados. Por exemplo, o [armazenamento com redundância geográfica do Azure](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replica os dados para uma região secundária automaticamente, garantindo que os dados sejam duráveis mesmo no caso de a região primária não ser recuperável. 

Observe que nem todos os serviços do Azure replicam dados automaticamente, nem todos os serviços do Azure fazem fallback automaticamente de uma região com falha para seu par.  Nesses casos, a recuperação e a replicação devem ser configuradas pelo cliente.

## <a name="can-i-select-my-regional-pairs"></a>Posso selecionar meus pares regionais?

Não. Alguns serviços do Azure dependem de pares regionais, como o [armazenamento redundante](./storage/common/storage-redundancy.md)do Azure. Esses serviços não permitem que você crie novos emparelhamentos regionais.  Da mesma forma, como o Azure controla a priorização de recuperação e manutenção planejada para pares regionais, você não pode definir seus próprios pares regionais para aproveitar esses serviços. No entanto, você pode criar sua própria solução de recuperação de desastres criando serviços em qualquer número de regiões e aproveitando os serviços do Azure para emparelhar. 

Por exemplo, você pode usar os serviços do Azure, como [AzCopy](./storage/common/storage-use-azcopy-v10.md) , para agendar backups de dados para uma conta de armazenamento em uma região diferente.  Usando o [DNS do Azure e o Gerenciador de tráfego do Azure](./networking/disaster-recovery-dns-traffic-manager.md), os clientes podem criar uma arquitetura resiliente para seus aplicativos que sobreviverão à perda da região primária.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Estou limitado ao uso de serviços em meus pares regionais?

Não. Embora um determinado serviço do Azure possa depender de um par regional, você pode hospedar seus outros serviços em qualquer região que atenda às suas necessidades de negócios.  Uma solução de armazenamento GRS do Azure pode emparelhar dados no Canadá central com um par no leste do Canadá ao usar recursos de computação localizados no leste dos EUA.  

## <a name="must-i-use-azure-regional-pairs"></a>É necessário usar pares regionais do Azure?

Não. Os clientes podem aproveitar os serviços do Azure para arquitetar um serviço resiliente sem depender dos pares regionais do Azure.  No entanto, recomendamos que você configure a BCDR (recuperação de desastre de continuidade de negócios) em pares regionais para se beneficiar do [isolamento](./security/fundamentals/isolation-choices.md) e melhorar a [disponibilidade](./availability-zones/az-overview.md). Para aplicativos compatíveis com várias regiões ativas, é recomendável usar ambas as regiões em um par de regiões, sempre que possível. Isso garante a disponibilidade ideal para aplicativos e o tempo de recuperação minimizado em caso de desastre. Sempre que possível, projete seu aplicativo para obter [máxima resiliência](/azure/architecture/framework/resiliency/overview) e facilidade de [recuperação de desastres](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Pares regionais do Azure

| painel Geografia do app&#39;s selecionado | Par regional A | Par regional B  |
|:--- |:--- |:--- |
| Asia-Pacific |Ásia Oriental (Hong Kong) | Sudeste Asiático (Cingapura) |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil |Centro-Sul dos Estados Unidos |
| Canada |Canadá Central |Leste do Canadá |
| China |Norte da China |Leste da China|
| China |Norte da China 2 |Leste da China 2|
| Europa |Norte da Europa (Irlanda) |Oeste da Europa (Países Baixos) |
| França |França Central|Sul da França|
| Alemanha |Alemanha Central |Nordeste da Alemanha |
| Índia |Índia Central |Sul da Índia |
| Índia |Oeste da Índia |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia do Sul |Coreia Central |Sul da Coreia |
| América do Norte |Leste dos EUA |Oeste dos EUA |
| América do Norte |Leste dos EUA 2 |Centro dos EUA |
| América do Norte |Centro-Norte dos EUA |Centro-Sul dos Estados Unidos |
| América do Norte |Oeste dos EUA 2 |Centro-Oeste dos EUA |
| Noruega | Leste da Noruega | Oeste da Noruega |
| África do Sul | Norte da África do Sul |Oeste da África do Sul |
| Suíça | Norte da Suíça |Oeste da Suíça |
| Reino Unido |Oeste do Reino Unido |Sul do Reino Unido |
| Emirados Árabes Unidos | Norte dos EAU | EAU Central
| Departamento de Defesa dos EUA |DoD do Leste dos EUA |DoD Central dos EUA |
| Governo dos EUA |Governo dos EUA do Arizona |Governo dos EUA do Texas |
| Governo dos EUA |US Gov Iowa |Gov. dos EUA – Virgínia |
| Governo dos EUA |Gov. dos EUA – Virgínia |Governo dos EUA do Texas |

> [!Important]
> - A Índia ocidental é emparelhada apenas em uma direção. A região secundária do Oeste da Índia é o Sul da Índia, mas a região secundária do Sul da Índia é a Índia Central.
> - O sul do Brasil é exclusivo porque está emparelhado com uma região fora de sua geografia. A região secundária do Sul do Brasil é EUA Central do Sul. A região secundária do Sul EUA Central não é sul do Brasil.


## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A imagem abaixo ilustra um aplicativo hipotético que usa o par regional para a recuperação de desastre. Os números verdes destacam as atividades entre regiões de três serviços do Azure (computação, armazenamento e banco de dados do Azure) e como eles são configurados para replicar entre regiões. As vantagens exclusivas de implantação entre regiões emparelhadas são realçadas pelos números laranja.

![Visão geral dos benefícios das regiões emparelhadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par da região do Azure hipotético

## <a name="cross-region-activities"></a>Atividades entre regiões
Como mencionado na Figura 2.

1. **Computação do Azure (IaaS)** – você deve provisionar recursos de computação adicionais com antecedência para garantir que os recursos estejam disponíveis em outra região durante um desastre. Para saber mais, confira as [Orientação técnica de resiliência do Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Armazenamento do Azure** – se você estiver usando discos gerenciados, saiba mais sobre [backups entre regiões](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) com o backup do Azure e [replicando VMs](./site-recovery/azure-to-azure-tutorial-enable-replication.md) de uma região para outra com Azure site Recovery. Se você estiver usando contas de armazenamento, o armazenamento com redundância geográfica (GRS) será configurado por padrão quando uma conta de armazenamento do Azure for criada. Com o GRS, seus dados são replicados automaticamente três vezes na região primária e três vezes na região emparelhada. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](storage/common/storage-redundancy.md).

3. **Banco de dados SQL do Azure** – com a replicação geográfica do banco de dados SQL do Azure, você pode configurar a replicação assíncrona de transações para qualquer região do mundo; no entanto, recomendamos que você implante esses recursos em uma região emparelhada para a maioria dos cenários de recuperação de desastres. Para saber mais, confira [Replicação geográfica no Banco de Dados SQL do Azure](./azure-sql/database/auto-failover-group-overview.md).

4. **Azure Resource Manager** – O Resource Manager fornece de maneira inerente isolamento lógico dos componentes entre as regiões. Isso significa que falhas lógicas em uma região são apresentam menor probabilidade de afetar as outras.

## <a name="benefits-of-paired-regions"></a>Benefícios de uma região emparelhada

5. **Isolamento físico** – quando possível, o Azure prefere pelo menos 300 milhas de separação entre data centers em um par regional, embora isso não seja prático ou possível em todas as regiões geográficas. A separação de data center físico reduz a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeito às restrições dentro da região geográfica (tamanho da região geográfica, disponibilidade da infraestrutura de rede/alimentação, normas, etc.).  

6. **Replicação fornecida pela plataforma** -alguns serviços como o armazenamento de Geo-Redundant fornecem replicação automática para a região emparelhada.

7. **Ordem de recuperação de região** – no caso de uma interrupção ampla, a recuperação de uma região é priorizada de cada par. Os aplicativos que são implantados em regiões emparelhadas são garantidos para terem uma das regiões recuperadas com prioridade. Se um aplicativo é implantado em regiões que não são emparelhadas, pode haver um atraso na recuperação – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

8. **Atualizações sequenciais** – as atualizações planejadas do sistema do Azure são distribuídas em regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de bugs e as falhas lógicas no evento raro de uma atualização inválida.

9. **Residência de dados** – uma região reside na mesma geografia que seu par (com exceção do Sul do Brasil) para atender aos requisitos de residência de dados para fins de jurisdição de impostos e autoridades.