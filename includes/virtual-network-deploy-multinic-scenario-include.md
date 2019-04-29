---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743235"
---
## <a name="scenario"></a>Cenário
Este documento orientará durante uma implantação que usa várias NICs em máquinas virtuais em um cenário específico. Nesse cenário, você tem uma carga de trabalho de IaaS em duas camadas hospedadas no Azure. Cada camada é implantada na sua própria sub-rede em uma rede virtual (VNet). A camada de front-end é composta de vários servidores da Web, agrupados em um balanceador de carga definida para alta disponibilidade. A camada de back-end é composta de vários servidores de banco de dados. Esses servidores de banco de dados serão implantados com duas NICs cada, uma para acesso de banco de dados, outra para gerenciamento. O cenário também inclui grupos de segurança de rede (NSGs) para controlar o que o tráfego seja permitido para cada sub-rede e NIC na implantação. A figura a seguir mostra a arquitetura básica desse cenário:

![Cenário de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)