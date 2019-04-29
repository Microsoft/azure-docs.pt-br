---
title: Visão geral de um cenário de recuperação de desastre do Oracle no ambiente do Azure | Microsoft Docs
description: Um cenário de recuperação de desastre do banco de dados Oracle Database 12c no ambiente do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 09df1421d6deae6db305cef2a46d6c40d0c12ba3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835834"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Recuperação de desastre para um banco de dados Oracle Database 12c em um ambiente do Azure

## <a name="assumptions"></a>Suposições

- Você compreende o design dos ambientes do Oracle Data Guard e do Azure.


## <a name="goals"></a>Metas
- Projetar a topologia e a configuração para atender aos seus requisitos de recuperação de desastre.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Cenário 1: Sites primário e recuperação de desastres no Azure

Um cliente tem uma configuração de banco de dados Oracle no site primário. Um site de recuperação de desastre está em uma região diferente. O cliente usa o Oracle Data Guard para recuperação rápida entre esses sites. O site primário também tem um banco de dados secundário para relatórios e outros usos. 

### <a name="topology"></a>Topologia

Veja aqui um resumo da configuração do Azure:

- Dois sites (um site primário e um site de recuperação de desastre)
- Duas redes virtuais
- Dois bancos de dados Oracle com Data Guard (primário e em espera)
- Dois bancos de dados Oracle com Golden Gate ou Data Guard (somente site primário)
- Dois serviços de aplicativos, um principal e um site de recuperação de desastre
- Um *conjunto de disponibilidade* usado para o banco de dados e para o serviço de aplicativo no site primário
- Um jumpbox em cada site, que restringe o acesso à rede privada, permitindo que somente o administrador se conecte
- Um jumpbox, um serviço de aplicativo, um banco de dados e um gateway de VPN em sub-redes separadas
- O NSG imposto em sub-redes de aplicativo e do banco de dados

![Captura de tela da página de topologia de recuperação de desastre](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Cenário 2: Site primário local e o site de recuperação de desastres no Azure

Um cliente tem uma configuração de banco de dados Oracle local (site primário). Um site de recuperação de desastre reside no Azure. O Oracle Data Guard é usado para recuperação rápida entre esses sites. O site primário também tem um banco de dados secundário para relatórios e outros usos. 

Há duas abordagens para essa configuração.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Abordagem 1: Conexões diretas entre local e o Azure, é necessário abrir as portas TCP no firewall 

Nós não recomendamos conexões diretas, pois expõem as portas TCP para o mundo exterior.

#### <a name="topology"></a>Topologia

Veja aqui um resumo da configuração do Azure:

- Um site de recuperação de desastre 
- Uma rede virtual
- Um banco de dados Oracle com Data Guard (ativo)
- Um serviço de aplicativo no site de recuperação de desastre
- Um Jumpbox, que restringe o acesso à rede privada, permitindo que somente o administrador se conecte
- Um jumpbox, um serviço de aplicativo, um banco de dados e um gateway de VPN em sub-redes separadas
- O NSG imposto em sub-redes de aplicativo e do banco de dados
- Uma política/regra de NSG para permitir entrada na porta TCP 1521 (ou uma porta definida pelo usuário)
- Adicione a política/regra de NSG para restringir somente endereços IP/locais (BD ou aplicativo) para acessarem a rede virtual

![Captura de tela da página de topologia de recuperação de desastre](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Abordagem 2: VPN site a site
A VPN site a site é uma abordagem melhor. Para saber mais sobre como configurar uma VPN, veja [Criar uma rede virtual com uma conexão VPN Site a Site usando a CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologia

Veja aqui um resumo da configuração do Azure:

- Um site de recuperação de desastre 
- Uma rede virtual 
- Um banco de dados Oracle com Data Guard (ativo)
- Um serviço de aplicativo no site de recuperação de desastre
- Um Jumpbox, que restringe o acesso à rede privada, permitindo que somente o administrador se conecte
- Um jumpbox, um serviço de aplicativo, um banco de dados e um gateway de VPN ficam em sub-redes separadas
- O NSG imposto em sub-redes de aplicativo e do banco de dados
- Conexão VPN site a site entre sites locais e o Azure

![Captura de tela da página de topologia de recuperação de desastre](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Leitura adicional

- [Projetar e implementar um banco de dados Oracle no Azure](oracle-design.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurar o Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e recuperação do Oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explorar exemplos da CLI do Azure de implantação de VM](../../linux/cli-samples.md)
