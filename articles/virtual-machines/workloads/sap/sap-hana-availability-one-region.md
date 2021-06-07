---
title: Disponibilidade do SAP HANA em uma região do Azure | Microsoft Docs
description: Descreve as operações do SAP HANA em VMs nativas do Azure em uma região do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 845cecfb6b09591b10de30267b31e6e1a80a7482
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504313"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidade do SAP HANA em uma região do Azure
Este artigo descreve vários cenários de disponibilidade em uma região do Azure. O Azure tem várias regiões, distribuídas em todo o mundo. Para obter a lista de regiões do Azure, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). Para implantar SAP HANA em VMs em uma região do Azure, a Microsoft oferece a implantação de uma única VM com uma instância do HANA. Para maior disponibilidade, você pode implantar duas VMs com duas instâncias do HANA dentro de um [Conjunto de disponibilidade do Azure](../../windows/tutorial-availability-sets.md) use replicação de sistema do HANA para disponibilidade. 

Atualmente, o Azure está oferecendo [Zonas de Disponibilidade do Azure](../../../availability-zones/az-overview.md). Este artigo não descreve as zonas de disponibilidade em detalhes. Porém, ele inclui uma discussão geral sobre como usar conjuntos de disponibilidade versus zonas de disponibilidade.

Regiões do Azure onde zonas de disponibilidade são oferecidas, têm vários datacenters. Os datacenters são independentes no fornecimento de fonte de alimentação, resfriamento e rede. O motivo para oferecer zonas diferentes em uma única região do Azure é implantar aplicativos em duas ou três zonas de disponibilidade que são oferecidas. A implantação entre zonas, problemas de energia e rede afetando apenas uma infraestrutura da Zona de Disponibilidade do Azure, a implantação de seu aplicativo em uma região do Azure ainda é funcional. Pode ocorrer alguma redução de capacidade. Por exemplo, as VMs em uma zona podem ser perdidas, mas as VMs em duas outras zonas ainda estariam ativas e em execução. 
 
Um Conjunto de Disponibilidade do Azure é um recurso de agrupamento lógico que ajuda a garantir que os recursos de VM que você coloca no Conjunto de Disponibilidade sejam isolados uns dos outros quando são implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um Conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Em algumas documentações do Azure, essa configuração é referida como posicionamentos em diferentes [domínios de atualização e de falha](../../availability.md). Esses posicionamentos normalmente estão dentro de um datacenter do Azure. Supondo que os problemas de fontes de alimentação ou rede afetem o datacenter que você está implantando, toda a sua capacidade em uma região do Azure será afetada.

O posicionamento de datacenters que representam Zonas de Disponibilidade do Azure é um compromisso entre fornecer latência de rede aceitável entre serviços implantados em diferentes zonas e uma distância entre os datacenters. As catástrofes naturais, de maneira ideal, não afetariam a energia, o fornecimento de rede e a infraestrutura de todas as zonas de disponibilidade nessa região. No entanto, como catástrofes naturais monumentais mostraram, as Zonas de Disponibilidade nem sempre fornecem a disponibilidade desejada dentro de uma região. Pense no furacão Maria que atingiu a ilha de Porto Rico em 20 de setembro de 2017. O furacão basicamente causou um blecaute em quase 100 por cento da ilha de 144 km de largura.

## <a name="single-vm-scenario"></a>Cenário de VM individual

Em um cenário de VM individual, você deve criar uma VM do Azure para a instância do SAP HANA. Você usa o Armazenamento Premium do Azure para hospedar o disco do sistema operacional e todos os seus discos de dados. O SLA de tempo de atividade do Azure de 99,9% e os SLAs de outros componentes do Azure são suficientes para que você possa cumprir seu SLAs de disponibilidade para seus clientes. Nesse cenário, você não precisa utilizar um Conjunto de Disponibilidade do Azure para VMs que executem a camada de DBMS. Neste cenário, você conta com dois recursos diferentes:

- Reinicialização automática de VM do Azure (também conhecida como recuperação de serviço do Azure)
- Reinicialização automática de SAP HANA

A reinicialização automática de VM do Azure ou recuperação de serviço é uma funcionalidade no Azure que funciona em dois níveis:

- O host do servidor do Azure verifica a integridade de uma VM hospedada no host do servidor.
- O controlador de malha do Azure monitora a integridade e a disponibilidade do host do servidor.

Uma funcionalidade de verificação de integridade monitora a integridade de todas as VMs hospedadas em um host de servidor do Azure. Se uma VM cair em um estado não íntegro, uma reinicialização da VM poderá ser iniciada pelo agente do host do Azure que verifica a integridade da VM. O controlador de malha verifica a integridade do host, verificando muitos parâmetros diferentes que podem indicar problemas com o hardware do host. Ele também verifica a acessibilidade do host por meio da rede. Uma indicação de problemas com o host pode levar aos seguintes eventos:

