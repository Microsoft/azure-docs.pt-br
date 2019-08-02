---
title: Atributos de segurança para Azure Load Balancer
description: Uma lista de verificação de atributos de segurança para avaliar Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440890"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Atributos de segurança para Azure Load Balancer

Este artigo documenta os atributos de segurança internos no Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia) | N/D | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| N/D | |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| N/D | |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas à API criptografadas| Sim | Por meio do [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/D | |
| Suporte à injeção de VNet| N/D | . |
| Isolamento de rede e suporte de firewall| N/D |  |
| Suporte a túnel forçado| N/D | |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| N/D |  |
| Autorização| N/D |  |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](load-balancer-monitor-log.md). |
| Log e auditoria do plano de dados | N/D |  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| N/D |  | 
