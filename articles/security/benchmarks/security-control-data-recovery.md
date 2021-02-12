---
title: Controle de segurança do Azure-recuperação de dados
description: Recuperação de dados do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 835e4f681d514bb6b92caa5ee076e3794ed59236
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698811"
---
# <a name="security-control-data-recovery"></a>Controle de segurança: recuperação de dados

Certifique-se de que todos os dados do sistema, configurações e segredos sejam automaticamente submetidos a backup regularmente.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9.1 | 10.1 | Cliente |

Habilite o backup do Azure e configure a fonte de backup (VMs do Azure, SQL Server ou compartilhamentos de arquivos), bem como a frequência e o período de retenção desejados.

- [Como habilitar o backup do Azure](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9.2 | 10,2 | Cliente |

Habilite o backup do Azure e as VMs de destino, bem como os períodos de frequência e retenção desejados. Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

- [Como habilitar o backup do Azure](../../backup/index.yml)

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9.3 | 10,3 | Cliente |

Garanta a capacidade de executar periodicamente a restauração de dados de conteúdo no backup do Azure. Teste a restauração de chaves de backup gerenciadas pelo cliente.

- [Como recuperar arquivos do backup de máquina virtual do Azure](../../backup/backup-azure-restore-files-from-vm.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantir a proteção de backups e chaves gerenciadas pelo cliente

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9.4 | 10.4 | Cliente |

Para backup local, a criptografia em repouso é realizada usando a frase secreta que você fornece ao fazer backup no Azure. Para VMs do Azure, os dados são criptografados em repouso usando SSE (Criptografia do Serviço de Armazenamento). Use o controle de acesso baseado em função do Azure para proteger backups e chaves gerenciadas pelo cliente.  

Habilite a proteção de Soft-Delete e limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.  Se o armazenamento do Azure for usado para armazenar backups, habilite a exclusão reversível para salvar e recuperar seus dados quando BLOBs ou instantâneos de blob forem excluídos. 

- [Entender o RBAC do Azure](../../role-based-access-control/overview.md)

- [Como habilitar a proteção de Soft-Delete e limpeza no Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Exclusão reversível para blobs do Armazenamento do Azure ](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>Próximas etapas

- Consulte o próximo controle de segurança:  [resposta a incidentes](security-control-incident-response.md)