- Se o host sinalizar um estado de integridade incorreto, uma reinicialização do host e uma reinicialização das VMs que estavam em execução no host serão acionadas.
- Se o host não estiver em um estado íntegro após a reinicialização bem-sucedida, será iniciada uma reimplementação das VMs que estavam originalmente no nó agora não íntegro em um servidor host íntegro. Nesse caso, o host original é marcado como não é saudável. Ele não será usado para outras implantações até que seja limpo ou substituído.
- Se o host não íntegro tiver problemas durante o processo de reinicialização, um reinício imediato das VMs em um host íntegro será acionado. 

Com o monitoramento do host e da VM fornecido pelo Azure, as VMs do Azure que apresentam problemas de host são automaticamente reiniciadas em um host íntegro do Azure. 

>[!IMPORTANT]
>A correção do serviço do Azure não reinicializará as VMs do Linux em que o sistema operacional convidado está em estado de pânico do kernel. As configurações padrão das versões mais usadas do Linux não estão reiniciando automaticamente as VMs ou o servidor em que o kernel do Linux está em estado de pânico. Em vez disso, o padrão prevê manter o sistema operacional em estado kernel panic para poder anexar um depurador de kernel para análise. O Azure está honrando esse comportamento não reiniciando automaticamente uma VM com o sistema operacional convidado em tal estado. A suposição é que tais ocorrências são extremamente raras. Você pode sobrescrever o comportamento padrão para ativar uma reinicialização da VM. Para mudar o comportamento padrão, habilite o parâmetro 'kernel.panic' em /etc/sysctl.conf. O tempo que você definiu para este parâmetro está em segundos. Os valores comuns recomendados são esperar por 20 a 30 segundos antes de acionar a reinicialização por meio desse parâmetro. Consulte também <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

O segundo recurso com o qual você pode contar nesse cenário é o fato de que o serviço HANA que é executado em uma VM reinicializada é iniciado automaticamente após a reinicialização da VM. Você pode configurar a [Reinicialização automática do serviço HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) por meio dos serviços de watchdog dos diversos serviços HANA.

