---
title: Opções de monitoramento - HSM dedicado do Azure | Microsoft Docs
description: Visão geral das opções de monitoramento do HSM Dedicado do Azure e responsabilidades de monitoramento
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2bcf1d1e007398cf80839f5e1d0bac78fd80db07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912222"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitoramento do HSM Dedicado do Azure

O serviço HSM Dedicado do Azure fornece um dispositivo físico para uso exclusivo do cliente, com controle administrativo e responsabilidade de gerenciamento. O dispositivo disponibilizado é um [Gemalto SafeNet Luna 7 HSM modelo A790 ](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  A Microsoft não tem acesso administrativo, uma vez provisionado por um cliente, além do anexo de porta serial física como uma função de monitoramento. Como resultado, os clientes são responsáveis por atividades operacionais típicas incluindo monitoramento abrangente e análise de logs.
Os clientes são totalmente responsáveis por aplicativos que usam os HSMs e devem funcionar com Gemalto para assistência de consultoria ou suporte. Devido à extensão de propriedade do cliente de higiene de troca operacional, não é possível para a Microsoft oferecer qualquer tipo de garantia de alta disponibilidade para esse serviço. É responsabilidade do cliente garantir que seus aplicativos estejam configurados corretamente para obter alta disponibilidade. A Microsoft irá monitorar e manter a conectividade de rede e integridade do dispositivo.

## <a name="microsoft-monitoring"></a>Monitoramento Microsoft

O dispositivo Gemalto SafeNet em uso tem por padrão SNMP e a porta serial como opções para monitorar o dispositivo. A Microsoft já usou a conexão de porta serial como meio de físico para se conectar ao dispositivo para recuperar a telemetria básica de integridade do dispositivo. Isso inclui itens como o status do componente e temperatura, como fontes de alimentação e ventiladores.
Para fazer isso, a Microsoft usa uma função não administrativa "monitorar" configurada no dispositivo Gemalto. Essa função fornece a capacidade de recuperar a telemetria, mas não fornece qualquer acesso ao dispositivo em termos de tarefa administrativa ou de qualquer forma, exibindo informações de criptografia. Nossos clientes podem ter certeza de que seu dispositivo é realmente seu para gerenciar, administrar e usar para armazenamento de chave criptográfica sensível. Caso qualquer cliente não esteja satisfeito com esse acesso mínimo para o monitoramento de integridade básicas, eles têm a opção de desabilitar a conta de monitoramento. A óbvia consequência disso é que a Microsoft não terá nenhuma informação e, portanto, não há capacidade de fornecer qualquer notificação de problemas de integridade do dispositivo. Nessa situação, o cliente é responsável pela integridade do dispositivo.
A função do monitor em si é configurada para sondar o dispositivo a cada 10 minutos para obter dados de integridade. Devido à natureza propensa a erro de comunicação serial, somente depois de vários indicadores de integridade negativos durante um período de uma hora seria um alerta ser gerado. Esse alerta, por fim, poderia levar a uma comunicação de cliente proativo notificando o problema.
Dependendo da natureza do problema, o curso de ação apropriado seria executado para reduzir o impacto e certificar-se da correção de baixo risco. Por exemplo, uma falha de fonte de alimentação é um procedimento intercambiável com nenhum evento de adulteração, assim pode ser realizado sem impacto baixo e risco mínimo para a operação. Outros procedimentos podem exigir um dispositivo seja zerado e desprovisionado para minimizar qualquer risco de segurança para o cliente. Nessa situação, um cliente provisionaria um dispositivo alternativo, reintegraria um emparelhamento de alta disponibilidade, portanto, disparando a sincronização de dispositivos. A operação normal será retomada no mínimo de tempo, com interrupção mínima e menor risco de segurança.  

## <a name="customer-monitoring"></a>Monitoramento de cluster

Uma proposta de valor do serviço HSM dedicado é o controle que obtém o cliente do dispositivo, especialmente considerando que é um dispositivo de entrega de nuvem. Uma consequência desse controle é a responsabilidade para monitorar e gerenciar a integridade do dispositivo. O dispositivo Gemalto SafeNet vem com diretrizes para implementação de SNMP e Syslog. Recomenda-se que os clientes do HSM Dedicado usem-no, mesmo quando a conta de monitoramento da Microsoft permanecer ativa e deve-se considerá-lo obrigatório se desabilitar a conta de monitoramento da Microsoft.
Qualquer técnica disponível permitiria que um cliente identifique problemas e chamaria o suporte da Microsoft para iniciar o trabalho de correção apropriada.

## <a name="next-steps"></a>Próximas etapas

É recomendável que todos os principais conceitos do serviço, como alta disponibilidade e segurança, por exemplo, sejam bem compreendidos antes de qualquer provisionamento de dispositivos e o design do aplicativo ou a implantação. Mais tópicos de nível de conceito:

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
