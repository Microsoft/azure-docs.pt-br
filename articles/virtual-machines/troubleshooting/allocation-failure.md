---
title: Solução de problemas de falhas de alocação de VM do Azure| Microsoft Docs
description: Solução de problemas de falha de alocação quando você cria, reinicia ou redimensiona uma VM no Azure
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: daberry
ms.openlocfilehash: 79bc043a991404a3ee9da954b9639bf1a41f2c51
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365866"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Solução de problemas de falha de alocação quando você cria, reinicia ou redimensiona VMs no Azure

Quando você cria uma VM (máquina virtual), reinicia VMs paradas (desalocadas) ou redimensiona uma VM, o Microsoft Azure aloca recursos de computação para sua assinatura. Estamos continuamente investindo em infraestrutura e recursos adicionais para garantir que sempre tenhamos todos os tipos de VM disponíveis para dar suporte à demanda do cliente. No entanto, você pode enfrentar ocasionalmente falhas de alocação de recursos devido ao crescimento sem precedentes da demanda por serviços do Azure em regiões específicas. Esse problema pode ocorrer quando você tenta criar ou iniciar VMs em uma região, enquanto as VMs exibem o seguinte código de erro e a seguinte mensagem:

**Código de erro** : AllocationFailed ou ZonalAllocationFailed

**Mensagem de erro** : "Falha na alocação. Não temos capacidade suficiente para o tamanho de VM solicitado nesta região. Leia mais sobre como melhorar a probabilidade de sucesso de alocação em https: \/ /aka.ms/Allocation-Guidance "

> [!NOTE]
> Se você estiver solucionando problemas de um conjunto de dimensionamento de máquinas virtuais (VMSS), o processo será o mesmo que uma VM padrão. Para resolver o problema, você deve seguir as instruções neste artigo.
> 
>**Mensagem de erro** : "Falha na alocação. Se você estiver tentando adicionar uma nova VM a um conjunto de dimensionamento de máquinas virtuais com um único grupo de posicionamento ou atualizar/redimensionar uma VM existente em um conjunto de dimensionamento de máquinas virtuais com um único grupo de posicionamento, observe que tal alocação tem como escopo um único cluster e é possível que o cluster esteja sem capacidade. Leia mais sobre como melhorar a probabilidade de sucesso de alocação em http: \/ /aka.ms/Allocation-Guidance. "

Este artigo explica as causas de algumas das falhas de alocação mais comuns e sugere possíveis correções.

