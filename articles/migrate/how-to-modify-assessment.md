---
title: Personalizar Avaliações para a avaliação do servidor de migrações para Azure | Microsoft Docs
description: Descreve como personalizar avaliações criadas com a avaliação de servidor de migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: de90cffe8760a55aafebf079678d139c6f6f99e1
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751097"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

Este artigo descreve como personalizar as avaliações criadas pela avaliação do servidor de migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de aplicativos e cargas de trabalho locais, bem como VMs da nuvem pública/privada para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

Você pode usar a ferramenta de avaliação de servidor de migrações para Azure para criar Avaliações para VMs VMware locais e VMs do Hyper-V, em preparação para a migração para o Azure. A ferramenta de avaliação do servidor avalia os servidores locais para migração para as máquinas virtuais IaaS do Azure e para a solução VMware do Azure (AVS). 

## <a name="about-assessments"></a>Sobre avaliações

As avaliações criadas com a avaliação do servidor são um instantâneo de dados de ponto no tempo. Há dois tipos de avaliações que podem ser criadas usando as Migrações para Azure: Avaliação de Servidor.

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md), [VMs do Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) locais para migração para o Azure usando esse tipo de avaliação.(concepts-assessment-calculation.md)
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

Uma avaliação de VM do Azure na avaliação do servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada nos dados de utilização da CPU e de memória.<br/><br/> A recomendação de tipo de disco (HDD/SSD Standard ou discos gerenciados Premium) é baseada na IOPS e na taxa de transferência dos discos locais.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada nos dados de utilização da CPU e de memória.
**No estado em que se encontra localmente** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada no tamanho da VM local<br/><br> O tipo de disco recomendado é baseado no que você seleciona na configuração de tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada no tamanho da VM local.


## <a name="how-is-an-assessment-done"></a>Como é feita uma avaliação?

