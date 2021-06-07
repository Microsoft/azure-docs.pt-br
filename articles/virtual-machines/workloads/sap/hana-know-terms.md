---
title: Conhecer os termos do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Conhecer os termos do SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ef2e59dab1921eae8e7d88249e75116601c597
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670861"
---
# <a name="know-the-terms"></a>Conhecer os termos

Várias definições comuns são amplamente usadas no guia de implantação técnica e arquitetura. Observe os seguintes termos e seus significados:

- **IaaS**: infraestrutura como serviço.
- **PaaS**: plataforma como serviço.
- **SaaS**: software como serviço.
- **Componente SAP**: um aplicativo SAP individual, como um componente central de ERP (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
- **Ambiente SAP**: um ou mais componentes SAP agrupados logicamente para executar uma função comercial, como desenvolvimento, garantia de qualidade, treinamento, recuperação de desastres ou produção.
- **Paisagem do SAP:** refere-se a todos os ativos do SAP no cenário de TI. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
- **SAP system**: a combinação da camada do DBMS e camada de aplicação de, por exemplo, um sistema de desenvolvimento SAP ERP, um sistema de teste SAP BW e um sistema de produção SAP CRM. As implantações do Azure não dão suporte à divisão dessas duas camadas entre local e Azure. Um sistema SAP é implantado no local ou é implantado no Azure. É possível implantar os diferentes sistemas de uma paisagem SAP no local ou no Azure. Por exemplo, você pode implantar os sistemas de desenvolvimento e teste do SAP CRM no Azure enquanto implanta o sistema de produção do SAP CRM no local. Para SAP HANA do Azure (Instâncias Grandes), pretende-se que você hospede a camada de aplicativo do SAP dos sistemas SAP em VMs e a instância do SAP HANA relacionada em uma unidade no carimbo do SAP HANA do Azure (Instâncias Grandes).
- **Carimbo da Instância Grande**: uma pilha de infraestrutura de hardware com certificação TDI do SAP HANA e dedicada para executar instâncias do SAP HANA no Azure.
- **SAP HANA do Azure (Instâncias Grandes):** nome oficial para a oferta no Azure para executar instâncias do HANA em hardware certificado por TDI do SAP HANA implantado em carimbos de Instância Grande em diferentes regiões do Azure. O termo relacionado *HANA em Instâncias Grandes* é a abreviação de *SAP HANA do Azure (Instâncias Grandes)* e é amplamente usado neste guia de implantação técnica.
- **Entre instalações**: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de ExpressRoute entre os data centers locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. O motivo da conexão é estender os domínios locais, OpenLDAP/Azure Active Directory local e DNS local para o Azure. A paisagem local é estendida aos ativos do Azure das assinaturas do Azure. Com essa extensão, as VMs podem fazer parte do domínio local. 

   Os usuários de domínio do domínio local podem acessar os servidores e executar serviços nessas VMs (como serviços de DBMS). A comunicação e a resolução de nomes entre VMs implantadas no local e VMs implantadas no Azure são possíveis. Esse cenário é típico da maneira como a maioria dos ativos do SAP é implantada. Para obter mais informações, consulte [Gateway de VPN do Azure](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) e [criar uma rede virtual com uma conexão site a site usando o portal do Azure](../../../vpn-gateway/tutorial-site-to-site-portal.md).
- **Locatário**: um cliente implantado no carimbo do SAP HANA em Instâncias Grandes é isolado em um *locatário.* Um locatário é isolado de outros locatários na camada de rede, de armazenamento e de computação. As unidades de computação e armazenamento atribuídas aos diferentes locatários não podem visualizar umas às outras nem comunicarem entre si no nível de carimbo do SAP HANA em Instâncias Grandes. Um cliente pode escolher ter implantações em diferentes locatários. Mesmo assim, não há nenhuma comunicação entre locatários no nível de selo de Instância Grande do HANA.
- **Categoria de SKU**: para o SAP HANA em Instâncias Grandes, são oferecidas as duas categorias de SKUs a seguir:
    - **Classe tipo I**: s72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 e S224m
    - **Classe do tipo II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm e S960m
- **Stamp**: define o tamanho da implantação interna da Microsoft de instâncias grandes do Hana. Antes que as unidades de instância grande do HANA possam ser implantadas, um carimbo de instância grande do HANA que consiste em racks de computação, rede e armazenamento precisa ser implantado em um local de datacenter. Essa implantação é chamada de carimbo de instância grande do HANA ou da revisão 4 (veja abaixo) em nós usamos a alternativa do termo da **linha de instância grande**
- **Revisão**: há duas revisões de carimbo diferentes para carimbos de instância grande do Hana. Eles diferem na arquitetura e na proximidade com os hosts da máquina virtual do Azure
    - "Revisão 3" (Rev. 3): é o design original que foi implantado a partir do meio do ano 2016
    - "Revisão 4" (Rev 4): é um novo design que pode fornecer proximidade com os hosts de máquina virtual do Azure e com essa latência de rede menor entre as VMs do Azure e as unidades de instância grande do HANA 
    - "Revisão 4,2" (Rev 4,2): na revisão 4 DCs existentes, os recursos são remarcados para a infraestrutura BareMetal.  Os clientes podem acessar seus recursos como instâncias de BareMetal do portal do Azure. 

Uma variedade de recursos adicionais está disponível sobre como implantar uma carga de trabalho do SAP na nuvem. Se você planeja executar uma implantação do SAP HANA do Azure, é necessário ter experiência e estar ciente dos princípios do IaaS do Azure e da implantação das cargas de trabalho do SAP no IaaS do Azure. Antes de continuar, consulte [Usar as soluções SAP nas máquinas virtuais do Azure ](get-started.md) para obter mais informações. 

**Próximas etapas**
- Veja [Certificação HLI](hana-certification.md)