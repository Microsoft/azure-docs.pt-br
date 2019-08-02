---
title: Infraestrutura e conectividade para o SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Configure a infraestrutura de conectividade necessária para usar SAP HANA no Azure (instâncias grandes).
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
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a1df7406ab4f4d7137d12dd7131a4c26b617cb2
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869217"
---
# <a name="sap-hana-large-instances-deployment"></a>Implantação do SAP HANA (instâncias grandes) 

Este artigo pressupõe que você concluiu a compra do SAP HANA no Azure (instâncias grandes) da Microsoft. Antes de ler este artigo, familiarize-se com os [termos comuns do HANA em Instâncias Grandes](hana-know-terms.md) e os [SKUs do HANA em instâncias grandes](hana-available-skus.md).


A Microsoft requer as seguintes informações para implantar unidades de instância grande do HANA:

- Nome do cliente.
- Informações de contatos comerciais (incluindo endereço de email e número de telefone).
- Informações de contatos técnicos (incluindo endereço de email e número de telefone).
- Informações de contatos da rede técnica (incluindo endereço de email e número de telefone).
- Região de implantação do Azure (por exemplo, oeste dos EUA, Leste da Austrália ou Europa Setentrional).
- SAP HANA no Azure (instâncias grandes) SKU (configuração).
- Para cada região de implantação do Azure:
    - Um intervalo de endereços IP /29 para conexões de ER-P2P que conectar redes virtuais do Azure para instâncias grandes HANA.
    - Um bloco CIDR /24 usado para o pool de IPs de servidores de instâncias grandes HANA.
    - Opcional ao usar o [ExpressRoute alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para habilitar o roteamento direto do local para as unidades de instância grande do Hana ou o roteamento entre unidades de instância grande do Hana em diferentes regiões do Azure, você precisa reservar outro/29 intervalo de endereços IP. Esse intervalo específico não pode se sobrepor a nenhum dos outros intervalos de endereços IP definidos anteriormente.
- Os valores de intervalo de endereços IP usados no atributo de espaço de endereço de rede virtual de cada rede virtual do Azure que se conecta às instâncias grandes do HANA.
- Dados para cada sistema de instâncias grandes HANA:
  - Nome de host desejado - o ideal é usar o nome de domínio totalmente qualificado.
  - Endereço IP desejado para a unidade de instância grande HANA fora do intervalo de endereços do pool de IP do servidor. (os 30 primeiros endereços IP no intervalo de endereços do pool IP do servidor são reservados para uso interno em instâncias grandes HANA).
  - Nome do SID do SAP HANA da instância do SAP HANA (necessário para criar os volumes de disco relacionados ao SAP HANA necessários). A Microsoft precisa do SID do HANA para criar as permissões para sidadm nos volumes NFS. Esses volumes anexam a unidade de instância grande do HANA. O HANA SID também é usado como um dos componentes de nome dos volumes de disco que são montados. Se você desejar executar mais de uma instância do HANA na unidade, deverá listar vários SIDs do HANA. Cada uma obtém um conjunto separado de volumes atribuídos.
  - No sistema operacional Linux, o usuário sidadm tem uma ID de grupo. Essa ID é necessária para criar os volumes de disco relacionados ao SAP HANA. A instalação do SAP HANA normalmente cria o grupo de sapsys com uma ID de grupo de 1001. O usuário sidadm faz parte do grupo.
  - No sistema operacional Linux, o usuário sidadm tem uma ID de usuário. Essa ID é necessária para criar os volumes de disco relacionados ao SAP HANA. Se você estiver executando várias instâncias do HANA na unidade, liste todos os usuários sidadm. 
- ID de assinatura do Azure da assinatura do Azure na qual as Instâncias Grandes do HANA do SAP HANA no Azure serão conectadas diretamente. Essa ID de assinatura referencia a assinatura do Azure, que será cobrada com as unidades da Instância Grande do HANA ou unidades.

Depois de fornecer as informações acima, a Microsoft provisiona SAP HANA no Azure (instâncias grandes). A Microsoft envia informações para vincular suas redes virtuais do Azure para instâncias grandes do HANA. Você também pode acessar as unidades de instância grande do HANA.

Use a seguinte sequência para se conectar a instâncias grandes do HANA depois que a Microsoft implantou:

1. [Conectar as VMs do Azure para instâncias grandes do HANA](hana-connect-azure-vm-large-instances.md)
2. [Conectar uma VNet ao ExpressRoute do HANA em instâncias grandes](hana-connect-vnet-express-route.md)
3. [Requisitos de rede adicionais (opcional)](hana-additional-network-requirements.md)

