---
title: Segurança física do HSM – HSM dedicado do Azure | Microsoft Docs
description: Informações sobre a segurança física dos dispositivos de HSM dedicado do Azure em data centers
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8437d12075a148a3e8062f62e195bc019d89a85b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606956"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Segurança física do HSM dedicado do Azure

O HSM dedicado do Azure ajuda você a atender aos requisitos avançados de segurança para armazenamento de chaves. Ele é gerenciado seguindo práticas de segurança rigorosas em todo o seu ciclo de vida para atender às necessidades dos clientes.

## <a name="security-through-procurement"></a>Segurança por meio de compras

A Microsoft segue um processo de aquisição seguro. Gerenciamos a cadeia de custódia e garantimos que o dispositivo específico solicitado e enviado seja o dispositivo que chega aos nossos data centers. Os dispositivos estão em contêineres e pacotes de plástico de evento de violação serializados. Eles são armazenados em uma área de armazenamento seguro até serem comissionados na galeria de dados do data center.  Os racks que contêm os dispositivos HSM são considerados de alto impacto nos negócios (HBI). Os dispositivos estão bloqueados e sob vigilância por vídeo em todos os momentos, frente e verso.

## <a name="security-through-deployment"></a>Segurança através da implantação

Os HSMs são instalados em racks juntamente com componentes de rede associados. Depois de instalados, eles devem ser configurados antes de serem disponibilizados como parte do Serviço HSM Dedicado do Azure. Essa atividade de configuração é executada por funcionários da Microsoft que passaram por uma verificação de antecedentes. A administração “Just In Time” (JIT) é usada para limitar o acesso apenas aos funcionários certos e apenas pelo tempo em que o acesso é necessário. Os procedimentos e sistemas utilizados também asseguram que toda atividade relacionada aos dispositivos HSM seja registrada.

## <a name="security-in-operations"></a>Segurança em operações

Os HSMs são dispositivos de hardware (sendo o HSM real uma placa PCI dentro do dispositivo), portanto, é possível que problemas no nível do componente possam surgir. Problemas potenciais incluem, mas não se limitam a, falhas no ventilador e na fonte de alimentação. Esse tipo de evento exigirá atividades de manutenção ou de interrupção/correção para substituir quaisquer componentes trocáveis.

### <a name="component-replacement"></a>Substituição de componentes

Depois que um dispositivo é provisionado e sob gerenciamento do cliente, a fonte de alimentação hot-swap é os únicos componentes que seriam substituídos. Este componente está fora do limite de segurança e não causa um evento de violação. Um sistema de emissão de bilhetes é usado para autorizar um engenheiro da Microsoft a acessar a parte traseira do rack HBI. Quando o ticket é processado, uma chave física temporária é emitida. Essa chave dá ao engenheiro acesso ao dispositivo e permite que ele troque o componente afetado. Qualquer outro acesso (ou seja, evento de violação) seria feito quando um dispositivo não é alocado para um cliente, minimizando assim o risco de segurança e disponibilidade.  

### <a name="device-replacement"></a>Substituição do dispositivo

No caso de falha total do dispositivo, um processo semelhante ao usado durante a falha do componente é seguido. Se um cliente não conseguir zerar o dispositivo ou o dispositivo estiver em um estado desconhecido, os dispositivos portadores de dados serão removidos e colocados em um escaninho de destruição no rack. Os dispositivos colocados no escaninho serão destruídos de maneira controlada e segura. Nenhum dispositivo portador de dados de um rack HBI deixará um datacenter da Microsoft.

### <a name="other-rack-access-activities"></a>Outras atividades de acesso ao rack

Se um engenheiro da Microsoft precisar acessar o rack usado pelos dispositivos HSM (por exemplo, manutenção do dispositivo de rede), os procedimentos de segurança padrão serão usados para obter acesso ao rack seguro da HBI. Todo o acesso será sob vigilância em vídeo. Os dispositivos HSM são validados para [FIPS 140-2 Nível 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf), portanto, qualquer acesso não autorizado aos Dispositivos HSM será sinalizado para o cliente e os dados serão zerados.

## <a name="logical-level-security-considerations"></a>Considerações de segurança no nível lógico

Os HSMs são provisionados para uma rede virtual criada pelo cliente no espaço de endereço IP privado do cliente.  Essa configuração fornece um valioso isolamento de nível de rede lógico e garante o acesso apenas pelo cliente. Isso implica que todos os controles de segurança de nível lógico são de responsabilidade do cliente.

## <a name="next-steps"></a>Próximas etapas

Recomenda-se que todos os principais conceitos do serviço, como alta disponibilidade e segurança e suporte, por exemplo, sejam bem compreendidos antes do provisionamento do dispositivo, do design do aplicativo ou da implantação.

* [Alta disponibilidade](high-availability.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitoring](monitoring.md)
* [Arquitetura de implantação](deployment-architecture.md)
