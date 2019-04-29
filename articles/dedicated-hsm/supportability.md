---
title: Capacidade de suporte - HSM Dedicado do Azure | Microsoft Docs
description: Opções de suporte e áreas de responsabilidade para o HSM Dedicado do Azure em cenários diferentes
services: dedicated-hsm
author: johndaw
manager: barbkess
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: 8341e9057b07fbb72957eddc342ae875e4591993
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912240"
---
# <a name="azure-dedicated-hsm-supportability"></a>Capacidade de Suporte do HSM Dedicado do Azure

O serviço HSM Dedicado do Azure fornece um dispositivo físico para uso exclusivo do cliente, com controle administrativo e responsabilidade de gerenciamento. O dispositivo disponibilizado é um [Gemalto SafeNet Luna 7 HSM modelo A790 ](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). A Microsoft não tem acesso administrativo, uma vez provisionado por um cliente, além do anexo de porta serial física como uma função de monitoramento.  Sem acesso, a Microsoft não pode ter responsabilidades quanto à manutenção contínua em nível de software ou administração de sistema. Como resultado, os clientes são responsáveis por atividades operacionais típicas.
Os clientes são totalmente responsáveis por aplicativos que usam os HSMs e devem funcionar com Gemalto para suporte ou assistência baseada em consultoria. Devido à extensão de propriedade do cliente de higiene de troca operacional, não é possível para a Microsoft oferecer qualquer tipo de garantia de alta disponibilidade para esse serviço. É responsabilidade do cliente garantir que seus aplicativos estejam configurados corretamente para obter alta disponibilidade. A Microsoft irá monitorar e manter a conectividade de rede e integridade do dispositivo.

## <a name="getting-support"></a>Obtendo suporte

Atendimento ao cliente para o HSM dedicado é um esforço conjunto entre a Microsoft e a Gemalto. Quaisquer problemas de hardware ou problemas de caminho de rede serão abordados pela Microsoft, e nada a ver com o HSM real, como configuração, software, firmware e desenvolvimento de aplicativos, será tratado por Gemalto. Esse modelo de suporte garante que a rota mais rápida para o suporte mais eficaz. Em caso de dúvida, com um problema específico, envie uma solicitação de suporte com a Microsoft e estamos garantirá que você é direcionado corretamente. A Microsoft se envolvam em todos os cenários de suporte e se esforçar para ter a melhor experiência de suporte para nossos clientes.

## <a name="gemalto-support"></a>Suporte a Gemalto