Uma avaliação feita na avaliação do servidor de migrações para Azure tem três estágios. A avaliação começa com uma análise de adequação, seguida de dimensionamento, e por fim, uma estimativa de custo mensal. Uma máquina só passa para um estágio posterior se passar pelo anterior. Por exemplo, se um computador falhar na verificação de adequação do Azure, será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão calculados. [Saiba mais.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>O que há em uma avaliação de VM do Azure?

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | O local do Azure para o qual você deseja migrar.<br/> Atualmente, a avaliação do servidor dá suporte a estas regiões de destino: leste da Austrália, sudeste da Austrália, sul do Brasil, central do Canadá, leste do Canadá, Índia central, EUA Central, Leste da China, Norte da China, Ásia Oriental, leste dos EUA, leste dos dos EUA 2, Alemanha central, Alemanha nordeste, leste do Japão, oeste do Japão, EUA Central, Europa Setentrional, EUA Central Arizona , US Gov Texas, US Gov-Virgínia, Oeste EUA Central, Europa Ocidental, oeste da Índia, oeste dos EUA e dos EUA 2 ocidental.
**Tipo de armazenamento** | Você pode usar essa propriedade para especificar o tipo de discos para o qual deseja mover, no Azure.<br/><br/> Para o dimensionamento local, você pode especificar o tipo de armazenamento de destino como discos gerenciados Premium, discos gerenciados SSD Standard ou discos gerenciados por HDD Standard. Para o dimensionamento baseado em desempenho, você pode especificar o tipo de disco de destino como discos automáticos, gerenciados Premium, discos gerenciados HDD Standard ou discos gerenciados por SSD Standard.<br/><br/> Quando você especifica o tipo de armazenamento como automático, a recomendação de disco é feita com base nos dados de desempenho dos discos (IOPS e taxa de transferência). Se você especificar o tipo de armazenamento como Premium/Standard, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se você quiser obter um SLA de VM de instância única de 99,9%, talvez queira especificar o tipo de armazenamento como discos gerenciados Premium. Isso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium. Azure
**Instâncias reservadas (RI)** | Essa propriedade ajuda a especificar se você tem [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure, as estimativas de custo na avaliação são então feitas em descontos de ri. As instâncias reservadas atualmente só têm suporte para a oferta de Pagamento Conforme o Uso em Migrações para Azure.
**Critério de dimensionamento** | O critério a ser usado para o tamanho correto das VMs para o Azure. Você pode fazer o dimensionamento *com base no desempenho* ou dimensionar as VMs *como locais*, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a ser considerada para avaliar os dados de desempenho dos computadores. Essa propriedade só é aplicável quando o critério de dimensionamento é *baseado em desempenho*.
**Utilização de percentual** | O valor percentual da amostra de desempenho definido para ser considerado para o redimensionamento. Essa propriedade só é aplicável quando o dimensionamento é *baseado em desempenho*.
**Série da VM** |     Você pode especificar a série de VM que deseja considerar para o dimensionamento correto. Por exemplo, se você tiver um ambiente de produção que não planeja migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série e o dimensionamento correto será feito apenas na série selecionada.
**Fator de conforto** | A avaliação de servidor de migrações para Azure considera um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você se inscreveu. As Migrações para Azure calculam o custo de acordo com isso.
**Moeda** | Moeda de cobrança.
**Desconto (%)** | Qualquer desconto específico da assinatura  recebido por você sobre a oferta do Azure.<br/> A configuração padrão é 0%.
**Tempo de atividade da VM** | Se as VMs não estiverem em execução 24x7 no Azure, será possível especificar a duração da execução (número de dias por mês e número de horas por dia) e as estimativas de custo serão feitas adequadamente.<br/> O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifique se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para VMs do Windows. O padrão é Sim.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>O que há de uma avaliação da AVS (solução do Azure VMware)?

Aqui estão as novidades incluídas em uma avaliação de AVS na avaliação do servidor:


| **Propriedade** | **Detalhes** |
| - | - |
| **Local de destino** | Especifica o local da nuvem privada AVS para o qual você deseja migrar.<br/><br/> A avaliação da AVS na avaliação do servidor atualmente dá suporte a estas regiões de destino: leste dos EUA, Europa Ocidental, oeste dos EUA. |
| **Tipo de armazenamento** | Especifica o mecanismo de armazenamento a ser usado na AVS.<br/><br/> Observe que as avaliações de AVS só dão suporte ao vSAN como um tipo de armazenamento padrão. |
**Instâncias reservadas (RIs)** | Essa propriedade ajuda a especificar instâncias reservadas no AVS. Atualmente, não há suporte para RIs em nós de AVS. |
**Tipo de nó** | Especifica o [tipo de nó AVS](../azure-vmware/concepts-private-clouds-clusters.md) usado para mapear as VMs locais. Observe que o tipo de nó padrão é AV36. <br/><br/> As migrações para Azure recomendarão um número necessário de nós para que as VMs sejam migradas para a AVS. |
**Configuração de FTT, nível de RAID** | Especifica a falha aplicável a tolerar e combinações de RAID. A opção FTT selecionada combinada com o requisito de disco de VM local determinará o armazenamento total do vSAN necessário na AVS. |
**Critério de dimensionamento** | Define os critérios a serem usados para as VMs de _tamanho correto_ para a AVS. Você pode optar pelo dimensionamento _baseado em desempenho_ ou _como local_ sem considerar o histórico de desempenho. |
**Histórico de desempenho** | Define a duração a ser considerada na avaliação dos dados de desempenho dos computadores. Essa propriedade é aplicável somente quando os critérios de dimensionamento são _baseados em desempenho_. |
**Utilização de percentual** | Especifica o valor percentual do conjunto de amostras de desempenho a ser considerado para o dimensionamento correto. Essa propriedade é aplicável somente quando o dimensionamento é baseado em desempenho.|
**Fator de conforto** | A avaliação de servidor de migrações para Azure considera um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. |
**Oferta** | Exibe a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) que você está inscrito no. As Migrações para Azure calculam o custo de acordo com isso.|
**Moeda** | Mostra a moeda de cobrança da sua conta. |
**Desconto (%)** | Lista qualquer desconto específico de assinatura que você recebe na parte superior da oferta do Azure. A configuração padrão é 0%. |
**Benefício Híbrido do Azure** | Especifica se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Embora isso não tenha nenhum impacto no preço das soluções do Azure VMware devido ao preço baseado no nó, os clientes ainda podem aplicar suas licenças do sistema operacional local (com base na Microsoft) em AVS usando os benefícios híbridos do Azure. Outros fornecedores de sistema operacional de software precisarão fornecer seus próprios termos de licenciamento, como o RHEL, por exemplo. |
**vCPU excesso de assinatura** | Especifica a proporção do número de núcleos virtuais vinculados a 1 núcleo físico no nó AVS. O valor padrão nos cálculos é 4 vCPU: 1 núcleo físico em AVS. <br/><br/> Os usuários da API podem definir esse valor como um inteiro. Observe que a assinatura vCPU > 4:1 pode começar a causar degradação do desempenho, mas pode ser usada para cargas de trabalho do tipo de servidor Web. |

## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

Para editar as propriedades de avaliação depois de criar uma avaliação, faça o seguinte:

1. No projeto migrações para Azure, clique em **servidores**.
2. Em **migrações para Azure: avaliação de servidor**, clique na contagem de avaliações.
3. Em **avaliação**, clique na avaliação relevante > **Editar propriedades**.
5. Personalize as propriedades de avaliação de acordo com as tabelas acima.
6. Clique em **Salvar** para atualizar a avaliação.


Você também pode editar as propriedades de avaliação quando estiver criando uma avaliação.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações de VM do Azure são calculadas.
- [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md) sobre como as avaliações de AVS são calculadas.
