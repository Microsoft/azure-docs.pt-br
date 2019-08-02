---
title: Grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP | Microsoft Docs
description: Descreve os cenários de implantação do SAP com grupos de posicionamento de proximidade do Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f97cce2d1a8b2de5634215629ddb997dc8f7196a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235191"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupos de posicionamento de proximidade do Azure para obter a latência da rede ideal com aplicativos SAP
Os aplicativos SAP baseados na arquitetura SAP NetWeaver ou SAP S/4HANA são sensíveis à latência de rede entre a camada de aplicativo SAP e a camada de banco de dados SAP. A razão dessa sensibilidade dessas arquiteturas é enraizada no fato de que a maior parte da lógica de negócios é executada na camada de aplicativo. Como resultado da execução da lógica de negócios, a camada de aplicativo SAP emite consultas à camada de banco de dados em alta frequência de milhares e dezenas de milhares por segundo. Na maioria dos casos, a natureza dessas consultas é simples. E, muitas vezes, podem ser executados na camada de banco de dados em menos de 500 microssegundos ou até mesmo menos. O tempo gasto na rede para enviar tal consulta da camada de aplicativo para a camada de banco de dados e receber o conjunto de resultados de volta da camada de banco de dados tem um grande impacto no tempo necessário para executar processos de negócios. Essa sensibilidade à latência de rede é o tempo que o motivo precisa ser gasto em projetos de implantação do SAP para obter a latência de rede ideal. No [SAP Note #1100926-perguntas frequentes: Desempenho](https://launchpad.support.sap.com/#/notes/1100926/E)de rede, a SAP publicou algumas diretrizes sobre como classificar a latência de rede.

Por um lado, em muitas regiões do Azure, o número de data centers cresceu, também disparado pela introdução do Zonas de Disponibilidade. Por outro lado, os clientes, especialmente para sistemas SAP de alto nível, usaram mais SKus de VM especiais fora da família série M ou do HANA em instâncias grandes. Tipos de máquina virtual do Azure que não estão presentes em todos os data centers em uma região específica do Azure. Como resultado dessas duas as tendências, os clientes enfrentou casos, em que a latência de rede não estava no intervalo ideal e, em alguns casos, resultou em um desempenho inferior de seus sistemas SAP.

Para evitar esse problema, o Azure oferece um constructo que é chamado de [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Essa nova funcionalidade já foi usada para implantar vários sistemas SAP diferentes. Verifique o artigo referenciado para restrições de grupos de posicionamento de proximidade. Este artigo abordará os diferentes cenários do SAP, nos quais os grupos de posicionamento de proximidade do Azure podem ser usados ou devem ser usados.

## <a name="what-are-proximity-placement-groups"></a>O que são grupos de posicionamento de proximidade 
Um grupo de posicionamento de proximidade do Azure é uma construção lógica, que, na fase de definição, está associada a uma região do Azure e a um grupo de recursos do Azure. Durante a implantação de VMs, um grupo de posicionamento de proximidade é referenciado por:

- A primeira VM do Azure implantada para liquidar no datacenter. A primeira máquina virtual pode ser vista como uma VM de âncora que é implantada em um datacenter com base nos algoritmos de alocação do Azure eventualmente combinados com definições de usuário para uma zona de disponibilidade específica do Azure.
- Por todas as VMs subsequentes implantadas fazendo referência ao grupo de posicionamento de proximidade para colocar todas as VMs do Azure implantadas subsequentes no mesmo datacenter em que a primeira máquina virtual foi colocada.

> [!NOTE]
> Se não houver nenhum hardware de host implantado que possa executar um tipo de VM específico no mesmo datacenter em que a primeira VM foi colocada, a implantação do tipo de VM exigida não terá êxito e terminará com uma mensagem de falha. Eles podem ser casos em que mais VMs não convencionais, como máquinas virtuais com GPUs ou tipos de VM HPC, devem ser centradas por exemplo, uma VM da série M que foi implantada como o primeiro tipo de VM

Um único [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) pode ter vários grupos de posicionamento de proximidade atribuídos a ele mesmo. No entanto, um grupo de posicionamento de proximidade só pode ser atribuído a um grupo de recursos do Azure.

Usando grupos de posicionamento de proximidade, você deve estar ciente de:

- Quando você impulsiona o desempenho ideal para seu sistema SAP e se limita a um único datacenter do Azure para esse sistema usando grupos de posicionamento de proximidade, talvez não seja possível combinar todos os tipos de famílias de VMs dentro desse grupo de posicionamento de proximidade. O motivo é que certos hardwares de host que são necessários para executar exclusivamente um determinado tipo de VM podem não estar presentes no datacenter, sua ' VM de âncora ' do grupo de posicionamento foi implantada
- No ciclo de vida desse sistema SAP, você pode ser forçado a mover o sistema para outro datacenter. Essa movimentação pode ser forçada em casos em que você decidiu que a camada do DBMS do HANA de expansão deve, por exemplo, mover de quatro nós para 16 nós. Mas não há mais capacidade suficiente para obter mais 12 VMs do tipo que você já usou no mesmo datacenter.
- Devido ao encerramento de hardware, a Microsoft pode criar capacidades para os tipos de VM que você usou em outro datacenter, em vez do mesmo datacenter. Essa ocorrência pode significar que você move todas as VMs do grupo de posicionamento de proximidade para outro datacenter.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Grupos de posicionamento de proximidade do Azure com sistemas SAP usando VMs do Azure exclusivamente
O caso principal das implantações de sistemas SAP NetWeaver e S/4HANA no Azure não usa [instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Para implantações desses sistemas, é importante fornecer o desempenho ideal entre a camada do aplicativo SAP e a camada do DBMS. Para fazer isso, você definiria um grupo de posicionamento de proximidade do Azure apenas para este sistema. Na maioria das implantações do cliente, os clientes optaram por criar um único [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) para sistemas SAP. Nesse caso, haveria uma relação de 1:1 entre, por exemplo, os grupos de recursos do sistema ERP de produção e seu grupo de posicionamento de proximidade. Em alguns outros casos de implantação, os clientes organizaram seus grupos de recursos horizontalmente e coletaram todos os sistemas de produção em um único grupo de recursos. Nesse caso, você teria uma relação de 1 a muitos entre o grupo de recursos para sistemas SAP de produção e vários grupos de posicionamento de proximidade de seu SAP ERP de produção, SAP BW, etc. O agrupamento de vários ou até mesmo de todos os sistemas de produção ou não produção SAP em um único grupo de posicionamento de proximidade deve ser evitado. Em exceções, em que um pequeno número de sistemas SAP ou um sistema SAP e alguns aplicativos ao redor precisam ter comunicação de rede de baixa latência, você pode considerar mover esses sistemas para um grupo de posicionamento de proximidade. Motivo para a recomendação como essa é que quanto mais sistemas você agrupar em um grupo de posicionamento de proximidade, maior será a probabilidade:

- Que você precisa de um tipo de VM que não pode ser executado no datacenter específico, o grupo de posicionamento de proximidade foi ancorado.
- Esses recursos de determinadas VMs não convencionais, como a série M, podem, eventualmente, não ser mais atendidos à medida que você solicitar mais ao adicionar software adicional a um grupo de posicionamento de proximidade existente ao longo do tempo.

O uso ideal conforme descrito seria semelhante a:

![Grupos locais de proximidade para todas as VMs do Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Nesse caso, os sistemas SAP únicos são agrupados em um grupo de recursos cada um com um grupo de posicionamento de proximidade cada. Não há nenhuma dependência sobre se você usa as configurações de expansão do HANA ou expansão do DBMS.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Grupos de posicionamento de proximidade do Azure e instâncias grandes do HANA
Para os casos em que alguns de seus sistemas SAP dependem de [instâncias grandes Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) como camada de aplicativo, melhorias graves na latência de rede entre a unidade de instância grande do Hana e VMs do Azure podem ser obtidas ao usar unidades de instância grande do Hana que foram implantado na [revisão 4 linhas ou carimbos](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Um dos aprimoramentos é que as unidades de instâncias grandes do HANA, como foram implantadas, já foi implantada uma parte do grupo de posicionamento de proximidade. Você pode usar esse grupo de posicionamento de proximidade para implantar suas VMs de camada de aplicativo. Como resultado, essas VMs serão implantadas no mesmo datacenter que hospeda sua unidade de instância grande do HANA.

Para detectar se sua unidade de instância grande do HANA está implantada em um carimbo ou linha de revisão 4, verifique o artigo [controle de instâncias grandes do Azure Hana por meio de portal do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). Na visão geral dos atributos de sua unidade de instância grande do HANA, você também pode descobrir o nome do grupo de posicionamento de proximidade, pois ele foi criado no momento da implantação para sua unidade de instância grande do HANA. O nome exibido na visão geral de atributos é o nome do grupo de posicionamento de proximidade que você deve usar para implantar suas VMs de camada de aplicativo no.

Ao contrário dos sistemas SAP que usam apenas máquinas virtuais do Azure, a decisão de quantos [grupos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) devem ser usados é retirada de você para um grau ao usar as instâncias grandes do Hana. Todas as unidades de instância grande do HANA de um [locatário de instância grande do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) são agrupadas em um único grupo de recursos do Azure, conforme descrito [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). A menos que você queira uma implantação em locatários diferentes para separar, por exemplo, produção e não produção ou certos sistemas, todas as suas unidades de instância grande do HANA serão implantadas em um locatário de instância grande do HANA, que novamente tem uma relação 1:1 com um Azure Grupo de recursos. Enquanto todas as unidades únicas terão um grupo de posicionamento de proximidade separado definido. 

Como resultado, o agrupamento entre os grupos de recursos do Azure e os grupos de posicionamento de proximidade para um único locatário terá a seguinte aparência:

![Grupos de posicionamento de proximidade para todas as VMs do Azure](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Breve exemplo de implantação com grupos de posicionamento de proximidade do Azure
Para demonstrar, como você pode usar os grupos de posicionamento de proximidade do Azure para implantar sua VM, aqui está uma lista de comandos do PowerShell que demonstram seu uso para um primeiro pequeno exercício com os grupos de posicionamento de proximidade do Azure

A primeira etapa depois de fazer logon com sua [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) é verificar se você está na assinatura do Azure correta que deseja usar para implantar com o comando:

<pre><code>
Get-AzureRmContext
</code></pre>

Se você precisar alterar para uma assinatura diferente, poderá fazer isso executando este comando:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Como uma terceira etapa, você deseja criar um novo grupo de recursos do Azure com este comando:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Você pode criar o novo grupo de posicionamento de proximidade agora com:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Agora você pode começar a implantar sua primeira VM nesse grupo de posicionamento de proximidade com um comando como:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Com o comando acima, uma VM baseada em Windows é implantada. Depois que essa implantação de VM for bem-sucedida, o escopo do datacenter do grupo de posicionamento de proximidade será definido na região do Azure. Todas as implantações de VM subsequentes que fazem referência ao grupo de posicionamento de proximidade como no último comando serão implantadas no mesmo datacenter do Azure, desde que o tipo de VM possa ser hospedado em hardware colocado no datacenter e/ou a capacidade para esse tipo de VM esteja disponível.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combinar conjuntos de disponibilidade e Zonas de Disponibilidade com grupos de posicionamento de proximidade 
Usando Zonas de Disponibilidade para implantações de sistema SAP, uma das desvantagens é o fato de que a camada de aplicativo SAP não pode ser controlada implantada usando conjuntos de disponibilidade dentro da zona específica. Como você deseja ter a camada de aplicativo SAP implantada nas mesmas zonas que a camada do DBMS, e fazer referência a uma zona de disponibilidade e um conjunto de disponibilidade ao implantar uma única VM não tem suporte, você foi forçado a implantar sua camada de aplicativo referenciando uma zona e, portanto, perdem a capacidade de garantir que as VMs da camada de aplicativo se espalhem entre diferentes domínios de falha e atualização. Com a ajuda dos grupos de posicionamento de proximidade, você pode superar essa restrição. A sequência de implantações teria a seguinte aparência:

- Criar um grupo de posicionamento de proximidade
- Implante sua ' VM de âncora ', geralmente o servidor DBMS fazendo referência a uma determinada zona de disponibilidade do Azure
- Crie um conjunto de disponibilidade que faça referência ao grupo de proximidade do Azure (veja abaixo)
- Implantar as VMs da camada de aplicativo referenciando o conjunto de disponibilidade e o grupo de posicionamento de proximidade

Em vez de implantar a primeira VM conforme demonstrado acima, você faz referência a uma zona de disponibilidade do Azure e ao grupo de posicionamento de proximidade ao implantar a VM como:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Uma implantação bem-sucedida dessa máquina virtual que hospedaria a instância do banco de dados do meu sistema SAP em uma zona de disponibilidade do Azure, o escopo do grupo de posicionamento de proximidade é fixado em um dos data centers que estão representando a zona de disponibilidade que você definiu .

Supomos que você implante as VMs dos serviços centrais da mesma forma que as VMs do DBMs referenciando as mesmas zonas para as VMs do DBMS e os mesmos grupos de posicionamento de proximidade. Na próxima etapa, você precisa criar os conjuntos de disponibilidade que deseja usar para a camada de aplicativo do seu sistema SAP.
O grupo de posicionamento de proximidade precisa ser definido e criado. O comando para criar o conjunto de disponibilidade requer uma referência adicional para a ID do grupo de posicionamento de proximidade (não o nome). Você pode obter a ID do grupo de posicionamento de proximidade com:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Ao criar o conjunto de disponibilidade, você precisa considerar parâmetros adicionais ao usar discos gerenciados (padrão, a menos que especificado de forma diferente) e grupos de posicionamento de proximidade:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

O ideal é que você use três domínios de falha. No entanto, o número de domínios de falha com suporte pode variar de região para região. Nesse caso, a contagem máxima de domínio de falha possível para as regiões específicas era duas. Para implantar suas VMs de camada de aplicativo, você precisa adicionar uma referência ao nome do conjunto de disponibilidade e ao nome do grupo de posicionamento de proximidade, conforme demonstrado aqui:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O resultado final dessa sequência será uma camada DBMS e os serviços centrais do seu sistema SAP localizados em uma zona de disponibilidade específica e uma camada de aplicativo SAP que está localizada por meio de conjuntos de disponibilidade nos mesmos data centers do Azure que as VMs do DBMS foram obtidas Plant.

> [!NOTE]
> Ao implantar uma VM DBMS em uma zona e a segunda VM do DBMS em outra zona para criar uma configuração de alta disponibilidade, você precisará de grupos de posicionamento de proximidade diferentes para cada uma das zonas. Mesmo si verdadeiro para o conjunto de disponibilidade que você pode usar

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Obter um sistema existente em grupos de posicionamento de proximidade do Azure
Como você já tem os sistemas SAP implantados, convém otimizar a latência de rede de alguns dos seus sistemas críticos e localizar a camada de aplicativo e a camada DBMS no mesmo datacenter. No estágio da visualização pública da funcionalidade do grupo de posicionamento de proximidade, uma exclusão das VMs e uma nova criação das VMs é necessária para executar essa movimentação em grupos de posicionamento de proximidade. Nesse estágio da funcionalidade, não é bom desligar as VMs para poder atribuir essas máquinas virtuais de desligamento a grupos de posicionamento de proximidade.


## <a name="next-steps"></a>Próximas etapas
Consulte a documentação:

- [Lista de verificação de carga de trabalho do SAP no planejamento e implantação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Visualização: Implantar VMs em grupos de posicionamento de proximidade usando CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Visualização: Implantar VMs em grupos de posicionamento de proximidade usando o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

