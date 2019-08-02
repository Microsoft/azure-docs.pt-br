---
title: Atributos de segurança para mensagens do barramento de serviço do Azure
description: Uma lista de verificação de atributos de segurança para avaliar as mensagens do barramento de serviço do Azure
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e1d6e041b47a261b549fb8b608cf09d0d6362dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443894"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Atributos de segurança para mensagens do barramento de serviço do Azure

Este artigo documenta os atributos de segurança internos no sistema de mensagens do barramento de serviço do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)|  Sim, para criptografia do lado do servidor, em repouso, por padrão. | As chaves gerenciadas pelo cliente e BYOK ainda não têm suporte. A criptografia do lado do cliente é a responsabilidade do cliente |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Dá suporte ao mecanismo HTTPS/TLS padrão. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |   |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas à API criptografadas| Sim | As chamadas à API são feitas por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim (somente camada Premium) | Os pontos de extremidade do serviço VNet têm suporte somente para a [camada Premium do barramento de serviço](service-bus-premium-messaging.md) . |
| Suporte à injeção de VNet| Não | |
| Isolamento de rede e suporte de firewall| Sim (somente camada Premium) |  |
| Suporte a túnel forçado| Não |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Com suporte via [Azure monitor e alertas](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Gerenciado por meio de [Azure Active Directory identidade de serviço gerenciada](service-bus-managed-service-identity.md); consulte [autenticação e autorização do barramento de serviço](service-bus-authentication-and-authorization.md).|
| Autorização| Sim | Dá suporte à autorização via [RBAC](service-bus-role-based-access-control.md) (visualização) e token SAS; consulte [autenticação e autorização do barramento de serviço](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Os logs de operações estão disponíveis; consulte [os logs de diagnóstico do barramento de serviço](service-bus-diagnostic-logs.md).  |
| Log e auditoria do plano de dados| Não |  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Dá suporte ao controle de versão do provedor de recursos por meio da [API Azure Resource Manager](/rest/api/resources/).|
