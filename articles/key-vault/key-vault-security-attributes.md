---
title: Atributos de segurança para Azure Key Vault
description: Uma lista de verificação de atributos de segurança para avaliar Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444014"
---
# <a name="security-attributes-for-azure-key-vault"></a>Atributos de segurança para Azure Key Vault

Este artigo documenta os atributos de segurança internos no Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)| Sim | Todos os objetos são criptografados. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Toda a comunicação é por meio de chamadas à API criptografadas |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente controla todas as chaves em seu Key Vault. Quando as chaves com suporte ao módulo de segurança de hardware (HSM) são especificadas, um HSM de nível 2 do FIPS protege a chave, o certificado ou o segredo. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim | Usar HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Usando pontos de extremidade de serviço de rede virtual (VNet). |
| Suporte à injeção de VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | Usando regras de firewall de VNet. |
| Suporte a túnel forçado| Não |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando o Log Analytics. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | Usando a política de acesso ao Key Vault. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Usando o Log Analytics. |
| Log e auditoria do plano de dados| Sim | Usando o Log Analytics. |

## <a name="access-controls"></a>Controles de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Controles de acesso do plano de controle/gerenciamento | Sim | RBAC (Controle de Acesso Baseado em Função) do Azure Resource Manager |
| Controles de acesso do plano de dados (em cada nível de serviço) | Sim | Política de acesso ao Key Vault |