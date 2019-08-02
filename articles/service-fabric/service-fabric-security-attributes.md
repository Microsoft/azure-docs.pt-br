---
title: Atributos de segurança para o Azure Service Fabric
description: Uma lista de verificação de atributos de segurança para avaliar o Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443864"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Atributos de segurança para o Azure Service Fabric

Este artigo documenta os atributos de segurança internos do Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim |  |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim | Chamadas à API do Service Fabric são feitas por meio do Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim |  |
| Isolamento de rede e suporte de firewall| Sim | Usando NSGs (Grupos de Segurança de Rede). |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando o suporte ao monitoramento do Azure e o suporte de terceiros. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | IAM (Gerenciamento de identidade e de acesso) para chamadas via SFRP. Chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: Usuário e administrador. O cliente pode mapear as APIs para qualquer uma dessas duas funções. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações de plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Log e auditoria do plano de dados| N/D | O cliente é proprietário do cluster.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |