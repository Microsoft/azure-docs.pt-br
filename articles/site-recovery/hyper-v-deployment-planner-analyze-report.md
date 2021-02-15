---
title: Analisar o relatório de Planejador de Implantações do Hyper-V no Azure Site Recovery
description: Este artigo descreve como analisar o relatório gerado pelo Planejador de Implantações do Azure Site Recovery para recuperação de desastre de VMs Hyper-V para o Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: mayg
ms.openlocfilehash: f230445ecdb046c2b631e89567df71e1d09c3234
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999222"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analise o relatório do Planejador de Implantações do Azure Site Recovery
Este artigo aborda as planilhas contidas no relatório do Excel gerado pelo Planejador de Implantações do Azure Site Recovery em um cenário Hyper-V para Azure.

## <a name="on-premises-summary"></a>Resumo local
A planilha de resumo local fornece uma visão geral do ambiente Hyper-V analisado.

![Resumo local](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Data de início** e **data de término**: as datas de início e de término dos dados de criação de perfil considerados para geração de relatórios. Por padrão, a data de início é a data em que a criação de perfil começou, e a data de término é a data em que a criação de perfil é interrompida. Essas informações poderão ser os valores "StartDate" e "EndDate" se o relatório for gerado com esses parâmetros.

**Número total de dias de criação de perfil**: o número total de dias de criação de perfil entre as datas de início e de término para as quais o relatório é gerado.

**Número de máquinas virtuais compatíveis**: o número total de VMs compatíveis para as quais a largura de banda de rede necessária, o número necessário de contas de armazenamento e os núcleos do Azure são calculados.

**Número total de discos em todas as máquinas virtuais compatíveis**: o número total de discos em todas as VMs compatíveis.

**Número médio de discos por máquina virtual compatível**: o número médio de discos calculado em todas as VMs compatíveis.

**Tamanho médio de disco (GB)**: o tamanho médio de disco calculado em todas as VMs compatíveis.

**RPO desejado (minutos)**: o objetivo do ponto de recuperação padrão ou o valor passado para o parâmetro "DesiredRPO" no momento da geração do relatório para estimar a largura de banda necessária.

**Largura de banda desejada (Mbps)**: o valor passado para o parâmetro 'Bandwidth' no momento da geração de relatórios para estimar o RPO (objetivo de ponto de recuperação) atingível.

**Variação de dados típica observada por dia (GB)**: a variação de dados média observada em todos os dias de criação de perfil.

## <a name="recommendations"></a>Recomendações 
A planilha de recomendações do Hyper-V para o relatório do Azure apresenta os seguintes detalhes de acordo com o RPO selecionado:

![Recomendações do Hyper-V para o relatório do Azure](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Dados de perfil
![Dados de perfil](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Período de dados com criação de perfil**: o período durante o qual a criação de perfil foi executada. Por padrão, a ferramenta inclui todos os dados de criação de perfil no cálculo. Caso tenha usado a opção StartDate e EndDate na geração de relatórios, é gerado o relatório para o período específico. 

**Número de servidores Hyper-V com o perfil criado**: o número de servidores Hyper-V cujo relatório de VMs é gerado. Selecione o número para exibir o nome dos servidores Hyper-V. A planilha Requisitos de Armazenamento Local é aberta e mostra todos os servidores, juntamente com suas necessidades de armazenamento. 

**RPO Desejado**: o objetivo de ponto de recuperação para sua implantação. Por padrão, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na planilha. Caso tenha usado o parâmetro DesiredRPOinMin ao gerar o relatório, esse valor será mostrado no resultado do RPO Desejado.

### <a name="profiling-overview"></a>Visão geral da criação de perfil
![Visão geral da criação de perfil](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Total de Máquinas Virtuais com Criação de Perfil**: o número total de VMs cujos dados de criação de perfil estão disponíveis. Se VMListFile tiver nomes de quaisquer VMs sem criação de perfil, essas VMs não serão consideradas na geração de relatórios e serão excluídas da contagem total de VMs com criação de perfil.

**Máquinas Virtuais Compatíveis**: o número de VMs que podem ser protegidas no Azure usando o Azure Site Recovery. É o número total de VMs compatíveis para as quais são calculados a largura de banda de rede necessária, o número de contas de armazenamento e o número de núcleos do Azure. Os detalhes de cada VM compatível estão disponíveis na seção "VMs compatíveis".

**Máquinas Virtuais Incompatível**: o número de VMs com criação de perfil que são incompatíveis para proteção com o Site Recovery. Os motivos da incompatibilidade são indicados na seção "VMs incompatíveis". Se VMListFile tiver nomes de VMs sem criação de perfil, essas VMs serão excluídas da contagem de VMs incompatíveis. Essas VMs são listadas como "Dados não encontrados" ao fim da seção "VMs incompatíveis".

**RPO Desejado**: seu objetivo de ponto de recuperação desejado, em minutos. O relatório é gerado para três valores de RPO: 15 (padrão), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base em sua seleção na lista suspensa **RPO Desejado** no canto superior direito da planilha. Se você gerou o relatório usando o parâmetro -DesiredRPO com um valor personalizado, esse valor personalizado será exibido como o padrão na lista suspensa **RPO Desejado**.

### <a name="required-network-bandwidth-mbps"></a>Largura de banda necessária (Mbps)
![Largura de banda necessária](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Para atender ao RPO 100% do tempo:** a largura de banda recomendada em Mbps a ser alocada para atender ao RPO desejado 100% do tempo. Essa quantidade de largura de banda deve ser dedicada para a replicação delta de estado estacionário de todas as VMs compatíveis para evitar violações de RPO.

**Para atender ao RPO 90% do tempo**: talvez por causa dos preços de banda larga ou outro motivo, você não possa definir a largura de banda necessária para atender o RPO desejado 100% do tempo. Se esse for o caso, você pode usar uma configuração de largura de banda inferior que atenda ao RPO desejado 90% do tempo. Para entender as implicações da configuração dessa largura de banda inferior, o relatório fornece uma análise do tipo "e se" sobre o número e a duração de violações de RPO a serem esperadas.

**Taxa de transferência obtida**: a taxa de transferência do servidor em que você executou o comando GetThroughput para a região do Azure em que se encontra a conta de armazenamento. Esse número de taxa de transferência indica o nível estimado que você pode obter ao proteger as VMs compatíveis usando o Site Recovery. As características de armazenamento e rede do servidor do Hyper-V devem permanecer iguais às do servidor no qual você executa a ferramenta.

Para todas as implantações do Site Recovery corporativo, é recomendável usar o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Contas de armazenamento necessárias
O gráfico a seguir mostra o número total de contas de armazenamento (standard e premium) que são necessárias para proteger todas as VMs compatíveis. Para saber qual conta de armazenamento deve ser usada para cada VM, confira a seção "Posicionamento de VM-storage".

![Contas do Armazenamento do Azure necessárias](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Número necessário de núcleos do Azure
Esse resultado é o número total de núcleos a serem configurados antes do failover ou do failover de teste de todas as VMs compatíveis. Se houver poucos núcleos disponíveis na assinatura, o Site Recovery não criará VMs no momento do failover de teste ou do failover.

![Número necessário de núcleos do Azure](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Requisito de armazenamento local adicional

O total de armazenamento livre necessário nos servidores do Hyper-V para replicação inicial e a replicação delta bem-sucedidas para fazer com que a replicação de VM não cause nenhum tempo de inatividade indesejável para seus aplicativos de produção. Mais informações sobre cada requisito de volume estão disponíveis no [requisito de armazenamento local](#on-premises-storage-requirement). 

Para entender por que o espaço livre é necessário para a replicação, confira a seção [Requisito de armazenamento local](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication).

![Requisito de armazenamento local e frequência de cópia](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Frequência máxima de cópia
A frequência máxima de cópia recomendada deve ser definida para que a replicação alcance o RPO desejado. O padrão é de cinco minutos. Você pode definir a frequência de cópia para 30 segundos a fim de alcançar um RPO melhor.

### <a name="what-if-analysis"></a>Análise de hipóteses
![Teste de hipóteses](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Essa análise descreve quantas violações poderiam ocorrer durante o período de criação de perfil quando você define uma largura de banda menor para que o RPO desejado seja atingido somente 90% do tempo. Uma ou mais violações de RPO podem ocorrer em qualquer dia. O grafo mostra o pico de RPO do dia. Com base nesta análise, você pode decidir se o número de violações de RPO em todos os dias e o pico de ocorrências de RPO por dia são aceitáveis com a menor largura de banda especificada. Se for aceitável, você poderá alocar menos largura de banda para replicação. Se for inaceitável, aloque maior largura de banda como sugerido para atender ao RPO desejado 100% do tempo. 

### <a name="recommendation-for-successful-initial-replication"></a>Recomendação para a replicação inicial com êxito
Esta seção aborda o número de lotes nos quais as VMs devem ser protegidas e a largura de banda mínima necessária para concluir a IR (replicação inicial) com êxito. 

![Lote de IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

A VM deve ser protegia na ordem de lotes determinada. Cada lote tem uma lista de VMs específica. As VMs do Lote 1 devem ser protegidas antes das VMs do Lote 2. As VMs do Lote 2 devem ser protegidas antes do Lote 3 e assim por diante. Assim que a replicação das VMs do Lote 1 for concluída, será possível habilitar a replicação das VMs do Lote 2. Da mesma forma, assim que a replicação inicial das VMs do Lote 2 for concluída, será possível habilitar a replicação das VMs do Lote 3 e assim por diante. 

Caso a ordem de lote não seja seguida, a largura de banda suficiente para a replicação inicial pode não estar disponível para as VMs que serão protegidas posteriormente. O resultado é que as VMs nunca concluirão a replicação inicial ou algumas VMs protegidas poderão entrar no modo de ressincronização. O envio em lote de IR para a planilha RPO selecionada tem as informações detalhadas de quais VMs devem ser incluídas em cada lote.

O gráfico aqui mostra a distribuição de largura de banda para replicação inicial e a replicação delta entre os lotes na ordem de lote determinada. Quando você protege o primeiro lote de VMs, a largura de banda total fica disponível para a replicação inicial. Assim que a replicação inicial do primeiro lote é concluída, parte da largura de banda é exigida para a replicação delta. A largura de banda restante estará disponível para a replicação inicial do segundo lote de VMs. 

A barra do Lote 2 mostra a largura de banda de replicação delta necessária para VMs do Lote 1 e a largura de banda disponível para a replicação inicial de VMs do Lote 2. De modo semelhante, a barra do Lote 3 mostra a largura de banda de replicação delta necessária para lotes anteriores (VMs do Lote 1 e do Lote 2) e a largura de banda disponível para a replicação inicial do Lote 3 e assim por diante. Assim que a replicação inicial de todos os lotes for concluída, a última barra mostrará a largura de banda necessária para a replicação delta de todas as VMs protegidas.

**Por que eu preciso de replicação inicial de envio em lote?**
O tempo de conclusão da replicação inicial é baseado no tamanho do disco da VM, espaço em disco usado e na taxa de transferência de rede disponível. O detalhe está disponível no envio em lote do IR para uma planilha RPO selecionada.

### <a name="cost-estimation"></a>Estimativa de custo
O gráfico mostra a exibição de resumo do custo total de DR (recuperação de desastre) estimado para o Azure da sua região de destino escolhida e da moeda que você especificou para a geração de relatórios.

![Resumo de estimativa de custo](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

O resumo ajuda a entender o custo que precisa ser pago para armazenamento, computação, rede e licenciamento ao proteger todas as suas VMs compatíveis para o Azure usando o Site Recovery. O custo é calculado para VMs compatíveis e não todas as VMs com criação de perfil. 
 
Você pode exibir o custo mensal ou anual. Saiba mais sobre [regiões de destino com suporte](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) e [moedas com suporte](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes**: o custo total da recuperação de desastre é dividido em quatro componentes: custos de licença de computação, armazenamento, rede e do Site Recovery. O custo é calculado com base no consumo incorrido durante a replicação e no momento da análise de DR. A computação, o armazenamento (premium e standard), a ExpressRoute/VPN que está configurada entre o site local e o Azure e a licença do Site Recovery são usados para os cálculos.

**Custo por estados**: o custo total da recuperação de desastre se baseia em categorias em dois estados diferentes, replicação e análise de DR. 

**Custo de replicação**: o custo incorrido durante a replicação. Abrange o custo de armazenamento, de rede e da licença do Site Recovery. 

**Custo de análise de recuperação de desastre**: o custo incorrido durante os failovers de teste. O Site Recovery gira máquinas virtuais durante o failover de teste. O custo de análise de análise de risco abrange os custos de computação e armazenamento das VMs em execução. 

**Custo de Armazenamento do Azure por mês/ano**: o gráfico de barras mostra o custo total de armazenamento incorrido no armazenamento standard e premium para replicação e análise de DR. Você pode exibir a análise de custo detalhada por VM na planilha [Estimativa de Custo](hyper-v-deployment-planner-cost-estimation.md).

### <a name="growth-factor-and-percentile-values-used"></a>Fator de crescimento e valores de percentil usados
Esta seção na parte inferior da planilha mostra o valor percentual usado para todos os contadores de desempenho das VMs com perfil (o padrão é o 95° percentil). Ele também mostra o fator de crescimento (o padrão é 30%) usado em todos os cálculos.

![Fator de crescimento e valores de percentil usados](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações com largura de banda disponível como entrada
![Visão geral de criação de perfil com entrada de largura de banda](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Pode haver uma situação em que você saiba que não é possível definir uma largura de banda de mais de x Mbps para replicação do Site Recovery. Você pode usar a ferramenta para inserir a largura de banda disponível (usando o parâmetro -Bandwidth durante a geração de relatórios) e obter o RPO possível em minutos. Com esse valor de RPO que pode ser obtido, você pode decidir se precisa provisionar largura de banda adicional ou se está satisfeito com uma solução de recuperação de desastre com este RPO.

![RPO alcançável](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Recomendação de posicionamento do armazenamento de VM 
![Posicionamento de VM-Storage](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Tipo de Armazenamento de Disco**: uma conta de armazenamento standard ou premium, que é usada para replicar todas as VMs correspondentes mencionadas na coluna **VMs para Posicionar**.

**Prefixo Sugerido**: o prefixo de três caracteres sugerido que pode ser usado para nomear a conta de armazenamento. Você pode usar seu próprio prefixo, mas sugestão da ferramenta segue a [convenção de nomenclatura de partição para contas de armazenamento](/en-in/azure/storage/blobs/storage-performance-checklist).

**Nome de Conta Sugerido**: o nome de conta de armazenamento depois que você inclui o prefixo sugerido. Substitua o nome entre colchetes angulares (< e >) por sua entrada personalizada.

**Conta de Armazenamento de Log:**: todos os logs de replicação são armazenados em uma conta de armazenamento standard. Para VMs que são replicadas para uma conta de armazenamento premium, configure uma conta de armazenamento standard adicional para o armazenamento de log. Uma conta de armazenamento de log standard pode ser usada por várias contas de armazenamento de replicação premium. VMs replicadas para contas de armazenamento standard usam a mesma conta de armazenamento para logs.

**Nome de Conta de Log Sugerido**: o nome de conta de armazenamento de log depois que você inclui o prefixo sugerido. Substitua o nome entre colchetes angulares (< e >) por sua entrada personalizada.

**Resumo de Posicionamento**: um resumo da carga total das VMs na conta de armazenamento no momento da replicação e do failover ou failover de teste. O resumo inclui:

* O número total de VMs mapeadas para a conta de armazenamento. 
* O IOPS total de leitura/gravação em todas as VMs que estão sendo colocadas na conta de armazenamento.
* O IOPS de gravação (replicação) total.
* O tamanho total de instalação em todos os discos.
* Número total de discos.

**VMs para Posicionar**: uma lista de todas as VMs que devem ser posicionadas na conta de armazenamento específica para obter o melhor desempenho e uso.

## <a name="compatible-vms"></a>VMs compatíveis
O relatório do Excel gerado pelo Planejador de Implantações do Site Recovery fornece todos os detalhes de VMs compatíveis na planilha "VMs compatíveis".

![VMs compatíveis](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**Nome da VM**: o nome da VM que é usado em VMListFile quando um relatório é gerado. Essa coluna também lista os discos (VHDs) que estão anexados às VMs. Os nomes incluem os nomes de host de Hyper-V em que as VMs foram colocadas quando a ferramenta as descobriu durante o período de criação de perfil.

**Compatibilidade de VM**: os valores são **Sim** e **Sim**\*. **Sim** \* é para instâncias nas quais a VM é uma opção para [SSDs Premium](../virtual-machines/disks-types.md). Aqui, a alta variação com criação de perfil ou o disco IOPS se ajustam em um tamanho de disco premium maior que o tamanho mapeado para o disco. A conta de armazenamento decide para qual tipo de disco de armazenamento premium um disco deve ser mapeado, com base em seu tamanho: 
* <128 GB é P10.
* 128 GB a 256 GB é um P15.
* 256 GB a 512 GB é um P20.
* 512 GB a 1024 GB é P30.
* 1025 GB a 2048 GB é P40.
* 2049 GB a 4095 GB é P50.

Por exemplo, se as características de carga de trabalho de um disco o colocarem na categoria P20 ou p30, mas o tamanho o mapear para um tipo de disco de armazenamento Premium inferior, a ferramenta marcará essa VM como **Sim** \* . A ferramenta também recomenda que você altere o tamanho do disco de origem para se ajustar ao tipo de disco de armazenamento premium recomendado ou altere o tipo de disco de destino após o failover.

**Tipo de armazenamento**: standard ou premium.

**Prefixo Sugerido**: o prefixo de conta de armazenamento com três caracteres.

**Conta de Armazenamento**: o nome que usa o prefixo de conta de armazenamento sugerido.

**IOPS de L/G de pico (com Fator de Crescimento)**: IOPS de leitura/gravação de carga de trabalho de pico no disco (o padrão é o 95º percentil), junto com o fator de crescimento futuro (o padrão é 30%). O IOPS total de leitura/gravação de uma VM nem sempre é a soma dos IOPS de leitura/gravação dos discos individuais da VM. A IOPS de leitura/gravação da VM é o pico da soma dos IOPS de leitura/gravação dos discos individuais durante cada minuto do período da criação de perfil.

**Variação de dados de pico em MB/s (com Fator de Crescimento)**: a taxa de variação de pico no disco (o padrão é o 95º percentil), junto com o fator de crescimento futuro (o padrão é 30%). A variação de dados total da VM nem sempre é a soma da variação de dados dos discos individuais da VM. O pico de variação dos dados da VM é o pico da soma da variação dos seus discos individuais durante cada minuto do período de criação de perfil.

**Tamanho de VM do Azure**: o tamanho ideal de VM mapeada dos Serviços de Nuvem do Azure para essa VM local. O mapeamento é baseado na memória da VM local, no número de discos/núcleos/NICs e IOPS de leitura/gravação. A recomendação é sempre o menor tamanho de VM do Azure que corresponde a todas as características da VM local.

**Número de discos**: o número total de discos de máquina virtual (VHDs) na VM.

**Tamanho do disco (GB)**: o tamanho total de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos**: o número de núcleos de CPUs na VM.

**Memória (MB)**: RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: o tipo de inicialização da VM. Pode ser o BIOS ou EFI.

## <a name="incompatible-vms"></a>VMs incompatíveis
O relatório do Excel gerado pelo Planejador de Implantações do Site Recovery fornece todos os detalhes de VMs incompatíveis na planilha "VMs incompatíveis".

![VMs incompatíveis](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**Nome da VM**: o nome da VM que é usado em VMListFile quando um relatório é gerado. Essa coluna também lista os discos (VHDs) que estão anexados às VMs. Os nomes incluem os nomes de host de Hyper-V em que as VMs foram colocadas quando a ferramenta as descobriu durante o período de criação de perfil.

**Compatibilidade de VM**: indica por que a VM específica é incompatível com o Site Recovery. Os motivos são descritos para cada disco incompatível da VM e, com base nos [limites de armazenamento](/en-in/azure/storage/common/scalability-targets-standard-account) publicados, podem ser qualquer um dos seguintes:

* O tamanho do disco é maior que 4095 GB. Atualmente, o Armazenamento do Azure não dá suporte a tamanhos de disco de dados maiores que 4095 GB.

* O disco do sistema operacional é maior que 2047 GB para a VM de Geração 1 (tipo de inicialização BIOS). O Site Recovery não dá suporte a tamanhos de disco do sistema operacional maiores que 2047 GB para VMs de Geração 1.

* O disco do sistema operacional é maior que 300 GB para a VM de Geração 2 (tipo de inicialização EFI). O Site Recovery não dá suporte a tamanhos de disco do sistema operacional maiores que 300 GB para VMs de Geração 2.

* Um nome de VM não tem suporte quando há um dos seguintes caracteres: "" [] '. A ferramenta não consegue obter dados de perfil de VMs que contenham algum desses caracteres em seus nomes. 

* O VHD é compartilhado por duas ou mais VMs. O Azure não dá suporte a VMs com um VHD compartilhado.

* Não há suporte a VMs com Virtual Fiber Channel. O Site Recovery não dá suporte a VMs com Virtual Fiber Channel.

* Um cluster Hyper-V não contém um agente de replicação. O Site Recovery não dá suporte a uma VM em um cluster Hyper-V caso o Agente de Replicação do Hyper-V não esteja configurado para o cluster.

* Uma VM não está altamente disponível. O Site Recovery não dá suporte a uma VM de nó do cluster Hyper-V cujos VHDs são armazenados no disco local e não no disco de cluster. 

* O tamanho total da VM (replicação + failover de teste) excede o limite de tamanho de conta de armazenamento premium com suporte (35 TB). Essa incompatibilidade normalmente ocorre quando um único disco na VM tem uma característica de desempenho que excede os limites máximo com suporte do Azure ou do Site Recovery para o armazenamento standard. Essa instância coloca a VM na zona de armazenamento premium. No entanto, o tamanho máximo com suporte de uma conta de armazenamento premium é 35 TB. Uma única VM protegida não pode ser protegida entre várias contas de armazenamento. 

    Quando um failover de teste é executado em uma VM protegida e caso um disco não gerenciado esteja configurado para o failover de teste, ele é executado na mesma conta de armazenamento onde a replicação está em andamento. Nessa instância, a mesma quantidade de espaço de armazenamento adicional é necessária, assim como a da replicação. Isso garante que a replicação continue e que o failover de teste tenha êxito em paralelo. Quando o disco gerenciado está configurado para o failover de teste, nenhum espaço adicional deve ser considerado com a VM do failover de teste.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 7.500 por disco.

* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 80.000 por VM.

* A variação média de dados da VM de origem excede o limite de variação de dados Site Recovery com suporte de 20 MB/s para o tamanho médio de e/s.

* O IOPS médio de gravação eficiente da VM de origem excede o limite com suporte de 840 de IOPS do Site Recovery.

* O armazenamento de instantâneos calculado excede o limite com suporte de 10 TB para armazenamento de instantâneos.

**IOPS de L/G de pico (com Fator de Crescimento)**: o pico de IOPS de carga de trabalho no disco (o padrão é o 95º percentil), junto com o fator de crescimento futuro (o padrão é 30%). O IOPS total de leitura/gravação da VM nem sempre é a soma dos IOPS de leitura/gravação dos discos individuais da VM. A IOPS de leitura/gravação da VM é o pico da soma dos IOPS de leitura/gravação dos discos individuais durante cada minuto do período da criação de perfil.

**Variação de dados de pico (MB/s) (com Fator de Crescimento)**: a taxa de variação de pico no disco (o padrão é o 95º percentil), junto com o fator de crescimento futuro (o padrão é 30%). Observe que a variação de dados total da VM nem sempre é a soma da variação de dados dos discos individuais da VM. O pico de variação dos dados da VM é o pico da soma da variação dos seus discos individuais durante cada minuto do período de criação de perfil.

**Número de discos**: o número total de VHDs na VM.

**Tamanho do disco (GB)**: o tamanho total da configuração de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos**: o número de núcleos de CPUs na VM.

**Memória (MB)**: a quantidade de RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: o tipo de inicialização da VM. Pode ser o BIOS ou EFI.

## <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery
A tabela a seguir fornece os limites do Site Recovery. Esses limites são baseados em testes, mas eles não podem abranger todas as combinações possíveis de E/S de aplicativos. Os resultados reais podem variar dependendo da combinação de E/S do aplicativo. Para obter os melhores resultados, mesmo após planejar a implantação, execute testes de aplicativos amplamente usando um failover de teste para obter a visão real do desempenho do aplicativo.
 
**Destino de armazenamento de replicação** | **Tamanho médio de e/s da VM de origem** |**Variação média de dados da VM de origem** | **Variação total de dados da VM de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s por VM | 168 GB por VM
Armazenamento Premium | 8 KB  | 5 MB/s por VM | 421 GB por VM
Armazenamento Premium | 16 KB ou mais| 20 MB/s por VM | 1684 GB por VM

Esses limites são números médios, pressupondo uma sobreposição de E/S de 30%. O Site Recovery pode lidar com uma maior taxa de transferência com base na taxa de sobreposição, em tamanhos maiores de gravação e em comportamento de E/S de carga de trabalho real. Os números anteriores pressupõem uma lista de pendências típica de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados, e um ponto de recuperação é criado dentro de cinco minutos.

## <a name="on-premises-storage-requirement"></a>Requisito de armazenamento local

A planilha fornece o requisito de espaço de armazenamento total livre para cada volume de servidores Hyper-V (onde os VHDs residem) para replicação inicial e delta com êxito. Antes de habilitar a replicação, adicione o espaço de armazenamento necessário nos volumes para fazer com que a replicação não cause nenhum tempo de inatividade indesejável em seus aplicativos de produção. 

  O Planejador de Implantações do Site Recovery identifica o requisito de espaço de armazenamento ideal baseado no tamanho dos VHDs e da largura de banda de rede usada para replicação.

![Requisito de armazenamento local](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Por que preciso de espaço livre no servidor Hyper-V para a replicação?
* Ao habilitar a replicação de uma VM, o Site Recovery tira um instantâneo de cada VHD da VM para a replicação inicial. Enquanto a replicação inicial está em andamento, novas alterações são gravadas nos discos pelo aplicativo. O Site Recovery acompanha essas alterações delta nos arquivos de log, o que requer espaço de armazenamento adicional. Os arquivos de log ficam armazenados localmente até que a replicação inicial seja concluída. 

    Se não houver espaço suficiente para os arquivos de log e o instantâneo (AVHDX), a replicação entrará no modo de ressincronização e nunca será concluída. Na pior das hipóteses, é necessário um espaço livre adicional de 100% do tamanho do VHD para a replicação inicial.
* Quando a replicação inicial termina, a replicação delta é iniciada. O Site Recovery acompanha essas alterações delta nos arquivos de log, os quais são armazenados no volume onde residem os VHDs da VM. Esses arquivos de log são replicados para o Azure em uma frequência de cópia configurada. Com base na largura de banda de rede disponível, os arquivos de log levam certo tempo para serem replicados para o Azure. 

    Se não houver espaço livre suficiente para armazenar os arquivos de log, a replicação ficará em pausa. Em seguida, o status de replicação da máquina virtual entrará em "ressincronização necessária".
* Se a largura de banda de rede não for suficiente para efetuar o push dos arquivos de log para o Azure, esses arquivos serão empilhados no volume. Na pior das hipóteses, quando o tamanho dos arquivos de log é aumentado para 50% do tamanho do VHD, a replicação da VM entra em “ressincronização necessária”. Na pior das hipóteses, é necessário um espaço livre adicional de 50% do tamanho do VHD para a replicação delta.

**Host Hyper-V**: a lista de servidores Hyper-V com criação de perfil. Se um servidor fizer parte de um cluster Hyper-V, todos os nós de cluster serão agrupados juntos.

**Volume (caminho do VHD)**: cada volume de um host Hyper-V em que os VHDs/VHDXs estão presentes. 

**Espaço livre disponível (GB)**: o espaço livre disponível no volume.

**Espaço de armazenamento total necessário no volume (GB)**: o espaço livre de armazenamento total necessário no volume para replicação inicial e Delta com êxito. 

**Total de armazenamento adicional a ser provisionado no volume para replicação com êxito (GB)**: recomenda o espaço total adicional que deve ser provisionado no volume para replicação inicial e delta com êxito.

## <a name="initial-replication-batching"></a>Envio em lote da replicação inicial 

### <a name="why-do-i-need-initial-replication-batching"></a>Por que eu preciso de replicação inicial de envio em lote?
Se todas as máquinas virtuais forem protegidas ao mesmo tempo, o requisito de armazenamento livre será muito maior. Se não houver armazenamento suficiente disponível, a replicação das VMs entrará no modo de ressincronização. Além disso, o requisito de largura de banda de rede é muito maior para concluir com êxito a replicação inicial de todas as VMs em conjunto. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Envio em lote da replicação inicial para um RPO selecionado
Esta planilha fornece a exibição de detalhes de cada lote para IR. Para cada RPO, é criada uma planilha separada de envio em lote de IR. 

Depois de seguir a recomendação de requisito de armazenamento local para cada volume, a principal informação que precisa ser replicada é a lista de VMs que podem ser protegidas em paralelo. Essas VMs são agrupadas em um lote, e pode haver vários lotes. Você deve proteger as VMs na ordem de lotes fornecida. Primeiro, proteja as VMs do Lote 1. Depois que a replicação inicial for concluída, proteja as VMs do Lote 2 e assim por diante. Você pode obter a lista de lotes e VMs correspondentes nesta planilha. 

![Detalhes de loteamento da IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Detalhes adicionais de loteamento da IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Cada lote fornece as seguintes informações 
**Host Hyper-V**: o host Hyper-V da VM a ser protegida.

**Máquina virtual**: a VM a ser protegida. 

**Comentários**: se nenhuma ação for necessária para um volume específico de uma VM, o comentário será fornecido aqui. Por exemplo, se não houver espaço livre suficiente em um volume, o comentário dirá “Adicionar armazenamento extra para proteger essa VM”.

**Volume (caminho do VHD)**: o nome do volume onde residem os VHDs da VM. 

**Espaço livre disponível no volume (GB)**: o espaço livre em disco disponível no volume para a VM. Ao calcular o espaço livre nos volumes, considera-se o espaço em disco usado para a replicação delta pelas VMs dos lotes anteriores cujo VHDs estão no mesmo volume. 

Por exemplo, VM1, VM2 e VM3 residem em um volume, digamos, E:\VHDpath. Antes da replicação, o espaço livre no volume é de 500 GB. VM1 é parte do Lote 1, VM2 é parte do Lote 2 e VM3 é parte do Lote 3. Para a VM1, o espaço livre disponível é de 500 GB. Para a VM2, o espaço livre disponível é 500, o espaço em disco necessário para a replicação delta da VM1. Digamos que a VM1 requer 300 GB de espaço para a replicação delta; o espaço livre disponível para a VM2 é 500 GB - 300 GB = 200 GB. De modo semelhante, a VM2 requer 300 GB para a replicação delta. O espaço livre disponível para a VM3 é 200 GB - 300 GB = -100 GB.

**Armazenamento necessário no volume para replicação inicial (GB)**: o espaço livre de armazenamento necessário no volume para a replicação inicial da VM.

**Armazenamento necessário no volume para replicação delta (GB)**: o espaço livre de armazenamento necessário no volume para a VM para replicação delta.

**Armazenamento adicional necessário baseado no déficit para evitar falhas de replicação (GB)**: o espaço de armazenamento adicional necessário no volume para a VM. É o máximo de requisito de espaço de armazenamento da replicação inicial e da replicação delta menos o espaço livre disponível no volume.

**Largura de banda mínima necessária para a replicação inicial (Mbps)**: a largura de banda mínima necessária para a replicação inicial da VM.

**Largura de banda mínima necessária para a replicação delta (Mbps)**: a largura de banda mínima necessária para a replicação delta para a VM.

### <a name="network-utilization-details-for-each-batch"></a>Detalhes de utilização de rede para cada lote 
Cada tabela de lote fornece um resumo da utilização de rede do lote.

**Largura de banda disponível para o lote**: a largura de banda disponível para o lote depois de considerar a largura de banda de replicação delta do lote anterior.

**Largura de banda aproximada disponível para a replicação inicial do lote**: a largura de banda disponível para a replicação inicial das VMs do lote. 

**Largura de banda aproximada consumida para a replicação delta do lote**: a largura de banda necessária para a replicação delta das VMs do lote. 

**Tempo estimado de replicação inicial para o lote (hh: mm)**: o tempo estimado de replicação inicial em horas: minutos.



## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [estimativa de custo](hyper-v-deployment-planner-cost-estimation.md).