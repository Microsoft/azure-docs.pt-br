---
title: Avaliações de VM do Azure em migrações para Azure
description: Saiba mais sobre as avaliações nas migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 16c3b59bcfa14cc02f13dadd726e0380d934598b
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023802"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Visão geral da avaliação (migrar para VMs do Azure)

Este artigo fornece uma visão geral das avaliações na ferramenta [migrações para Azure: descoberta e avaliação](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . A ferramenta pode avaliar servidores locais no ambiente VMware virtual e Hyper-V e servidores físicos para migração para o Azure.

## <a name="whats-an-assessment"></a>O que é uma avaliação?

Uma avaliação com a ferramenta de descoberta e avaliação mede a prontidão e estima o efeito de migrar servidores locais para o Azure.

> [!NOTE]
> No Azure governamental, examine os locais de avaliação de [destino com suporte](migrate-support-matrix.md#supported-geographies-azure-government) . Observe que as recomendações de tamanho de VM em avaliações usarão a série de VMs especificamente para regiões de nuvem do governo. [Saiba mais](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) sobre os tipos de VM.

## <a name="types-of-assessments"></a>Tipos de avaliações

Há três tipos de avaliações que você pode criar usando as migrações para Azure: descoberta e avaliação.

***Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. Você pode avaliar seus servidores locais no ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs do Azure usando esse tipo de avaliação.
**SQL do Azure** | Avaliações para migrar seus SQL Servers locais de seu ambiente VMware para o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada.
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

As avaliações criadas com as migrações para Azure são um instantâneo de dados de ponto no tempo. Uma avaliação de VM do Azure fornece duas opções de critérios de dimensionamento:

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | A recomendação de tamanho da VM é baseada em dados de utilização de CPU e RAM.<br/><br/> A recomendação de tipo de disco se baseia nas operações de entrada/saída por segundo (IOPS) e na taxa de transferência dos discos locais. Os tipos de disco são Azure HDD Standard, Azure SSD Standard e discos Premium do Azure.
**No estado em que se encontra localmente** | Avaliações que não usam dados de desempenho para fazer recomendações | A recomendação de tamanho da VM é baseada no tamanho do servidor local.<br/><br> O tipo de disco recomendado é baseado no tipo de armazenamento selecionado para a avaliação.

## <a name="how-do-i-run-an-assessment"></a>Como fazer executar uma avaliação?

Há duas maneiras de executar uma avaliação.

- Avalie servidores usando os metadados de servidor coletados por um dispositivo leve de migrações para Azure. O dispositivo descobre servidores locais. Em seguida, ele envia metadados de servidor e dados de desempenho para migrações para Azure.
- Avalie servidores usando metadados de servidor que são importados em um formato CSV (valores separados por vírgula).

## <a name="how-do-i-assess-with-the-appliance"></a>Como fazer avaliar com o dispositivo?

Se você estiver implantando um dispositivo de migrações para Azure para descobrir servidores locais, execute as seguintes etapas:

1. Configure o Azure e seu ambiente local para trabalhar com as migrações para Azure.
1. Para sua primeira avaliação, crie um projeto do Azure e adicione a ferramenta de descoberta e avaliação a ele.
1. Implante um dispositivo leve de migrações para Azure. O dispositivo descobre continuamente servidores locais e envia metadados de servidor e dados de desempenho para migrações para Azure. Implante o dispositivo como uma VM ou um servidor físico. Você não precisa instalar nada em servidores que deseja avaliar.

Depois que o dispositivo iniciar a descoberta do servidor, você poderá coletar servidores que deseja avaliar em um grupo e executar uma avaliação para o grupo com o tipo de avaliação **VM do Azure**.

Siga nossos tutoriais para [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)ou [servidores físicos](./tutorial-discover-physical.md) para experimentar essas etapas.

## <a name="how-do-i-assess-with-imported-data"></a>Como fazer avaliar com os dados importados?

Se você estiver avaliando servidores usando um arquivo CSV, não precisará de um dispositivo. Em vez disso, execute as seguintes etapas:

1. Configurar o Azure para trabalhar com as migrações para Azure
1. Para sua primeira avaliação, crie um projeto do Azure e adicione a ferramenta de descoberta e avaliação a ele.
1. Baixe um modelo CSV e adicione dados do servidor a ele.
1. Importar o modelo para as migrações para Azure
1. Descubra os servidores adicionados com a importação, reúna-os em um grupo e execute uma avaliação para o grupo com o tipo de avaliação **VM do Azure**.

## <a name="what-data-does-the-appliance-collect"></a>Quais dados o dispositivo coleta?

Se você estiver usando o dispositivo de migração do Azure para avaliação, saiba mais sobre os metadados e dados de desempenho coletados para o [VMware](migrate-appliance.md#collected-data---vmware) e o [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como o dispositivo calcula os dados de desempenho?

Se você usar o dispositivo para descoberta, ele coletará dados de desempenho para configurações de computação com estas etapas:

1. O dispositivo coleta um ponto de exemplo em tempo real.

    - **VMs VMware**: um ponto de amostra é coletado A cada 20 segundos.
    - **VMs do Hyper-V**: um ponto de amostra é coletado A cada 30 segundos.
    - **Servidores físicos**: um ponto de amostra é coletado A cada cinco minutos.

1. O dispositivo combina os pontos de exemplo para criar um único ponto de dados a cada 10 minutos para servidores VMware e Hyper-V e a cada 5 minutos para servidores físicos. Para criar o ponto de dados, o dispositivo seleciona os valores de pico de todos os exemplos. Em seguida, ele envia o ponto de dados para o Azure.
1. A avaliação armazena todos os pontos de dados de 10 minutos para o último mês.
1. Quando você cria uma avaliação, a avaliação identifica o ponto de dados apropriado a ser usado para a identificação de direitos. A identificação é baseada nos valores de percentil para o *histórico de desempenho* e *utilização de percentil*.

    - Por exemplo, se o histórico de desempenho for de uma semana e a utilização do percentil for o 95 º percentil, a avaliação classificará os pontos de exemplo de 10 minutos da última semana. Ele os classifica em ordem crescente e escolhe o valor de percentil de 95 º para direitos.
    - O valor do 95 º percentil garante que você ignore as exceções, que poderão ser incluídas se você tiver escolhido o 99 º percentil.
    - Se você quiser escolher o pico de uso do período e não quiser perder as exceções, selecione o 99 º percentil para utilização de percentil.

1. Esse valor é multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivos para essas métricas que o dispositivo coleta:

    - Utilização da CPU
    - Utilização de RAM
    - IOPS de disco (leitura e gravação)
    - Taxa de transferência do disco (leitura e gravação)
    - Taxa de transferência de rede (entrada e saída)

## <a name="how-are-azure-vm-assessments-calculated"></a>Como as avaliações de VM do Azure são calculadas?

A avaliação usa os metadados e os dados de desempenho dos servidores locais para calcular as avaliações. Se você implantar o dispositivo de migrações para Azure, a avaliação usará os dados coletados pelo dispositivo. Mas se você executar uma avaliação importada usando um arquivo CSV, forneça os metadados para o cálculo.

Os cálculos ocorrem nestes três estágios:

1. **Calcular a preparação do Azure**: avalie se os servidores são adequados para a migração para o Azure.
1. **Calcular recomendações de dimensionamento**: estimar a computação, o armazenamento e o dimensionamento de rede.
1. **Calcular os custos mensais**: Calcule a computação mensal estimada e os custos de armazenamento para executar os servidores no Azure após a migração.

Os cálculos estão na ordem anterior. Um servidor de servidor será movido para um estágio posterior somente se ele passar o anterior. Por exemplo, se um servidor falhar no estágio de preparação do Azure, ele será marcado como inadequado para o Azure. Os cálculos de dimensionamento e custo não são feitos para esse servidor.

## <a name="whats-in-an-azure-vm-assessment"></a>O que há em uma avaliação de VM do Azure?

Aqui estão as novidades incluídas em uma avaliação de VM do Azure:

**Propriedade** | **Detalhes**
--- | ---
**Local de destino** | O local para o qual você deseja migrar. Atualmente, a avaliação dá suporte a essas regiões do Azure de destino:<br/><br/> Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, Índia central, EUA Central, Leste da China, Norte da China, Ásia Oriental, leste dos EUA, leste dos EUA 2, Alemanha central, Alemanha Northeast, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, EUA Central Arizona, Europa setentrional Texas, EUA Central , Oeste EUA Central, Europa Ocidental, oeste da Índia, oeste dos EUA e oeste dos EUA 2.
**Disco de armazenamento de destino (como está sendo dimensionado)** | O tipo de disco a ser usado para armazenamento no Azure. <br/><br/> Especifique o disco de armazenamento de destino como gerenciado Premium, SSD Standard gerenciado ou gerenciado por HDD Standard.
**Disco de armazenamento de destino (dimensionamento baseado em desempenho)** | Especifica o tipo de disco de armazenamento de destino como automático, gerenciado Premium, HDD Standard gerenciado ou gerenciado por SSD Standard.<br/><br/> **Automático**: a recomendação de disco baseia-se nos dados de desempenho dos discos, o que significa IOPS e taxa de transferência.<br/><br/>**Premium ou Standard**: a avaliação recomenda um SKU de disco dentro do tipo de armazenamento selecionado.<br/><br/> Se você quiser um SLA (contrato de nível de serviço) de VM de instância única de 99,9%, considere o uso de discos gerenciados Premium. Esse uso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium.<br/><br/> As migrações para Azure dão suporte apenas a Managed disks para avaliação de migração.
**Instâncias de VM reservadas do Azure** | Especifica as [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) para que as estimativas de custo na avaliação as levem em conta.<br/><br/> Quando você seleciona ' instâncias reservadas ', o ' desconto (%) ' e as propriedades ' VM de tempo de atividade ' não são aplicáveis.<br/><br/> Atualmente, as migrações para Azure dão suporte a instâncias de VM reservadas do Azure somente para ofertas pagas conforme o uso.
**Critérios de dimensionamento** | Usado para asrightr a VM do Azure.<br/><br/> Use o tamanho com base no tamanho ou no desempenho.
**Histórico de desempenho** | Usado com o dimensionamento baseado em desempenho. Histórico de desempenho especifica a duração usada quando os dados de desempenho são avaliados.
**Utilização de percentual** | Usado com o dimensionamento baseado em desempenho. Utilização de percentil especifica o valor percentual do exemplo de desempenho usado para a permissão de direitos.
**Série de VMs** | A série de VMs do Azure que você deseja considerar para a permissão de direitos. Por exemplo, se você não tiver um ambiente de produção que exija VMs da série A no Azure, poderá excluir a série A da lista de séries.
**Fator de conforto** | O buffer usado durante a avaliação. Ele é aplicado aos dados de CPU, RAM, disco e rede para VMs. Ele conta com problemas como uso sazonal, histórico de desempenho curto e, provavelmente, aumenta o uso futuro.<br/><br/> Por exemplo, uma VM de 10 núcleos com 20% de utilização normalmente resulta em uma VM de dois núcleos. Com um fator de conforto de 2,0, o resultado é uma VM de quatro núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você está inscrito. A avaliação estima o custo para essa oferta.
**Moeda** | A moeda de cobrança da sua conta.
**Desconto (%)** | Quaisquer descontos específicos de assinatura recebidos por cima da oferta do Azure. A configuração padrão é 0%.
**Tempo de atividade da VM** | A duração em dias por mês e horas por dia para VMs do Azure que não serão executadas continuamente. As estimativas de custo são baseadas nessa duração.<br/><br/> Os valores padrão são 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se a configuração tiver o valor padrão "Sim", os preços do Azure para sistemas operacionais diferentes do Windows serão considerados para VMs do Windows.
**Assinatura de EA** | Especifica que uma assinatura Contrato Enterprise (EA) é usada para estimativa de custo. Leva em conta o desconto aplicável à assinatura. <br/><br/> Deixe as configurações para instâncias reservadas, desconto (%) e as propriedades de tempo de atividade da VM com suas configurações padrão.


[Examine as práticas recomendadas](best-practices-assessment.md) para criar uma avaliação com as migrações para Azure.

## <a name="calculate-readiness"></a>Calcular preparação

Nem todos os servidores são adequados para execução no Azure. Uma avaliação de VM do Azure avalia todos os servidores locais e atribui a eles uma categoria de prontidão.

- **Pronto para o Azure**: o servidor pode ser migrado no estado em que se encontra no Azure sem nenhuma alteração. Ele será iniciado no Azure com o suporte completo do Azure.
- **Condicionalmente pronto para o Azure**: o servidor pode iniciar no Azure, mas pode não ter suporte completo do Azure. Por exemplo, o Azure não dá suporte a um servidor que esteja executando uma versão antiga do Windows Server. Você deve ter cuidado antes de migrar esses servidores para o Azure. Para corrigir quaisquer problemas de preparação, siga as diretrizes de correção que a avaliação sugere.
- **Não está pronto para o Azure**: o servidor não será iniciado no Azure. Por exemplo, se um disco do servidor local armazena mais de 64 TB, o Azure não pode hospedar o servidor. Siga as diretrizes de correção para corrigir o problema antes da migração.
- **Preparação desconhecida**: as migrações para Azure não podem determinar a prontidão do servidor devido a metadados insuficientes.

Para calcular a preparação, a avaliação revisa as propriedades do servidor e as configurações do sistema operacional resumidas nas tabelas a seguir.

### <a name="server-properties"></a>Propriedades do servidor

Para uma avaliação de VM do Azure, a avaliação revisa as seguintes propriedades de uma VM local para determinar se ela pode ser executada em VMs do Azure.

Propriedade | Detalhes | Status de preparação do Azure
--- | --- | ---
**Tempo de inicialização** | O Azure dá suporte a VMs com um tipo de inicialização de BIOS, não UEFI. | Condicionalmente pronto se o tipo de inicialização for UEFI
**Núcleos** | Cada servidor deve ter até 128 núcleos, que é o número máximo com suporte de uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão os núcleos utilizados para comparação. Se as configurações de avaliação especificarem um fator de conforto, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as migrações para Azure usarão os núcleos alocados para aplicar o fator de conforto. | Pronto se o número de núcleos estiver dentro do limite
**RAM** | Cada servidor deve ter até 3.892 GB de RAM, que é o tamanho máximo que uma VM da série M do Azure Standard_M128m &nbsp; <sup>2</sup> dá suporte. [Saiba mais](../virtual-machines/sizes.md).<br/><br/> Se o histórico de desempenho estiver disponível, as migrações para Azure considerarão a RAM utilizada para comparação. Se um fator de conforto for especificado, a RAM utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver nenhum histórico, a RAM alocada será usada para aplicar um fator de conforto.<br/><br/> | Pronto se a quantidade de RAM estiver dentro do limite
**Disco de armazenamento** | O tamanho alocado de um disco não deve ter mais de 32 TB. Embora o Azure dê suporte a discos de 64 TB com discos SSD Ultra do Azure, a avaliação atualmente verifica 32 TB como o limite de tamanho de disco porque ele ainda não dá suporte a SSD Ultra. <br/><br/> O número de discos anexados ao servidor, incluindo o disco do sistema operacional, deve ser de 65 ou menos. | Pronto se o tamanho e o número do disco estiverem dentro dos limites
**Rede** | Um servidor deve ter no máximo 32 NICs (interfaces de rede) conectadas a ele. | Pronto se o número de NICs estiver dentro do limite

### <a name="guest-operating-system"></a>Sistema operacional convidado

Para uma avaliação de VM do Azure, juntamente com a revisão das propriedades da VM, a avaliação examina o sistema operacional convidado de um servidor para determinar se ele pode ser executado no Azure.

> [!NOTE]
> Para lidar com a análise de convidado para VMs VMware, a avaliação usa o sistema operacional especificado para a VM no vCenter Server. No entanto, vCenter Server não fornece a versão do kernel para sistemas operacionais de VM do Linux. Para descobrir a versão, você precisa configurar a [descoberta de aplicativos](./how-to-discover-applications.md). Em seguida, o dispositivo descobre informações de versão usando as credenciais de convidado que você especifica ao configurar a descoberta de aplicativo.


A avaliação usa a seguinte lógica para identificar a prontidão do Azure com base no sistema operacional:

**Sistema operacional** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2012 R2 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2012 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2008 R2 com todos os SPs | O Azure fornece suporte total.| Pronto para o Azure.
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte total. | Pronto para o Azure.
Windows Server 2003 e Windows Server 2003 R2 | Esses sistemas operacionais passaram suas datas de fim de suporte e precisam de um [contrato de suporte personalizado (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) para dar suporte no Azure. | Condicionalmente pronto para o Azure. Considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 e MS-DOS | Esses sistemas operacionais passaram por suas datas de fim de suporte. O servidor pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. | Condicionalmente pronto para o Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Windows 7, Windows 8 e Windows 10 | O Azure fornece suporte apenas com uma [assinatura do Visual Studio.](../virtual-machines/windows/client-images.md) | Condicionalmente pronto para o Azure.
Windows 10 Pro | O Azure oferece suporte com [Direitos de Hospedagem multilocatário.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Condicionalmente pronto para o Azure.
Windows Vista e Windows XP Professional | Esses sistemas operacionais passaram por suas datas de fim de suporte. O servidor pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. | Condicionalmente pronto para o Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Linux | Consulte os [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md) que o Azure endossa. Outros sistemas operacionais Linux podem ser iniciados no Azure. Mas é recomendável que você atualize o sistema operacional para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão é aprovada.<br/><br/>Condicionalmente pronto se a versão não for endossada.
Outros sistemas operacionais, como Oracle Solaris, Apple macOS e FreeBSD | O Azure não endossa esses sistemas operacionais. O servidor pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. | Condicionalmente pronto para o Azure. Recomendamos que você instale um sistema operacional com suporte antes de migrar para o Azure.  
Sistema operacional especificado como **Outros** no vCenter Server | As migrações para Azure não podem identificar o sistema operacional nesse caso. | Preparação desconhecida. Verifique se o Azure dá suporte ao sistema operacional em execução dentro da VM.
Sistemas operacionais de 32 bits | O servidor pode iniciar no Azure, mas o Azure pode não fornecer suporte completo. | Condicionalmente pronto para o Azure. Considere atualizar para um sistema operacional de 64 bits antes de migrar para o Azure.

## <a name="calculating-sizing"></a>Calculando o dimensionamento

Depois que o servidor estiver marcado como pronto para o Azure, a avaliação fará recomendações de dimensionamento na avaliação de VM do Azure. Essas recomendações identificam a VM do Azure e o SKU do disco. Os cálculos de dimensionamento dependem de você estar usando o como está em dimensionamento local ou dimensionamento baseado em desempenho.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcular o dimensionamento (como está no local)

 Se você usar como está o dimensionamento local, a avaliação não considerará o histórico de desempenho das VMs e dos discos na avaliação de VM do Azure.

- **Dimensionamento de computação**: a avaliação ALOCA uma SKU de VM do Azure com base no tamanho alocado no local.
- **Armazenamento e dimensionamento de disco**: a avaliação examina o tipo de armazenamento especificado nas propriedades de avaliação e recomenda o tipo de disco apropriado. Os tipos de armazenamento possíveis são HDD Standard, SSD Standard e Premium. O tipo de armazenamento padrão é Premium.
- **Dimensionamento de rede**: a avaliação considera o adaptador de rede no servidor local.

### <a name="calculate-sizing-performance-based"></a>Calcular o dimensionamento (baseado em desempenho)

Se você usar o dimensionamento baseado em desempenho em uma avaliação de VM do Azure, a avaliação fará recomendações de dimensionamento da seguinte maneira:

- A avaliação considera o histórico de desempenho do servidor para identificar o tamanho da VM e o tipo de disco no Azure.
- Se você importar servidores usando um arquivo CSV, os valores especificados serão usados. Esse método é especialmente útil se você tiver superalocado o servidor local, a utilização é baixa e você deseja que o tenha como direitos a VM do Azure para economizar custos.
- Se você não quiser usar os dados de desempenho, redefina os critérios de dimensionamento como estão no local, conforme descrito na seção anterior.

#### <a name="calculate-storage-sizing"></a>Calcular o dimensionamento de armazenamento

Para o dimensionamento de armazenamento em uma avaliação de VM do Azure, as migrações para Azure tentam mapear cada disco anexado ao servidor para um disco do Azure. O dimensionamento funciona da seguinte maneira:

1. A avaliação adiciona o IOPS de leitura e gravação de um disco para obter o IOPS total necessário. Da mesma forma, ele adiciona os valores de taxa de transferência de leitura e gravação para obter a taxa de transferência total de cada disco. No caso de avaliações baseadas em importação, você tem a opção de fornecer o total de IOPS, a taxa de transferência total e o n º total. de discos no arquivo importado sem especificar configurações de disco individuais. Se você fizer isso, o dimensionamento de disco individual será ignorado e os dados fornecidos serão usados diretamente para calcular o dimensionamento e selecionar uma SKU de VM apropriada.

1. Se você tiver especificado o tipo de armazenamento como automático, o tipo selecionado será baseado nos valores de taxa de transferência e IOPS efetivos. A avaliação determina se o disco deve ser mapeado para um HDD Standard, SSD Standard ou disco Premium no Azure. Se o tipo de armazenamento for definido como um desses tipos de disco, a avaliação tentará localizar um SKU de disco dentro do tipo de armazenamento selecionado.
1. Os discos são selecionados da seguinte maneira:
    - Se a avaliação não conseguir localizar um disco com a IOPS e a taxa de transferência necessárias, ela marcará o servidor como inadequado para o Azure.
    - Se a avaliação encontrar um conjunto de discos adequados, ela selecionará os discos que dão suporte ao local especificado nas configurações de avaliação.
    - Se houver vários discos qualificados, a avaliação selecionará o disco com o menor custo.
    - Se os dados de desempenho de qualquer disco não estiverem disponíveis, o tamanho do disco de configuração será usado para localizar um disco SSD Standard no Azure.

#### <a name="calculate-network-sizing"></a>Calcular o dimensionamento da rede

Para uma avaliação de VM do Azure, a avaliação tenta encontrar uma VM do Azure que dá suporte ao número e ao desempenho necessário de adaptadores de rede conectados ao servidor local.

- Para obter o desempenho de rede efetivo do servidor local, a avaliação agrega a taxa de transmissão de dados do servidor (saída de rede) em todos os adaptadores de rede. Em seguida, ele aplica o fator de conforto. Ele usa o valor resultante para encontrar uma VM do Azure que pode dar suporte ao desempenho de rede necessário.
- Junto com o desempenho da rede, a avaliação também considera se a VM do Azure pode dar suporte ao número necessário de adaptadores de rede.
- Se os dados de desempenho de rede estiverem indisponíveis, a avaliação considerará apenas a contagem do adaptador de rede para o dimensionamento da VM.

#### <a name="calculate-compute-sizing"></a>Calcular o dimensionamento de computação

Depois de calcular os requisitos de armazenamento e de rede, a avaliação considera os requisitos de CPU e RAM para encontrar um tamanho de VM adequado no Azure.

- As migrações para Azure analisam os núcleos e a RAM utilizados em vigor para encontrar um tamanho de VM do Azure adequado.
- Se nenhum tamanho adequado for encontrado, o servidor será marcado como inadequado para o Azure.
- Se um tamanho adequado for encontrado, as Migrações para Azure aplicarão os cálculos de armazenamento e rede. Em seguida, ele aplica o local e as configurações de camada de preço para a recomendação de tamanho final da VM.
- Se houver vários discos qualificados, será recomendado aquele com o menor custo.

## <a name="confidence-ratings-performance-based"></a>Classificações de confiança (baseadas em desempenho)

Cada avaliação de VM do Azure baseada em desempenho nas migrações para Azure está associada a uma classificação de confiança. A classificação varia de um (menor) a cinco estrelas (mais alta). A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho que o Azure migra fornece.

- A classificação de confiança é atribuída a uma avaliação. A classificação é baseada na disponibilidade de pontos de dados que são necessários para calcular a avaliação.
- Para o dimensionamento baseado em desempenho, a avaliação precisa de:
    - Os dados de utilização para CPU e RAM.
    - Os dados de taxa de transferência e IOPS de disco para cada disco anexado ao servidor.
    - A e/s de rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede anexado a um servidor.

Se qualquer um desses números de utilização não estiver disponível, as recomendações de tamanho podem não ser confiáveis.

> [!NOTE]
> As classificações de confiança não são atribuídas a servidores avaliados usando um arquivo CSV importado. As classificações também não são aplicáveis para a avaliação local.

### <a name="ratings"></a>Classificações

Esta tabela mostra as classificações de confiança de avaliação, que dependem da porcentagem de pontos de dados disponíveis:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0-20% | 1 estrela
   21-40% | 2 estrelas
   41-60% | 3 estrelas
   61-80% | 4 estrelas
   81-100% | 5 estrelas

### <a name="low-confidence-ratings"></a>Classificações de baixa confiança

Aqui estão algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, deverá aguardar pelo menos um dia depois de iniciar a descoberta para todos os pontos de dados a serem coletados.
- A avaliação não é capaz de coletar os dados de desempenho de alguns ou de todos os servidores no período de avaliação. Para uma classificação de alta confiança, verifique se: 
    - Os servidores estão ligados durante a avaliação
    - Conexões de saída nas portas 443 são permitidas
    - Para servidores Hyper-V, a memória dinâmica está habilitada 
    
    “Recalcule” a avaliação para refletir as alterações mais recentes na classificação de confiança.

- Alguns servidores foram criados durante o tempo durante o qual a avaliação foi calculada. Por exemplo, suponha que você criou uma avaliação para o histórico de desempenho do último mês, mas alguns servidores foram criados há apenas uma semana atrás. Nesse caso, os dados de desempenho para os novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for menor que cinco estrelas, recomendamos que você aguarde pelo menos um dia para o dispositivo criar o perfil do ambiente e, em seguida, recalcular a avaliação. Caso contrário, o dimensionamento baseado em desempenho pode não ser confiável. Nesse caso, recomendamos que você alterne a avaliação para o dimensionamento local.

## <a name="calculate-monthly-costs"></a>Calcular custos mensais

Após a conclusão das recomendações de dimensionamento, uma avaliação de VM do Azure nas migrações para Azure calcula os custos de computação e armazenamento para após a migração.

- **Custo de computação**: as migrações para Azure usam o tamanho de VM do Azure recomendado e a API de cobrança do Azure para calcular o custo mensal do servidor.

    O cálculo leva em conta o:
    - Sistema operacional
    - Software Assurance
    - Instâncias reservadas
    - Tempo de atividade da VM
    - Localização
    - Configurações de moeda

    A avaliação agrega o custo em todos os servidores para calcular o custo de computação mensal total.

- **Custo de armazenamento**: o custo de armazenamento mensal de um servidor é calculado pela agregação do custo mensal de todos os discos que estão anexados ao servidor.

    A avaliação calcula o total de custos de armazenamento mensal agregando os custos de armazenamento de todos os servidores. Atualmente, o cálculo não considera as ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação.

## <a name="next-steps"></a>Próximas etapas

[Examine](best-practices-assessment.md) as melhores práticas para a criação de avaliações. 

- Saiba mais sobre a execução de Avaliações para servidores em execução no ambiente [VMware](./tutorial-discover-vmware.md) e [Hyper-V ](./tutorial-discover-hyper-v.md) e [servidores físicos](./tutorial-discover-physical.md).
- Saiba mais sobre como avaliar servidores [importados com um arquivo CSV](./tutorial-discover-import.md).
- Saiba mais sobre como configurar a [visualização de dependência](concepts-dependency-visualization.md).