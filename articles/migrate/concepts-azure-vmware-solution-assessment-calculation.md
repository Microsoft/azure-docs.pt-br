---
title: Cálculos de avaliação de AVS nas migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação de AVS no serviço migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: a489902372b0b88a0ffec07fa685ee745ec59c50
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880450"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Visão geral da avaliação do servidor (migrar para a solução VMware do Azure)

As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais. Ele também controla suas instâncias de nuvem pública e privada para o Azure. O Hub oferece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.

A avaliação do servidor é uma ferramenta em migrações para Azure que avalia os servidores locais para migração para as máquinas virtuais IaaS do Azure e para a solução VMware do Azure (AVS). Este artigo fornece informações sobre como as avaliações da AVS (solução do Azure VMware) são calculadas.

> [!NOTE]
> A avaliação da solução de VMware Azure (AVS) está atualmente em visualização e pode ser criada somente para VMs VMware.

## <a name="types-of-assessments"></a>Tipos de avaliações

As avaliações criadas com a avaliação do servidor são um instantâneo de dados de ponto no tempo. Há dois tipos de avaliações que podem ser criadas usando as Migrações para Azure: Avaliação de Servidor.

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Você pode avaliar suas [VMs VMware](how-to-set-up-appliance-vmware.md)locais, VMS do [Hyper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para o Azure usando esse tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

A avaliação da solução de VMware Azure (AVS) na avaliação do servidor fornece duas opções de critérios de dimensionamento:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações baseadas em dados de desempenho coletados de VMs locais. | **Tamanho de nó recomendado**: com base nos dados de utilização de CPU e memória, juntamente com o tipo de nó, o tipo de armazenamento e a configuração de FTT que você seleciona para a avaliação.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de nó recomendado**: com base no tamanho da VM local, juntamente com o tipo de nó, o tipo de armazenamento e a configuração de FTT que você seleciona para a avaliação.

## <a name="how-do-i-run-an-assessment"></a>Como fazer executar uma avaliação?

Há duas maneiras de executar uma avaliação.

- Avaliar máquinas usando os metadados do servidor coletados por um dispositivo leve de migrações para Azure. O dispositivo descobre computadores locais. Em seguida, ele envia metadados de computador e dados de desempenho para migrações para Azure.
- Avaliar máquinas usando metadados de servidor que são importados em um formato CSV (valores separados por vírgula).

## <a name="how-do-i-assess-with-the-appliance"></a>Como fazer avaliar com o dispositivo?

Se você estiver implantando um dispositivo de migrações para Azure para descobrir servidores locais, execute as seguintes etapas:

1. Configure o Azure e seu ambiente local para trabalhar com a avaliação do servidor.
2. Para sua primeira avaliação, crie um projeto do Azure e adicione a ferramenta de avaliação do servidor a ele.
3. Implante um dispositivo leve de migrações para Azure. O dispositivo descobre continuamente computadores locais e envia metadados de computador e dados de desempenho para migrações para Azure. Implante o dispositivo como uma VM. Você não precisa instalar nada em computadores que deseja avaliar.

Depois que o dispositivo iniciar a descoberta de máquina, você poderá reunir as máquinas que deseja avaliar em um grupo e executar uma avaliação para o grupo com o tipo de avaliação **solução do Azure VMware (AVS)**.

Crie sua primeira avaliação da AVS (solução do Azure VMware) seguindo as etapas [aqui](how-to-create-azure-vmware-solution-assessment.md).

## <a name="how-do-i-assess-with-imported-data"></a>Como fazer avaliar com os dados importados?

Se você estiver avaliando servidores usando um arquivo CSV, não precisará de um dispositivo. Em vez disso, execute as seguintes etapas:

1. Configure o Azure para trabalhar com a avaliação do servidor.
2. Para sua primeira avaliação, crie um projeto do Azure e adicione a ferramenta de avaliação do servidor a ele.
3. Baixe um modelo CSV e adicione dados do servidor a ele.
4. Importe o modelo para a avaliação do servidor.
5. Descubra os servidores adicionados com a importação, reúna-os em um grupo e execute uma avaliação para o grupo com o tipo de avaliação **Azure VMware Solution (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Quais dados o dispositivo coleta?

Se você estiver usando o dispositivo de migração do Azure para avaliação, saiba mais sobre os metadados e dados de desempenho coletados para o [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como o dispositivo calcula os dados de desempenho?

Se você usar o dispositivo para descoberta, ele coletará dados de desempenho para configurações de computação com estas etapas:

1. O dispositivo coleta um ponto de exemplo em tempo real.

    - **VMs VMware**: um ponto de amostra é coletado A cada 20 segundos.

2. O dispositivo combina os pontos de exemplo para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados, o dispositivo seleciona os valores de pico de todos os exemplos. Em seguida, ele envia o ponto de dados para o Azure.
3. A avaliação do servidor armazena todos os pontos de dados de 10 minutos para o último mês.
4. Quando você cria uma avaliação, a avaliação do servidor identifica o ponto de dados apropriado a ser usado para a identificação de direitos. A identificação é baseada nos valores de percentil para o *histórico de desempenho* e *utilização de percentil*.

    - Por exemplo, se o histórico de desempenho for de uma semana e a utilização do percentil for o 95 º percentil, a avaliação do servidor classificará os pontos de exemplo de 10 minutos da última semana. Ele os classifica em ordem crescente e escolhe o valor de percentil de 95 º para direitos.
    - O valor do 95 º percentil garante que você ignore as exceções, que poderão ser incluídas se você tiver escolhido o 99 º percentil.
    - Se você quiser escolher o pico de uso do período e não quiser perder as exceções, selecione o 99 º percentil para utilização de percentil.

5. Esse valor é multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivos para essas métricas que o dispositivo coleta:

    - Utilização da CPU
    - Utilização de RAM
    - IOPS de disco (leitura e gravação)
    - Taxa de transferência do disco (leitura e gravação)
    - Taxa de transferência de rede (entrada e saída)

Os dados de desempenho a seguir são coletados, mas não usados em recomendações de dimensionamento para avaliações de AVS:
  - Os dados de taxa de transferência e IOPS de disco para cada disco anexado à VM.
  - A e/s de rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede anexado a uma VM.

## <a name="how-are-avs-assessments-calculated"></a>Como as avaliações de AVS são calculadas?

A avaliação do servidor usa os metadados e os dados de desempenho dos computadores locais para calcular as avaliações. Se você implantar o dispositivo de migrações para Azure, a avaliação usará os dados coletados pelo dispositivo. Mas se você executar uma avaliação importada usando um arquivo CSV, forneça os metadados para o cálculo.

Os cálculos ocorrem nestes três estágios:

1. **Calcule a prontidão da AVS (solução do Azure VMware)**: se as VMs locais são adequadas para a migração para a solução VMware do Azure (AVS).
2. **Calcular o número de nós de AVS e utilização entre nós**: número estimado de nós de AVS necessários para executar as VMs e utilização de CPU, memória e armazenamento projetada em todos os nós.
3. **Estimativa de custo mensal**: os custos mensais estimados para todos os nós da AVS (solução do Azure VMware) que executam as VMs locais.

Os cálculos estão na ordem anterior. Um servidor de computador será movido para um estágio posterior somente se ele passar o anterior. Por exemplo, se um servidor falhar no estágio de preparação de AVS, ele será marcado como inadequado para o Azure. Os cálculos de dimensionamento e custo não são feitos para esse servidor

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>O que há de uma avaliação da AVS (solução do Azure VMware)?

Aqui estão as novidades incluídas em uma avaliação de AVS na avaliação do servidor:


| **Propriedade** | **Detalhes** 
| - | - 
| **Local de destino** | Especifica o local da nuvem privada AVS para o qual você deseja migrar.<br/><br/> A avaliação da AVS na avaliação do servidor atualmente dá suporte a estas regiões de destino: leste dos EUA, Europa Ocidental, oeste dos EUA. 
| **Tipo de armazenamento** | Especifica o mecanismo de armazenamento a ser usado na AVS.<br/><br/> As avaliações da AVS dão suporte apenas a vSAN como um tipo de armazenamento padrão. 
**Instâncias reservadas (RIs)** | Essa propriedade ajuda a especificar instâncias reservadas no AVS. Atualmente, não há suporte para RIs em nós de AVS. 
**Tipo de nó** | Especifica o [tipo de nó AVS](../azure-vmware/concepts-private-clouds-clusters.md) usado para mapear as VMs locais. O tipo de nó padrão é AV36. <br/><br/> As migrações para Azure recomendarão um número necessário de nós para que as VMs sejam migradas para a AVS. 
**Configuração de FTT, nível de RAID** | Especifica a falha aplicável a tolerar e combinações de RAID. A opção FTT selecionada combinada com o requisito de disco de VM local determinará o armazenamento total do vSAN necessário na AVS. 
**Critério de dimensionamento** | Define os critérios a serem usados para as VMs de *tamanho correto* para a AVS. Você pode optar pelo dimensionamento *baseado em desempenho* ou *como local* sem considerar o histórico de desempenho. 
**Histórico de desempenho** | Define a duração a ser considerada na avaliação dos dados de desempenho dos computadores. Essa propriedade é aplicável somente quando os critérios de dimensionamento são *baseados em desempenho*. 
**Utilização de percentual** | Especifica o valor percentual do conjunto de amostras de desempenho a ser considerado para o dimensionamento correto. Essa propriedade é aplicável somente quando o dimensionamento é baseado em desempenho.
**Fator de conforto** | A avaliação de servidor de migrações para Azure considera um buffer (fator de conforto) durante a avaliação. Esse buffer é aplicado sobre os dados de utilização da máquina para VMs (CPU, memória, disco e rede). O fator de conforto considera problemas como uso sazonal, histórico curto de desempenho e aumento provável do uso futuro.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização normalmente resulta em uma VM de dois núcleos. No entanto, com um fator de conforto de 2.0x, o resultado é uma VM de quatro núcleos. 
**Oferta** | Exibe a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) que você está inscrito no. As Migrações para Azure calculam o custo de acordo com isso.
**Moeda** | Mostra a moeda de cobrança da sua conta. 
**Desconto (%)** | Lista qualquer desconto específico de assinatura que você recebe na parte superior da oferta do Azure. A configuração padrão é 0%. 
**Benefício Híbrido do Azure** | Especifica se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Embora não tenha impacto sobre o preço das soluções VMware do Azure devido ao preço baseado em nó, os clientes ainda podem aplicar as licenças do sistema operacional local (baseado na Microsoft) em AVS usando os benefícios híbridos do Azure. Outros fornecedores de sistema operacional de software precisarão fornecer seus próprios termos de licenciamento, como o RHEL, por exemplo. 
**vCPU excesso de assinatura** | Especifica a proporção do número de núcleos virtuais vinculados a um núcleo físico no nó AVS. O valor padrão nos cálculos é 4 vCPU: 1 núcleo físico em AVS. <br/><br/> Os usuários da API podem definir esse valor como um inteiro. Observe que o vCPU Oversubscription > 4:1 pode afetar as cargas de trabalho dependendo do uso da CPU. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Análise de adequação da solução VMware do Azure (AVS)

As avaliações da AVS na avaliação do servidor avaliam cada VM local para sua adequação à AVS examinando as propriedades do computador. Ele também atribui cada computador avaliado a uma das seguintes categorias de adequação:

- **Pronto para a sincronização automática**: o computador pode ser migrado no estado em que se encontra para o Azure (AVS) sem nenhuma alteração. Ele será iniciado no AVS com suporte completo a AVS.
- **Pronto com condições**: a VM pode ter problemas de compatibilidade com a versão atual do vSphere, bem como exigir ferramentas do VMware e outras configurações, antes que a funcionalidade completa da VM possa ser obtida na AVS.
- **Não está pronto para o AVS**: a VM não será iniciada na AVS. Por exemplo, se a VM do VMware local tiver um dispositivo externo anexado, como um CD-ROM, a operação de VMware VMotion falhará (se estiver usando VMware VMotion).
- **Preparação desconhecida**: as migrações para Azure não puderam determinar a prontidão do computador devido a metadados insuficientes coletados do ambiente local.

A avaliação do servidor revisa as propriedades da máquina para determinar a prontidão do Azure do computador local.

### <a name="machine-properties"></a>Propriedades do computador

A avaliação do servidor revisa a seguinte propriedade da VM local para determinar se ela pode ser executada na solução de VMware do Azure (AVS).


| **Propriedade** | **Detalhes** | **Status de preparação de AVS** 
| - | - | - 
| **Protocolo de Internet** | Atualmente, a AVS não oferece suporte a endereçamento de Internet IPv6.<br/><br/> Entre em contato com sua equipe local de GBB do AVS da MSFT para obter orientação sobre as diretrizes de correção se seu computador for detectado com o IPv6.| Protocolo de Internet condicionalmente pronto


### <a name="guest-operating-system"></a>Sistema operacional convidado

Atualmente, as avaliações da AVS não revisam o sistema operacional como parte da análise de adequação. Todos os sistemas operacionais em execução em VMs locais provavelmente serão executados na AVS (solução VMware do Azure).

Juntamente com as propriedades da VM, a avaliação do servidor examina o sistema operacional convidado das máquinas para determinar se ele pode ser executado no Azure.


## <a name="sizing"></a>Dimensionamento

Depois que um computador é marcado como pronto para a sincronização automática, a avaliação AVS na avaliação do servidor faz recomendações de dimensionamento de nó, o que envolve a identificação dos requisitos de VM locais apropriados e a localização do número total de nós de AVS necessários. Essas recomendações variam, dependendo das propriedades de avaliação especificadas.

- Se a avaliação usar o *dimensionamento baseado em desempenho*, as migrações para Azure considerarão o histórico de desempenho do computador para fazer a recomendação de dimensionamento apropriada para a AVS. Esse método é especialmente útil se você tiver alocado a VM local com excesso, mas a utilização estiver baixa e você quiser dimensionar a VM na AVS para economizar custos. Esse método ajudará você a otimizar os tamanhos durante a migração.
- Se você não quiser considerar os dados de desempenho para o dimensionamento da VM e desejar colocar os computadores locais como estão para a AVS, poderá definir os critérios de dimensionamento *como locais*. Em seguida, a avaliação do servidor irá dimensionar as VMs com base na configuração local sem considerar os dados de utilização. 


### <a name="ftt-sizing-parameters"></a>Parâmetros de dimensionamento de FTT

O mecanismo de armazenamento usado na AVS é a vSAN. as políticas de armazenamento vSAN definem os requisitos de armazenamento para suas máquinas virtuais. Essas políticas garantem o nível de serviço necessário para suas VMs, pois determinam como o armazenamento é alocado para a VM. As combinações de FTT-Raid disponíveis são: 

**Falhas a tolerar (FTT)** | **Configuração RAID** | **Hosts mínimos necessários** | **Consideração de dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (espelhamento) | 3 | Uma VM de 100 GB consumiria 200 GB.
1 | RAID-5 (codificação de eliminação) | 4 | Uma VM de 100 GB consumiria 133,33 GB
2 | RAID-1 (espelhamento) | 5 | Uma VM de 100 GB consumiria 300 GB.
2 | RAID-6 (codificação de eliminação) | 6 | Uma VM de 100 GB consumiria 150 GB.
3 | RAID-1 (espelhamento) | 7 | Uma VM de 100 GB consumiria 400 GB.

### <a name="performance-based-sizing"></a>Dimensionamento com base no desempenho

Para o dimensionamento baseado em desempenho, a avaliação do servidor começa com os discos anexados à VM, seguidos por adaptadores de rede. Em seguida, ele mapeia os requisitos de VM para um número apropriado de nós para a AVS. O dispositivo de migrações para Azure faz o perfil do ambiente local para coletar dados de desempenho para CPU, memória, discos e adaptador de rede.

**Etapas da coleta de dados de desempenho:**

1. Para VMs VMware, o dispositivo migrações para Azure coleta um ponto de exemplo em tempo real em cada intervalo de 20 segundos. 
2. O dispositivo acumula os pontos de exemplo coletados a cada 10 minutos e envia o valor máximo para a avaliação dos últimos 10 minutos para o servidor.
3. A avaliação do servidor armazena todos os pontos de exemplo de 10 minutos para o último mês. Em seguida, dependendo das propriedades de avaliação especificadas para o *histórico de desempenho* e a utilização de *percentil*, ele identifica o ponto de dados apropriado a ser usado para o dimensionamento correto. Por exemplo, se o histórico de desempenho for definido como 1 dia e a utilização do percentil for o 95 º percentil, a avaliação do servidor usará os pontos de exemplo de 10 minutos para o último dia, os classificará em ordem crescente e escolherá o valor do 95 º percentil para o dimensionamento correto.
4. Esse valor é multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivos para cada métrica (utilização de CPU, utilização de memória, IOPS de disco (leitura e gravação), taxa de transferência de disco (leitura e gravação) e taxa de transferência de rede (dentro e fora) que o dispositivo coleta.

Depois que o valor de utilização efetivo é determinado, o armazenamento, a rede e o dimensionamento de computação são tratados da seguinte maneira.

**Dimensionamento de armazenamento**: as migrações para Azure usam o espaço total em disco da VM local como um parâmetro de cálculo para determinar os requisitos de armazenamento da AVS vSAN, além da configuração FTT selecionada pelo cliente. FTT-falhas para tolerar e exigir um mínimo de nós por opção de FTT determinarão o armazenamento de vSAN total necessário combinado com o requisito de disco de VM.

**Dimensionamento de rede**: Atualmente, a avaliação do servidor não leva em consideração as configurações de rede para avaliações da AVS.

**Dimensionamento de computação**: depois de calcular os requisitos de armazenamento, a avaliação do servidor considera os requisitos de CPU e memória para determinar o número de nós necessários para a sincronização automática com base no tipo de nó.

- Com base nos critérios de dimensionamento, a avaliação do servidor examina os dados da VM baseada em desempenho ou a configuração da VM local. A configuração fator de conforto permite especificar o fator de crescimento do cluster. Atualmente, o hyperthreading está habilitado por padrão e, portanto, um nó de 36 núcleos terá 72 vCores. Considera-se 4 vCores por núcleo físico para determinar os limites da CPU por cluster, de acordo com o padrão VMware de não ultrapassar 80% da utilização, para possibilitar a manutenção ou o tratamento de falhas sem comprometer a disponibilidade do cluster. Atualmente, não há substituição disponível para alterar os valores de excesso de assinatura e podemos ter isso em versões futuras.

### <a name="as-on-premises-sizing"></a>Como dimensionamento local

Se você usar *como dimensionamento local*, a avaliação do servidor não considerará o histórico de desempenho das VMs e dos discos. Em vez disso, ele aloca nós de AVS com base no tamanho alocado no local. O tipo de armazenamento padrão é vSAN na AVS.

[Saiba mais](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) sobre como examinar uma avaliação de solução do Azure VMware.

## <a name="confidence-ratings"></a>Classificações de confiança

Cada avaliação baseada em desempenho nas migrações para Azure está associada a uma classificação de confiança que varia de uma (mais baixa) a cinco estrelas (mais alta).

- A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure.
- As classificações de confiança não são aplicáveis para *as avaliações locais* .
- Para o dimensionamento baseado em desempenho, as avaliações de AVS na avaliação do servidor precisam dos dados de utilização da CPU e da memória da VM. Os dados a seguir são coletados, mas não são usados em recomendações de dimensionamento para a sincronização automática:
  - Os dados de taxa de transferência e IOPS de disco para cada disco anexado à VM.
  - A e/s de rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede anexado a uma VM.

  Se qualquer um desses números de utilização estiver indisponível em vCenter Server, a recomendação de tamanho poderá não ser confiável.

Dependendo da porcentagem de pontos de dados disponíveis, a classificação de confiança para a avaliação é a seguinte.


| **Disponibilidade dos pontos de dados** | **Classificação de confiança** |
| - | - |
| 0-20% | 1 estrela |
| 21-40% | 2 estrelas |
| 41-60% | 3 estrelas |
| 61-80% | 4 estrelas |
| 81-100% | 5 estrelas |

### <a name="low-confidence-ratings"></a>Classificações de baixa confiança

Aqui estão algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:

- Você não fez o profile do seu ambiente pela duração para a qual está criando a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, deverá aguardar pelo menos um dia depois de iniciar a descoberta para todos os pontos de dados a serem coletados.
- A avaliação não é capaz de coletar os dados de desempenho de algumas ou de todas as VMs no período de avaliação. Para uma classificação de alta confiança, verifique se: 
    - As VMs são ativadas durante a avaliação
    - Conexões de saída nas portas 443 são permitidas
    - Para VMs do Hyper-V, a memória dinâmica está habilitada 
    
    “Recalcule” a avaliação para refletir as alterações mais recentes na classificação de confiança.

- Algumas VMs foram criadas durante o tempo durante o qual a avaliação foi calculada. Por exemplo, suponha que você criou uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas há apenas uma semana atrás. Nesse caso, os dados de desempenho das novas VMs não estariam disponíveis durante todo o período e a classificação de confiança seria baixa.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for menor que cinco estrelas, recomendamos que você aguarde pelo menos um dia para o dispositivo criar o perfil do ambiente e recalcular a avaliação. Caso contrário, o dimensionamento baseado em desempenho pode não ser confiável. Nesse caso, recomendamos que você alterne a avaliação para o dimensionamento local.

## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Depois que as recomendações de dimensionamento forem concluídas, as migrações para Azure calcularão o custo total da execução das cargas de trabalho locais na AVS multiplicando o número de nós de AVS exigidos pelo preço do nó. O custo por VM é calculado com a divisão do custo total pelo número de VMs na avaliação. 
- O cálculo leva o número de nós necessários, o tipo de nó e o local em conta.
- Ele agrega o custo em todos os nós para calcular o custo mensal total.
- Os custos são exibidos na moeda especificada nas configurações de avaliação.

Como o preço da solução do Azure VMware (AVS) é por nó, o custo total não tem custo de computação e distribuição de custo de armazenamento. [Saiba mais](../azure-vmware/introduction.md)

Observe que, como a solução do Azure VMware (AVS) está em versão prévia, os preços do nó na avaliação são os preços da versão prévia. Entre em contato com sua equipe local do MSFT AVS GBB para obter diretrizes.

## <a name="migration-tool-guidance"></a>Diretrizes da ferramenta de migração

No relatório de Preparação para o Azure para a avaliação da Solução VMware no Azure (AVS), você pode ver as seguintes sugestões de ferramentas: 
- **VMware HCX ou Enterprise**: para máquinas VMware, a solução de HCX (extensão de nuvem híbrida) do VMware é a ferramenta de migração sugerida para migrar sua carga de trabalho local para sua nuvem privada da AVS (solução VMware) do Azure. [Saiba mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Desconhecido**: Para computadores importados por meio de um arquivo CSV, a ferramenta de migração padrão é desconhecida. No entanto, para máquinas VMware, é recomendável usar a solução de HCX (extensão de nuvem híbrida) do VMware.

## <a name="next-steps"></a>Próximas etapas

Crie uma avaliação para [VMs VMware de AVS](how-to-create-azure-vmware-solution-assessment.md).