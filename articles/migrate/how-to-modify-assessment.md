---
title: Personalizar Avaliações para migrações para Azure | Microsoft Docs
description: Descreve como personalizar avaliações criadas com as migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785199"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

Este artigo descreve como personalizar as avaliações criadas pela ferramenta de avaliação e descoberta de migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de aplicativos e cargas de trabalho locais, bem como VMs da nuvem pública/privada para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

Você pode usar a ferramenta de avaliação e descoberta de migrações para Azure para criar Avaliações para VMs do VMware e VMs do Hyper-V locais, em preparação para a migração para o Azure. A ferramenta de descoberta e avaliação avalia os servidores locais para migração para as máquinas virtuais IaaS do Azure e para a solução VMware do Azure (AVS). 

## <a name="about-assessments"></a>Sobre avaliações

As avaliações criadas com a ferramenta de descoberta e avaliação são um instantâneo pontual dos dados. Há dois tipos de avaliações que você pode criar usando as migrações para Azure: descoberta e avaliação.

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md), [VMs do Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) locais para migração para o Azure usando esse tipo de avaliação.(concepts-assessment-calculation.md)
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

Opções de dimensionamento de critérios nas avaliações de migração do Azure:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada nos dados de utilização da CPU e de memória.<br/><br/> A recomendação de tipo de disco (HDD/SSD Standard ou discos gerenciados Premium) é baseada na IOPS e na taxa de transferência dos discos locais.<br/><br/>**Avaliação do SQL do Azure**: a configuração do SQL do Azure baseia-se nos dados de desempenho de instâncias e bancos de dado do SQL, o que inclui: utilização da CPU, utilização de memória, IOPS (arquivos de dados e de log), taxa de transferência e latência de operações de e/s<br/><br/>**Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada nos dados de utilização da CPU e de memória.
**No estado em que se encontra localmente** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada no tamanho da VM local<br/><br> O tipo de disco recomendado é baseado no que você seleciona na configuração de tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada no tamanho da VM local.

## <a name="how-is-an-assessment-done"></a>Como é feita uma avaliação?

