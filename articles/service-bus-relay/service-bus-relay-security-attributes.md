---
title: Atributos de segurança para retransmissão do barramento de serviço do Azure
description: Uma lista de verificação de atributos de segurança para avaliar a retransmissão do barramento de serviço do Azure
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 2047f64914d4a286e6de38b7b2c8524d98eba562
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443874"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Atributos de segurança para retransmissão do barramento de serviço do Azure

Este artigo documenta os atributos de segurança internos na retransmissão do barramento de serviço do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)|  N/D | A retransmissão é um soquete da Web e não mantém os dados. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O serviço requer TLS. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não | Usa somente certificados TLS da Microsoft.  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas à API criptografadas| Sim | HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não |  |
| Isolamento de rede e suporte de firewall| Não |  |
| Suporte a túnel forçado| N/D | Retransmissão é o túnel TLS  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Via SAS. |
| Autorização|  Sim | Via SAS. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Log e auditoria do plano de dados| Sim | Êxito na conexão/falha e erros e registrados.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml).|
