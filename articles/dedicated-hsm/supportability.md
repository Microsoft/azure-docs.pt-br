---
title: Capacidade de suporte - HSM Dedicado do Azure | Microsoft Docs
description: Opções de suporte e áreas de responsabilidade para o HSM Dedicado do Azure em cenários diferentes
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606922"
---
# <a name="azure-dedicated-hsm-supportability"></a>Capacidade de Suporte do HSM Dedicado do Azure

O serviço HSM Dedicado do Azure fornece um dispositivo físico para uso exclusivo do cliente, com controle administrativo e responsabilidade de gerenciamento. O dispositivo disponibilizado é um [modelo de HSM Thales Luna 7 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). A Microsoft não tem acesso administrativo, uma vez provisionado por um cliente, além do anexo de porta serial física como uma função de monitoramento.  Sem acesso, a Microsoft não pode ter responsabilidades quanto à manutenção contínua em nível de software ou administração de sistema. Como resultado, os clientes são responsáveis por atividades operacionais típicas.
Os clientes são totalmente responsáveis por aplicativos que usam os HSMs e devem trabalhar com Thales para obter suporte ou assistência baseada em consultoria. Devido à extensão de propriedade do cliente de higiene de troca operacional, não é possível para a Microsoft oferecer qualquer tipo de garantia de alta disponibilidade para esse serviço. É responsabilidade do cliente garantir que seus aplicativos estejam configurados corretamente para obter alta disponibilidade. A Microsoft irá monitorar e manter a conectividade de rede e integridade do dispositivo.

## <a name="getting-support"></a>Obtendo suporte

O atendimento ao cliente para HSM dedicado é um esforço conjunto entre a Microsoft e o Thales. Quaisquer problemas de hardware ou de caminho de rede serão abordados pela Microsoft, e qualquer coisa a fazer com o HSM real, como configuração, software, firmware e desenvolvimento de aplicativos, será abordada pelo Thales. Esse modelo de suporte garante a rota mais rápida para o suporte mais eficaz. Se estiver em dúvida com um problema específico, gere uma solicitação de suporte com a Microsoft e garantiremos que você será direcionado adequadamente. A Microsoft se manterá envolvida em todos os cenários de suporte e buscará a melhor experiência de suporte para nossos clientes.

## <a name="thales-support"></a>Suporte do Thales

