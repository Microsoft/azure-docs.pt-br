---
title: Excluir um projeto das Migrações para Azure
description: Neste artigo, saiba como você pode excluir um projeto de migrações para Azure usando o portal do Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: face3d02ee72d1e05c6c08330dae4fffc2fd0e0b
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754242"
---
# <a name="delete-an-azure-migrate-project"></a>Excluir um projeto das Migrações para Azure

Este artigo descreve como excluir um projeto de [migrações para Azure](./migrate-services-overview.md) .


## <a name="before-you-start"></a>Antes de começar

Antes de excluir um projeto:

- Quando você exclui um projeto, o projeto e os metadados de computador descobertos são excluídos.
- Se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor para análise de dependência, decida se deseja excluir o espaço de trabalho. 
    - O espaço de trabalho não é excluído automaticamente. Exclua-o manualmente.
    - Verifique o que um espaço de trabalho é usado para antes de excluí-lo. O mesmo espaço de trabalho Log Analytics pode ser usado para vários cenários.
    - Antes de excluir o projeto, você pode encontrar um link para o espaço de trabalho em **migrações do Azure-servidores**  >  **migra-Server migração do Azure**, em **espaço de trabalho do OMS**.
    - Para excluir um espaço de trabalho após excluir um projeto, localize o espaço de trabalho no grupo de recursos relevante e siga [estas instruções](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Excluir um projeto


1. No portal do Azure, abra o grupo de recursos no qual o projeto foi criado.
2. Na página grupo de recursos, selecione **Mostrar tipos ocultos**.
3. Selecione o projeto e os recursos associados que você deseja excluir.
    - O tipo de recurso para projetos de migração do Azure é **Microsoft. Migrate/migrateprojects**.
    - Na próxima seção, examine os recursos criados para descoberta, avaliação e migração em um projeto de migrações para Azure.
    - Se o grupo de recursos contiver apenas o projeto de migrações para Azure, você poderá excluir o grupo de recursos inteiro.
    - Se você quiser excluir um projeto da versão anterior das migrações para Azure, as etapas serão as mesmas. O tipo de recurso para esses projetos é **projeto de migração**.


## <a name="created-resources"></a>Recursos criados

Essas tabelas resumem os recursos criados para descoberta, avaliação e migração em um projeto de migrações para Azure.

> [!NOTE]
> Exclua o cofre de chaves com cuidado porque ele pode conter chaves de segurança.

### <a name="vmwarephysical-server"></a>VMware/servidor físico

**Recurso** | **Tipo**
--- | ---
KV "appliancename" | Cofre de chaves
Site "appliancename" | Microsoft. OffAzure/VMwareSites
ProjectName | Microsoft. Migrate/migrateprojects
Projeto "ProjectName" | Microsoft. Migrate/assessmentProjects
Rsvault "ProjectName" | Cofre dos Serviços de Recuperação
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação
migrateappligwsa* | Conta de armazenamento
migrateapplilsa* | Conta de armazenamento
migrateapplicsa* | Conta de armazenamento
migrateapplikv* | Cofre de chaves
migrateapplisbns16041 | Namespace do Barramento de Serviço

### <a name="hyper-v-vm"></a>VM do Hyper-V 

**Recurso** | **Tipo**
--- | ---
ProjectName | Microsoft. Migrate/migrateprojects
Projeto "ProjectName" | Microsoft. Migrate/assessmentProjects
HyperV * kV | Cofre de chaves
Site do HyperV * | Microsoft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação


## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração](how-to-migrate.md) . 
