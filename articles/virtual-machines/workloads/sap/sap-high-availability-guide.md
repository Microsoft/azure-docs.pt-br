---
title: Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver | Microsoft Docs
description: Guia de alta disponibilidade do SAP NetWeaver em máquinas virtuais do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b88ac9a728606581c3364ac536b6c3fc2691024
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720297"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade de várias SID do SAP)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md



As Máquinas Virtuais do Azure são a solução para as organizações que precisam de recursos de computação, armazenamento e rede, no mínimo de tempo e sem ciclos de compra longos. Você pode usar Máquinas Virtuais do Azure para implantar aplicativos clássicos como o ABAP baseado no SAP NetWeaver, Java e uma pilha ABAP+Java. Estenda a confiabilidade e a disponibilidade sem recursos locais adicionais. As Máquinas Virtuais do Azure dão suporte à conectividade entre locais, o que permite que você integre as Máquinas Virtuais do Azure aos seus domínios locais, a nuvens privadas e à estrutura do sistema SAP.

Neste artigo, abordaremos as etapas que você pode tomar para implantar sistemas SAP de alta disponibilidade no Azure usando o modelo de implantação do Azure Resource Manager. Vamos percorrer estas tarefas principais:

* Encontrar os guias de instalação e Notas de SAP corretos, listados na seção [Recursos][sap-ha-guide-2]. Este artigo complementa a documentação de instalação do SAP e as Notas do SAP, que são os principais recursos para ajudar a instalar e a implantar o software SAP em plataformas específicas.
* Descubra as diferenças entre o modelo de implantação do Azure Resource Manager e o modelo de implantação clássico do Azure.
* Aprenda sobre os modos de quorum do Clustering de Failover do Windows Server para que você possa escolher o modelo certo para sua implantação do Azure.
* Aprenda sobre o armazenamento compartilhado do Windows Server Failover Clustering nos serviços Azure.
* Saiba como proteger componentes com ponto único de falha como ABAP (Advanced Business Application Programming) do ASCS (SAP Central Services)/SCS (SAP Central Services) e DBMS (sistemas de gerenciamento de banco de dados), e componentes redundantes como Servidor de Aplicativos SAP, no Azure.
* Execute um exemplo passo a passo da instalação e configuração de um sistema SAP de alta disponibilidade em um cluster do Clustering de Failover do Windows Server no o Azure usando o Azure Resource Manager.
* Saiba mais sobre etapas adicionais necessárias para usar o Windows Server Failover Clustering no Azure, mas que não são necessárias em uma implantação local.

Para simplificar a implantação e a configuração, estamos usando os modelos de três camadas SAP do Resource Manager de alta disponibilidade neste artigo. Os modelos automatizam a implantação de toda a infraestrutura de que você precisa para um sistema SAP de alta disponibilidade. A infraestrutura também dá suporte ao dimensionamento SAPS (SAP Application Performance Standard) do seu sistema SAP.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Pré-requisitos
Antes de começar, verifique se os pré-requisitos descritos nos capítulos a seguir são atendidos. Além disso, não deixe de verificar todos os recursos listados na seção [Recursos][sap-ha-guide-2].

Neste artigo, podemos usar modelos do Azure Resource Manager para [SAP NetWeaver de três camadas usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Para uma visão geral dos modelos, confira [Modelos do Azure Resource Manager para SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Recursos
Esses artigos abordam implantações SAP no Azure:

* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP NetWeaver][dbms-guide]
* [Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver (este guia)][sap-ha-guide]

> [!NOTE]
> Sempre que possível, daremos a você um link para o guia de instalação do SAP de referência (confira os [guias de instalação de SAP][sap-installation-guides]). Para pré-requisitos e informações sobre o processo de instalação, é recomendável que você leia os guias de instalação do SAP NetWeaver cuidadosamente. Este artigo aborda apenas as tarefas específicas para sistemas baseados no SAP NetWeaver que você pode usar com as Máquinas Virtuais do Azure.
>
>

As seguintes Notas do SAP são relacionadas ao tópico do SAP no Azure:

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: Produtos com suporte e dimensionamento |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1999351] |Virtualização no Windows: Monitoramento Avançado |
| [2243692] |Usar o Armazenamento SSD Premium do Azure para instância do SAP DBMS |

Saiba mais sobre as [limitações das assinaturas do Azure][azure-subscription-service-limits-subscription], incluindo limitações máximas e gerais padrão.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP de alta disponibilidade com o Azure Resource Manager versus o modelo de implantação clássico do Azure
Os modelos de implantação clássico do Azure e do Azure Resource Manager são diferentes nos seguintes aspectos:

- Grupos de recursos
- Dependência do balanceador interno de carga do Azure no grupo de recursos do Azure
- Suporte para cenário SAP com vários SID

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupos de recursos
No Azure Resource Manager, você pode usar grupos de recursos para gerenciar todos os recursos do aplicativo em sua assinatura do Azure. Uma abordagem integrada, em um grupo de recursos, todos os recursos têm o mesmo ciclo de vida. Por exemplo, todos os recursos são criados ao mesmo tempo e excluídos ao mesmo tempo. Saiba mais sobre [grupos de recursos](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Dependência do balanceador interno de carga do Azure no grupo de recursos do Azure

No modelo de implantação clássico do Azure, há uma dependência entre o balanceador de carga interno do Azure (o serviço do Azure Load Balancer) e os serviços de nuvem. Cada balanceador de carga interno precisa de um serviço de nuvem.

No Azure Resource Manager, todos os recursos do Azure precisam ser colocados em um grupo de recursos do Azure, e isso também é válido para o balanceador de carga do Azure. No entanto, não é necessário ter um grupo de recursos do Azure por balanceador de carga do Azure, por exemplo, um grupo de recursos do Azure pode conter vários balanceadores de carga do Azure. O ambiente é mais simples e mais flexível. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Suporte para cenário SAP com vários SID

No Azure Resource Manager, você pode instalar várias instâncias ASCS/SCS do SID (identificador do sistema SAP) em um cluster. Instâncias de vários SID são possíveis devido ao suporte de vários endereços IP para cada balanceador de carga interno de carga do Azure.

Para usar o modelo de implantação clássico do Azure, siga os procedimentos descritos em [SAP NetWeaver no Azure: Clustering de instâncias SAP ASCS/SCS usando o Clustering de Failover do Windows Server no Azure com SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Recomendamos fortemente que você use o modelo de implantação do Azure Resource Manager para as instalações do SAP. Ele oferece muitos benefícios que não estão disponíveis no modelo de implantação clássico. Saiba mais sobre os [modelos de implantação do Azure][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering
O Windows Server Failover Clustering é a base de uma instalação do SAP ASCS/SCS de alta disponibilidade e DBMS no Windows.

Um cluster de failover é um grupo servidores 1+n independentes (nós) que trabalham juntos para aumentar a disponibilidade de aplicativos e serviços. Se ocorrer uma falha de nó, o Clustering de Failover do Windows Server calculará o número de falhas que podem ocorrer e manterá um cluster íntegro para fornecer serviços e aplicativos. Você pode escolher dentre diferentes modos de quorum para obter o clustering de failover.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modos de quorum
Você pode escolher dentre quatro modos de quorum quando usa o Windows Server Failover Clustering:

* **Maioria de nós**. Cada nó do cluster pode votar. O cluster funciona somente com a maioria dos votos, ou seja, com mais da metade dos votos. Recomendamos essa opção para clusters com número ímpar de nós. Por exemplo, três nós em um cluster de sete nós podem falhar e o cluster ainda pode ter maioria e continuar a executar.  
* **Maioria de nós e disco**. Todos os nós e um disco designado no armazenamento de cluster (a testemunha de disco) podem votar sempre que estão disponíveis e em comunicação. O cluster funciona somente com a maioria dos votos, ou seja, com mais da metade dos votos. Esse modo faz sentido em um ambiente de cluster com um número par de nós. Se a metade dos nós e o disco estiverem online, o cluster permanecerá em estado íntegro.
* **Maioria de nós e compartilhamento de arquivos**. Todos os nós e um compartilhamento de arquivos designado criado pelo administrador (a testemunha de compartilhamento de arquivos) poderão votar, independente de serem os nós e compartilhamentos de arquivos disponíveis e em comunicação. O cluster funciona somente com a maioria dos votos, ou seja, com mais da metade dos votos. Esse modo faz sentido em um ambiente de cluster com um número par de nós. É semelhante ao modo Maioria de nós e disco, mas usa uma testemunha de compartilhamento de arquivos em vez de uma testemunha de disco. Esse modo é fácil de implementar, mas se o próprio compartilhamento de arquivos não estiver altamente disponível, ele poderá se tornar um ponto único de falha.
* **Sem Maioria: Somente Disco**. O cluster terá um quorum se um nó estiver disponível e em comunicação com um disco específico no armazenamento de cluster. Somente os nós que também estão em comunicação com esse disco podem ingressar no cluster. Recomendamos que você não use esse modo.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server Failover Clustering no local
A Figura 1 mostra um cluster de dois nós. Se a conexão de rede entre os nós falha e eles permanecem ativos e em execução, um compartilhamento de arquivo ou disco de quorum determina qual nó continuará a fornecer os serviços e aplicativos do cluster. O nó que tem acesso ao compartilhamento de arquivo ou disco de quorum é o nó que garante que os serviços continuarão.

Como esse exemplo usa um cluster de dois nós, usamos o modo de quorum Maioria dos nós e compartilhamento de arquivos. A maioria dos nós e disco também é uma opção válida. Em um ambiente de produção, recomendamos que você use um disco de quorum. Você pode usar tecnologia de sistema de rede e de armazenamento para torná-lo altamente disponível.

![Figura 1: Exemplo de uma configuração de Windows Server Failover Clustering para SAP ASCS/SCS no Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Exemplo de uma configuração de Windows Server Failover Clustering para SAP ASCS/SCS no Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Armazenamento compartilhado
A Figura 1 também mostra um cluster de armazenamento compartilhado de dois nós. Em um cluster de armazenamento compartilhado local, todos os nós no cluster detectam armazenamento compartilhado. Um mecanismo de bloqueio protege os dados contra corrupção. Todos os nós podem detectar se outro nó falhar. Se um nó falhar, o outro assume a propriedade dos recursos de armazenamento e garante a disponibilidade dos serviços.

> [!NOTE]
> Você não precisa de discos compartilhados de alta disponibilidade com alguns aplicativos de DBMS, como o SQL Server. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS do disco local de um nó do cluster para o disco local de outro nó do cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco compartilhado.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Resolução de nome e rede
Os computadores clientes acessam o cluster em um endereço IP virtual com um nome de host virtual que o servidor DNS fornece. Os nós locais e o servidor DNS podem lidar com vários endereços IP.

Em uma instalação comum, você usa duas ou mais conexões de rede:

* Uma conexão dedicada com o armazenamento
* Uma conexão de rede interna de cluster para a pulsação
* Uma rede pública que os clientes usam para se conectar ao cluster

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover Clustering no Azure
Comparados a implantações de nuvem privada ou bare metal, as Máquinas Virtuais do Azure exigem etapas adicionais para configurar o Windows Server Failover Clustering. Quando você cria um disco de cluster compartilhado, precisa definir vários endereços IP e nomes de host virtual para a instância do SAP ASCS/SCS.

Neste artigo, discutiremos os principais conceitos e as etapas adicionais necessárias para criar um cluster SAP de serviços centrais de alta disponibilidade no Azure. Mostramos a você como configurar a ferramenta de terceiros SIOS DataKeeper e como configurar o balanceador de carga interno do Azure. Você pode usar essas ferramentas para criar um cluster de failover do Windows com uma testemunha de compartilhamento de arquivo no Azure.

![Figura 2: Configuração de Clustering de Failover do Windows Server no Azure sem um disco compartilhado][sap-ha-guide-figure-1001]

_**Figura 2:** Configuração de Clustering de Failover do Windows Server no Azure sem um disco compartilhado_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco compartilhado no Azure com SIOS DataKeeper
Você precisa de armazenamento compartilhado em cluster para uma instância do SAP ASCS/SCS de alta disponibilidade. A partir de setembro de 2016, o Azure não oferece armazenamento compartilhado que você possa usar para criar um cluster de armazenamento compartilhado. Você pode usar o software de terceiros SIOS DataKeeper Cluster Edition para criar um armazenamento espelhado que simula o armazenamento compartilhado de cluster. A solução SIOS fornece replicação síncrona de dados em tempo real. É assim que você pode criar um recurso de disco compartilhado para um cluster:

1. Anexe um disco adicional a cada uma das máquinas virtuais (VMs) em uma configuração de cluster do Windows.
2. Execute o SIOS DataKeeper Cluster Edition em ambos os nós da máquina virtual.
3. Configure o SIOS DataKeeper Cluster Edition para que ele reflita o conteúdo do volume anexado do disco adicional da máquina virtual de origem para o volume anexado do disco adicional da máquina virtual de destino. O SIOS DataKeeper abstrai os volumes locais de origem e de destino e os apresenta ao Windows Server Failover Clustering como um disco compartilhado.

Saiba mais sobre [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Figura 3: Configuração do Clustering de Failover do Windows Server no Azure com o SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Configuração do Clustering de Failover do Windows Server no Azure com o SIOS DataKeeper_

> [!NOTE]
> Você não precisa de discos compartilhados de alta disponibilidade com alguns produtos de DBMS, como o SQL Server. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS do disco local de um nó do cluster para o disco local de outro nó do cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco compartilhado.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Resolução de nomes no Azure
A plataforma de nuvem do Azure não oferece a opção de configurar endereços IP virtual, como endereços IPs flutuantes. Você precisa de uma solução alternativa para configurar um endereço IP virtual a fim de alcançar o recurso de cluster na nuvem.
O Azure tem um balanceador de carga interno no serviço Azure Load Balancer. Com o balanceador de carga interno, os clientes alcançam o cluster pelo endereço IP virtual do cluster.
Você precisa implantar o balanceador de carga interno no grupo de recursos que contém os nós do cluster. Em seguida, configure todas as regras necessárias de encaminhamento de porta com as portas de investigação do balanceador de carga interno.
Os clientes podem se conectar por meio do nome de host virtual. O servidor DNS resolve o endereço IP do cluster e o balanceador de carga interno trata da porta que encaminha para o nó ativo do cluster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver de alta disponibilidade na Azure IaaS (Infraestrutura como um serviço)
Para obter a alta disponibilidade do SAP, como no caso de componentes de software SAP, você precisará proteger os componentes a seguir:

* Instância do Servidor de Aplicativos SAP
* Instância ASCS/SCS SAP
* Servidor DBMS

Para obter mais informações sobre como proteger os componentes SAP em cenários de alta disponibilidade, consulte [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Servidor de Aplicativos SAP de Alta Disponibilidade
Normalmente, não é necessária uma solução específica de alta disponibilidade para Servidor de Aplicativos SAP e instâncias de diálogo. Você atinge alta disponibilidade por redundância e configura várias instâncias de caixa de diálogo em diferentes Máquinas Virtuais do Azure. Você deve ter pelo menos duas instâncias do aplicativo SAP instaladas em duas Máquinas Virtuais do Azure.

![Figura 4: Servidor de Aplicativos SAP de Alta Disponibilidade][sap-ha-guide-figure-2000]

_**Figura 4:** Servidor de Aplicativos SAP de Alta Disponibilidade_

Você deve colocar todas as máquinas virtuais que hospedam instâncias do Servidor de Aplicativos SAP no mesmo conjunto de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais são parte do mesmo domínio de atualização. Por exemplo, um domínio de atualização faz com que as máquinas virtuais não sejam atualizadas ao mesmo tempo durante o tempo de inatividade de manutenção planejada.
* Todas as máquinas virtuais são parte do mesmo domínio de falha. Por exemplo, um domínio de falha faz com que as máquinas virtuais sejam implantadas de forma que nenhum ponto único de falha afete a disponibilidade de todas as máquinas virtuais.

Saiba mais sobre como [gerenciar a disponibilidade de máquinas virtuais][virtual-machines-manage-availability].

Somente discos não gerenciados: Como a conta de armazenamento do Azure é um possível ponto único de falha, é importante ter pelo menos duas contas de armazenamento do Azure, nas quais pelo menos duas máquinas virtuais estejam distribuídas. Em uma configuração ideal, os discos de cada máquina virtual que está executando uma instância de diálogo SAP deveria ser implantada em uma conta de armazenamento diferente.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Instância do SAP ASCS/SCS de alta disponibilidade
A Figura 5 é um exemplo de instância do SAP ASCS/SCS de alta disponibilidade.

![Figura 5: Instância do SAP ASCS/SCS de alta disponibilidade][sap-ha-guide-figure-2001]

_**Figura 5:** Instância do SAP ASCS/SCS de alta disponibilidade_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Alta disponibilidade da Instância do SAP ASCS/SCS com o Clustering de Failover do Windows Server no Azure
Comparados a implantações de nuvem privada ou bare metal, as Máquinas Virtuais do Azure exigem etapas adicionais para configurar o Windows Server Failover Clustering. Para criar um cluster de failover do Windows, você precisará de um Disco de Cluster Compartilhado, vários endereços IP e nomes de host virtuais e um balanceador de carga interno do Azure para clustering da instância do SAP ASCS/SCS. Abordaremos isso em mais detalhes mais adiante neste artigo.

![Figura 6: Configuração do Windows Server Failover Cluster para SAP ASCS/SCS no Azure usando SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Configuração do Windows Server Failover Cluster para SAP ASCS/SCS no Azure com SIOS DataKeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Instância do DBMS de alta disponibilidade
O DBMS também é um ponto único de contato em um sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A Figura 7 mostra uma solução de alta disponibilidade Always On do SQL Server no Clustering de Failover do Windows Server e no balanceador interno de carga do Azure. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS usando sua própria replicação DBMS. Nesse caso, você não precisa de discos compartilhados de cluster, o que simplifica toda a configuração.

![Figura 7: Exemplo de um DBMS SAP de alta disponibilidade, com o Always On do SQL Server][sap-ha-guide-figure-2003]

_**Figura 7:** Exemplo de um DBMS SAP de alta disponibilidade, com o Always On do SQL Server_

Para saber mais sobre o clustering do SQL Server no Azure usando o modelo de implantação do Azure Resource Manager, confira estes artigos:

* [Configure o grupo de disponibilidade Always On em Máquinas Virtuais do Azure manualmente usando o Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configurar um balanceador interno de carga do Azure para um grupo de disponibilidade Always On no Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Cenários de implantação de alta disponibilidade de ponta a ponta

### <a name="deployment-scenario-using-architectural-template-1"></a>Cenário de implantação usando o Modelo Arquitetônico 1

A Figura 8 mostra um exemplo de uma arquitetura de alta disponibilidade do SAP NetWeaver no Azure para **um** sistema SAP. Esse cenário é definido da seguinte maneira:

- Um cluster dedicado é usado para a instância SAP ASCS/SCS.
- Um cluster dedicado é usado para a instância DBMS.
- Instâncias do Servidor de Aplicativos SAP são implantadas em suas próprias VMs dedicadas.

![Figura 8: Modelo Arquitetônico 1 de alta disponibilidade de SAP, com cluster dedicado para ASCS/SCS e DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Modelo Arquitetônico 1 de alta disponibilidade de SAP, clusters dedicados para ASCS/SCS e DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Cenário de implantação usando o Modelo Arquitetônico 2

A Figura 9 mostra um exemplo de uma arquitetura de alta disponibilidade do SAP NetWeaver no Azure para **um** sistema SAP. Esse cenário é definido da seguinte maneira:

- Um cluster dedicado é usado para **ambas** a instância SAP ASCS/SCS e o DBMS.
- Instâncias do Servidor de Aplicativos SAP são implantados em VMs próprias dedicadas.

![Figura 9: Modelo Arquitetônico 2 de alta disponibilidade de SAP, com um cluster dedicado para ASCS/SCS e outro dedicado para DBMS][sap-ha-guide-figure-2005]

_**Figura 9:** Modelo Arquitetônico 2 de alta disponibilidade de SAP, com um cluster dedicado para ASCS/SCS e outro dedicado para DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Cenário de implantação usando o Modelo Arquitetônico 3

A Figura 10 mostra um exemplo de uma arquitetura de alta disponibilidade do SAP NetWeaver no Azure para **dois** sistemas SAP, com &lt;SID1&gt; e &lt;SID2&gt;. Esse cenário é definido da seguinte maneira:

- Um cluster dedicado é usado para **ambas** as instâncias SID1 de SAP ASCS/SCS *e* SID2 de SAP ASCS/SCS (um cluster).
- Um cluster dedicado é usado para SID1 e outro para SID2 de DBMS (dois clusters).
- Instâncias do Servidor de Aplicativos SAP para o SID1 do Sistema SAP são implantadas em suas próprias VMs dedicadas.
- Instâncias do Servidor de Aplicativos SAP para o SID2 do Sistema SAP são implantadas em suas próprias VMs dedicadas.

![Figura 10: Modelo Arquitetônico 3 de alta disponibilidade de SAP, com cluster dedicado para diferentes instâncias de ASCS/SCS][sap-ha-guide-figure-6003]

_**Figura 10:** Modelo Arquitetônico 3 de alta disponibilidade de SAP, com cluster dedicado para diferentes instâncias de ASCS/SCS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparar a infraestrutura

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para o Modelo Arquitetônico 1
Os modelos do Azure Resource Manager para SAP ajudam a simplificar a implantação dos recursos necessários.

Os modelos de três camadas no Azure Resource Manager também dão suporte a cenários de alta disponibilidade, tal como o Modelo Arquitetônico 1, que tem dois clusters. Cada cluster é um ponto único de falha de SAP para SAP ASCS/SCS e DBMS.

É aqui que você pode obter os modelos do Azure Resource Manager para o cenário de exemplo descrito neste artigo:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem do Marketplace do Azure usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para o Modelo Arquitetônico 1:

- No Portal do Azure, na folha **Parâmetros**, na caixa **SYSTEMAVAILABILITY**, selecione **HA**.

  ![Figura 11: Definir os parâmetros de SAP de alta disponibilidade do Azure Resource Manager][sap-ha-guide-figure-3000]

_**Figura 11:** Definir os parâmetros de SAP de alta disponibilidade do Azure Resource Manager_


  Os modelos criam:

  * **Máquinas virtuais**:
    * Máquinas virtuais do Servidor de Aplicativos SAP: <*SAPSystemSID*>-di-<*Number*>
    * Máquinas virtuais de cluster ASCS/SCS: <*SAPSystemSID*>-ascs-<*Number*>
    * Cluster DBMS: <*SAPSystemSID*>-db-<*Number*>

  * **Placas de rede para todas as máquinas virtuais, com endereços IP associados**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Contas de armazenamento do Azure (apenas discos não gerenciados)**

  * **Grupos de Disponibilidade** para:
    * Máquinas virtuais do Servidor de Aplicativos do SAP: <*SAPSystemSID*>-avset-di
    * Máquinas virtuais de cluster ASCS/SCS: <*SAPSystemSID*>-avset-ascs
    * Máquinas virtuais de cluster DBMS: <*SAPSystemSID*>-avset-db

  * **Balanceador de carga interno do Azure**:
    * Com todas as portas para a instância ASCS/SCS e endereço IP <*SAPSystemSID*>-lb-ascs
    * Com todas as portas para o DBMS do SQL Server e endereço IP <*SAPSystemSID*>-lb-db

  * **Grupo de segurança de rede**: <*SAPSystemSID*>-nsg-ascs-0  
    * Com uma porta de protocolo RDP externa aberta para o <*SAPSystemSID*>-ascs-0 virtual machine

> [!NOTE]
> Todos os endereços IP das placas de rede e dos balanceadores de carga interno do Azure são **dinâmicos** por padrão. Altere-os para endereços IP **Estáticos**. Descrevemos como fazer isso posteriormente neste artigo.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implantar máquinas virtuais com conectividade de rede corporativa (entre locais) para uso na produção
Para sistemas SAP de produção, implante máquinas virtuais do Azure com [conectividade de rede corporativa (entre instalações)][planning-guide-2.2] usando o VPN Site a Site ou o ExpressRoute do Azure.

> [!NOTE]
> Você pode usar a instância da Rede Virtual do Azure. A rede virtual e a sub-rede já foram criadas e preparadas.
>
>

1. No Portal do Azure, na folha **Parâmetros**, na caixa **NEWOREXISTINGSUBNET**, selecione **existente**.
2. Na caixa **SUBNETID**, adicione a cadeia de caracteres completa da sua SubnetID da rede do Azure preparada na qual você planeja implantar suas máquinas virtuais do Azure.
3. Para obter uma lista de todas as sub-redes da rede do Azure, execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O campo **ID** mostra o **SUBNETID**.
4. Para obter uma lista de todos os valores **SUBNETID**, execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   O **SUBNETID** tem esta aparência:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implantar instâncias de SAP somente na nuvem para teste e demonstração
Você pode implantar o sistema SAP de alta disponibilidade em um modelo de implantação somente na nuvem. Esse tipo de implantação é principalmente útil para casos de uso de demonstração e teste. Ele não é adequado para casos de uso de produção.

- No Portal do Azure, na folha **Parâmetros**, na caixa **NEWOREXISTINGSUBNET**, selecione **novo**. Deixe o campo **SUBNETID** vazio.

  O modelo do Resource Manager de SAP cria automaticamente a rede virtual e a sub-rede do Azure.

> [!NOTE]
> Você também precisa implantar pelo menos uma máquina virtual dedicada para o Active Directory e DNS na mesma instância de rede virtual do Azure. O modelo não cria essas máquinas virtuais.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para o Modelo Arquitetônico 2

Você pode usar esse modelos do Azure Resource Manager para SAP para ajudar a simplificar a implantação dos recursos de infraestrutura necessários para o Modelo Arquitetônico 2 de SAP.

Aqui é onde você pode obter os modelos do Azure Resource Manager para esse cenário de implantação:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem do Marketplace do Azure usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para o Modelo Arquitetônico 3

Você pode preparar a infraestrutura e configurar SAP para **vários SID**. Por exemplo, você pode adicionar uma instância SAP ASCS/SCS adicional em uma configuração de cluster *existente*. Para obter mais informações, consulte [Configurar uma instância adicional do SAP ASCS/SCS em uma configuração de cluster existente para criar uma configuração de vários SID de SAP no Azure Resource Manager][sap-ha-multi-sid-guide].

Se você quiser criar um novo cluster com múltiplos SID, use os [modelos de início rápido de vários SID no GitHub](https://github.com/Azure/azure-quickstart-templates).
Para criar um novo cluster de vários SID, você precisa implantar os três modelos a seguir:

* [Modelo de ASCS/SCS](#ASCS-SCS-template)
* [Modelo de banco de dados](#database-template)
* [Modelo dos servidores de aplicativo](#application-servers-template)

As seções a seguir têm mais detalhes sobre os modelos e os parâmetros que você precisa fornecer nos modelos.

#### <a name="ASCS-SCS-template"></a> Modelo de ASCS/SCS

O modelo de ASCS/SCS implanta duas máquinas virtuais que podem ser usadas para criar um cluster de failover do Windows Server que hospeda várias instâncias de ASCS/SCS.

Para configurar o modelo de vários SID de ASCS/SCS, no [modelo de vários SID de ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] ou [modelo de vários SID de ASCS/SCS usando Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md], insira valores para os seguintes parâmetros:

  - **Prefixo de Recursos**.  Defina o prefixo do recurso, que é usado para prefixar todos os recursos que são criados durante a implantação. Como os recursos não pertencem apenas a um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo deve ter entre **três e seis caracteres**.
  - **Tipo de Pilha**. Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Azure Load Balancer tem um (ABAP ou Java apenas) ou dois (ABAP+Java) endereços IP privados por sistema de SAP.
  -  **Tipo de sistema operacional**. Selecione o sistema operacional das máquinas virtuais.
  -  **Contagem do Sistema SAP**. Selecione o número de sistemas de SAP que você deseja instalar neste cluster.
  -  **Disponibilidade do Sistema**. Selecione **HA**.
  -  **Nome de Usuário e Senha de Administrador**. Cria um novo usuário que pode ser usado para se conectar no computador.
  -  **Sub-rede Nova ou Existente**. Define se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente deve ser usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione a rede **existente**.
  -  **ID da Sub-rede**. Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID normalmente é semelhante a: / subscriptions / <*ID da assinatura*> / resourceGroups / <*nome do grupo de recursos*> / providers / Microsoft.Network / virtualNetworks / <*nome da rede virtual*> / subnets / < *nome da sub-rede*>

O modelo implanta uma instância do Azure Load Balancer que dá suporte a vários sistemas SAP.

- As instâncias ASCS são configuradas para o número de instância 00, 10, 20...
- As instâncias SCS são configuradas para o número de instância 01, 11, 21...
- As instâncias ASCS ERS (Enqueue Replication Server) (somente Linux) são configuradas para o número de instância 02, 12, 22...
- As instâncias SCS ERS (somente Linux) são configuradas para o número de instância 03, 13, 23...

O balanceador de carga contém 1 (2 para Linux) VIPs, 1x VIP para ASCS/SCS e 1x VIP para ERS (somente no Linux).

A lista a seguir contém todas as regras de balanceamento de carga (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Portas específicas do Windows para cada o sistema SAP: 445, 5985
- Portas ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SSCS (número de instância x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas ASCS ERS no Linux (número de instância x2): 33x2, 5x213, 5x214, 5x216
- Portas SCS ERS no Linux (número de instância x3): 33x3, 5x313, 5x314, 5x316

O balanceador de carga será configurado para usar as seguintes portas de investigação (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de investigação do balanceador de carga interno ASCS/SCS: 620x0
- Porta de investigação do balanceador de carga interno ERS (somente Linux): 621x2

#### <a name="database-template"></a> Modelo de banco de dados

O modelo de banco de dados implanta uma ou duas máquinas virtuais que você pode usar para instalar o RDBMS (sistema de gerenciamento de banco de dados relacional) para um sistema SAP. Se, por exemplo, você implantou um modelo ASCS/SCS para cinco sistemas SAP, precisará implantar esse modelo cinco vezes.

Para configurar o modelo de vários SID de banco de dados, no [modelo de vários SID de banco de dados][sap-templates-3-tier-multisid-db-marketplace-image] ou [modelo de vários SID de banco de dados usando Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md], insira valores para os seguintes parâmetros:

- **ID do sistema SAP**. Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID será usada como um prefixo para os recursos que serão implantados.
- **Tipo de sistema operacional**. Selecione o sistema operacional das máquinas virtuais.
- **Dbtype**. Selecione o tipo do banco de dados que você deseja instalar no cluster. Selecione **SQL** se você deseja instalar o Microsoft SQL Server. Selecione **HANA** se você planeja instalar SAP HANA nas máquinas virtuais. Selecione o tipo de sistema operacional correto: **Windows** para SQL e uma distribuição Linux para HANA. O Azure Load Balancer que está conectado às máquinas virtuais será configurado para suporte ao tipo de banco de dados selecionado:
  * **SQL**. O balanceador de carga balanceará a carga da porta 1433. Use essa porta para a instalação do SQL Server Always On.
  * **HANA**. O balanceador de carga balanceará a carga das portas 35015 e 35017. Instale o SAP HANA com o número de instância **50**.
  O balanceador de carga usará a porta de investigação 62550.
- **Tamanho do Sistema SAP**. Defina o número de SAPS que o novo sistema fornecerá. Se você não tiver certeza de quantos SAPs o sistema precisará, pergunte ao seu Parceiro de Tecnologia SAP ou Integrador de Sistemas.
- **Disponibilidade do Sistema**. Selecione **HA**.
- **Nome de Usuário e Senha de Administrador**. Cria um novo usuário que pode ser usado para se conectar no computador.
- **ID da Sub-rede**. Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.

#### <a name="application-servers-template"></a> Modelo dos servidores de aplicativos

O modelo de servidores de aplicativo implanta duas ou mais máquinas virtuais que podem ser usadas como instâncias do Servidor de Aplicativos SAP para um sistema SAP. Se, por exemplo, você implantou um modelo ASCS/SCS para cinco sistemas SAP, precisará implantar esse modelo cinco vezes.

Para configurar o modelo de vários SID de servidores de aplicativos, no [modelo de vários SID de servidores de aplicativos][sap-templates-3-tier-multisid-apps-marketplace-image] ou [modelo de vários SID de servidores de aplicativos usando Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md], insira valores para os seguintes parâmetros:

  -  **ID do sistema SAP**. Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID será usada como um prefixo para os recursos que serão implantados.
  -  **Tipo de sistema operacional**. Selecione o sistema operacional das máquinas virtuais.
  -  **Tamanho do Sistema SAP**. O número de SAPS que o novo sistema fornecerá. Se você não tiver certeza de quantos SAPs o sistema precisará, pergunte ao seu Parceiro de Tecnologia SAP ou Integrador de Sistemas.
  -  **Disponibilidade do Sistema**. Selecione **HA**.
  -  **Nome de Usuário e Senha de Administrador**. Cria um novo usuário que pode ser usado para se conectar no computador.
  -  **ID da Sub-rede**. Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rede virtual do Azure
Em nosso exemplo, o espaço de endereço da rede virtual do Azure é 10.0.0.0/16. Há uma sub-rede chamada **Subnet**, com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e balanceadores de carga internos são implantados nessa rede virtual.

> [!IMPORTANT]
> Não faça nenhuma alteração nas configurações de rede dentro do sistema operacional convidado. Isso inclui endereços IP, servidores DNS e sub-rede. Configure todas as configurações de rede no Azure. O serviço do protocolo DHCP propaga as configurações.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Endereços IP de DNS

Para definir os endereços IP de DNS necessários, execute as seguintes etapas.

1. No Portal do Azure, na folha de **Servidores DNS**, verifique se sua opção de rede virtual **Servidores DNS** está definida para **DNS Personalizado**.
2. Selecione suas configurações com base no seu tipo de rede. Para saber mais, consulte os recursos a seguir:
   * [Conectividade de rede corporativa (entre instalações)][planning-guide-2.2]: adicione os endereços IP dos servidores DNS locais.  
   Você pode estender os servidores DNS locais às máquinas virtuais que estão em execução no Azure. Nesse cenário, você pode adicionar os endereços IP das máquinas virtuais do Azure nos quais executa o serviço DNS.
   * Para implantações de VM isoladas no Azure: implantar uma máquina virtual adicional na mesma instância de Rede Virtual que serve como servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que você configurou para executar o serviço DNS.

   ![Figura 12: Configurar servidores DNS da Rede Virtual do Azure][sap-ha-guide-figure-3001]

   _**Figura 12:** Configurar servidores DNS da Rede Virtual do Azure_

   > [!NOTE]
   > Se você mudar os endereços IP dos servidores DNS, será preciso reinicializar as máquinas virtuais do Azure de modo a aplicar a alteração e propagar os novos servidores DNS.
   >
   >

Em nosso exemplo, o serviço DNS está instalado e configurado nas seguintes máquinas virtuais do Windows:

| Função da máquina virtual | Nome do host de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomes de host e endereços IP estáticos para a instância clusterizada do SAP ASCS/SCS e a instância clusterizada do DBMS

Para implantação local, você precisa destes endereços IP e nomes de host reservados:

| Função de nome de host virtual | Nome de host virtual | Endereço IP estático virtual |
| --- | --- | --- |
| Nome de host virtual do primeiro cluster SAP ASCS/SCS (para gerenciamento de cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome de host virtual da instância do SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nome de host virtual do segundo cluster SAP DBMS (gerenciamento de cluster) |pr1-dbms-vir |10.0.0.32 |

Quando você criar o cluster, crie os nomes de host virtual **pr1-ascs-vir** e **pr1-dbms- vir** e os endereços IP associados que gerenciam o cluster. Para obter informações sobre como fazer isso, consulte [Coletar nós de cluster em uma configuração de cluster][sap-ha-guide-8.12.1].

Você pode criar manualmente os outros dois nomes de host virtuais, **pr1-ascs-sap** e **pr1-dbms-sap**, e os endereços IP associados no servidor DNS. A instância clusterizada da instância SAP ASCS/SCS e a instância clusterizada do DBMS usam esses recursos. Para obter informações sobre como fazer isso, consulte [Criar um nome de host virtual para uma instância SAP ASCS/SCS clusterizada][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Definir endereços IP estáticos para as máquinas virtuais de SAP
Depois de implantar as máquinas virtuais para usar no seu cluster, você precisará definir endereços IP estáticos para todas as máquinas virtuais. Faça isso na configuração da Rede Virtual do Azure e não no sistema operacional convidado.

1. No Portal do Azure, selecione **Grupo de Recursos** > **Placa de Rede** > **Configurações** > **Endereço IP**.
2. Na folha **Endereços IP**, em **Atribuição**, selecione **Estático**. Na caixa **Endereço IP**, digite o endereço IP que você deseja usar.

   > [!NOTE]
   > Se você mudar o endereço IP da placa de rede, será preciso reinicializar as máquinas virtuais do Azure para aplicar a alteração.  
   >
   >

   ![Figura 13: Definir endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 13:** Definir endereços IP estáticos para a placa de rede de cada máquina virtual_

   Repita essa etapa para todas as interfaces de rede, isto é, para todas as máquinas virtuais, incluindo as máquinas virtuais que você deseja usar para seu serviço DNS/Active Directory.

Em nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Função da máquina virtual | Nome do host de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância do Servidor de Aplicativos SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância do Servidor de Aplicativos SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância do Servidor de Aplicativos SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância do ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância do ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância do DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância do DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Defina um endereço IP estático para o balanceador interno de carga do Azure

O modelo do Azure Resource Manager para SAP cria um balanceador de carga interno do Azure usado para o cluster da instância do ASCS/SCS e para o cluster do DBMS do SAP.

> [!IMPORTANT]
> O endereço IP do nome de host virtual do SAP ASCS/SCS é o mesmo que o endereço IP do balanceador de carga interno SAP ASCS/SCS: **pr1-lb-ascs**.
> O endereço IP do nome virtual do DBMS é o mesmo que o endereço IP do balanceador interno de carga do DBMS: **pr1-lb-dbms**.
>
>

Para definir um endereço IP estático para o balanceador interno de carga do Azure:

1. A implantação inicial define o endereço IP do balanceador de carga interno como **Dinâmico**. No Portal do Azure, na folha **Endereços IP**, em **Atribuição**, selecione **Estático**.
2. Defina o endereço IP do balanceador interno de carga, **pr1-lb-ascs**, para o endereço IP do nome de host virtual da instância do SAP ASCS/SCS.
3. Defina o endereço IP do balanceador interno de carga, **pr1-lb-dbms**, para o endereço IP do nome de host virtual da instância do DBMS.

   ![Figura 14: Definir endereços IP estáticos para o balanceador de carga interno da instância SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 14:** Definir endereços IP estáticos para o balanceador de carga interno da instância SAP ASCS/SCS_

Em nosso exemplo, temos dois balanceadores de carga internos do Azure que têm esses endereços IP estáticos:

| Função do balanceador de carga interno do Azure | Nome balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno da instância SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno de DBMS SAP |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure

O modelo do Azure Resource Manager de SAP cria as portas de que você precisa:
* Uma instância do ASCS ABAP com número de instância padrão **00**
* Uma instância do SCS Java com número de instância padrão **01**

Quando você instala sua instância SAP ASCS/SCS, deve usar o número de instância padrão **00** para sua instância ASCS ABAP e o número de instância padrão **01** para sua instância SCS Java.

Em seguida, crie os pontos de extremidade de balanceamento interno de carga necessários para as portas do SAP NetWeaver.

Para criar os pontos de extremidade de balanceamento interno de carga, crie esses pontos de extremidade do balanceamento de carga para as portas do ASCS ABAP do SAP NetWeaver:

| Nome da regra do balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do ASCS com número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Enfileirar Servidor / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| Servidor de Mensagens ABAP / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Mensagem interna ABAP / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| HTTP do Servidor de Mensagens / *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| HTTP do SAP para Iniciar Serviço ASCS / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| HTTPS do SAP para Iniciar Serviço ASCS / *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Enfileirar Replicação / *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| RM Win *Lbrule5985* | |5985 |
| Compartilhamento de Arquivos *Lbrule445* | |445 |

_**Tabela 1:** Números de porta das instâncias do ASCS ABAP do SAP NetWeaver_

Em seguida, crie os pontos de extremidade do balanceamento de carga para as portas do SCS Java do SAP NetWeaver:

| Nome da regra do balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do SCS com número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Enfileirar Servidor / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Servidor do Gateway / *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Servidor de Mensagens Java / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| HTTP do Servidor de Mensagens / *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| HTTP do SAP para Iniciar Serviço SCS / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| HTTPS do SAP para Iniciar Serviço SCS / *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Enfileirar Replicação / *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| RM Win *Lbrule5985* | |5985 |
| Compartilhamento de Arquivos *Lbrule445* | |445 |

_**Tabela 2:** Números de porta das instâncias do SCS Java do SAP NetWeaver_

![Figura 15: Regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure][sap-ha-guide-figure-3004]

_**Figura 15:** Regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure_

Defina o endereço IP do balanceador de carga **pr1-lb-dbms** como o endereço IP do nome de host virtual da instância do DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Alterar as regras do balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure

Se você quiser usar números diferentes para as instâncias SAP ASCS ou SCS, precisará alterar os nomes e os valores das portas do padrão.

1. No portal do Azure, selecione **<*SID*>-lb-ascs load balancer** > **Regras de Balanceamento de Carga**.
2. Para todas as regras de balanceamento de carga que pertencem à instância do SAP ASCS ou SCS, altere estes valores:

   * NOME
   * Porta
   * Porta de back-end

   Por exemplo, se você quer alterar o número de instância do ASCS padrão de 00 para 31, precisa fazer as alterações em todas as portas listadas na Tabela 1.

   Veja um exemplo de uma atualização para a porta *lbrule3200*.

   ![Figura 16: Altere as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-figure-3005]

   _**Figura 16:** Altere as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicionar máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 17: Adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 17:** Adicionar uma máquina virtual a um domínio_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS

O Azure Load Balancer tem um balanceador de carga interno que fecha conexões quando elas estão ociosas por determinado período de tempo (um tempo limite de ociosidade). Os processos de trabalho do SAP em instâncias de caixa de diálogo abrem conexões com o processo de enfileiramento do SAP assim que a primeira solicitação para enfileirar/desenfileirar precisa ser enviada. Essas conexões geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de enfileiramento seja reiniciado. No entanto, se a conexão ficar ociosa por um certo período, o balanceador interno de carga do Azure fechará as conexões. Isso não é um problema porque o processo de trabalho do SAP restabelece a conexão com o processo de enfileiramento se ele não existe mais. Essas atividades são documentadas nos rastreamentos de desenvolvedor dos processos da SAP, mas criam uma grande quantidade de conteúdo extra nesses rastreamentos. É uma boa ideia para alterar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine essas alterações nos parâmetros de TCP/IP com parâmetros de perfil do SAP, descritos posteriormente neste artigo.

Para adicionar entradas de Registro em ambos os nós de cluster da instância SAP ASCS/SCS, primeiro adicione essas entradas de Registro do Windows a ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Value |120000 |
| Vincular à documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**Tabela 3:** Alterar o primeiro parâmetro de TCP/IP_

Em seguida, adicione as seguintes entradas de Registro em nós de cluster do Windows para SAP ASCS/SCS:

| Caminho | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Value |120000 |
| Vincular à documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**Tabela 4:** Alterar o segundo parâmetro de TCP/IP_

**Para aplicar as alterações, reinicie ambos os nós de cluster**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurar um cluster Windows Server Failover Clustering para uma instância do SAP ASCS/SCS

Configurar um cluster do Clustering de Failover do Windows Server para uma instância do SAP ASCS/SCS envolve as seguintes tarefas:

- Coletar nós de cluster em uma configuração de cluster
- Configurando a testemunha de compartilhamento de arquivos do cluster

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Coletar nós de cluster em uma configuração do cluster

1. O Assistente para Adicionar Funções e Recursos adiciona clustering de failover a ambos os nós de cluster.
2. Configure o cluster de failover usando o Gerenciador de Cluster de Failover. No Gerenciador de Cluster de Failover, selecione **Criar Cluster** e adicione apenas o nome do primeiro cluster, do nó A. Não adicione o segundo nó ainda; ele será adicionado posteriormente.

   ![Figura 18: Adicionar o nome de servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

   _**Figura 18:** Adicionar o nome de servidor ou máquina virtual do primeiro nó de cluster_

3. Insira o nome de rede (nome de host virtual) do cluster.

   ![Figura 19: Inserir o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 19:** Inserir o nome do cluster_

4. Depois de criar o cluster, execute um teste de validação de cluster.

   ![Figura 20: Executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

   _**Figura 20:** Executar a verificação de validação de cluster_

   Você pode ignorar os avisos sobre discos nessa altura do processo. Você adicionará uma testemunha de compartilhamento de arquivo e discos compartilhados do SIOS mais tarde. Neste estágio, você não precisa se preocupar em ter um quorum.

   ![Figura 21: Nenhum disco de quorum é encontrado][sap-ha-guide-figure-3010]

   _**Figura 21:** Nenhum disco de quorum é encontrado_

   ![Figura 22: Recurso de cluster principal precisa de um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 22:** Recurso de cluster principal precisa de um novo endereço IP_

5. Altere o endereço IP do serviço de cluster principal. O cluster não pode iniciar até você alterar o endereço IP do serviço de cluster principal, pois o endereço IP do servidor aponta para um dos nós da máquina virtual. Faça isso na página **Propriedades** do recurso IP do serviço de cluster principal.

   Por exemplo, precisamos atribuir um endereço IP (em nosso exemplo **10.0.0.42**) ao nome de host virtual do cluster **pr1-ascs-vir**.

   ![Figura 23: Na caixa de diálogo Propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 23:** Na caixa de diálogo **Propriedades**, altere o endereço IP_

   ![Figura 24: Atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 24:** Atribuir o endereço IP reservado para o cluster_

6. Coloque o nome do host virtual de cluster online.

   ![Figura 25: Serviço principal do cluster funcionando com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 25:** Serviço principal do cluster funcionando com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço principal do cluster está em execução, você pode adicionar o segundo nó do cluster.

   ![Figura 26: Adicionar o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 26:** Adicionar o segundo nó de cluster_

8. Insira um nome para o segundo host do nó de cluster.

   ![Figura 27: Inserir o segundo nome de host do nó de cluster][sap-ha-guide-figure-3016]

   _**Figura 27:** Inserir o segundo nome de host do nó de cluster_

   > [!IMPORTANT]
   > Verifique se a caixa de seleção **Adicionar todo o armazenamento qualificado ao cluster** **NÃO** está marcada.  
   >
   >

   ![Figura 28: Não marque a caixa de seleção][sap-ha-guide-figure-3017]

   _**Figura 28:** **Não** marque a caixa de seleção_

   Você pode ignorar os avisos sobre quorum e discos. Você configurará o quorum e compartilhará o disco mais tarde, conforme descrito em [Instalando o SIOS DataKeeper Cluster Edition para o disco compartilhado de cluster do SAP ASCS/SCS][sap-ha-guide-8.12.3].

   ![Figura 29: Ignorar os avisos sobre o quorum de disco][sap-ha-guide-figure-3018]

   _**Figura 29:** Ignorar os avisos sobre o quorum de disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurar a testemunha de compartilhamento de arquivos do cluster

Configurar a testemunha de compartilhamento de arquivos do cluster envolve as seguintes tarefas:

- Criar um compartilhamento de arquivos
- Configurar o quorum de testemunha de compartilhamento de arquivos no Gerenciador de Cluster de Failover

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Criar um compartilhamento de arquivos

1. Selecione uma testemunha de compartilhamento de arquivos em vez de um disco de quorum. O SIOS DataKeeper dá suporte a essa opção.

   Nos exemplos citados neste artigo, a testemunha de compartilhamento de arquivo está no servidor DNS/Active Directory em execução no Azure. A testemunha de compartilhamento de arquivo é chamada **domcontr-0**. Como uma conexão VPN seria configurada para o Azure (via VPN Site a Site ou Azure ExpressRoute), o serviço Active Directory/DNS é local e não é adequado para executar uma testemunha de compartilhamento de arquivos.

   > [!NOTE]
   > Se seu serviço DNS/Active Directory é executado somente no local, não configure a testemunha de compartilhamento de arquivo no sistema operacional Windows do Active Directory/DNS que está sendo executado no local. A latência de rede entre os nós de cluster em execução no Azure e o Active Directory/DNS local pode ser muito grande e causar problemas de conectividade. Não deixe de configurar a testemunha de compartilhamento de arquivo em uma máquina virtual do Azure que está em execução perto do nó do cluster.  
   >
   >

   A unidade de quorum precisa de pelo menos 1.024 MB de espaço livre. É recomendável ter 2.048 MB de espaço livre na unidade de quorum.

2. Adicione o objeto do nome do cluster.

   ![Figura 30: Atribuir as permissões no compartilhamento para o objeto de nome do cluster][sap-ha-guide-figure-3019]

   _**Figura 30:** Atribuir as permissões no compartilhamento para o objeto de nome do cluster_

   Verifique se as permissões incluem o poder de alterar dados no compartilhamento para o objeto de nome de cluster (em nosso exemplo **pr1-ascs-vir$**).

3. Para adicionar o objeto de nome do cluster à lista, selecione **Adicionar**. Altere o filtro para procurar objetos de computador além daqueles mostrados na Figura 31.

   ![Figura 31: Alterar os tipos de objetos para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 31:** Alterar os tipos de objetos para incluir computadores_

   ![Figura 32: Marcar a caixa de seleção Computadores][sap-ha-guide-figure-3021]

   _**Figura 32:** Marcar a caixa de seleção **Computadores**_

4. Insira o objeto de nome do cluster conforme mostrado na Figura 31. Como o registro já foi criado, você pode alterar as permissões conforme mostrado na Figura 30.

5. Selecione a guia **Segurança** do compartilhamento e defina permissões mais detalhadas para o objeto de nome do cluster.

   ![Figura 33: Definir os atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos][sap-ha-guide-figure-3022]

   _**Figura 33:** Definir os atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Definir o quorum de testemunha de compartilhamento de arquivos no Gerenciador de Cluster de Failover

1. Abra o Assistente para Definir a Configuração de Quorum.

   ![Figura 34: Iniciar o Assistente de configuração de quorum do cluster][sap-ha-guide-figure-3023]

   _**Figura 34:** Iniciar o Assistente de configuração de quorum do cluster_

2. Na página **Selecionar Configuração de Quorum**, escolha **Selecionar a testemunha de quorum**.

   ![Figura 35: Configurações de quorum à sua escolha][sap-ha-guide-figure-3024]

   _**Figura 35:** Configurações de quorum à sua escolha_

3. Na página **Selecionar Testemunha de Quorum**, escolha **Configurar uma testemunha de compartilhamento de arquivos**.

   ![Figura 36: Selecionar a testemunha de compartilhamento de arquivo][sap-ha-guide-figure-3025]

   _**Figura 36:** Selecionar a testemunha de compartilhamento de arquivo_

4. Digite o caminho UNC para o compartilhamento de arquivos (em nosso exemplo, \\domcontr-0\FSW). Para ver uma lista das alterações que você pode fazer, selecione **Avançar**.

   ![Figura 37: Definir o local do compartilhamento de arquivos para a testemunha de compartilhamento][sap-ha-guide-figure-3026]

   _**Figura 37:** Definir o local do compartilhamento de arquivos para a testemunha de compartilhamento_

5. Selecione as alterações que você deseja e selecione **Avançar**. Você precisa redefinir com êxito a configuração do cluster, conforme mostrado na Figura 38.  

   ![Figura 38: Confirmação de que você reconfigurou o cluster][sap-ha-guide-figure-3027]

   _**Figura 38:** Confirmação de que você reconfigurou o cluster_

Depois de instalar com êxito o Cluster de Failover do Windows, é necessário alterar alguns limites para adaptar a detecção de failover às condições no Azure. Os parâmetros a serem alterados estão documentados neste blog: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Supondo que as duas VMs que criam a configuração de Cluster do Windows para ASCS/SCS estejam na mesma sub-rede, os parâmetros a seguir precisam ser alterados para estes valores:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Essas configurações foram testadas com clientes e, por um lado, forneceram um compromisso satisfatório de serem suficientemente resilientes. Por outro lado, essas configurações ofereciam failover suficientemente rápido em condições de erro real de software SAP ou falha de nó/VM. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalar SIOS DataKeeper Cluster Edition para disco de compartilhamento de cluster do SAP ASCS/SCS

Agora você tem uma configuração do Clustering de Failover do Windows Server em funcionamento no Azure. Mas para instalar uma instância do SAP ASCS/SCS, você precisa de um recurso de disco compartilhado. Não é possível criar os recursos de disco compartilhado que você precisa no Azure. O SIOS DataKeeper Cluster Edition é uma solução de terceiros que você pode usar para criar recursos de disco compartilhados.

Instalar o SIOS DataKeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS envolve as seguintes tarefas:

- Adicionar o .NET Framework 3.5
- Instalando o SIOS DataKeeper
- Configurar o SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adicionar o .NET Framework 3.5
O Microsoft .NET Framework 3.5 não é automaticamente ativado ou instalado no Windows Server 2012 R2. Como SIOS DataKeeper requer que o .NET Framework esteja em todos os nós nos quais o DataKeeper for instalado, será necessário instalar o .NET Framework 3.5 no sistema operacional convidado de todas as máquinas virtuais no cluster.

Há duas maneiras de adicionar o .NET Framework 3.5:

- Uma delas é usar o Assistente para Adicionar Funções e Recursos no Windows, como mostrado na Figura 39.

  ![Figura 39: Instalar o .NET Framework 3.5 usando o assistente Adicionar funções e recursos][sap-ha-guide-figure-3028]

  _**Figura 39:** Instalar o .NET Framework 3.5 usando o assistente Adicionar funções e recursos_

  ![Figura 40: Barra de progresso da instalação no momento que você instala o .NET Framework 3.5 usando o Assistente para Adicionar Funções e Recursos][sap-ha-guide-figure-3029]

  _**Figura 40:** Barra de progresso da instalação no momento que você instala o .NET Framework 3.5 usando o Assistente para Adicionar Funções e Recursos_

- Use a ferramenta de linha de comando dism.exe. Para esse tipo de instalação, você precisa acessar o diretório SxS na mídia de instalação do Windows. Em um prompt de comandos com privilégios elevados, digite:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalar SIOS DataKeeper

Instale o SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar um armazenamento compartilhado virtual com o SIOS DataKeeper, crie um espelho sincronizado e simule o armazenamento compartilhado do cluster.

Antes de instalar o software SIOS, crie o usuário de domínio **DataKeeperSvc**.

> [!NOTE]
> Adicione esse usuário **DataKeeperSvc** ao grupo **Administradores locais** em ambos os nós do cluster.
>
>

Para instalar SIOS DataKeeper:

1. Instale o software SIOS em ambos os nós do cluster.

   ![Instalador do SIOS][sap-ha-guide-figure-3030]

   ![Figura 41: Primeira página da instalação do SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figura 41:** Primeira página da instalação do SIOS DataKeeper_

2. Na caixa de diálogo mostrada na figura 42, selecione **Sim**.

   ![Figura 42: O DataKeeper informa a você que um serviço será desabilitado][sap-ha-guide-figure-3032]

   _**Figura 42:** O DataKeeper informa a você que um serviço será desabilitado_

3. Na caixa de diálogo mostrada na Figura 43, recomendamos que você selecione **Conta de domínio ou servidor**.

   ![Figura 43: Seleção do usuário para o SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figura 43:** Seleção do usuário para o SIOS DataKeeper_

4. Insira o nome de usuário da conta de domínio e senhas que você criou para o SIOS DataKeeper.

   ![Figura 44: Inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Figura 44:** Inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper_

5. Instale a chave de licença para o SIOS DataKeeper conforme mostrado na Figura 45.

   ![Figura 45: Insira a chave da licença do SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Figura 45:** Insira a chave da licença do SIOS DataKeeper_

6. Quando solicitado, reinicie a máquina virtual.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurar SIOS DataKeeper

Depois de instalar o SIOS DataKeeper em ambos os nós, você precisará iniciar a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre os discos adicionais anexados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de Configuração e Gerenciamento de DataKeeper e selecione **Conectar Servidor**. (Na Figura 46, essa opção é marcada em vermelho.)

   ![Figura 46: Ferramenta de Gerenciamento e Configuração do SIOS DataKeeper][sap-ha-guide-figure-3036]

   _**Figura 46:** Ferramenta de Gerenciamento e Configuração do SIOS DataKeeper_

2. Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de Gerenciamento e Configuração deve se conectar e, em uma segunda etapa, as informações do segundo nó.

   ![Figura 47: Inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de Gerenciamento e Configuração deve se conectar e, em uma segunda etapa, as informações do segundo nó][sap-ha-guide-figure-3037]

   _**Figura 47:** Inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de Gerenciamento e Configuração deve se conectar e, em uma segunda etapa, as informações do segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 48: Criar trabalho de replicação][sap-ha-guide-figure-3038]

   _**Figura 48:** Criar trabalho de replicação_

   Um assistente orienta você pelo processo de criação de um trabalho de replicação.
4. Defina o nome, o endereço TCP/IP e o volume de disco do nó de origem.

   ![Figura 49: Definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _**Figura 49:** Definir o nome do trabalho de replicação_

   ![Figura 50: Definir os dados de base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 50:** Definir os dados de base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino.

   ![Figura 51: Definir os dados base para o nó, que deve ser o nó de destino atual][sap-ha-guide-figure-3041]

   _**Figura 51:** Definir os dados base para o nó, que deve ser o nó de destino atual_

6. Defina os algoritmos de compactação. Em nosso exemplo, recomendamos que você compacte o fluxo de replicação. Particularmente em situações de ressincronização, a compactação do fluxo de replicação reduz consideravelmente o tempo de ressincronização. Observe que a compactação utiliza os recursos de CPU e RAM de uma máquina virtual. Conforme aumenta a taxa de compactação, aumenta o volume de recursos de CPU usados. Você também pode ajustar essa configuração mais tarde.

7. Outra configuração que precisa ser verificada é se a replicação é executada de modo síncrono ou assíncrono. *Quando você protege configurações SAP ASCS/SCS, deve usar a replicação síncrona*.  

   ![Figura 52: Definir detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 52:** Definir detalhes de replicação_

8. Defina se o volume que é replicado pelo trabalho de replicação deve ser representado em uma configuração de cluster Clustering de Failover do Windows Server como um disco compartilhado. Para a configuração do SAP ASCS/SCS, escolha **Sim** para que o cluster do Windows veja o volume replicado como disco compartilhado que pode ser usado como volume de cluster.

   ![Figura 53: Selecionar Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 53:** Selecionar **Sim** para definir o volume replicado como um volume de cluster_

   Depois que o volume é criado, a ferramenta de Configuração e Gerenciamento do DataKeeper mostra que o trabalho de replicação está ativo.

   ![Figura 54: O espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 54:** O espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo_

   O Gerenciador de Cluster de Failover agora mostra o disco como um disco do DataKeeper, conforme ilustrado na Figura 55.

   ![Figura 55: O Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado][sap-ha-guide-figure-3045]

   _**Figura 55:** O Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Instalar o sistema SAP NetWeaver

Não descreveremos a instalação DBMS porque as configurações variam dependendo do sistema DBMS que você usar. No entanto, supomos que as preocupações de alta disponibilidade com o DBMS são dissipadas com o suporte às funcionalidades que os diferentes fornecedores de DBMS dão para o Azure. Por exemplo, o Always On ou o Espelhamento de Banco de Dados para SQL Server e Oracle Data Guard para bancos de dados Oracle. No cenário que usamos neste artigo, não adicionamos outra proteção ao DBMS.

Não existem considerações especiais quando diferentes serviços DBMS interagem com esse tipo de configuração de SAP ASCS/SCS clusterizada no Azure.

> [!NOTE]
> O procedimento de instalação dos sistemas ABAP, sistemas Java e sistemas ABAP+Java do SAP NetWeaver é quase idêntico. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema ABAP+Java do SAP tem uma instância ASCS e uma instância ABAP+Java em execução no mesmo grupo de cluster de failover da Microsoft. As diferenças de instalação para cada pilha de instalação do SAP NetWeaver serão explicitamente mencionadas. Você pode assumir que todas as outras partes são as mesmas.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Não coloque o arquivo da página em volumes espelhados do DataKeeper. O DataKeeper não dá suporte a volumes espelhados. Você pode deixar o arquivo de página na unidade D temporária de uma máquina virtual do Azure, o que é o padrão. Se já não estiver lá, mova o arquivo da página do Windows para a unidade D: da máquina virtual do Azure.
>
>

Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade envolve as seguintes tarefas:

- Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS
- Instalar o primeiro nó de cluster do SAP
- Modificar o perfil SAP da instância do ASCS/SCS
- Adicionando uma porta de investigação
- Abrindo a porta de investigação do Firewall do Windows

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS

1. No Gerenciador de DNS do Windows, crie uma entrada DNS para o nome de host virtual da instância ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP atribuído ao nome do host virtual da instância do ASCS/SCS deve ser o mesmo que o endereço IP atribuído ao Azure Load Balancer (**<*SID*>-lb-ascs**).  
   >
   >

   O endereço IP do nome de host virtual do SAP ASCS/SCS (**pr1-ascs-sap**) é o mesmo que o endereço IP do Azure Load Balancer (**pr1-lb-ascs**).

   ![Figura 56: Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 56:** Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP_

2. Para definir o endereço IP atribuído ao nome de host virtual, selecione **Gerenciador de DNS** > **Domínio**.

   ![Figura 57: Novo nome virtual e endereço TCP/IP para a configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 57:** Novo nome virtual e endereço TCP/IP para a configuração de cluster do SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalar o primeiro nó do cluster do SAP

1. Execute a primeira opção do nó de cluster no nó A de cluster, por exemplo, no host **pr1-ascs-0**.
2. Para manter as portas padrão para o balanceador interno de carga do Azure, escolha:

   * **Sistema ABAP**: **ASCS** com número de instância **00**
   * **Sistema Java**: **SCS** com número de instância **01**
   * **Sistema ABAP+Java**: **ASCS** com número de instância **00** e **SCS** com número de instância **01**

   Para usar números de instância além de 00 para a instância do ASCS ABAP e 01 para a instância do SCS Java, primeiramente será preciso alterar as regras padrão de balanceamento de carga do Azure Internal Load Balancer, conforme descrito em [Alterar as regras do balanceamento de carga padrão do ASCS/SCS para o Azure Internal Load Balancer][sap-ha-guide-8.9].

As próximas tarefas não são descritas na documentação de instalação padrão do SAP.

> [!NOTE]
> A documentação de instalação do SAP descreve como instalar o primeiro nó ASCS/SCS do cluster.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância do ASCS/SCS

Você precisa adicionar um novo parâmetro de perfil. O parâmetro de perfil impede conexões entre os processos de trabalho do SAP e o servidor de enfileiramento de fechar quando estão ociosas por muito tempo. Mencionamos o cenário de problema em [Adicionar entradas do Registro a ambos os nós de cluster da instância SAP ASCS/SCS][sap-ha-guide-8.11]. Nessa seção, também apresentamos duas alterações para alguns parâmetros básicos de conexão TCP/IP. Na segunda etapa, você precisará configurar o servidor de enfileiramento para enviar um sinal `keep_alive` para que as conexões não atinjam o limite de ociosidade do balanceador interno de carga do Azure.

Para modificar o perfil SAP da instância do ASCS/SCS:

1. Adicione este parâmetro de perfil ao perfil da instância do SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Em nosso exemplo, o caminho é:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Por exemplo, para o perfil da instância do SAP SCS e o caminho correspondente:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Para aplicar as alterações, reinicie a instância SAP ASCS /SCS.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicionar uma porta de investigação

Use a funcionalidade de investigação do balanceador interno de carga para fazer com que toda a configuração do cluster funcione com o Azure Load Balancer. Normalmente, um balanceador de carga interno do Azure distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes. No entanto, isso não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar parte da carga de trabalho. A funcionalidade de investigação ajuda quando o balanceador de carga interno do Azure atribui o trabalho apenas a uma instância ativa. Com a funcionalidade de investigação, o balanceador de carga interno pode detectar quais instâncias estão ativas e mirar apenas na instância com a carga de trabalho.

Para adicionar uma porta de investigação:

1. Verifique a configuração **ProbePort** atual executando o seguinte comando do PowerShell. Execute-o dentro de uma das máquinas virtuais na configuração do cluster.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Defina uma porta de investigação. O número da porta de investigação padrão é **0**. Em nosso exemplo, usamos a porta de investigação **62000**.

   ![Figura 58: A porta de investigação da configuração de cluster é 0 por padrão][sap-ha-guide-figure-3048]

   _**Figura 58:** A porta de investigação da configuração de cluster padrão é 0_

   O número da porta é definido nos modelos do Azure Resource Manager para SAP. Você pode atribuir o número da porta no PowerShell.

   Para definir um novo valor de ProbePort para o recurso de cluster **SAP <*SID*> IP**, execute o script do PowerShell a seguir. Atualize as variáveis do PowerShell para seu ambiente. Depois que o script é executado, será solicitado que você reinicie o grupo de clusters do SAP para ativar as alterações.

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Depois de colocar a função de cluster SAP **SAP <*SID*>** online, verifique se **ProbePort** está definido com o novo valor.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Figura 59: Investigar a porta de cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 59:** Investigar a porta de cluster depois de definir o novo valor_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Abrir a porta de investigação do Firewall do Windows

Você precisa abrir a porta de investigação do firewall do Windows nos dois nós de cluster. Use o script a seguir para abrir uma porta de investigação no firewall do Windows. Atualize as variáveis do PowerShell para seu ambiente.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** é definida como **62000**. Agora é possível acessar o compartilhamento de arquivos **\\ascsha-clsap\sapmnt** de outros hosts como **ascsha-dbas**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalar a instância de banco de dados

Para instalar a instância de banco de dados, siga o processo descrito na documentação de instalação do SAP.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalar o segundo nó do cluster

Para instalar o segundo cluster, siga as etapas no guia de instalação do SAP.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Alterar o tipo de início da instância do serviço Windows ERS do SAP

Altere o tipo de inicialização do serviço Windows do SAP ERS para **Automático (Atraso na Inicialização)** em ambos os nós do cluster.

![Figura 60: Alterar o tipo de serviço da instância ERS do SAP para atraso automático][sap-ha-guide-figure-3050]

_**Figura 60:** Alterar o tipo de serviço da instância ERS do SAP para atraso automático_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalar o servidor de aplicativos primário SAP

Instalar a instância <*SID*>-di-0 do PAS (Servidor de Aplicativos primário) na máquina virtual que você designou para hospedar o PAS. Não há dependências nas configurações específicas do Azure ou do DataKeeper.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalar o servidor de aplicativos SAP adicional

Instale um AAS (Servidor de Aplicativos Adicional) SAP em todas as máquinas virtuais que você designou para hospedar um Servidor de Aplicativos SAP. Por exemplo, em <*SID*>-di-1 para <*SID*>-di-&lt;n&gt;.

> [!NOTE]
> Isso conclui a instalação de um sistema SAP NetWeaver de alta disponibilidade. Em seguida, continue com o teste de failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testar o failover da instância do SAP ASCS/SCS e a replicação do SIOS
É muito fácil testar e monitorar um failover de instância de SAP ASCS/SCS e a replicação do disco SIOS usando o Gerenciador de Cluster de Failover e a ferramenta de Gerenciamento e Configuração do SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> A instância do SAP ASCS/SCS está em execução no Nó A do Cluster

O grupo de clusters **SAP PR1** está em execução no nó A do cluster. Por exemplo, em **pr1-ascs-0**. Atribua a unidade de disco compartilhado S, que é parte do grupo de cluster **SAP PR1** e é usada pela instância ASCS/SCS, ao cluster A do nó.

![Figura 61: Gerenciador de Cluster de Failover: O grupo de clusters SAP <SID> está em execução no nó de cluster A][sap-ha-guide-figure-5000]

_**Figura 61:** Gerenciador de Cluster de Failover: O grupo de clusters SAP <*SID*> está em execução no nó de cluster A_

Na ferramenta de Gerenciamento e Configuração do DataKeeper SIOS, você pode ver que os dados do disco compartilhado são replicados de modo síncrono da unidade do volume de origem S em um nó A de cluster para a unidade do volume de destino S no nó B de cluster. Por exemplo, é replicado de **pr1-ascs-0 [10.0.0.40]** a **pr1-ascs-1 [10.0.0.41]**.

![Figura 62: No SIOS DataKeeper, replique o volume local do nó de cluster A para o nó de cluster B][sap-ha-guide-figure-5001]

_**Figura 62:** No SIOS DataKeeper, replique o volume local do nó de cluster A para o nó de cluster B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover do nó A para o nó B

1. Você pode usar estas opções para iniciar um failover do grupo de clusters <*SID*> do SAP do nó A para o nó B de cluster:
   - Usar o Gerenciador de Cluster de Failover  
   - Usar o PowerShell de Cluster de Failover

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reiniciar o nó A de cluster no sistema operacional convidado do Windows (isso iniciará um failover automático do grupo de clusters SAP <*SID*> do nó A para o nó B).  
3. Reiniciar o nó de cluster A do portal do Azure (isso iniciará um failover automático do grupo de clusters SAP <*SID*> do nó A para o nó B).  
4. Reiniciar o nó de cluster A usando o Azure PowerShell (isso iniciará um failover automático do grupo de clusters SAP <*SID*> do nó A para o nó B).

   Após o failover, o grupo de clusters SAP <*SID*> está em execução no nó de cluster B. Por exemplo, em **pr1-ascs-1**.

   ![Figura 63: No Gerenciador de Cluster de Failover, o grupo de clusters SAP <SID> está em execução no nó de cluster B][sap-ha-guide-figure-5002]

   _**Figura 63**: No Gerenciador de Cluster de Failover, o grupo de clusters SAP <*SID*> está em execução no nó de cluster B_

   O disco compartilhado agora é montado no nó de cluster B. O SIOS DataKeeper está replicando dados da unidade do volume de origem S no nó de cluster B para a unidade do volume de destino S no nó de cluster A. Por exemplo, ele está replicando **pr1-ascs-1 [10.0.0.41]** a **pr1-ascs-0 [10.0.0.40]**.

   ![Figura 64: O SIOS DataKeeper replica o volume local do nó de cluster B para o nó de cluster A][sap-ha-guide-figure-5003]

   _**Figura 64:** O SIOS DataKeeper replica o volume local do nó de cluster B para o nó de cluster A_