Os clientes que usam o serviço HSM dedicado se qualificam para o suporte do Thales de acordo com seu plano de suporte Plus. Isso apenas requer um processo de registro usando o portal de suporte do Thales. Uma ID de cliente e instruções serão fornecidas para isso como parte do envolvimento inicial com a Microsoft para obter acesso ao serviço HSM dedicado. O mecanismo para obter suporte do Thales é por meio de seu [portal de atendimento ao cliente](https://supportportal.thalesgroup.com/csm).
Um ponto importante de observação é que o Thales fornecerá todos os softwares e a documentação necessários para usar o HSM (por exemplo, software de acesso para cliente e SDKs) por meio do download no portal de atendimento ao cliente.

### <a name="software-components"></a>Componentes de software

Vários componentes de software são usados na configuração de dispositivos HSM:

* Software cliente
* .
* Ferramentas

### <a name="guidance"></a>Diretrizes

O Thales disponibiliza as diretrizes de administração e configuração disponíveis por meio do [portal de suporte ao cliente do Thales](https://supportportal.thalesgroup.com/csm). Depois de conectado usando uma ID de cliente válido, esses documentos estão disponíveis para download. O Thales também fornece uma série de guias de integração para ajudar os clientes com diferentes cenários e integrações de software. Para obter mais informações, consulte o [site de parceiros do Thales para a Microsoft](https://cpl.thalesgroup.com/partners/overview).

### <a name="support"></a>Suporte

Qualquer problema ou questão de nível de software em relação ao uso dos HSMs como parte do serviço HSM dedicado deve ser endereçado diretamente ao suporte do Thales. Todos os componentes de software listados acima, e qualquer configuração de HSM personalizada que esteja após o provisionamento, serão tratados pelo Thales. Para obter mais informações, consulte o [portal de suporte ao cliente do Thales](https://supportportal.thalesgroup.com/csm).

### <a name="consulting-services"></a>Serviços de consultoria

Para qualquer assistência no design, no desenvolvimento e na implantação de aplicativos personalizados que usam o HSM, entre em contato com seu representante de conta do Thales.

## <a name="microsoft-support"></a>Suporte da Microsoft

A Microsoft garantirá que os dispositivos HSM físicos estejam acessíveis à rede e em um estado operacional para o uso exclusivo de um único cliente. Os clientes são responsáveis pela configuração, administração e gerenciamento do dispositivo. As responsabilidades da Microsoft incluem:

* Certificar-se que o dispositivo tenha alimentação e resfriamento
* Manter um estado operacional do HSM (por exemplo, cenários de reparos)
* O dispositivo é acessível pela rede.

Problemas como a seguir devem ser relatados à Microsoft:

* Falhas de componente
* Falha de dispositivo completo
* Problemas de acesso à rede
* Problemas de provisionamento e desprovisionamento.

A Microsoft tem acesso de porta serial física ao dispositivo por meio de uma função de monitoramento (que é uma função não administrativa) que permite a telemetria de integridade básica.  Isso permitirá que a Microsoft forneça notificação proativa de problemas para o cliente, a menos que o cliente escolha restringir essa permissão. 

### <a name="provisioning-and-decommissioning"></a>Provisionamento e encerramento

Depois que um cliente tiver um registro aprovado para o serviço HSM Dedicado, poderá criar recursos HSM (no momento por meio do PowerShell ou interface de linha de comando e não o portal do Azure). O recurso passa por um processo de alocação que mapeia um dispositivo físico em uma região especificada para uma rede virtual (VNet) predefinida do cliente. Depois que é visível em uma VNet, o cliente pode acessar o dispositivo e configurá-lo ainda mais, de acordo com requisitos. Os clientes acessam seus HSMs dedicados usando o software cliente e as ferramentas do Thales. O processo de criação de recursos tem suporte pela Microsoft. O processo de configuração personalizada e posteriores tem suporte do Thales. (consulte suporte do Thales acima). Quando um cliente tiver terminado de usar um HSM, ele deve ser redefinido (ou zerado) para garantir nenhuma persistência de dados. O processo de redefinição do dispositivo remove todos os dados e configuração personalizada. A Microsoft desaloca o dispositivo e retorna-o para o pool em um estado puro. Isso significa que, quando o dispositivo for devolvido ao pool, não haverá nenhuma evidência de atividades anteriores do cliente. 

### <a name="hardware-issues"></a>Problemas de hardware

O dispositivo HSM tem fontes de alimentação redundantes e substituíveis e unidades de ventilador.  No entanto, a remoção da unidade do ventilador ainda causará um evento de violação. Quando ocorrer uma falha de componente, a Microsoft usará o processo mais apropriado para resolver o problema de nível de componente de forma que faça com o mínimo de interrupção e menor risco a disponibilidade do serviço de nossos clientes.
Qualquer falha mais grave do dispositivo fará com que o dispositivo seja substituído por um novo dispositivo do pool livre. O cliente simplesmente inclui o novo dispositivo no par HA existente para sincronizar e retornar para o estado operacional completo. O dispositivo com falha terá seus dados que contêm dispositivos removidos e destruídos no site no data center. 

### <a name="networking-issues"></a>Problemas de rede

Se os clientes enfrentarem problemas de acesso de rede para o dispositivo HSM, ele deverá contatar o suporte da Microsoft. Um teste simples para acesso de rede é usar o SSH para se conectar ao dispositivo HSM. Se isso falhar, entre em contato com o suporte da Microsoft.

## <a name="service-level-expectations-for-support"></a>Expectativas de nível de serviço para o suporte

Para os níveis de serviço de suporte da Microsoft, consulte o [plano de suporte do Azure](https://azure.microsoft.com/support/plans/).
Para obter os níveis de serviço de suporte do Thales, consulte o [Essentials de suporte do Thales](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Recomenda-se que todos os principais conceitos, como alta disponibilidade e segurança, sejam bem compreendidos antes do provisionamento do dispositivo e do design ou implantação do aplicativo.

* [Arquitetura de implantação](deployment-architecture.md)
* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)