Se o problema do Azure não for resolvido neste artigo, visite os [fóruns do Azure no MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou usando @AzureSupport no Twitter. Além disso, você pode registrar uma solicitação de suporte do Azure selecionando Obter suporte no site de [suporte do Azure](https://azure.microsoft.com/support/options/).

Até o tipo de VM preferencial estar disponível em sua região preferida, aconselhamos que os clientes que encontrarem problemas de implantação considerem a diretriz na tabela a seguir como uma solução alternativa. 

Identifique o cenário que melhor descreva seu caso e, em seguida, tente novamente a solicitação de alocação usando a solução alternativa sugerida correspondente para aumentar a probabilidade de sucesso de alocação. Como alternativa, você pode sempre tentar novamente mais tarde. Isso ocorre porque recursos suficientes podem ter sido liberados no cluster, região ou zona para acomodar a solicitação. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Redimensionar uma VM ou adicionar VMs a um conjunto de disponibilidade existente

### <a name="cause"></a>Causa

A solicitação de redimensionar ou adicionar uma VM a um conjunto de disponibilidade existente deve ser tentada no cluster original que hospeda esse conjunto. O tamanho da VM solicitada é suportado pelo cluster, mas o cluster pode não ter capacidade suficiente no momento. 

### <a name="workaround"></a>Solução alternativa

Quando a VM puder fazer parte de um conjunto de disponibilidade diferente, crie uma VM em um conjunto de disponibilidade diferente (na mesma região). Essa nova VM pode ser adicionada à mesma rede virtual.

Pare (desaloque) todas as VMs no mesmo conjunto de disponibilidade e reinicie cada uma delas.
Para parar: clique em Grupos de recursos > [seu grupo de recursos] > Recursos > [seu conjunto de disponibilidade] > Máquinas Virtuais > [sua máquina virtual] > Parar.
Depois de parar todas as VMs, selecione a primeira VM e clique em Iniciar.
Essa etapa garante que uma nova tentativa de alocação seja executada e que um novo cluster com capacidade suficiente possa ser selecionado.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)

### <a name="cause"></a>Causa

A desalocação parcial significa que você parou (desalocou) uma ou mais, mas não todas, VMs em um conjunto de disponibilidade. Quando você desaloca uma VM, os recursos associados são liberados. Reiniciar as VMs em um conjunto de disponibilidade parcialmente desalocado é o mesmo que adicionar VMs a um conjunto de disponibilidade existente. Portanto, a solicitação de alocação deve ser tentada no cluster original que hospeda o grupo de disponibilidade existente que pode não ter capacidade suficiente.

### <a name="workaround"></a>Solução alternativa

Pare (desaloque) todas as VMs no mesmo conjunto de disponibilidade e reinicie cada uma delas.
Para parar: clique em Grupos de recursos > [seu grupo de recursos] > Recursos > [seu conjunto de disponibilidade] > Máquinas Virtuais > [sua máquina virtual] > Parar.
Depois de parar todas as VMs, selecione a primeira VM e clique em Iniciar.
Isso garantirá que uma nova tentativa de alocação seja executada e que um novo cluster com capacidade suficiente possa ser selecionado.

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs totalmente paradas (desalocadas)

### <a name="cause"></a>Causa

A desalocação total significa que você parou (desalocou) todas as VMs em um conjunto de disponibilidade. A solicitação de alocação para reiniciar essas VMs se destinará a todos os clusters compatíveis com o tamanho desejado dentro da região ou da zona. Altere sua solicitação de alocação de acordo com as sugestões deste artigo e repita a solicitação para aumentar a chance de sucesso de alocação. 

### <a name="workaround"></a>Solução alternativa

Se você usar séries ou tamanhos de VM mais antigos, como Dv1, DSv1, Av1, D15v2 ou DS15v2, considere a migração para versões mais recentes. Consulte essas recomendações para tamanhos específicos de VM.
Se você não tiver a opção de usar um tamanho de VM diferente, tente implantar em uma região diferente dentro a mesma área geográfica. Para obter mais informações sobre tamanhos de VM disponíveis em cada região em https://aka.ms/azure-regions

Se você estiver usando zonas de disponibilidade, tente outra zona dentro da região que pode ter a capacidade disponível para o tamanho solicitado de VM.

Se sua solicitação de alocação for grande (mais de 500 núcleos), consulte o guia nas seções a seguir para dividir a solicitação em implantações menores.

Tente [reimplantar a VM](./redeploy-to-new-node-windows.md). Reimplantar a VM aloca a VM para um novo cluster dentro da região.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Falhas de alocação para tamanhos de VM mais antigos (Av1, Dv1, DSv1, D15v2, DS15v2 etc.)

À medida que expandimos a infraestrutura do Azure, nós implantamos hardware de geração mais recente projetado para dar suporte aos tipos de máquina virtual mais recentes. Algumas das VMs de séries mais antigas não serão executadas em nossa infraestrutura de geração mais recente. Por esse motivo, os clientes podem ocasionalmente enfrentar falhas de alocação para essas SKUs herdadas. Para evitar esse problema, recomendamos que os clientes que estão usando máquinas virtuais de séries legadas considerem a migração para as VMs mais recentes equivalentes de acordo com as seguintes recomendações: essas VMs são otimizadas para o hardware mais recente e permitirão que você aproveite as vantagens de preços e desempenho melhores. 

|Série/tamanho de VM herdada|Série/tamanho recomendado de VM mais nova|Mais informações|
|----------------------|----------------------------|--------------------|
|Série Av1|[Série Av2](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Série Dv1 ou DSv1 (D1 a D5)|[Série Dv3 ou DSv3](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Série Dv1 ou DSv1 (D11 a D14)|[Série Ev3 ou ESv3](../ev3-esv3-series.md)|
|D15v2 ou DS15v2|Se você estiver usando o modelo de implantação do Gerenciador de Recursos para aproveitar as vantagens dos tamanhos de VM maiores, considere a migração para D16v3/DS16v3 ou D32v3/DS32v3. Eles são projetados para execução em hardware de geração mais recente. Se você estiver usando o modelo de implantação do Gerenciador de Recursos para se certificar de que sua instância de VM esteja isolada para o hardware dedicado a um único cliente, considere a migração para os novos tamanhos de VM isolados, E64i_v3 ou E64is_v3, que são projetados para execução em hardware de geração mais recente. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Falhas de alocação para implantações grandes (mais de 500 núcleos)

Reduza o número de instâncias do tamanho de VM solicitado e repita a operação de implantação. Além disso, para implantações maiores, convém avaliar os [conjunto de dimensionamento de máquinas virtuais do Azure](../../virtual-machine-scale-sets/index.yml). O número de instâncias VM pode automaticamente aumentar ou diminuir em resposta à demanda ou a um agendamento definido, e você tem uma maior probabilidade de êxito de alocação porque as implantações podem ser distribuídas em vários clusters. 

## <a name="background-information"></a>Informações básicas
### <a name="how-allocation-works"></a>Como funciona a alocação
Os servidores são particionados em clusters nos datacenters do Microsoft Azure. Normalmente, uma tentativa de solicitação de alocação é feita em vários clusters, mas é possível que determinadas restrições da solicitação de alocação forcem a plataforma do Azure a repeti-la em um único cluster. Neste artigo, chamaremos isso de "fixada a um cluster". O diagrama 1 a seguir ilustra o caso de uma tentativa de alocação normal feita em vários clusters. O diagrama 2 ilustra o caso de uma alocação que foi fixada ao Cluster 2 por se tratar do local de hospedagem do Serviço de Nuvem CS_1 ou do conjunto de disponibilidade existente.
![Diagrama de alocação](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Motivos das falhas de alocação
Quando a solicitação de alocação é fixada a um cluster, é mais provável que haja falha na localização de recursos gratuitos, pois o pool de recursos disponíveis é menor. Além disso, quando a solicitação de alocação é fixada a um cluster, mas não há suporte para o tipo de recurso solicitado ao cluster, a solicitação falha mesmo que o cluster tenha recursos livres. O Diagrama 3 a seguir demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não dispõe de recursos gratuitos. O diagrama 4 demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não é compatível com o tamanho da VM solicitado, mesmo que o cluster disponha de recursos livres.

![Falha na alocação fixada](./media/virtual-machines-common-allocation-failure/Allocation2.png)
