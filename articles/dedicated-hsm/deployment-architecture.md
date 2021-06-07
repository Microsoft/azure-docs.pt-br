---
title: Arquitetura de implantação - HSM Dedicado do Azure | Microsoft Docs
description: Considerações de design básicas ao usar HSM Dedicado do Azure como parte de uma arquitetura do aplicativo
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c454b2e4df7a9ce5fadd33386e5bb413b503c6e4
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608418"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Arquitetura de implantação de HSM Dedicado do Azure

O HSM dedicado do Azure fornece armazenamento de chave criptográfica no Azure. Atende aos rigorosos requisitos de segurança. Os clientes se beneficiarão do uso do HSM dedicado do Azure se:

* Deve atender [à certificação FIPS 140-2 nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final)
* Exigir que eles tenham acesso exclusivo ao HSM
* deve ter controle completo de seus dispositivos

Os HSMs são distribuídos pelos datacenters da Microsoft e podem ser facilmente provisionados como um par de dispositivos como a base de uma solução altamente disponível. Eles também podem ser implantados em várias regiões para uma solução resiliente a desastres. As regiões com HSM dedicado disponível atualmente podem ser verificadas usando a [página produtos por região](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm). 

* Leste dos EUA
* Leste dos EUA 2
* Oeste dos EUA
* Oeste dos EUA 2
* Centro-Sul dos Estados Unidos
* Sudeste Asiático
* Leste da Ásia
* Centro da Índia
* Sul da Índia
* Leste do Japão
* Oeste do Japão
* Norte da Europa
* Europa Ocidental
* Sul do Reino Unido
* Oeste do Reino Unido
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Sudeste da Austrália
* Norte da Suíça
* Oeste da Suíça
* Gov. dos EUA – Virgínia
* Governo dos EUA do Texas

Cada uma dessas regiões tem racks HSM implantados em dois datacenters independentes ou em pelo menos duas zonas de disponibilidade independentes. O Sudeste da Ásia possui três zonas de disponibilidade e o Leste dos EUA 2 possui duas. Há um total de vinte três regiões na Europa, na Ásia e no América do Norte que oferecem o serviço HSM dedicado. Para obter mais informações sobre regiões do Azure, consulte as [informações oficiais das regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Alguns fatores de design para qualquer solução Dedicada com base em HSM são localização/latência, alta disponibilidade e suporte para outros aplicativos distribuídos.

## <a name="device-location"></a>Localização do dispositivo

A localização ideal do dispositivo HSM está mais próxima dos aplicativos que executam operações criptográficas. Espera-se que a latência na região seja de um único dígito em milissegundos. A latência entre regiões pode ser de 5 a 10 vezes maior do que isso.

## <a name="high-availability"></a>Alta disponibilidade

Para obter alta disponibilidade, um cliente deve usar dois dispositivos HSM em uma região configurada usando o software Thales como um par de alta disponibilidade. Esse tipo de implantação garante a disponibilidade de chaves se um único dispositivo tiver um problema que o impeça de processar operações de chave. Também reduz significativamente o risco durante a manutenção de interrupções/reparos, como a substituição da fonte de alimentação. É importante que um design seja responsável por qualquer tipo de falha no nível regional. Falhas no nível regional podem acontecer quando há desastres naturais, como furacões, inundações ou terremotos. Esses tipos de eventos devem ser atenuados pelo provisionamento de dispositivos HSM em outra região. Dispositivos implantados em outra região podem ser emparelhados por meio da configuração de software Thales. Isso significa que a implantação mínima para uma solução altamente disponível e resiliente a desastres é de quatro dispositivos HSM em duas regiões. A redundância local e a redundância entre regiões podem ser usadas como uma linha de base para adicionar outras implantações de dispositivos HSM para suportar latência, capacidade ou atender a outros requisitos específicos de aplicativos.

## <a name="distributed-application-support"></a>Suporte de aplicativo distribuído

Os dispositivos HSM dedicados geralmente são implementados no suporte a aplicativos que precisam executar operações de armazenamento de chaves e recuperação de chaves. Os dispositivos HSM dedicados possuem 10 partições para suporte de aplicativo independente. O local do dispositivo deve ser baseado em uma visão holística de todos os aplicativos que precisam usar o serviço.

## <a name="next-steps"></a>Próximas etapas

Depois que a arquitetura de implantação for determinada, a maioria das atividades de configuração para implementar essa arquitetura será fornecida pelo Thales. Isso inclui a configuração do dispositivo e também os cenários de integração de aplicativos. Para obter mais informações, use o portal de [suporte ao cliente do Thales](https://supportportal.thalesgroup.com/csm) e baixe os guias de administração e configuração. O site parceiro da Microsoft possui uma variedade de guias de integração.
Recomenda-se que todos os principais conceitos do serviço, como alta disponibilidade e segurança, por exemplo, sejam bem compreendidos antes do provisionamento do dispositivo ou do design e da implantação do aplicativo.
Mais tópicos de nível de conceito:

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitoring](monitoring.md)