Você pode melhorar esse cenário de VM única adicionando um nó de failover passivo para uma configuração de SAP HANA. Na documentação do SAP HANA, essa configuração é chamada de [failover automático de host](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Essa configuração pode fazer sentido em uma situação de implantação local na qual o hardware do servidor é limitado e um nó de servidor único é dedicado como nó de Failover automático do host para um conjunto de hosts de produção. Mas no Azure, quando a infraestrutura subjacente do Azure fornece um servidor de destino íntegro para uma reinicialização bem-sucedida da VM, não faz sentido implantar failover automático do host do SAP HANA. Devido à correção do serviço do Azure, não há uma arquitetura de referência que prevê um nó de espera para o failover automático do host do HANA. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Caso especial de configurações de escalabilidade de saída do SAP HANA no Azure
A alta disponibilidade das configurações de escalabilidade do SAP HANA depende da recuperação de serviço das VMs do Azure e da reinicialização da instância do SAP HANA à medida que a VM é instalada e está sendo executada novamente. As arquiteturas de alta disponibilidade baseadas na replicação do sistema HANA serão introduzidas posteriormente. 


## <a name="availability-scenarios-for-two-different-vms"></a>Cenários de disponibilidade para duas VMs diferentes

Se você usar duas VMs do Azure em um Conjunto de Disponibilidade do Azure, poderá aumentar o tempo de atividade entre essas duas VMs se elas forem colocadas em um Conjunto de Disponibilidade do Azure em uma região do Azure. A configuração básica no Azure pareceria com:

![Diagrama de duas VMs com todas as camadas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar os diferentes cenários de disponibilidade, algumas das camadas no diagrama são omitidas. O diagrama mostra somente camadas que descrevem as máquinas virtuais, hosts, conjuntos de disponibilidade e regiões do Azure. Instâncias de Rede Virtual do Azure, grupos de recursos e assinaturas não têm função nos cenários descritos nesta seção.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replicar backups para uma segunda máquina virtual

Uma das configurações mais rudimentares é o uso de backups. Em particular, você pode ter backups de logs de transações enviados de uma VM para a outra VM do Azure. Você pode escolher o tipo de Armazenamento do Azure. Nessa configuração, você é responsável por gerar script da cópia dos backups agendados realizados na primeira VM para a segunda VM. Se você precisar usar as instâncias da segunda VM, você precisa restaurar os backups totais, incrementais/diferenciais e de log de transação até o ponto que precisa. 

A arquitetura é semelhante ao:

![Diagrama que mostra a arquitetura de duas VMs com replicação de armazenamento.](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Essa configuração não é adequada para alcançar excelentes tempos de RPO (objetivo de ponto de recuperação) e RTO (objetivo de tempo de recuperação). Os tempos RTO seriam especialmente afetados devido à necessidade de restaurar completamente todo o banco de dados usando os backups copiados. No entanto, essa configuração é útil para recuperação após a exclusão não intencional de dados nas instâncias principais. Com essa configuração, é possível a qualquer momento restaurar para um determinado ponto no tempo, extrair os dados e importar os dados excluídos para sua instância principal. Portanto, pode fazer sentido usar um método de cópia de backup em combinação com outros recursos de alta disponibilidade. 

Enquanto os backups estão sendo copiados, você poderá usar uma VM menor do que a VM principal em que o SAP HANA está sendo executado. Tenha em mente que você pode anexar um número menor de VHDs em VMs menores. Para obter informações sobre os limites de tipos de VM individuais, verifique [Tamanhos de máquinas virtuais Linux no Azure](../../sizes.md).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replicação de sistema do SAP HANA sem failover automático

Os cenários descritos nesta seção usam a replicação de sistema do SAP HANA. Para obter a documentação do SAP, consulte [Replicação do sistema](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Cenários sem failover automático não são comuns para configurações em uma região do Azure. Uma configuração sem failover automático, embora evite uma configuração do Pacemaker, obriga a monitorar e fazer failover manualmente. Como isso é necessário e os esforços também, a maioria dos clientes está contando com a correção do serviço do Azure. Há alguns casos extremos em que essa configuração pode ajudar em termos de cenários de falha. Ou, em alguns casos, um cliente talvez queira obter mais eficiência.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>A replicação de sistema do SAP HANA sem failover automático e sem pré-carregamento de dados

Nesse cenário, você pode usar a replicação de sistema SAP HANA para mover dados de forma síncrona para alcançar um RPO de 0. Por outro lado, você tem tempo suficiente de RTO e não precisa fazer failover ou pré-carregamento de dados no cache de instância do HANA. Nesse caso, é possível obter ainda mais economia em sua configuração por meio das seguintes ações:

- Execute outra instância do SAP HANA na segunda VM. A instância do SAP HANA na segunda VM usa a maior parte da memória da máquina virtual. No caso de um failover para a segunda VM, você precisa desligar a instância do SAP HANA em execução que tem os dados totalmente carregados na segunda VM, para que os dados replicados possam ser carregados no cache da instância do HANA de destino na segunda VM .
- Use um tamanho menor de VM na segunda VM. Se ocorrer um failover, você terá uma etapa adicional antes do failover manual. Nesta etapa, você vai redimensionar a VM ao tamanho da VM de origem. 
 
Esse cenário é da seguinte maneira:

![Diagrama de duas VMs com a replicação de armazenamento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Mesmo que você não use o pré-carregamento de dados no destino de replicação do sistema do HANA, é necessário pelo menos de 64 GB de memória. Você também precisa de memória suficiente além de 64 GB para manter os dados de rowstore na memória da instância de destino.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>A replicação de sistema do SAP HANA sem failover automático e com pré-carregamento de dados

Nesse cenário, os dados replicados para a instância do HANA na segunda VM são pré-carregados. Isso elimina as duas vantagens de não pré-carregar dados. Nesse caso, você não pode executar outro sistema SAP HANA na segunda VM. Você também não pode usar uma VM de tamanho menor. Portanto, os clientes raramente implementam esse cenário.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replicação de sistema do SAP HANA com failover automático

Na configuração de disponibilidade padrão e mais comum dentro de uma região do Azure, duas VMs do Azure executando o Linux SLES tem um cluster de failover definido. O cluster do Linux SLES é baseado na estrutura [Pacemaker](./high-availability-guide-suse-pacemaker.md) em conjunto com um dispositivo [STONITH](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device). 

Do ponto de vista do SAP HANA, o modo de replicação usado é sincronizado e um failover automático é configurado. A segunda VM, a instância do SAP HANA age como nó em espera ativa. O nó em espera recebe um fluxo síncrono de registros de alteração da instância primária do SAP HANA. Como as transações são confirmadas pelo aplicativo no nó primário do HANA, o nó primário do HANA espera para confirmar para o aplicativo até a confirmação de que o nó secundário do SAP HANA recebeu o registro de confirmação. O SAP HANA oferece dois diferentes modos de replicação síncrona. Para obter detalhes e para uma descrição das diferenças entre esses dois modos de replicação síncrona, consulte o artigo do SAP [Modos de replicação para a replicação do sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A configuração geral se parece com:

![Diagrama de duas VMs com a replicação de armazenamento e failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Você pode escolher essa solução porque ela permite que você obtenha um RPO = 0 e um RTO baixo. Configure a conectividade de cliente do SAP HANA de modo que os clientes do SAP HANA usem o endereço IP virtual para conectar-se à configuração de replicação de sistema do HANA. Essa configuração elimina a necessidade de reconfigurar o aplicativo se ocorrer um failover no nó secundário. Nesse cenário, os SKUs de VM do Azure para as VMs primária e secundária devem ser o mesmo.

## <a name="next-steps"></a>Próximas etapas

Para obter orientação passo a passo sobre como definir essas configurações no Azure, consulte:

- [Configurar a replicação de sistema do SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Alta disponibilidade para SAP HANA usando replicação de sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Para obter mais informações sobre a disponibilidade do SAP HANA em regiões do Azure, consulte:

- [Disponibilidade do SAP HANA entre regiões do Azure](./sap-hana-availability-across-regions.md)