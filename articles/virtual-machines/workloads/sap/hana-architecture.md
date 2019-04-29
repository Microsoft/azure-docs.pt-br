---
title: Arquitetura do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Arquitetura de como implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 80ef63cdd9de8cb2340fe15d761402bb9f00fae9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795946"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Arquitetura do SAP HANA (Instâncias Grandes) no Azure

Em um nível alto, a solução SAP HANA do Azure (Instâncias Grandes) tem a camada de aplicativo SAP que reside nas VMs. A camada de banco de dados reside no hardware configurado por TDI do SAP localizado em um carimbo da Instância Grande na mesma região do Azure conectada à IaaS do Azure.

> [!NOTE]
> Implante a camada de aplicativo do SAP na mesma região do Azure que a camada do DBMS do SAP. Essa regra é bem documentada em informações publicadas sobre cargas de trabalho do SAP no Azure. 

A arquitetura geral do SAP HANA do Azure (Instâncias Grandes) fornece uma configuração de hardware certificado por TDI do SAP, que é um servidor de alto desempenho, não virtualizado e bare-metal para o banco de dados do SAP HANA. Além disso, fornece a capacidade e a flexibilidade do Azure para dimensionar recursos para a camada de aplicativos do SAP para atender às suas necessidades.

![Visão geral da arquitetura do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três seções:

- **à direita**: Mostra uma infraestrutura local que executa aplicativos diferentes em data centers para que os usuários finais possam acessar aplicativos, como o SAP de LOB. O ideal é que essa infraestrutura local seja conectada ao Azure com [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro de**: Mostra a IaaS do Azure e, nesse caso, uso de VMs para hospedar SAP ou outros aplicativos que usam o SAP HANA como um sistema DBMS. Instâncias menores do HANA que funcionam com a memória que as VMs fornecem são implantadas em VMs em conjunto com a camada de aplicativo. Para obter mais informações sobre máquinas virtuais, consulte [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

   Os serviços de rede do Azure são usados para agrupar sistemas SAP em conjunto com outros aplicativos em redes virtuais. Essas redes virtuais conectam-se a sistemas locais, bem como ao SAP HANA do Azure (Instâncias Grandes).

   Para aplicativos SAP NetWeaver e bancos de dados que têm suporte para execução no Azure, consulte [suporte a nota SAP n º 1928533 – aplicativos SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533). Para obter documentação sobre como implantar soluções SAP no Azure, consulte:

  -  [Usar o SAP em máquinas virtuais do Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Usar as soluções SAP em máquinas virtuais do Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Left**: Mostra o hardware certificado por TDI do SAP HANA no carimbo da instância grande do Azure. As unidades do SAP HANA em Instâncias Grandes são conectadas às redes virtuais da assinatura, usando a mesma tecnologia que a conectividade do local para o Azure.

O carimbo de Instância Grande do Azure em si combina os seguintes componentes:

- **Computação**: Servidores baseados em processadores Intel Xeon E7-8890v3 ou Intel Xeon E7-8890v4 que fornecem a capacidade de computação necessária e são certificados pelo SAP HANA.
- **Rede**: Uma malha de rede unificada de alta velocidade que interconexões de computação, armazenamento e componentes de LAN.
- **Armazenamento**: Uma infraestrutura de armazenamento que é acessada por meio de uma malha de rede unificada. A capacidade de armazenamento específica fornecida depende da configuração específica do SAP HANA do Azure (Instâncias Grandes) implantada. Mais capacidade de armazenamento está disponível por um custo mensal adicional.

Na infraestrutura multilocatária do carimbo de Instância Grande, os clientes são implantados como locatários isolados. Na implantação do locatário, você nomeia uma assinatura do Azure na inscrição do Azure. Essa assinatura do Azure é aquela com a qual o SAP HANA em Instâncias Grandes é cobrado. Esses locatários têm uma relação de 1:1 para a assinatura do Azure. Para uma rede, é possível acessar uma unidade SAP HANA em Instâncias Grandes implantada em um locatário em uma região do Azure, a partir de diferentes redes virtuais que pertencem a diferentes assinaturas do Azure. Essas assinaturas do Azure devem pertencer à mesma inscrição do Azure. 

Assim como nas VMs, o SAP HANA do Azure (Instâncias Grandes) é oferecido em várias regiões do Azure. Para oferecer recursos de recuperação de desastre, você pode optar por aceitar. Os carimbos de Instância Grande contidos em uma região geopolítica do Azure são conectados uns aos outros. Por exemplo, os Carimbos do SAP HANA em Instâncias Grandes no Oeste dos EUA e Leste dos EUA são conectados por meio de um link de rede dedicado para replicação de recuperação de desastre. 

Assim como é possível escolher entre os diferentes tipos de VM com Máquinas Virtuais do Azure, você pode escolher entre as diferentes SKUs do SAP HANA em Instâncias Grandes que são personalizadas para diferentes tipos de carga de trabalho do SAP HANA. O SAP aplica taxas de soquete de memória para processador para cargas de trabalho variadas com base nas gerações de processadores Intel. A tabela a seguir mostra os tipos de SKU oferecidos.

Você pode localizar SKUs disponíveis [SKUs disponíveis para HLI](hana-available-skus.md).

**Próximas etapas**
- Veja [Arquitetura de rede do SAP HANA (Instâncias Grandes)](hana-network-architecture.md)