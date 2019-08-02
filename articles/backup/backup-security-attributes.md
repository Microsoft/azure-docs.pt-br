---
title: Atributos de segurança para o backup do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o backup do Azure
author: utraghuv
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: c2500c6c9ff6882e521f4edce02426a92a0bd39f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464939"
---
# <a name="security-attributes-for-azure-backup"></a>Atributos de segurança para o backup do Azure

Este artigo documenta os atributos de segurança internos do backup do Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)| Sim | Usar a criptografia do serviço de armazenamento para contas de armazenamento. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Não | Usar HTTPS. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Não |  |
| Chamadas criptografadas à API| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não |  |
| Suporte à injeção de VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | Há suporte para túnel forçado no backup de VM. Não há suporte para túnel forçado em cargas de trabalho em execução dentro de VMs. |
| Suporte a túnel forçado| Não |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Há suporte para o Log Analytics por meio dos logs de diagnóstico. Consulte [monitorar cargas de trabalho protegidas de backup do Azure usando log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | São usadas regras de RBAC internas e criadas pelo cliente. Consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação de backup do Azure](/azure/backup/backup-rbac-rs-vault) para obter mais informações. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as ações disparadas pelo cliente no portal do Azure são registradas em logs de atividades. |
| Log e auditoria do plano de dados| Não | O plano de dados do Backup do Azure não pode ser acessado diretamente.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim|  |