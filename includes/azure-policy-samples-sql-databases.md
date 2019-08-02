---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7bf885f2ab81e5d86878d2b0b53f4e98b8aedd9a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172084"
---
### <a name="sql-databases"></a>BANCOS DE DADOS SQL

|  |  |
|---------|---------|
| [SKUs do BD SQL permitidas](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Exige que bancos de dados SQL usem uma SKU aprovada. Especifique uma matriz de IDs de SKU permitidas ou uma matriz de nomes de SKU permitidos. |
| [Auditar a configuração de detecção de ameaças no nível do BD](../articles/governance/policy/samples/audit-db-threat-detection-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar criptografia do Banco de Dados SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Audita se o banco de dados SQL não tem a Transparent Data Encryption habilitada. |
| [Auditar a Configuração de Auditoria no Nível do BD SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Audita as configurações de auditoria de banco de dados SQL se essas configurações não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas.  |