---
title: Arquitetura do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Arquitetura de como implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45b84360c941e879461500a033ada3642fd5cbb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671622"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Arquitetura do SAP HANA (Instâncias Grandes) no Azure

Em um nível alto, a solução SAP HANA do Azure (Instâncias Grandes) tem a camada de aplicativo SAP que reside nas VMs. A camada de banco de dados reside no hardware configurado por TDI do SAP localizado em um carimbo da Instância Grande na mesma região do Azure conectada à IaaS do Azure.

> [!NOTE]
> Implante a camada de aplicativo do SAP na mesma região do Azure que a camada do DBMS do SAP. Essa regra é bem documentada em informações publicadas sobre cargas de trabalho do SAP no Azure. 

A arquitetura geral do SAP HANA do Azure (Instâncias Grandes) fornece uma configuração de hardware certificado por TDI do SAP, que é um servidor de alto desempenho, não virtualizado e bare-metal para o banco de dados do SAP HANA. Além disso, fornece a capacidade e a flexibilidade do Azure para dimensionar recursos para a camada de aplicativos do SAP para atender às suas necessidades.

![Visão geral da arquitetura do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três seções:

- **Direita**: mostra uma infraestrutura local que executa aplicativos diferentes em data centers para que os usuários finais possam acessar aplicativos de linha de negócios, como o SAP. O ideal é que essa infraestrutura local esteja conectada ao Azure com o [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro**: mostra a IaaS do Azure e, nesse caso, o uso de VMs para hospedar o SAP ou outros aplicativos que usam o SAP HANA, como um sistema de DBMS. Instâncias menores do HANA que funcionam com a memória que as VMs fornecem são implantadas em VMs em conjunto com a camada de aplicativo. Para obter mais informações sobre máquinas virtuais, consulte [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

   Os serviços de rede do Azure são usados para agrupar sistemas SAP em conjunto com outros aplicativos em redes virtuais. Essas redes virtuais conectam-se a sistemas locais, bem como ao SAP HANA do Azure (Instâncias Grandes).

   Para bancos de dados e aplicativos do SAP NetWeaver com suporte para execução no Azure, consulte a [Nota de Suporte SAP nº 1928533 – Aplicativos do SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533). Para obter documentação sobre como implantar soluções SAP no Azure, consulte:

  -  [Usar o SAP em máquinas virtuais do Windows](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Usar as soluções SAP em máquinas virtuais do Azure](get-started.md)

- **Esquerda**: mostra o hardware certificado por TDI do SAP HANA no carimbo da Instância Grande do Azure. As unidades de instância grande do HANA estão conectadas às redes virtuais da sua assinatura do Azure usando a mesma tecnologia que a conectividade do local para o Azure. A partir de maio de 2019, foi introduzida uma otimização que permite a comunicação entre as unidades de instância grande do HANA e as VMs do Azure sem envolvimento do gateway de ExpressRoute. Essa otimização chamada caminho rápido do ExpressRoute é exibida nessa arquitetura (linhas vermelhas). 

O carimbo de Instância Grande do Azure em si combina os seguintes componentes:

- **Computação**: servidores baseados em diferentes gerações de processadores Intel Xeon que fornecem o recurso de computação necessário e que são SAP Hana certificados.
- **Rede**: uma malha de rede unificada de alta velocidade que interconecta os componentes de computação, armazenamento e LAN.
- **Armazenamento:** uma infraestrutura de armazenamento que é acessada por meio de uma malha de rede unificada. A capacidade de armazenamento específica fornecida depende da configuração específica do SAP HANA do Azure (Instâncias Grandes) implantada. Mais capacidade de armazenamento está disponível por um custo mensal adicional.

Na infraestrutura multilocatária do carimbo de Instância Grande, os clientes são implantados como locatários isolados. Na implantação do locatário, você nomeia uma assinatura do Azure na inscrição do Azure. Essa assinatura do Azure é aquela com a qual o SAP HANA em Instâncias Grandes é cobrado. Esses locatários têm uma relação de 1:1 para a assinatura do Azure. Para uma rede, é possível acessar uma unidade SAP HANA em Instâncias Grandes implantada em um locatário em uma região do Azure, a partir de diferentes redes virtuais que pertencem a diferentes assinaturas do Azure. Essas assinaturas do Azure devem pertencer à mesma inscrição do Azure. 

Assim como nas VMs, o SAP HANA do Azure (Instâncias Grandes) é oferecido em várias regiões do Azure. Para oferecer recursos de recuperação de desastre, você pode optar por aceitar. Os carimbos de Instância Grande contidos em uma região geopolítica do Azure são conectados uns aos outros. Por exemplo, os Carimbos do SAP HANA em Instâncias Grandes no Oeste dos EUA e Leste dos EUA são conectados por meio de um link de rede dedicado para replicação de recuperação de desastre. 

Assim como é possível escolher entre os diferentes tipos de VM com Máquinas Virtuais do Azure, você pode escolher entre as diferentes SKUs do SAP HANA em Instâncias Grandes que são personalizadas para diferentes tipos de carga de trabalho do SAP HANA. O SAP aplica taxas de soquete de memória para processador para cargas de trabalho variadas com base nas gerações de processadores Intel. A tabela a seguir mostra os tipos de SKU oferecidos.

Você pode localizar SKUs disponíveis [SKUs disponíveis para HLI](hana-available-skus.md).

**Próximas etapas**
- Veja [Arquitetura de rede do SAP HANA (Instâncias Grandes)](hana-network-architecture.md)