Os clientes que usam o serviço HSM dedicado se qualifica para o suporte da Gemalto, de acordo com suas além de plano de suporte. Isso requer apenas um processo de registro usando o portal de suporte a Gemalto. Uma ID de cliente e instruções serão fornecidas para isso como parte do contrato inicial com a Microsoft para acessar o serviço HSM dedicado. O mecanismo para obter suporte da Gemalto é por meio de seu [portal de suporte do cliente](https://supportportal.gemalto.com/csm/).
Um ponto-chave de nota é que a Gemalto fornecerá todos os software e a documentação necessária para usar o HSM (por exemplo, software de acesso de cliente e SDKs) por meio de download no portal de suporte do cliente.

### <a name="software-components"></a>Componentes de software

Vários componentes de software são usados na configuração de dispositivos HSM:

* Software cliente
* .
* Ferramentas

### <a name="guidance"></a>Diretrizes

A Gemalto torna a administração disponível e o guia de configuração através do [portal de suporte do cliente](https://supportportal.gemalto.com/csm/). Depois de conectado usando uma ID de cliente válido, esses documentos estão disponíveis para download. A Gemalto também fornece uma série de guias de integração para ajudar os clientes com diferentes cenários e integrações de software. Para obter mais informações, consulte o [site de parceiros Gemalto da Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Suporte

Qualquer problema de nível de software ou uma pergunta em relação ao uso de HSMs como parte do serviço do HSM Dedicado, devem ser endereçados diretamente ao suporte da Gemalto. Todos os componentes de software listados acima e qualquer configuração de HSM personalizada pós-provisionamento, serão tratadas pela Gemalto. Para obter mais informações, consulte o [portal de suporte do cliente Gemalto](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Serviços de consultoria

Para obter assistência no design, desenvolvimento e implantação de aplicativos personalizados que usam o HSM, entre em contato com seu representante de conta Gemalto.

## <a name="microsoft-support"></a>Suporte da Microsoft

Microsoft garantirá que os dispositivos HSM físicos são acessível e em um estado operacional para o uso exclusivo de um único cliente de rede. Os clientes são responsáveis por configuração, administração e gerenciamento do dispositivo. As responsabilidades da Microsoft incluem:

* Certificar-se que o dispositivo tenha alimentação e resfriamento
* Manter um estado operacional do HSM (por exemplo, cenários de reparos)
* O dispositivo é acessível pela rede.

Problemas como a seguir devem ser relatados à Microsoft:

* Falhas de componente
* Falha de dispositivo completo
* Problemas de acesso à rede
* Problemas de provisionamento e desprovisionamento.

A Microsoft tem acesso de porta serial física para o dispositivo por meio de uma função de monitoramento (ou seja, função não administrativa) que permite a telemetria de integridade básica.  Isso permitirá que a Microsoft forneça notificação proativa de problemas para o cliente, a menos que o cliente escolha restringir essa permissão. 

### <a name="provisioning-and-decommissioning"></a>Provisionamento e encerramento

Depois que um cliente tiver um registro aprovado para o serviço HSM Dedicado, poderá criar recursos HSM (no momento por meio do PowerShell ou interface de linha de comando e não o portal do Azure). O recurso passa por um processo de alocação que mapeia um dispositivo físico em uma região especificada para uma rede virtual (VNet) predefinida do cliente. Depois que é visível em uma VNet, o cliente pode acessar o dispositivo e configurá-lo ainda mais, de acordo com requisitos. Os clientes acessam seus HSMs dedicados usando o software e ferramentas do cliente Gemalto. O processo de criação de recursos tem suporte pela Microsoft. Processo de configuração personalizada e posteriores têm suporte pela Gemalto. (consulte o suporte Gemalto acima). Quando um cliente tiver terminado de usar um HSM, ele deve ser redefinido (ou zerado) para garantir nenhuma persistência de dados. O processo de redefinição do dispositivo remove todos os dados e configuração personalizada. A Microsoft desaloca o dispositivo e retorna-o para o pool em um estado puro. Isso significa que, quando o dispositivo for devolvido ao pool, não haverá nenhuma evidência de atividades anteriores do cliente. 

### <a name="hardware-issues"></a>Problemas de hardware

O dispositivo HSM tem fontes de alimentação redundantes e substituíveis e unidades de ventilador.  No entanto, a remoção do ventilador unidade ainda fará com que um evento de violação. Quando ocorrer uma falha de componente, a Microsoft usará o processo mais apropriado para resolver o problema de nível de componente de forma que faça com o mínimo de interrupção e menor risco a disponibilidade do serviço de nossos clientes.
Qualquer falha mais grave do dispositivo fará com que o dispositivo seja substituído por um novo do pool livre. O cliente simplesmente inclui o novo dispositivo no par HA existente para sincronizar e retornar para o estado operacional completo. O dispositivo com falha terá seus dados que contêm dispositivos removidos e destruídos no site no data center. Somente o chassi será retornado a Gemalto para reciclagem.


### <a name="networking-issues"></a>Problemas de rede

Se os clientes enfrentarem problemas de acesso de rede para o dispositivo HSM, ele deverá contatar o suporte da Microsoft. Um teste simples para acesso de rede é usar o SSH para se conectar ao dispositivo HSM. Se isso falhar, entre em contato com o suporte da Microsoft.

## <a name="service-level-expectations-for-support"></a>Expectativas de nível de serviço para o suporte

Para os níveis de serviço de suporte da Microsoft, consulte o [plano de suporte do Azure](https://azure.microsoft.com/support/plans/).
Para obter os níveis de suporte da Gemalto, consulte o [Gemalto Support Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Recomenda-se que todos os principais conceitos, como alta disponibilidade e segurança, sejam bem compreendidos antes do provisionamento do dispositivo e do design ou implantação do aplicativo.

* [Arquitetura de implantação](deployment-architecture.md)
* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)

