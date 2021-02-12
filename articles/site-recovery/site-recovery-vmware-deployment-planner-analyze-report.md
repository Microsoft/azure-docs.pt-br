---
title: Analisar o relatório de Planejador de Implantações para a recuperação de desastres do VMware com Azure Site Recovery
description: Este artigo descreve como analisar o relatório gerado pelo Planejador de Implantações de recuperação para a recuperação de desastres do VMware no Azure, usando Azure Site Recovery.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: ramamill
ms.openlocfilehash: 7e2db720bb37a25b8511bd1c42c0c18e139aa216
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186596"
---
# <a name="analyze-the-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analisar o relatório de Planejador de Implantações para a recuperação de desastres do VMware para o Azure

O relatório gerado do Microsoft Excel contém as seguintes planilhas:
## <a name="on-premises-summary"></a>Resumo local
A planilha Resumo local fornece uma visão geral do ambiente VMware analisado.

![Resumo local de ambiente VMware](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Data de Início** e **Data de Término** : as datas de início e de término dos dados de criação de perfil considerados para a geração de relatórios. Por padrão, a data de início é a data em que a criação de perfil começou, e a data de término é a data em que a criação de perfil é interrompida. Poderão ser os valores 'StartDate' e 'EndDate' se o relatório for gerado com esses parâmetros.

**Número total de dias de criação de perfil** : o número total de dias de criação de perfil entre as datas de início e de término para as quais o relatório é gerado.

**Número de máquinas virtuais compatíveis** é o número total de máquinas virtuais compatíveis para as quais a largura de banda de rede necessária, o número necessário de contas de Armazenamento do Azure, os núcleos do Microsoft Azure, os Servidores de Configuração e Servidores de Processo adicionais são calculados.

**Número total de discos em todas as máquinas virtuais compatíveis** : o número que é usado como uma das entradas para decidir o número de servidores de configuração e servidores de processo adicionais a serem usados na implantação.

**Número médio de discos por máquina virtual compatível** : o número médio de discos calculado em todas as VMs compatíveis.

**Tamanho médio de disco (GB)** : o tamanho médio de disco calculado em todas as VMs compatíveis.

**RPO desejado (minutos)** : o objetivo de ponto de recuperação padrão ou o valor passado para o parâmetro 'DesiredRPO' no momento da geração de relatórios para estimar a largura de banda necessária.

**Largura de banda desejada (Mbps)** : o valor passado para o parâmetro 'Bandwidth' no momento da geração de relatórios para estimar o RPO atingível.

**Variação de dados típica observada por dia (GB)** : a variação de dados média observada em todos os dias de criação de perfil. Esse número é usado como uma das entradas para decidir o número de servidores de configuração e servidores de processo adicionais a serem usados na implantação.

## <a name="recommendations"></a>Recomendações

A planilha de recomendações do relatório do VMware para o Azure apresenta os seguintes detalhes de acordo com o RPO selecionado:

![Recomendações para relatório do VMware para Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Dados de criação de perfil
![O modo de exibição de dados de criação de perfil no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Período de dados com criação de perfil** : o período durante o qual a criação de perfil foi executada. Por padrão, a ferramenta inclui todos os dados com criação de perfil no cálculo, a menos que gere o relatório para um período específico usando as opções StartDate e EndDate durante a geração do relatório.

**Nome do Servidor** : o nome ou o endereço IP do host VMware vCenter ou ESXi sobre cujas VMss é gerado o relatório.

**RPO Desejado** : o objetivo de ponto de recuperação para sua implantação. Por padrão, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na planilha. Se você tiver usado o parâmetro *DesiredRPOinMin* ao gerar o relatório, esse valor será mostrado no resultado de RPO desejado.

### <a name="profiling-overview"></a>Visão geral da criação de perfil

![Resultados da criação de perfil no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Total de Máquinas Virtuais com Criação de Perfil** : o número total de VMs cujos dados de criação de perfil estão disponíveis. Se VMListFile tiver nomes de VMs sem criação de perfil, essas VMs não serão consideradas na geração de relatórios e serão excluídas da contagem total de VMs com criação de perfil.

**Máquinas Virtuais Compatíveis** : o número de VMs que podem ser protegidas no Azure usando o Site Recovery. É o número total de VMs compatíveis para as quais são calculados a largura de banda de rede necessária, o número de contas de armazenamento, o número de núcleos do Azure e o número de servidores de configuração e servidores de processo adicionais. Os detalhes de cada VM compatível estão disponíveis na seção "VMs compatíveis".

**Máquinas Virtuais Incompatível** : o número de VMs com criação de perfil que são incompatíveis para proteção com o Site Recovery. Os motivos da incompatibilidade são indicados na seção "VMs incompatíveis". Se VMListFile tiver nomes de VMs sem criação de perfil, essas VMs serão excluídas da contagem de VMs incompatíveis. Essas VMs são listadas como "Dados não encontrados" ao fim da seção "VMs incompatíveis".

**RPO Desejado** : seu objetivo de ponto de recuperação desejado, em minutos. O relatório é gerado para três valores de RPO: 15 (padrão), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base em sua seleção na lista suspensa RPO Desejado no canto superior direito da planilha. Se você tiver gerado o relatório usando o parâmetro *-DesiredRPO* com um valor personalizado, esse valor personalizado será exibido como o padrão na lista suspensa RPO Desejado.

### <a name="required-network-bandwidth-mbps"></a>Largura de banda necessária (Mbps)

![Largura de banda necessária no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Para atender ao RPO 100% do tempo:** a largura de banda recomendada em Mbps a ser alocada para atender ao RPO desejado 100% do tempo. Essa quantidade de largura de banda deve ser dedicada para a replicação delta de estado estacionário de todas as VMs compatíveis para evitar violações de RPO.

**Para atender ao RPO 90% do tempo** : devido aos preços de banda larga ou por qualquer outro motivo, se não puder definir a largura de banda necessária para atender ao RPO desejado 100% do tempo, você poderá optar por usar uma configuração de largura de banda mais baixa que possa satisfazer ao RPO desejado 90% do tempo. Para entender as implicações da configuração dessa largura de banda inferior, o relatório fornece uma análise do tipo "e se" sobre o número e a duração de violações de RPO a serem esperadas.

**Taxa de transferência obtida** A taxa de transferência do servidor em que você executou o comando GetThroughput para a região do Microsoft Azure em que se encontra a conta de armazenamento. Esse número de taxa de transferência indica o nível estimado que você pode obter ao proteger as VMs compatíveis usando o Site Recovery, desde que as características de rede e de armazenamento do servidor de configuração ou do servidor de processo permaneçam iguais às do servidor no qual você executou a ferramenta.

Para a replicação, você deve definir a largura de banda recomendada para atender ao RPO 100% do tempo. Depois que você definir a largura de banda, se não houver um aumento na taxa de transferência obtida, conforme relatado pela ferramenta, faça o seguinte:

1. Verifique se há QoS (Qualidade de Serviço de Rede) que está limitando a taxa de transferência do Site Recovery.

2. Verifique se o cofre do Site Recovery está na região do Microsoft Azure com suporte mais próxima fisicamente para minimizar a latência de rede.

3. Verifique as características de armazenamento local para determinar se você pode melhorar o hardware (por exemplo, de HDD para SSD).

4. Altere as configurações do Site Recovery no servidor de processo para [aumentar o valor de largura de banda de rede usado para replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se você estiver executando a ferramenta em um servidor de configuração ou de processo que já tenha VMs protegidas, execute a ferramenta algumas vezes. O número da taxa de transferência obtida é alterado dependendo da quantidade de variação processada no momento.

Para todas as implantações do Site Recovery corporativo, é recomendável usar o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Contas de armazenamento necessárias
O gráfico a seguir mostra o número total de contas de armazenamento (standard e premium) que são necessárias para proteger todas as VMs compatíveis. Para saber qual conta de armazenamento deve ser usada para cada VM, confira a seção "Posicionamento de VM-storage". Se você estiver usando a v 2.5 de Planejador de Implantações, essa recomendação mostrará apenas o número de contas de armazenamento de cache padrão que são necessárias para a replicação, uma vez que os dados estão sendo gravados diretamente no Managed Disks.

![Contas de armazenamento necessárias no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Número necessário de núcleos do Azure
Esse resultado é o número total de núcleos a serem configurados antes do failover ou do failover de teste de todas as VMs compatíveis. Se houver poucos núcleos disponíveis na assinatura, o Site Recovery não criará VMs no momento do failover de teste ou do failover.

![Número necessário de núcleos do Azure no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Infraestrutura local necessária
Esse valor é o número total de servidores de configuração e servidores de processo adicionais a serem configurados que seriam suficientes para proteger todas as VMs compatíveis. Dependendo das [recomendações de tamanho para o servidor de configuração](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server) com suporte, a ferramenta poderá recomendar servidores adicionais. A recomendação é baseada na variação por dia ou no número máximo de VMs protegidas, o que for maior (presumindo que uma média de três discos por VM), o que for atingido primeiro no servidor de configuração ou no servidor de processo adicional. Você encontrará os detalhes de variação total por dia e o número total de discos protegidos na seção "Resumo local".

![Infraestrutura local necessária no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Análise de hipóteses
Essa análise descreve quantas violações podem ocorrer durante o período de criação de perfil quando você define menos largura de banda para que o RPO desejado seja atingido somente 90% do tempo. Uma ou mais violações de RPO podem ocorrer em qualquer dia. O grafo mostra o pico de RPO do dia.
Com base nesta análise, você pode decidir se o número de violações de RPO em todos os dias e o pico de ocorrências de RPO por dia são aceitáveis com a menor largura de banda especificada. Se for aceitável, você poderá alocar menos largura de banda para replicação. Caso contrário, aloque a maior largura de banda sugerida para atender ao RPO desejado 100% do tempo.

![Análise de hipóteses no planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Tamanho de lote de VMs recomendado para a replicação inicial
Nesta seção, é recomendável que o número de VMs que podem ser protegidas em paralelo conclua a replicação inicial dentro de 72 horas com a largura de banda sugerida para atender ao RPO desejado 100% do tempo definido. Esse valor é configurável. Para alterá-lo em tempo de geração de relatórios, use o parâmetro *GoalToCompleteIR* .

Aqui, o grafo mostra um intervalo de valores de largura de banda e uma contagem de tamanho de lote de VM calculada para concluir a replicação inicial em 72 horas, com base na média detectada de tamanho de VM em todas as VMs compatíveis.

Na visualização pública, o relatório não especifica quais VMs devem ser incluídas em um lote. Você pode usar o tamanho de disco mostrado na seção "VMs compatíveis" para obter o tamanho de cada VM e selecioná-las para um lote ou pode selecionar as VMs com base em características de carga de trabalho conhecidas. O tempo de conclusão das alterações de replicação inicial proporcionalmente, com base no tamanho do disco de VM real, no espaço em disco usado e na taxa de transferência de rede disponível.

![Tamanho de lote de VM recomendado](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Estimativa de custo
O gráfico mostra a exibição de resumo do custo total de recuperação de desastre (DR) estimado para o Azure da sua região de destino escolhida e da moeda que você especificou para a geração de relatórios.

![Resumo de estimativa de custo](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

O resumo ajuda a entender o custo que precisa ser pago para armazenamento, computação, rede e licença ao proteger todas as suas VMs compatíveis para o Azure usando o Azure Site Recovery. O custo é calculado sobre as VMs compatíveis e não todas as VMs com criação de perfil.  

Você pode exibir o custo mensal ou anual. Saiba mais sobre [regiões de destino com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e [moedas com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes** O custo total da recuperação de desastre é dividido em quatro componentes: custos de licença de Computação, Armazenamento, Rede e do Azure Site Recovery. O custo é calculado com base no consumo incorrido durante a replicação e em tempo de análise de recuperação de desastre para computação, armazenamento (premium e standard), ExpressRoute/VPN configurado entre o site local e o Azure, e a licença do Azure Site Recovery.

**Custo por estados** O custo total da recuperação de desastre (DR) se baseia em categorias em dois estados diferentes - Replicação e análise de recuperação de desastre.

**Custo de replicação** : o custo incorrido durante a replicação. Abrange o custo de armazenamento, de rede e de licença do Azure Site Recovery.

**Custo de análise de DR** : o custo incorrido durante os failovers de teste. O Azure Site Recovery gira máquinas virtuais durante o failover de teste. O custo de análise de análise de risco abrange os custos de computação e armazenamento das VMs em execução.

**Custo de armazenamento do Azure por mês/ano** Mostra o custo total de armazenamento incorrido no armazenamento standard e premium para replicação e análise de recuperação de desastre.
Você pode exibir a análise de custo detalhada por VM na planilha [Estimativa de Custo](site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="growth-factor-and-percentile-values-used"></a>Fator de crescimento e valores de percentil usados
Esta seção na parte inferior da planilha mostra o valor de percentil usado para todos os contadores de desempenho das VMs com criação de perfil (o padrão é o 95º percentil) e o fator de crescimento em % usado em todos os cálculos (o padrão é 30%).

![Fator de crescimento e valores de percentil usados](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações com largura de banda disponível como entrada

![Recomendações com largura de banda disponível como entrada](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Pode haver uma situação em que você saiba que não é possível definir uma largura de banda de mais de x Mbps para replicação do Site Recovery. A ferramenta permite indicar a largura de banda (usando o parâmetro -Bandwidth durante a geração de relatórios) e obter o RPO possível em minutos. Com esse valor de RPO que pode ser obtido, você pode decidir se precisa configurar largura de banda adicional ou se pode ter uma solução de recuperação de desastres com este RPO.

![O RPO possível para a largura de banda de 500 Mbps](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Posicionamento de VM-Storage

>[!Note]
>O Planejador de Implantações v 2.5 em diante recomenda o posicionamento de armazenamento para computadores que serão replicados diretamente em discos gerenciados.

![Posicionamento de VM-Storage](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Tipo de armazenamento de replicação** : um disco gerenciado Standard ou Premium, que é usado para replicar todas as VMs correspondentes mencionadas na coluna **VMs para o local** .

**Tipo de conta de armazenamento de log** : todos os logs de replicação são armazenados em uma conta de armazenamento padrão.

**Prefixo sugerido para a conta de armazenamento** : o prefixo de três caracteres sugerido que pode ser usado para nomear a conta de armazenamento em cache. Você pode usar seu próprio prefixo, mas sugestão da ferramenta segue a [convenção de nomenclatura de partição para contas de armazenamento](/en-in/azure/storage/blobs/storage-performance-checklist).

**Nome da conta de log sugerido** : o nome da conta de armazenamento depois que você inclui o prefixo sugerido. Substitua o nome entre colchetes angulares (< e >) por sua entrada personalizada.

**Resumo de posicionamento** : um resumo dos discos necessários para VMs protegidas por tipo de armazenamento. Ele inclui o número total de VMs, o tamanho total provisionado em todos os discos e o número total de discos.

**Máquinas Virtuais para Posicionar** : uma lista de todas as VMs que devem ser posicionadas na conta de armazenamento específica para obter o melhor desempenho e uso.

## <a name="compatible-vms"></a>VMs compatíveis
![Planilha do Excel de VMs compatíveis](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Nome da VM** : o nome da VM ou o endereço IP que é usado em VMListFile quando um relatório é gerado. Essa coluna também lista os discos (VMDKs) que estão anexados às VMs. Para diferenciar VMs vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome do host ESXi. O host ESXi listado é aquele em que a VM foi colocada quando a ferramenta realizou a descoberta durante o período de criação de perfil.

**Compatibilidade de VM** : os valores são **Sim** e **Sim \* *_. _* Sim** \* é para instâncias nas quais a VM é uma opção para o [SSDs Premium](../virtual-machines/disks-types.md). Aqui, a alta variação da criação de perfil ou o disco IOPS se encaixam na categoria P20 ou P30, mas o tamanho do disco faz com que ele seja mapeado para P10 ou P20. A conta de armazenamento decide para qual tipo de disco de armazenamento premium um disco deve ser mapeado, com base em seu tamanho. Por exemplo:
* <128 GB é P10.
* 128 GB até 256 GB é um P15
* 256 GB a 512 GB é um P20.
* 512 GB a 1024 GB é P30.
* 1025 GB a 2048 GB é P40.
* 2049 GB a 4095 GB é P50.

Por exemplo, se as características de carga de trabalho de um disco o colocarem na categoria P20 ou p30, mas o tamanho o mapear para um tipo de disco de armazenamento Premium inferior, a ferramenta marcará essa VM como **Sim** \* . A ferramenta também recomenda que você altere o tamanho do disco de origem para se ajustar ao tipo de disco de armazenamento premium recomendado ou altere o tipo de disco de destino após o failover.

**Tipo de armazenamento** : standard ou premium.

**Asrseeddisk (disco gerenciado) criado para replicação** : o nome do disco que é criado quando você habilita a replicação. Ele armazena os dados e seus instantâneos no Azure.

**IOPS de L/G de Pico (com Fator de Crescimento)** : o IOPS de leitura/gravação de carga de trabalho de pico no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que o IOPS total de leitura/gravação de uma VM nem sempre é a soma de IOPS de leitura/gravação dos discos individuais da VM, pois o IOPS de leitura/gravação de pico da VM é o pico da soma do IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação de Dados de Pico em Mbps (com Fator de Crescimento)** : a taxa de variação de pico no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que a variação total dos dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM, pois o pico da variação de dados da VM é o pico da soma da variação dos discos individuais durante cada minuto do período de criação de perfil.

**Tamanho de VM do Azure** : o tamanho ideal de máquina virtual mapeada dos Serviços de Nuvem do Azure para essa VM local. O mapeamento é baseado na memória da VM local, no número de discos/núcleos/NICs e IOPS de leitura/gravação. A recomendação é sempre o menor tamanho de VM do Azure que corresponde a todas as características de VM local.

**Número de Discos** : o número total de VMDKs (discos de máquina virtual) na VM.

**Tamanho de disco (GB)** : o tamanho total de instalação de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos** : o número de núcleos de CPUs na VM.

**Memória (MB)** : RAM na VM.

**NICs** : o número de NICs na VM.

**Tipo de inicialização** : tipo de inicialização da VM. Pode ser o BIOS ou EFI.  Atualmente, o Azure Site Recovery dá suporte às VMs do Windows Server EFI (Windows Server 2012, 2012 R2 e 2016) desde que o número de partições no disco de inicialização seja menor do que 4 e o tamanho do setor de inicialização seja de 512 bytes. Para proteger as VMs do EFI, a versão do serviço de mobilidade do Azure Site Recovery deverá ser a 9.13 ou superior. Somente o failover tem suporte para as VMs do EFI. Não há suporte para failback.  

**Tipo de sistema operacional** : o tipo de SO da VM. Pode ser Windows ou Linux ou outro com base no modelo do VMware vSphere escolhido durante a criação da máquina virtual.  

## <a name="incompatible-vms"></a>VMs incompatíveis

![Planilha do Excel de VMs incompatíveis
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Nome da VM** : o nome da VM ou o endereço IP que é usado em VMListFile quando um relatório é gerado. Essa coluna também lista os VMDKs que estão anexados às VMs. Para diferenciar VMs vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome do host ESXi. O host ESXi listado é aquele em que a VM foi colocada quando a ferramenta realizou a descoberta durante o período de criação de perfil.

**Compatibilidade de VM** : indica por que a VM específica é incompatível com o Site Recovery. Os motivos são descritos para cada disco incompatível da VM e, com base nos [limites de armazenamento](/en-in/azure/storage/common/scalability-targets-standard-account) publicados, podem ser qualquer um dos seguintes:

* Tamanho incorreto do disco de dados ou tamanho de disco do so incorreto. [Examine](vmware-physical-azure-support-matrix.md#azure-vm-requirements) os limites de suporte. 
* O tamanho total da VM (replicação + TFO) excede o limite de tamanho de conta de armazenamento com suporte (35 TB). Essa incompatibilidade normalmente ocorre quando um único disco na VM tem uma característica de desempenho que excede os limites máximo com suporte do Azure ou do Site Recovery para o armazenamento standard. Essa instância coloca a VM na zona de armazenamento premium. No entanto, o tamanho máximo com suporte de uma conta de armazenamento premium é de 35 TB, e uma única VM protegida não pode ser protegida em várias contas de armazenamento. Além disso, observe que quando um failover de teste é executado em uma VM protegida, ele é executado na mesma conta de armazenamento em que a replicação está em andamento. Nessa instância, configure duas vezes o tamanho do disco para que a replicação progrida e o failover de teste tenha êxito em paralelo.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 7500 por disco.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 80.000 por VM.

* A rotatividade média de dados excede o limite de variação de dados Site Recovery com suporte de 20 MB/s para o tamanho médio de e/s para o disco.

* A variação de dados de pico em todos os discos na VM excede o limite máximo de variação de dados de pico com suporte do Site Recovery de 54 MB!s por VM.

* O IOPS médio de gravação eficiente excede o limite de IOPS do Site Recovery de 840 com suporte para disco.

* O armazenamento de instantâneos calculado excede o limite com suporte de 10 TB para armazenamento de instantâneos.

* A variação do total de dados por dia excede o limite diário de variação de 2 TB por um Servidor de Processo.


**IOPS de L/G de Pico (com Fator de Crescimento)** : o pico de IOPS de carga de trabalho no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que o IOPS total de leitura/gravação da VM nem sempre é a soma de IOPS de leitura/gravação dos discos individuais da VM, pois o IOPS de leitura/gravação de pico da VM é o pico da soma do IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação nos Dados de Pico em Mbps (com Fator de Crescimento)** : a taxa de variação de pico no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é o 30%). Observe que a variação total dos dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM, pois o pico da variação de dados da VM é o pico da soma da variação dos discos individuais durante cada minuto do período de criação de perfil.

**Número de Discos** : o número total de VMDKs na VM.

**Tamanho de disco (GB)** : o tamanho total de instalação de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos** : o número de núcleos de CPUs na VM.

**Memória (MB)** : a quantidade de RAM na VM.

**NICs** : o número de NICs na VM.

**Tipo de inicialização** : tipo de inicialização da VM. Pode ser o BIOS ou EFI.  Atualmente, o Azure Site Recovery dá suporte às VMs do Windows Server EFI (Windows Server 2012, 2012 R2 e 2016) desde que o número de partições no disco de inicialização seja menor do que 4 e o tamanho do setor de inicialização seja de 512 bytes. Para proteger as VMs do EFI, a versão do serviço de mobilidade do Azure Site Recovery deverá ser a 9.13 ou superior. Somente o failover tem suporte para as VMs do EFI. Não há suporte para failback.

**Tipo de so** : é o tipo de so da VM. Pode ser Windows ou Linux ou outro com base no modelo do VMware vSphere escolhido durante a criação da máquina virtual.

## <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery
A tabela a seguir fornece os limites do Azure Site Recovery. Esses limites são baseados em nossos testes, mas eles não podem abranger todas as combinações possíveis de E/S de aplicativos. Os resultados reais podem variar dependendo da combinação de E/S do aplicativo. Para obter os melhores resultados, mesmo após planejar a implantação, é sempre recomendável executar amplos testes de aplicativos usando um failover de teste para obter a visão real do desempenho do aplicativo.

**Destino de armazenamento de replicação** | **Tamanho de E/S de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior | 20 MB/s | 1\.684 GB por disco

**Variação de dados de origem** | **Limite máximo**
---|---
Variação de dados de pico entre todos os discos em uma VM | 54 MB/s
Variação máxima de dados por dia com suporte de um Servidor de Processo | 2 TB

Esses são números médios, pressupondo uma sobreposição de E/S de 30%. O Site Recovery pode lidar com uma maior taxa de transferência com base na taxa de sobreposição, em tamanhos maiores de gravação e em comportamento de E/S de carga de trabalho real. Os números anteriores pressupõem uma lista de pendências típica de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados, e um ponto de recuperação é criado dentro de cinco minutos.


## <a name="cost-estimation"></a>Estimativa de custo
Saiba mais sobre [estimativa de custo](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [estimativa de custo](site-recovery-vmware-deployment-planner-cost-estimation.md).