---
title: Examinar estimativas de custo no Planejador de Implantações do Azure Site Recovery
description: Este artigo descreve como examinar as estimativas de custo no Planejador de Implantações do Azure Site Recovery para a recuperação de desastres do VMware.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: ramamill
ms.openlocfilehash: dba585fbadca479c146ad42ac1bc5aa9d5349f49
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186698"
---
# <a name="review-cost-estimations-in-the-vmware-deployment-planner"></a>Examinar estimativas de custo no Planejador de Implantações VMware 

O relatório do Planejador de Implantações fornece o resumo de estimativa de custo nas planilhas de [Recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) e a análise de custo detalhada na planilha Estimativa de Custo. Ele tem a análise de custo detalhada por VM. 

>[!Note]
>A versão atual da ferramenta planejador de implantação v 2.5 fornece estimativa de custo para replicações de VMs para Managed Disks.

### <a name="cost-estimation-summary"></a>Resumo de estimativa de custo 
O gráfico mostra a exibição de resumo do custo total de recuperação de desastre (DR) estimado para o Azure da sua região de destino escolhida e da moeda que você especificou para a geração de relatórios.
Resumo de estimativa de custo

![Resumo de estimativa de custo](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

O resumo ajuda a entender o custo que precisa ser pago para armazenamento, computação, rede e licença ao proteger todas as suas VMs compatíveis para o Azure usando o Azure Site Recovery. O custo é calculado sobre as VMs compatíveis e não todas as VMs com criação de perfil.  
 
Você pode exibir o custo mensal ou anual. Saiba mais sobre [regiões de destino com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e [moedas com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes** O custo total da recuperação de desastre é dividido em quatro componentes: custos de licença de Computação, Armazenamento, Rede e do Azure Site Recovery. O custo é calculado com base no consumo incorrido durante a replicação e em tempo de análise de recuperação de desastre para computação, armazenamento (premium e standard), ExpressRoute/VPN configurado entre o site local e o Azure, e a licença do Azure Site Recovery.

**Custo por estados** O custo total da recuperação de desastre (DR) se baseia em categorias em dois estados diferentes - Replicação e análise de recuperação de desastre. 

**Custo de replicação** : o custo incorrido durante a replicação. Abrange o custo de armazenamento, de rede e de licença do Azure Site Recovery. 

**Custo de análise de DR** : o custo incorrido durante os failovers de teste. O Azure Site Recovery gira máquinas virtuais durante o failover de teste. O custo de análise de análise de risco abrange os custos de computação e armazenamento das VMs em execução. 

**Custo de armazenamento do Azure por mês/ano** Mostra o custo total de armazenamento incorrido no armazenamento standard e premium para replicação e análise de recuperação de desastre.

## <a name="detailed-cost-analysis"></a>Análise de custo detalhada
Os preços do Azure para computação, armazenamento, rede etc. varia entre as regiões do Azure. Você pode gerar um relatório de estimativa de custo com os preços do Azure mais recentes com base em sua assinatura, a oferta associada à sua assinatura e para a região do Azure de destino especificada na moeda especificada. Por padrão, a ferramenta usa a região Oeste dos EUA 2 do Azure e a moeda dólar norte-americano (USD). Se você tiver usado qualquer região e moeda, na próxima vez em que gerar um relatório sem ID da assinatura, ID da oferta, região de destino e moeda, ele usará os preços da última região de destino utilizada e a moeda usada pela última vez para a estimativa de custo.
Esta seção mostra a ID da assinatura e a ID da oferta que você usou para geração de relatórios.  Se não usado, ele estará em branco.

Em todo o relatório, as células marcadas em cinza são somente leitura. As células em branco podem ser modificadas de acordo com seus requisitos.

![Detalhes da estimativa de custo1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Custo geral de recuperação de desastre por componentes
A primeira seção mostra o custo geral da recuperação de desastre pelos componentes e o custo de recuperação de desastre por estados. 

**Computação** : custo de VMs IaaS executadas no Azure para as necessidades de recuperação de desastre. Ele inclui as VMs criadas pelo Azure Site Recovery durante as análises de recuperação de desastre (failovers de teste) e as VMs em execução no Azure, como o SQL Server com Grupos de Disponibilidade AlwaysOn/Servidores de Nome de Domínio.

**Armazenamento** : custo do consumo de armazenamento do Azure para as necessidades de recuperação de desastre. Ele inclui o consumo de armazenamento para a replicação e durante os exercícios de recuperação de desastre.
Rede: custo de ExpressRoute e da VPN Site a Site para as necessidades de recuperação de desastre. 

**Licença do ASR** : custo da licença do Azure Site Recovery para todas as VMs compatíveis. Se você tiver inserido manualmente uma VM na tabela de análise de custo detalhada, o custo da licença do Azure Site Recovery também estará incluído para a VM.

### <a name="overall-dr-cost-by-states"></a>Custo geral de recuperação de desastre por estados
O custo total da recuperação de desastre é categorizado com base em dois estados diferentes - a replicação e a análise de recuperação de desastre.

**Custo de replicação** : o custo incorre no momento da replicação. Abrange o custo de armazenamento, de rede e de licença do Azure Site Recovery. 

**Custo de análise de recuperação de desastre** : o custo incorre no momento da análise de recuperação de desastre. O Azure Site Recovery gira máquinas virtuais durante a análise de recuperação de desastre. O custo de análise de recuperação de desastre abrange os custos de computação e armazenamento das VMs em execução.
Duração total da análise de recuperação de desastre em um ano = Número de análises de recuperação de desastre x Duração de análise de cada recuperação de desastre (dias) Custo médio de análise de recuperação de desastre (por mês) = Custo total de análise de recuperação de desastre/12

### <a name="storage-cost-table"></a>Tabela de custo de armazenamento:
Esta tabela mostra o custo de armazenamento premium e standard incorrido para replicação e análises de recuperação de desastre com e sem desconto.

### <a name="site-to-azure-network"></a>Site para rede do Azure
Selecione a configuração apropriada de acordo com seus requisitos. 

**ExpressRoute** : por padrão, a ferramenta seleciona o plano de ExpressRoute mais próximo que corresponda à largura de banda de rede necessária para a replicação delta. Você pode alterar o plano de acordo com suas necessidades.

**Gateway de VPN** : selecione o Gateway de VPN se você tiver algum em seu ambiente. Por padrão, é NA.

**Região de destino** : região do Azure especificada para Dr. O preço usado no relatório de computação, armazenamento, rede e licença baseia-se nos preços do Azure para essa região. 

### <a name="vm-running-on-azure"></a>VM em execução no Azure
Se você tiver qualquer controlador de domínio ou VM de DNS ou VM do SQL Server com Grupos de Disponibilidade AlwaysOn em execução no Azure para recuperação de desastre, poderá fornecer o número de máquinas virtuais e o tamanho para considerar os custos de computação no custo total de recuperação de desastre. 

### <a name="apply-overall-discount-if-applicable"></a>Aplicar desconto geral, se aplicável
Se você for um cliente ou um parceiro do Azure e estiver qualificado para qualquer desconto no preço geral do Azure, poderá usar esse campo. A ferramenta aplica o desconto (%) a todos os componentes.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Número de tipos de máquinas virtuais e custo de computação (por ano)
Esta tabela mostra o número de VMs do Windows e não Windows e o custo de computação de análise de recuperação de desastre para eles.

### <a name="settings"></a>Configurações 

**Moeda** : a moeda na qual o relatório é gerado. Duração de custo: você pode exibir todos os custos do mês ou do ano inteiro. 

## <a name="detailed-cost-analysis-table"></a>Tabela de análise detalhada de custo
![Análise de custo detalhada](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) A tabela lista a divisão de custo para cada VM compatível. Você também pode usar essa tabela para obter o custo estimado de recuperação de desastre do Azure de VMs não analisadas ao adicionar as máquinas virtuais manualmente. Isso será útil em casos onde é necessário estimar os custos do Azure para uma nova implantação de recuperação de desastre sem a execução de uma análise detalhada.
Para adicionar manualmente as máquinas virtuais: 
1.  Clique no botão 'Inserir linha' para inserir uma nova linha entre as linhas Início e Término.

2.  Preencha as colunas a seguir com base no tamanho aproximado da VM e no número de máquinas virtuais que correspondem a essa configuração: 

* Número de VMs, tamanho de IaaS (Sua seleção)
* Tipo de Armazenamento (Standard/Premium)
* Tamanho de armazenamento total da VM (GB) do computador de origem
* Número de detalhamentos de recuperação de desastre em um ano 
* Duração de cada detalhamento de recuperação de desastre (Dias) 
* Tipo de sistema operacional
* Redundância de dados 
* Benefício Híbrido do Azure

1. Você pode aplicar o mesmo valor a todas as VMs na tabela clicando no botão 'Aplicar a todos' para Número de Análises de Recuperação de Desastre em um ano, Duração da Análise de cada Recuperação de Desastre (Dias), Redundância de dados e Benefício Híbrido do Azure.

1. Clique em 'Recalcular custo' para atualizar o custo.

**Nome da VM** : o nome da VM.

**Número de VMs** : o número de VMs que correspondem à configuração. Você pode atualizar o número de VMs existentes se as VMs de configuração semelhantes não tiverem sido analisadas mas ainda assim serão protegidas.

**Tamanho de IaaS (Recomendação)** : é o tamanho da função de VM da máquina virtual compatível recomendado pela ferramenta. 

**Tamanho de IaaS (Sua seleção)** : por padrão, é igual ao tamanho de função de VM recomendado. Você pode alterar a função com base no requisito. O custo de computação se baseia no tamanho da função de VM selecionada.

**Tipo de armazenamento** : o tipo de armazenamento usado pela máquina virtual. É armazenamento standard ou premium.

**Tamanho total de armazenamento da VM (GB)** : o armazenamento total da VM de origem.

**Número de análises de recuperação de desastre em um ano** : o número de vezes que você executa análises de recuperação de desastre em um ano. Por padrão, é quatro vezes ao ano. Você pode modificar o período de VMs específicas ou aplicar o novo valor a todas as VMs inserindo o novo valor na linha superior e clicando no botão 'Aplicar a todos'. Com base no número de Análises de Recuperação de Desastre e do período de duração de cada Análise de Recuperação de Desastre, o custo total da Análise de Recuperação de Desastre é calculado.  

**Duração de cada análise de recuperação de desastre (Dias)** : a duração de cada análise de recuperação de desastre. Por padrão, é de sete dias a cada 90 dias, de acordo com o [benefício do Software Assurance de Recuperação de Desastre](https://azure.microsoft.com/pricing/details/site-recovery). Você pode modificar o período de VMs específicas ou pode aplicar um novo valor a todas as VMs inserindo o novo valor na linha superior e clicando no botão 'Aplicar a todos'. O custo total da Análise de Recuperação de Desastre é calculado com base no número de análises de recuperação de desastre em um ano e no período de duração de cada Análise de Recuperação de Desastre.
  
**Tipo de sistema operacional** : o tipo de SO da VM. É Windows ou Linux. Se o tipo de sistema operacional for Windows, o Benefício Híbrido do Azure poderá ser aplicado a essa VM. 

**Redundância de dados** : pode ser um destes itens - Armazenamento localmente redundante (LRS), Armazenamento com redundância geográfica (GRS) ou Armazenamento com redundância geográfica com acesso de leitura (RA-GRS). O padrão é LRS. Você pode alterar o tipo com base em sua conta de armazenamento para máquinas virtuais específicas ou pode aplicar o novo tipo a todas as VMs alterando o tipo da linha superior e clicando no botão 'Aplicar a todos'.  O custo de armazenamento para a replicação é calculado com base no preço de redundância de dados que você selecionou. 

**Benefício Híbrido do Azure** : é possível aplicar o Benefício Híbrido do Azure a VMs do Windows, se aplicável.  O padrão é Sim. Você pode alterar a configuração de máquinas virtuais específicas ou atualizar todas as máquinas virtuais clicando no botão 'Aplicar a todos'.

**Consumo total do Azure** : inclui o custo de computação, armazenamento e licença do Azure Site Recovery para a recuperação de desastre. Com base em sua seleção, mostra o custo mensal ou anual.

**Custo de replicação de estado estacionário** : inclui o custo de armazenamento para replicação.

**Custo total de análise de recuperação de desastre (média)** : inclui o custo de computação e de armazenamento para análise de recuperação de desastre.

**Custo da licença do ASR** : custo da licença do Azure Site Recovery.

## <a name="supported-target-regions"></a>Regiões de destino com suporte
O Planejador de Implantações do Azure Site Recovery fornece uma estimativa de custo para as seguintes regiões do Azure. Se sua região não estiver listada abaixo, você poderá usar qualquer uma das seguintes regiões com o preço mais próximo de sua região.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Moedas com suporte
O Planejador de Implantações do Azure Site Recovery pode gerar o relatório de custo com qualquer uma das moedas a seguir.

|Moeda|Name|Moeda|Name|Moeda|Name|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentino ($)|AUD|Dólar australiano ($)|BRL|Real brasileiro (R$)|
|CAD|Dólar canadense ($)|CHF|Franco suíço. (chf)|DKK|Coroa dinamarquesa (kr)|
|EUR|Euro (€)|GBP|Libras esterlinas (£)|HKD|Dólar de Hong Kong (HK$)|
|IDR|Rúpia da Indonésia (Rp)|INR|Rúpia indiana (₹)|JPY|Iene japonês (¥)|
|KRW|Won coreano (₩)|MXN|Peso mexicano (MXN$)|MYR|Ringgit malaio (RM$)|
|NOK|Coroa norueguesa (kr)|NZD|Dólar neozelandês ($)|RUB|Rublo russo (руб)|
|SAR|Rial saudita (SR)|SEK|Coroa sueca (kr)|TWD|Dólar taiwanês (NT$)|
|TRY|Lira turca (TL)|USD| Dólar americano (US$)|ZAR|Rand da África do Sul (R)|

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como proteger [VMs do VMware para o Azure usando o Azure Site Recovery](./vmware-azure-tutorial.md).
