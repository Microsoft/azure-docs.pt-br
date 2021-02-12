---
title: Benchmark de segurança do Azure v2-segurança do ponto de extremidade
description: Segurança de ponto de referência de segurança do Azure v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 937d0b379c4f669e5b57e5053d5e3bffeb091e78
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368946"
---
# <a name="security-control-v2-endpoint-security"></a>Controle de segurança v2: segurança do ponto de extremidade

O Endpoint Security abrange controles em resposta e detecção de ponto de extremidade. Isso inclui o uso de EDR (detecção de ponto de extremidade e resposta) e o serviço antimalware para pontos de extremidade em ambientes do Azure.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usar a detecção e resposta de ponto de extremidade (EDR)

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

Habilite recursos de EDR (detecção e resposta de ponto de extremidade) para servidores e clientes e integre com os processos de operações de segurança e SIEM.

A proteção avançada contra ameaças do Microsoft defender fornece o recurso EDR como parte de uma plataforma de segurança de ponto de extremidade empresarial para prevenir, detectar, investigar e responder a ameaças avançadas. 

- [Visão geral da proteção avançada contra ameaças do Microsoft defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Serviço Microsoft defender ATP para Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Serviço Microsoft defender ATP para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: usar software antimalware moderno gerenciado centralmente

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

Use uma solução antimalware de ponto de extremidade gerenciada centralmente capaz de verificação periódica e em tempo real

A central de segurança do Azure pode identificar automaticamente o uso de várias soluções Antimalware populares para suas máquinas virtuais e relatar o status de execução do Endpoint Protection e fazer recomendações. 

O Microsoft antimalware para serviços de nuvem do Azure é o antimalware padrão para VMs (máquinas virtuais) do Windows. Para VMs do Linux, use a solução antimalware de terceiros.  Além disso, você pode usar a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento do Azure. 

- [Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais](../fundamentals/antimalware.md)

- [Soluções de proteção de ponto de extremidade com suporte](../../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: garantir que o software anti-malware e as assinaturas sejam atualizados

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| ES-3 | 8.2 | SI-2, SI-3 |

Verifique se as assinaturas Antimalware são atualizadas de forma rápida e consistente. 

Siga as recomendações na central de segurança do Azure: " &amp; aplicativos de computação" para garantir que todos os pontos de extremidade estejam atualizados com as assinaturas mais recentes. O Microsoft Antimalware instalará automaticamente as assinaturas e atualizações de mecanismo mais recentes por padrão. Para o Linux, use a solução antimalware de terceiros.

- [Como implantar o Microsoft antimalware para serviços de nuvem e máquinas virtuais do Azure](../fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Avaliação e recomendações do Endpoint Protection na central de segurança do Azure](../../security-center/security-center-endpoint-protection.md)