---
title: Cálculos de avaliação em Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação no serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234283"
---
# <a name="assessment-calculations"></a>Cálculos de avaliação

As migrações para [Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais, bem como instâncias de nuvem privada/pública, para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.

A avaliação do servidor é uma ferramenta em migrações para Azure que avalia servidores locais para migração para o Azure. Este artigo fornece informações sobre como as avaliações são calculadas.

## <a name="whats-in-an-assessment"></a>O que é uma avaliação?

**Property** | **Detalhes**
--- | ---
**Local de destino** | O local do Azure para o qual você deseja migrar.<br/> Atualmente, a avaliação do servidor dá suporte a estas regiões de destino: Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, Índia central, EUA Central, Leste da China, Norte da China, Ásia Oriental, leste dos EUA, leste dos dos EUA 2, Alemanha central, Alemanha nordeste, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Oeste do Reino Unido, US Gov Arizona, US Gov Texas, US Gov-Virgínia, West EUA Central, Europa Ocidental, oeste da Índia, oeste dos EUA e West dos EUA 2.
**Tipo de armazenamento** | Você pode usar essa propriedade para especificar o tipo de discos para o qual deseja mover, no Azure. <br/><br/> Para o dimensionamento local, você pode especificar o tipo de armazenamento de destino como discos gerenciados Premium, discos gerenciados SSD Standard ou discos gerenciados por HDD Standard. Para o dimensionamento baseado em desempenho, você pode especificar o tipo de disco de destino como discos automáticos, gerenciados Premium, discos gerenciados HDD Standard ou discos gerenciados por SSD Standard.<br/><br/> Quando você especifica o tipo de armazenamento como automático, a recomendação de disco é feita com base nos dados de desempenho dos discos (IOPS e taxa de transferência). Se você especificar o tipo de armazenamento como Premium/Standard, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se você quiser obter um SLA de VM de instância única de 99,9%, talvez queira especificar o tipo de armazenamento como discos gerenciados Premium. Isso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium. Observe que as Migrações para Azure são compatíveis apenas com discos gerenciados para avaliação de migração.
**Instâncias reservadas (RI)** | Essa propriedade ajuda a especificar se você tem [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure, as estimativas de custo na avaliação são então feitas em descontos de ri. As instâncias reservadas atualmente só têm suporte para a oferta de Pagamento Conforme o Uso em Migrações para Azure.
**Critério de dimensionamento** | O critério a ser usado para o tamanho correto das VMs para o Azure. Você pode fazer o dimensionamento *com base no desempenho* ou dimensionar as VMs *como locais*, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a ser considerada para avaliar os dados de desempenho dos computadores. Essa propriedade só é aplicável quando o critério de dimensionamento é *baseado em desempenho*.
**Utilização de percentual** | O valor percentual da amostra de desempenho definido para ser considerado para o redimensionamento. Essa propriedade só é aplicável quando o dimensionamento é *baseado em desempenho*.
**Série de VM** |     Você pode especificar a série de VM que deseja considerar para o dimensionamento correto. Por exemplo, se você tiver um ambiente de produção que não planeja migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série e o dimensionamento correto será feito apenas na série selecionada.
**Fator de conforto** | A avaliação de servidor de migrações para Azure considera um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você se inscreveu. As Migrações para Azure calculam o custo de acordo com isso.
**Moeda** | Moeda de cobrança.
**Desconto (%)** | Qualquer desconto específico da assinatura  recebido por você sobre a oferta do Azure.<br/> A configuração padrão é 0%.
**Tempo de atividade da VM** | Se as VMs não estiverem em execução 24x7 no Azure, será possível especificar a duração da execução (número de dias por mês e número de horas por dia) e as estimativas de custo serão feitas adequadamente.<br/> O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifique se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, os preços do Windows Azure são considerados para VMs do Windows. O padrão é Sim.

## <a name="how-are-assessments-calculated"></a>Como as avaliações são calculadas?

Uma avaliação na avaliação do servidor de migrações para Azure é calculada usando os metadados coletados sobre os servidores locais. O cálculo da avaliação é feito em três estágios; para cada servidor, o cálculo da avaliação começa com a análise de adequação do Azure, seguida pelo dimensionamento e por último, uma estimativa de custo mensal. Um servidor só se moverá para um estágio posterior se ele passar o anterior. Por exemplo, se um servidor falhar na verificação de adequação do Azure, ele será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão feitos para o mesmo.

## <a name="azure-suitability-analysis"></a>Análise de adequação do Azure

Nem todos os computadores são adequados para execução no Azure. A avaliação do servidor de migrações para Azure avalia cada computador local quanto à adequação de migração para o Azure e categoriza os computadores avaliados em uma das seguintes categorias de adequação:
- **Pronto para o Azure** - O computador pode ser migrado como está para o Azure sem alterações. Ele será inicializado no Azure com o suporte completo do Azure.
- **Condicionalmente pronto para o Azure** - O computador pode ser inicializado no Azure, mas pode não ter suporte completo do Azure. Por exemplo, um computador com uma versão mais antiga do sistema operacional Windows Server não tem suporte no Azure. Você precisa ter cuidado antes de migrar esses computadores para o Azure e seguir as orientações de correção sugeridas na avaliação para corrigir os problemas de preparação antes de migrar.
- **Não está pronto para o Azure** - O computador não será inicializado no Azure. Por exemplo, se um computador local tiver um disco de tamanho maior que 64 TB anexado, ele não poderá ser hospedado no Azure. Você precisa seguir as diretrizes de correção sugeridas na avaliação para corrigir o problema de preparação antes de migrar para o Azure. O dimensionamento correto e a estimativa de custo não são feitos para computadores marcados como não prontos para o Azure.
- **Preparação desconhecida** -as migrações para Azure não puderam encontrar a prontidão do computador devido a metadados insuficientes coletados do ambiente local.

A avaliação de servidor de migrações para Azure revisa as propriedades do computador e o sistema operacional convidado para identificar a prontidão do Azure do computador local.

### <a name="machine-properties"></a>Propriedades do computador

A avaliação do servidor revisa as seguintes propriedades da VM local para identificar se ela pode ser executada no Azure.

**Property** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
**Tempo de inicialização** | O Azure oferece suporte a VMs com tipo de inicialização BIOS, e não UEFI. | Condicionalmente pronto se o tipo de inicialização for UEFI.
**Núcleos** | O número de núcleos nos computadores deve ser igual ou menor que o número máximo de núcleos (128 núcleos) com suporte para uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão os núcleos utilizados para comparação. Se um fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as Migrações para Azure usarão os núcleos alocados, sem aplicar o fator de conforto. | Pronto, se for menor que ou igual aos limites.
**Memória** | O tamanho de memória do computador deve ser igual ou menor do que a memória máxima (3892 GB na série M do Azure Standard_M128m&nbsp;<sup>2</sup>) permitida para uma VM do Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão a memória utilizada para comparação. Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a memória alocada será usada, sem aplicar o fator de conforto.<br/><br/> | Se estiver pronto dentro dos limites.
**Disco de armazenamento** | O tamanho alocado de um disco deve ser 32 TB ou menos.<br/><br/> O número de discos anexados à máquina deve ser de 65 ou menos, incluindo o disco do sistema operacional. | Se estiver pronto dentro dos limites.
**Rede** | Um computador deve ter 32 NICs ou menos conectados a ele. | Se estiver pronto dentro dos limites.

### <a name="guest-operating-system"></a>Sistema operacional convidado
Juntamente com as propriedades da VM, a avaliação do servidor de migrações para Azure examina o sistema operacional convidado dos computadores, para identificar se ele pode ser executado no Azure.

> [!NOTE]
> Para VMs VMware, a avaliação de servidor migrações para Azure usa o sistema operacional especificado para a VM em vCenter Server para fazer a análise do SO convidado. Para VMs do Linux em execução no VMware, atualmente ele não identifica a versão exata do kernel do sistema operacional convidado.

A lógica a seguir é usada pela avaliação do servidor de migrações para Azure para identificar a prontidão do Azure com base no sistema operacional.

**Sistema operacional** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 R2 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2008 R2 com todos os SPs | O Azure fornece suporte total.| Pronto para o Azure
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2003, 2003 R2 | Esses sistemas operacionais passaram da data final de suporte e precisam de um [Contrato de Suporte Personalizado (CSA)](https://aka.ms/WSosstatement) para obter suporte no Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Esses sistemas operacionais passaram da data final de suporte, o computador pode ser inicializado no Azure, mas não há suporte do sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Cliente do Windows 7, 8 e 10 | O Azure oferece suporte apenas [com a Assinatura do Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente pronta para o Azure
Windows 10 Pro Desktop | O Azure oferece suporte com [Direitos de Hospedagem multilocatário.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente pronta para o Azure
Windows Vista, XP Professional | Esses sistemas operacionais passaram da data final de suporte, o computador pode ser inicializado no Azure, mas não há suporte do sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Linux | O Azure endossa esses [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md). Outros sistemas operacionais podem ser inicializados no Azure, mas é recomendado atualizar o sistema operacional para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão é aprovada.<br/><br/>Condicionalmente pronto se a versão não é aprovada.
Outros sistemas operacionais<br/><br/> Por exemplo, Oracle Solaris, Apple Mac OS, etc., FreeBSD, etc. | O Azure não endossa esses sistemas operacionais. O computador pode ser inicializado no Azure, mas não há suporte do sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, é recomendável atualizar o sistema operacional antes de migrar para o Azure.  
Sistema operacional especificado como **Outros** no vCenter Server | As Migrações para Azure não podem identificar o sistema operacional neste caso. | Preparação desconhecida. Certifique-se de que o sistema operacional em execução dentro da VM tem suporte no Azure.
Sistemas operacionais de 32 bits | O computador pode ser inicializado no Azure, mas o Azure pode não fornecer suporte completo. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional do computador de 32 bits para 64 bits antes de migrar para o Azure.

## <a name="sizing"></a>Dimensionamento

Depois que um computador é marcado como pronto para o Azure, a avaliação do servidor executa o dimensionamento, o que envolve identificar a VM do Azure apropriada e o SKU do disco para a VM local. As recomendações de tamanho variam dependendo das propriedades de avaliação especificadas.

- Se a avaliação usar o *dimensionamento baseado em desempenho*, as migrações para Azure considerarão o histórico de desempenho do computador para identificar o tamanho da VM e o tipo de disco no Azure. Esse método é especialmente útil se você tiver alocado a VM local com excesso, mas a utilização for baixa e você quiser dimensionar a VM no Azure para economizar custos. Esse método ajudará você a otimizar os tamanhos durante a migração.
- Se você não quiser considerar os dados de desempenho para o dimensionamento de VM e desejar pegar os computadores locais no estado em que se encontram no Azure, poderá especificar o critério de dimensionamento como *local* e a avaliação do servidor irá dimensionar as VMs com base no local configuração sem considerar os dados de utilização. O dimensionamento de disco, nesse caso, será feito com base no tipo de armazenamento que você especificar nas propriedades de avaliação (HDD Standard discos, SSD Standard discos ou discos Premium).

### <a name="performance-based-sizing"></a>Dimensionamento com base no desempenho

Para o dimensionamento baseado em desempenho, a avaliação de servidor de migrações para Azure começa com os discos anexados à VM, seguidos por adaptadores de rede e, em seguida, mapeia uma SKU de VM do Azure com base nos requisitos de computação da VM local. O dispositivo de migrações para Azure faz o perfil do ambiente local para coletar dados de desempenho para CPU, memória, discos e adaptador de rede.

**Coleta de dados de desempenho**

- Para VMs VMware, o dispositivo de migrações para Azure coleta um ponto de exemplo em tempo real em cada intervalo de 20 segundos, para VMs do Hyper-V, o ponto de amostra em tempo real é coletado a cada intervalo de 30 segundos.
- Em seguida, o dispositivo acumula os pontos de exemplo coletados para cada 10 minutos e envia o valor máximo dos últimos 10 minutos para a avaliação do servidor de migrações para Azure.
- A avaliação de servidor de migrações para Azure armazena todos os pontos de exemplo de 10 minutos para o último mês e, dependendo das propriedades de avaliação especificadas para o *histórico de desempenho* e a *utilização de percentil*, ele identifica o ponto de dados apropriado que deve ser usado para o dimensionamento correto. Por exemplo, se o histórico de desempenho for definido como um dia e a utilização do percentil for 95 º percentil, ele usará os pontos de exemplo de 10 minutos para o último dia, os classificará em ordem crescente e escolherá o valor 95 º percentil para o dimensionamento correto.
- O valor acima é então multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivo para cada métrica (utilização de CPU, utilização de memória, IOPS de disco (leitura e gravação), taxa de transferência de disco (leitura e gravação), taxa de transferência de rede (entrada e saída)) que o dispositivo coleta.
- Quando o valor de utilização efetivo for identificado, o dimensionamento de computação, armazenamento e rede será feito da seguinte maneira:

**Dimensionamento de armazenamento**: As Migrações para Azure tentam mapear todos os discos anexados à máquina para um disco no Azure.

> [!NOTE]
> Migrações para Azure: A avaliação do servidor dá suporte apenas a discos gerenciados para avaliação.

  - O IOPS de leitura e gravação de um disco é adicionado para obter o total de IOPS necessário, leitura semelhante e valores de taxa de transferência de gravação são adicionados para obter a taxa de transferência total de cada disco
  - Se você tiver especificado o tipo de armazenamento como automático, com base nos valores de taxa de transferência e IOPS efetivos, a avaliação de servidor de migrações para Azure identificará se o disco deve ser mapeado para um HDD padrão, SSD padrão ou um disco Premium no Azure. Se o tipo de armazenamento for definido como HDD Standard/SSD/Premium, ele tentará localizar um SKU de disco dentro do tipo de armazenamento selecionado (discos HDD Standard/SSD/Premium).
  - Se a avaliação do servidor não conseguir localizar um disco com o IOPS necessário & taxa de transferência, ele marcará o computador como inadequado para o Azure.
  - Se encontrar um conjunto de discos adequados, ele selecionará aqueles que dão suporte ao local especificado nas configurações de avaliação.
  - Se houver vários discos qualificados, elas selecionarão os que têm o menor custo.
  - Se os dados de desempenho de qualquer disco não estiver disponível, os dados de configuração do disco (tamanho do disco) serão usados para localizar um disco SSD padrão no Azure.

**Dimensionamento de rede**: A avaliação de servidor de migrações para Azure tenta encontrar uma VM do Azure que pode dar suporte ao número de adaptadores de rede anexados à máquina local e o desempenho exigido por esses adaptadores de rede.
    - Para obter o desempenho de rede efetivo da VM local, a avaliação do servidor agrega os dados transmitidos por segundo (MBps) do computador (saída de rede), em todos os adaptadores de rede, e aplica o fator de conforto. Esse número é usado para localizar uma VM do Azure que pode dar suporte ao desempenho de rede necessário.
    - Juntamente com o desempenho da rede, ele também considera se a VM do Azure pode dar suporte ao número necessário de adaptadores de rede.
    - Se nenhum dado de desempenho de rede estiver disponível, somente a contagem de adaptadores de rede será considerada para o dimensionamento da VM.

**Dimensionamento de computação**: Depois que os requisitos de armazenamento e rede são calculados, a avaliação de servidor de migrações para Azure considera os requisitos de CPU e memória para encontrar um tamanho de VM adequado no Azure.
    - As migrações para Azure analisam os núcleos e a memória utilizados em vigor para encontrar um tamanho de VM adequado no Azure.
    - Se nenhum tamanho adequado for encontrado, a máquina será marcada como inadequada para o Azure.
    - Se um tamanho adequado for encontrado, as Migrações para Azure aplicarão os cálculos de armazenamento e rede. Depois, aplicam as configurações de localização e preços, para a recomendação final de tamanho da VM.
    - Se houver vários discos qualificados, será recomendado aquele com o menor custo.

### <a name="as-on-premises-sizing"></a>Como dimensionamento local

Se você usar como *dimensionamento local*, a avaliação do servidor não considerará o histórico de desempenho das VMs e dos discos e alocará um SKU de VM no Azure com base no tamanho alocado no local. Da mesma forma, para o dimensionamento de disco, ele examina o tipo de armazenamento especificado nas propriedades de avaliação (HDD Standard/SSD/Premium) e recomenda o tipo de disco de acordo. O tipo de armazenamento padrão é os discos Premium.

## <a name="confidence-ratings"></a>Classificações de confiança
Cada avaliação baseada em desempenho nas migrações para Azure está associada a uma classificação de confiança que varia de um (menor) a cinco inícios (mais alto).
- A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure.
- A classificação de confiança não é aplicável como avaliações locais.
- Para o dimensionamento baseado em desempenho, as necessidades de avaliação do servidor de migrações para Azure:
    - Os dados de utilização da CPU e da memória da VM.
    - Além disso, para cada disco anexado à VM, é necessário ter os dados da taxa de transferência e a IOPS do disco.
    - Da mesma forma, para cada adaptador de rede anexado a uma VM, a classificação de confiança precisa da e/s de rede para fazer o dimensionamento com base no desempenho.
    - Se qualquer um dos números de utilização acima não estiver disponível no vCenter Server, a recomendação de tamanho poderá não ser confiável.

Dependendo da porcentagem de pontos de dados disponível, o nível de confiança para as avaliações é fornecido conforme abaixo:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

### <a name="low-confidence-ratings"></a>Classificações de baixa confiança

Alguns dos motivos pelos quais uma avaliação pode obter uma classificação de baixa confiança:

- Você não fez o profile do seu ambiente pela duração para a qual está criando a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, precisará aguardar pelo menos um dia depois de iniciar a descoberta para todos os pontos de dados a serem coletados.
- Algumas VMs foram desligadas durante o período para o qual a avaliação é calculada. Se alguma VM fosse desligada por alguma duração, a avaliação do servidor de migrações para Azure não pode coletar os dados de desempenho para esse período.
- Algumas VMs foram criadas entre o período para o qual a avaliação é calculada. Por exemplo, se você criar uma avaliação para o histórico de desempenho do último mês, mas algumas VMs tiverem sido criadas no ambiente apenas uma semana atrás, o histórico de desempenho das novas VMs não estará lá durante toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação estiver abaixo de cinco estrelas, recomendamos que você aguarde pelo menos um dia para o dispositivo criar o perfil do ambiente e recalcular a avaliação. Caso contrário, o dimensionamento baseado em desempenho pode não ser confiável e recomendamos que você alterne a avaliação para usar como o dimensionamento local.

## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Após a conclusão das recomendações de dimensionamento, as migrações para Azure calculam os custos de computação e armazenamento para após a migração.

- **Custo de Computação**: Usando o tamanho de VM do Azure recomendado, as Migrações para Azure usam a API de Cobrança para calcular o custo mensal para a VM.
    - O cálculo leva em conta sistema operacional, Software Assurance, instâncias reservadas, tempo de atividade da VM, localização e configurações de moeda.
    - Ele agrega o custo em todas as máquinas, para calcular o custo total de computação mensal.
- **Custo de armazenamento**: O custo de armazenamento mensal de um computador é calculado agregando-se o custo mensal de todos os discos anexados ao computador
    - A avaliação de servidor de migrações para Azure calcula o total de custos de armazenamento mensal agregando os custos de armazenamento de todas as máquinas.
    - Atualmente, o cálculo não leva em consideração as ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação.


## <a name="next-steps"></a>Próximas etapas

Crie uma avaliação para VMs [VMware](tutorial-assess-vmware.md) ou [VMs do Hyper-V](tutorial-assess-hyper-v.md).