Uma avaliação feita na descoberta e na avaliação de migrações para Azure tem três estágios. A avaliação começa com uma análise de adequação, seguida de dimensionamento, e por fim, uma estimativa de custo mensal. Uma máquina só passa para um estágio posterior se passar pelo anterior. Por exemplo, se um computador falhar na verificação de adequação do Azure, será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão calculados. [Saiba mais.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>O que há em uma avaliação de VM do Azure?

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | O local do Azure para o qual você deseja migrar.<br/> A avaliação de VM do Azure atualmente dá suporte a estas regiões de destino: leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, Índia central, EUA Centrals, Leste da China, Norte da China, Ásia Oriental, leste dos EUA, leste dos dos EUA 2, Alemanha central, Alemanha nordeste, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia , US Gov Texas, US Gov-Virgínia, Oeste EUA Central, Europa Ocidental, oeste da Índia, oeste dos EUA e dos EUA 2 ocidental.
**Tipo de armazenamento** | Você pode usar essa propriedade para especificar o tipo de discos para o qual deseja mover, no Azure.<br/><br/> Para o dimensionamento local, você pode especificar o tipo de armazenamento de destino como discos gerenciados Premium, discos gerenciados SSD Standard ou discos gerenciados por HDD Standard. Para o dimensionamento baseado em desempenho, você pode especificar o tipo de disco de destino como discos automáticos, gerenciados Premium, discos gerenciados HDD Standard ou discos gerenciados por SSD Standard.<br/><br/> Quando você especifica o tipo de armazenamento como automático, a recomendação de disco é feita com base nos dados de desempenho dos discos (IOPS e taxa de transferência). Se você especificar o tipo de armazenamento como Premium/Standard, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se você quiser obter um SLA de VM de instância única de 99,9%, talvez queira especificar o tipo de armazenamento como discos gerenciados Premium. Isso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium. Azure
**Instâncias reservadas (RI)** | Essa propriedade ajuda a especificar se você tem [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure, as estimativas de custo na avaliação são então feitas em descontos de ri. As instâncias reservadas atualmente só têm suporte para a oferta de Pagamento Conforme o Uso em Migrações para Azure.
**Critério de dimensionamento** | O critério a ser usado para o tamanho correto das VMs para o Azure. Você pode fazer o dimensionamento *com base no desempenho* ou dimensionar as VMs *como locais*, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a ser considerada para avaliar os dados de desempenho dos computadores. Essa propriedade só é aplicável quando o critério de dimensionamento é *baseado em desempenho*.
**Utilização de percentual** | O valor percentual da amostra de desempenho definido para ser considerado para o redimensionamento. Essa propriedade só é aplicável quando o dimensionamento é *baseado em desempenho*.
**Série de VMs** |     Você pode especificar a série de VM que deseja considerar para o dimensionamento correto. Por exemplo, se você tiver um ambiente de produção que não planeja migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série e o dimensionamento correto será feito apenas na série selecionada.
**Fator de conforto** | A avaliação de VM do Azure considera um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você se inscreveu. As Migrações para Azure calculam o custo de acordo com isso.
**Moeda** | Moeda de cobrança.
**Desconto (%)** | Qualquer desconto específico da assinatura  recebido por você sobre a oferta do Azure.<br/> A configuração padrão é 0%.
**Tempo de atividade da VM** | Se as VMs não estiverem em execução 24x7 no Azure, será possível especificar a duração da execução (número de dias por mês e número de horas por dia) e as estimativas de custo serão feitas adequadamente.<br/> O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifique se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para VMs do Windows. O padrão é Sim.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>O que há de uma avaliação da AVS (solução do Azure VMware)?

Aqui estão as novidades incluídas em uma avaliação de AVS:


| **Propriedade** | **Detalhes** |
| - | - |
| **Local de destino** | Especifica o local da nuvem privada AVS para o qual você deseja migrar.<br/><br/> Atualmente, a avaliação de AVS dá suporte a estas regiões de destino: leste dos EUA, Europa Ocidental, oeste dos EUA. |
| **Tipo de armazenamento** | Especifica o mecanismo de armazenamento a ser usado na AVS.<br/><br/> Observe que as avaliações de AVS só dão suporte ao vSAN como um tipo de armazenamento padrão. |
**Instâncias reservadas (RIs)** | Essa propriedade ajuda a especificar instâncias reservadas no AVS. Atualmente, não há suporte para RIs em nós de AVS. |
**Tipo de nó** | Especifica o [tipo de nó AVS](../azure-vmware/concepts-private-clouds-clusters.md) usado para mapear as VMs locais. Observe que o tipo de nó padrão é AV36. <br/><br/> As migrações para Azure recomendarão um número necessário de nós para que as VMs sejam migradas para a AVS. |
**Configuração de FTT, nível de RAID** | Especifica a falha aplicável a tolerar e combinações de RAID. A opção FTT selecionada combinada com o requisito de disco de VM local determinará o armazenamento total do vSAN necessário na AVS. |
**Critério de dimensionamento** | Define os critérios a serem usados para as VMs de _tamanho correto_ para a AVS. Você pode optar pelo dimensionamento _baseado em desempenho_ ou _como local_ sem considerar o histórico de desempenho. |
**Histórico de desempenho** | Define a duração a ser considerada na avaliação dos dados de desempenho dos computadores. Essa propriedade é aplicável somente quando os critérios de dimensionamento são _baseados em desempenho_. |
**Utilização de percentual** | Especifica o valor percentual do conjunto de amostras de desempenho a ser considerado para o dimensionamento correto. Essa propriedade é aplicável somente quando o dimensionamento é baseado em desempenho.|
**Fator de conforto** | As Migrações para Azure consideram um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. |
**Oferta** | Exibe a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) que você está inscrito no. As Migrações para Azure calculam o custo de acordo com isso.|
**Moeda** | Mostra a moeda de cobrança da sua conta. |
**Desconto (%)** | Lista qualquer desconto específico de assinatura que você recebe na parte superior da oferta do Azure. A configuração padrão é 0%. |
**Benefício Híbrido do Azure** | Especifica se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Embora isso não tenha nenhum impacto no preço das soluções VMware do Azure devido ao preço baseado no nó, os clientes ainda podem aplicar suas licenças de sistema operacional locais (baseadas na Microsoft) em AVS usando os benefícios híbridos do Azure. Outros fornecedores de sistema operacional de software precisarão fornecer seus próprios termos de licenciamento, como o RHEL, por exemplo. |
**vCPU excesso de assinatura** | Especifica a proporção do número de núcleos virtuais vinculados a 1 núcleo físico no nó AVS. O valor padrão nos cálculos é 4 vCPU: 1 núcleo físico em AVS. <br/><br/> Os usuários da API podem definir esse valor como um inteiro. Observe que a assinatura vCPU > 4:1 pode começar a causar degradação do desempenho, mas pode ser usada para cargas de trabalho do tipo de servidor Web. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Quais propriedades são usadas para criar e personalizar uma avaliação do Azure SQL?

Aqui estão as novidades incluídas nas propriedades de avaliação do SQL do Azure:

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | A região do Azure para a qual você deseja migrar. As recomendações de configuração e custo do SQL do Azure são baseadas na localização especificada.
**Tipo de implantação de destino** | O tipo de implantação de destino no qual você deseja executar a avaliação: <br/><br/> Selecione **recomendado**. se você quiser migrações para Azure para avaliar a prontidão dos SQL Servers para migrar para o Azure SQL mi e o BD SQL do Azure e recomendar a opção de implantação de destino mais adequada, camada de destino, configuração do SQL do Azure e estimativas mensais.<br/><br/>Selecione **banco de dados SQL do Azure**, se você quiser avaliar seus SQL Servers para migrar somente para bancos de dados SQL do Azure e examinar a camada de destino, a configuração do BD SQL do Azure e as estimativas mensais.<br/><br/>Selecione **SQL do Azure mi**, se você quiser avaliar seus SQL Servers para migrar somente para bancos de dados SQL do Azure e examinar a camada de destino, a configuração de Mi do Azure SQL e as estimativas mensais.
**Capacidade reservada** | Especifica a capacidade reservada para que as estimativas de custo na avaliação as levem em conta.<br/><br/> Se você selecionar uma opção de capacidade reservada, não poderá especificar “Desconto (%)”.
**Critérios de dimensionamento** | Essa propriedade é usada para dimensionar a configuração do SQL do Azure. <br/><br/> Ele é padronizado com **base no desempenho** , o que significa que a avaliação coletará as métricas de desempenho de instâncias de SQL Server e de bancos de dados para recomendar uma recomendação de configuração/camada de banco de dados SQL do Azure instância gerenciada SQL e/ou Azure.
**Histórico de desempenho** | Histórico de desempenho especifica a duração usada quando os dados de desempenho são avaliados.
**Utilização de percentual** | Utilização de percentil especifica o valor percentual do exemplo de desempenho usado para a permissão de direitos.
**Fator de conforto** | O buffer usado durante a avaliação. Ele conta com problemas como uso sazonal, histórico de desempenho curto e, provavelmente, aumenta o uso futuro.<br/><br/> Por exemplo, uma instância de 10 núcleos com 20% de utilização normalmente resulta em uma instância de dois núcleos. Com um fator de conforto de 2,0, o resultado é uma instância de quatro núcleos.
**Programa de oferta/licenciamento** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você está inscrito. No momento, só é possível escolher Pagamento Conforme o Uso e Desenvolvimento/Teste Pago Conforme o Uso. Observe que você pode ter um desconto adicional com a aplicação de capacidade reservada e Benefício Híbrido do Azure sobre a oferta paga conforme o uso.
**Camada de serviço** | A opção de camada de serviço mais apropriada para acomodar suas necessidades de negócios para a migração para o banco de dados SQL do Azure e/ou o Azure SQL Instância Gerenciada:<br/><br/>**Recomendado** se você quiser migrações para Azure para recomendar a camada de serviço mais adequada para seus servidores. Isso pode ser Uso Geral ou Comercialmente Crítico. <br/><br/> **Uso geral** Se você quiser uma configuração SQL do Azure projetada para cargas de trabalho orientadas a orçamento. [Saiba mais](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Comercialmente crítico** Se você quiser uma configuração SQL do Azure projetada para cargas de trabalho de baixa latência com alta resiliência a falhas e failovers rápidos. [Saiba mais](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Moeda** | A moeda de cobrança da sua conta.
**Desconto (%)** | Quaisquer descontos específicos de assinatura recebidos por cima da oferta do Azure. A configuração padrão é 0%.
**Benefício Híbrido do Azure** | Especifica se você já tem uma licença de SQL Server. <br/><br/> Caso você tenha e ela esteja coberta pelo Software Assurance ativo de Assinaturas do SQL Server, inscreva-se no Benefício Híbrido do Azure ao trazer as licenças para o Azure.

## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

Para editar as propriedades de avaliação depois de criar uma avaliação, faça o seguinte:

1. No projeto migrações para Azure, clique em **servidores**.
2. Em **migrações para Azure: descoberta e avaliação**, clique na contagem de avaliações.
3. Em **avaliação**, clique na avaliação relevante > **Editar propriedades**.
5. Personalize as propriedades de avaliação de acordo com as tabelas acima.
6. Clique em **Salvar** para atualizar a avaliação.


Você também pode editar as propriedades de avaliação quando estiver criando uma avaliação.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações de VM do Azure são calculadas.
- [Saiba mais](concepts-azure-sql-assessment-calculation.md) sobre como as avaliações do SQL do Azure são calculadas.
- [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md) sobre como as avaliações de AVS são calculadas.

