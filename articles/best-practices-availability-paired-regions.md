---
title: "BCDR (continuidade dos negócios e recuperação de desastre): regiões emparelhadas do Azure | Microsoft Docs"
description: Saiba mais sobre os pares regionais do Azure, que garantem que os aplicativos sejam resilientes durante falhas de data centers.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: c2d0a21c-2564-4d42-991a-bc31723f61a4
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: raynew
ms.openlocfilehash: 27491e34ad9e47aec2f424cfc439fad614f0e435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Emparelhadas do Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém, pelo menos, uma Região do Azure. Uma região do Azure é uma área dentro de uma região geográfica que contém um ou mais datacenters.

Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. A exceção é o Sul do Brasil, que está associado a uma região fora de sua área geográfica.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1 – Diagrama do par da região do Azure

| painel Geografia do app&#39;s selecionado | Regiões emparelhadas |  |
|:--- |:--- |:--- |
| Ásia |Ásia Oriental |Sudeste Asiático |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Canadá |Canadá Central |Leste do Canadá |
| China |Norte da China |Leste da China|
| Índia |Índia Central |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia |Coreia Central |Sul da Coreia |
| América do Norte |Centro-Norte dos EUA |Centro-Sul dos Estados Unidos |
| América do Norte |Leste dos EUA |Oeste dos EUA |
| América do Norte |Leste dos EUA 2 |Centro dos EUA |
| América do Norte |Oeste dos EUA 2 |Centro-Oeste dos EUA |
| Europa |Norte da Europa |Europa Ocidental |
| Japão |Leste do Japão |Oeste do Japão |
| Brasil |Sul do Brasil (1) |Centro-Sul dos Estados Unidos |
| Governo dos EUA |Gov do Iowa nos EUA |Gov. dos EUA – Virgínia |
| Governo dos EUA |Governo dos EUA do Arizona |Governo dos EUA do Texas |
| Departamento de Defesa dos EUA |DoD do Leste dos EUA |DoD Central dos EUA |
| Reino Unido |Oeste do Reino Unido |Sul do Reino Unido |
| Alemanha |Alemanha Central |Nordeste da Alemanha |

Tabela 1 – mapeamento de pares regionais do Azure

> (1) O Sul do Brasil é exclusivo porque ele está associado a uma região fora de sua própria região geográfica. A região secundária do Sul do Brasil é o Centro-Sul dos EUA. No entanto, a região secundária do Centro-Sul dos EUA não é o Sul do Brasil.


É recomendável que você replique as cargas de trabalho entre os pares regionais para se beneficiar das políticas de isolamento e a disponibilidade do Azure. Por exemplo, as atualizações do sistema Azure planejadas são implantadas em sequência (não ao mesmo tempo) em regiões emparelhadas. Isso significa que, mesmo no caso de uma atualização falhar, ambas as regiões não serão afetadas simultaneamente. Além disso, no caso improvável de uma interrupção ampla, a recuperação de pelo menos uma região de cada par é priorizada.

## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A Figura 2 a seguir mostra um aplicativo hipotético, que usa o par regional para recuperação de desastre. Os números verdes realçam as atividades entre regiões dos três serviços do Azure (computação, armazenamento e banco de dados do Azure) e como eles são configurados para serem replicados entre as regiões. As vantagens exclusivas de implantação entre regiões emparelhadas são realçadas pelos números laranja.

![Visão geral dos benefícios das regiões emparelhadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par da região do Azure hipotético

## <a name="cross-region-activities"></a>Atividades entre regiões
Como mencionado na Figura 2.

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Computação do Azure (PaaS)** – Você deve provisionar recursos de computação adicionais com antecedência para garantir que os recursos estejam disponíveis em outra região durante um desastre. Para saber mais, confira as [Orientação técnica de resiliência do Azure](resiliency/resiliency-technical-guidance.md).

![Armazenamento](./media/best-practices-availability-paired-regions/2Green.png) **Armazenamento do Azure** – O GRS (armazenamento com redundância geográfica) é configurado por padrão quando uma conta de armazenamento do Azure é criada. Com o GRS, seus dados são replicados automaticamente três vezes na região primária e três vezes na região emparelhada. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](storage/common/storage-redundancy.md).

![SQL Azure](./media/best-practices-availability-paired-regions/3Green.png) **Bancos de Dados SQL do Azure** – Com a replicação geográfica padrão do SQL Azure, você pode configurar a replicação assíncrona de transações como uma região emparelhada. Com a replicação geográfica premium, você pode configurar a replicação para qualquer região do mundo. No entanto, é recomendável que esses recursos sejam implantados em uma região emparelhada para a maioria dos cenários de recuperação de desastre. Para saber mais, confira [Replicação geográfica no Banco de Dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** – O Resource Manager fornece de maneira inerente isolamento lógico dos componentes do gerenciamento de serviço entre as regiões. Isso significa que falhas lógicas em uma região são apresentam menor probabilidade de afetar as outras.

## <a name="benefits-of-paired-regions"></a>Benefícios de uma região emparelhada
Como mencionado na Figura 2.  

![Isolamento](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolamento físico** – Sempre que possível, o Azure prefere pelo menos 300 milhas de separação entre os datacenters em um par regional, embora isso não seja possível nem conveniente em todas as áreas geográficas. A separação de data center físico reduz a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeito às restrições dentro da região geográfica (tamanho da região geográfica, disponibilidade da infraestrutura de rede/alimentação, normas, etc.).  

![Replicação](./media/best-practices-availability-paired-regions/6Orange.png)
**Replicação fornecida pela plataforma** – Alguns serviços, como o armazenamento com redundância geográfica, fornecem replicação automática para a região emparelhada.

![Recuperação](./media/best-practices-availability-paired-regions/7Orange.png)
**Pedido de recuperação da região** – No caso de uma interrupção ampla, é priorizada a recuperação de uma região de cada par. Os aplicativos que são implantados em regiões emparelhadas são garantidos para terem uma das regiões recuperadas com prioridade. Se um aplicativo é implantado em regiões que não são emparelhadas, pode haver um atraso na recuperação – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

![Atualizações](./media/best-practices-availability-paired-regions/8Orange.png)
**Atualizações sequenciais** – As atualizações planejadas do sistema Azure são distribuídas em regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de erros e as falhas lógicas na eventualidade de uma atualização incorreta.

![Dados](./media/best-practices-availability-paired-regions/9Orange.png)
**Residência dos dados** – Uma região reside na mesma região geográfica que seu par (com exceção do Sul do Brasil) para atender aos requisitos de residência de dados para fins de jurisdição de imposição fiscal e legal.
