---
title: Atributos de segurança para o gerenciamento de API do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o gerenciamento de API
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442274"
---
# <a name="security-attributes-for-api-management"></a>Atributos de segurança para gerenciamento de API

Este artigo documenta os atributos de segurança internos do gerenciamento de API.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)| Sim (somente criptografia do lado do serviço) | Dados confidenciais, como certificados, chaves e valores nomeados por segredo, são criptografados com chaves gerenciadas por serviço, por chave de instância de serviço. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | A criptografia de [rota expressa](../expressroute/index.yml) e VNet é fornecida pela [rede do Azure](../virtual-network/index.yml). |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não | Todas as chaves de criptografia são por instância de serviço e são gerenciadas pelo serviço. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas à API criptografadas| Sim | As chamadas do plano de gerenciamento são feitas por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) sobre TLS. Um JWT (token Web JSON) válido é necessário.  As chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação com suporte (por exemplo, certificado de cliente ou JWT).
 |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não | |
| Suporte à injeção de VNet| Sim | |
| Isolamento de rede e suporte de firewall| Sim | Usar NSG (grupos de segurança de rede) e Aplicativo Azure gateway (ou outro dispositivo de software), respectivamente. |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | |
| Autorização| Sim | |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | [Logs de atividades Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Log e auditoria do plano de dados| Sim | [Azure monitor logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) e (opcionalmente) [aplicativo Azure](../azure-monitor/app/app-insights-overview.md)insights.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Usando o [DevOps Resource Kit de gerenciamento de API do Azure](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Vulnerabilidade verifica falsos positivos

Esta seção documenta as vulnerabilidades comuns, que não afetam o gerenciamento de API do Azure.

| Vulnerabilidade               | Descrição                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed é uma vulnerabilidade na implementação da extensão SessionTicket do TLS encontrada em alguns produtos F5. Ele permite o vazamento ("sangramento") de até 31 bytes de dados de memória não inicializada. Isso é causado pelo preenchimento da pilha TLS de uma ID de sessão, passada do cliente, com dados para torná-la de 32 bits de comprimento. |
