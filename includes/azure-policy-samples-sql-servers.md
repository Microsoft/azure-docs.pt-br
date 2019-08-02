---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172083"
---
### <a name="sql-servers"></a>SQL Servers

|  |  |
|---------|---------|
| [Auditar nenhum administrador do Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Auditar quando não há nenhum administrador do Azure Active Directory atribuído ao SQL server. |
| [Auditar a configuração de detecção de ameaças no nível do servidor](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar as configurações de auditoria do SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Audita o SQL Server para verificar se as configurações de auditoria estão habilitadas. |
| [Auditar a Configuração da Auditoria no Nível do SQL Server](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Auditará as configurações de auditoria do SQL Server se elas não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas. |
| [Exigir SQL Server versão 12.0](../articles/governance/policy/samples/require-sql-12.md) | Exige que servidores SQL usem a versão 12.0.  |