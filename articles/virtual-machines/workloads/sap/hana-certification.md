---
title: Certificação do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Certificação do SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d623a189da5c5eccd73018e1ae0b7e5564fe09ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709699"
---
# <a name="certification"></a>Certificação

Além de certificação NetWeaver, SAP exige uma certificação especial para SAP HANA dar suporte a SAP HANA em determinadas infraestruturas, como IaaS do Azure.

É a principal nota SAP no NetWeaver e uma certificação do SAP HANA grau, [nota SAP n º 1928533 – aplicativos SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533).

Os registros de certificação do SAP HANA em unidades do Azure (Instâncias Grandes) podem ser encontrados no [site IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) certificado pelo SAP HANA. 

Os tipos SAP HANA no Azure (Instâncias Grandes), referidos no site de Plataformas IaaS certificadas do SAP HANA, fornecem aos clientes da Microsoft e SAP a capacidade de implantar grandes SAP Business Suite, SAP BW, S / 4 HANA, BW / 4HANA ou outros Cargas de trabalho do HANA no Azure. A solução é baseada no carimbo de hardware dedicado certificado pelo SAP-HANA ([Integração de data center personalizada do SAP HANA - TDI](https://scn.sap.com/docs/DOC-63140)). Se você executar uma solução configurada pela TDI do SAP HANA, todos os aplicativos baseados no SAP HANA (como SAP Business Suite no SAP HANA, SAP BW no SAP HANA, S4/HANA e BW4/HANA) funcionarão na infraestrutura de hardware.

Em comparação com a execução do SAP HANA em VMs, essa solução tem um benefício. Ela fornece volumes de memória muito maiores. Para habilitar essa solução, é necessário reconhecer os aspectos-chave a seguir:

- A camada de aplicativo do SAP e aplicativos não SAP executa em VMs hospedadas nos carimbos de hardware normais do Azure.
- A infraestrutura local do cliente, os data centers e as implantações de aplicativo estão conectados à plataforma de nuvem por meio do ExpressRoute (recomendado) ou de uma VPN (rede privada virtual). O Active Directory e o DNS também são estendidos para o Azure.
- A instância de banco de dados do SAP HANA para carga de trabalho do HANA é executada no SAP HANA no Azure (Instâncias Grandes). O carimbo da Instância Grande está conectado à rede do Azure, portanto, o software em execução nas VMs pode interagir com a instância do HANA em execução no SAP HANA em Instâncias Grandes.
- O hardware do SAP HANA no Azure (Instâncias Grandes) é um hardware dedicado fornecido em uma IaaS com SUSE Linux Enterprise Server ou Red Hat Enterprise Linux pré-instalado. Assim como nas máquinas virtuais, outras atualizações e manutenções no sistema operacional são de sua responsabilidade.
- A instalação do HANA ou de quaisquer componentes adicionais necessários para executar o SAP HANA em unidades do SAP HANA em Instâncias Grandes é de sua responsabilidade. Todas as respectivas operações contínuas e administração do SAP HANA do Azure também são de sua responsabilidade.
- Além das soluções descritas aqui, você pode instalar outros componentes na sua assinatura do Azure que se conecta ao SAP HANA no Azure (Instâncias Grandes). Exemplos são componentes que permitem a comunicação com ou diretamente ao banco de dados do SAP HANA, como servidores de saltos, servidores RDP, SAP HANA Studio, cenários SAP Data Services para SAP BI ou soluções de monitoramento de rede.
- Como no Azure, o SAP HANA em Instâncias Grandes dá suporte para funcionalidade de recuperação de desastre e alta disponibilidade.

**Próximas etapas**
- Veja [SKUs disponíveis para HLI](hana-available-skus.md) 