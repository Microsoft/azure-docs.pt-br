---
title: Benchmark de segurança do Azure v2-backup e recuperação
description: Backup e recuperação do benchmark de segurança do Azure v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 089cf521a7c5428833be340001c88b870c568a8f
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368878"
---
# <a name="security-control-v2-backup-and-recovery"></a>Controle de segurança v2: backup e recuperação

O backup e a recuperação abrangem controles para garantir que os backups de dados e de configuração em diferentes camadas de serviço sejam executados, validados e protegidos.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantir backups automatizados regulares

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Verifique se você está fazendo backup de sistemas e dados para manter a continuidade dos negócios após um evento inesperado. Isso deve ser definido por quaisquer objetivos de RPO (objetivo de ponto de recuperação) e RTO (objetivo de tempo de recuperação).

Habilite o backup do Azure e configure a fonte de backup (por exemplo, VMs do Azure, SQL Server, bancos de dados HANA ou compartilhamentos de arquivos), bem como a frequência e o período de retenção desejados.  

Para um nível mais alto de proteção, você pode habilitar a opção de armazenamento com redundância geográfica para replicar dados de backup para uma região secundária e recuperar usando a restauração entre regiões.

- [Continuidade dos negócios e recuperação de desastres de escala empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Como habilitar o backup do Azure](../../backup/index.yml)

- [Como habilitar a restauração entre regiões](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Política e padrões](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: criptografar dados de backup

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Verifique se os backups estão protegidos contra ataques. Isso deve incluir a criptografia dos backups para proteger contra perda de confidencialidade.   

Para backups locais usando o backup do Azure, a criptografia em repouso é fornecida usando a senha que você fornecer. Para backups regulares do serviço do Azure, os dados de backup são criptografados automaticamente usando chaves gerenciadas pela plataforma do Azure. Você pode optar por criptografar os backups usando a chave gerenciada pelo cliente. Nesse caso, verifique se essa chave gerenciada pelo cliente no cofre de chaves também está no escopo de backup. 

Use o controle de acesso baseado em função do Azure no backup do Azure, Azure Key Vault ou outros recursos para proteger backups e chaves gerenciadas pelo cliente. Além disso, você pode habilitar recursos de segurança avançados para exigir MFA antes que os backups possam ser alterados ou excluídos.

- [Visão geral dos recursos de segurança no Backup do Azure](../../backup/security-overview.md)

- [Criptografia de dados de backup usando chaves gerenciadas pelo cliente](../../backup/encryption-at-rest-with-cmk.md) 

- [Como fazer backup de chaves de Key Vault no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Recursos de segurança para ajudar a proteger backups híbridos contra ataques](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Execute periodicamente a restauração de dados do seu backup. Verifique se você pode restaurar as chaves gerenciadas pelo cliente de backup.

- [Como recuperar arquivos do backup de máquina virtual do Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Como restaurar chaves de Key Vault no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: reduzir o risco de perda de chaves

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

Verifique se você tem medidas em vigor para evitar e se recuperar da perda de chaves. Habilite a exclusão temporária e a proteção de limpeza no Azure Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.  

- [Como habilitar a exclusão temporária e a proteção de limpeza no Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Segurança de dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)