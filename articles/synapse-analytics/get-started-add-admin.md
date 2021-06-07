---
title: 'Tutorial: introdução à adição de um administrador'
description: Neste tutorial, você aprenderá a adicionar outro usuário administrativo ao workspace.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553491"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Adicionar um administrador ao workspace do Azure Synapse

Neste tutorial, você aprenderá a adicionar um administrador ao workspace do Azure Synapse. Esse usuário terá controle total sobre o workspace.

## <a name="overview"></a>Visão geral

Até agora, no guia de introdução, nós nos concentramos nas atividades que *você* mesmo faz no workspace. Como você criou o workspace na ETAPA 1, você é o administrador do workspace do Azure Synapse. Agora, transformaremos outro usuário, Ryan (`ryan@contoso.com`), em um administrador. Quando terminarmos, Ryan será capaz de fazer tudo o que você pode fazer no workspace.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>RBAC do Azure: função de proprietário do workspace

Atribua a `ryan@contoso.com` a função de **Proprietário** no RBAC do Azure no workspace.

1. Abra o portal do Azure e abra o workspace do Azure Synapse.
1. Do lado esquerdo, selecione **Controle de Acesso (IAM)** .
1. Adicione `ryan@contoso.com` à função de **Proprietário**. 
1. Clique em **Save** (Salvar).
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>RBAC do Synapse: função de administrador do Azure Synapse para o workspace

Atribua a `ryan@contoso.com` a função RBAC de **Administrador do Azure Synapse** no workspace.

1. Abra o workspace no Azure Synapse Studio.
1. Do lado esquerdo, clique em **Gerenciar** para abrir o hub de gerenciamento.
1. Em **Segurança**, clique em **Controle de acesso**.
1. Clique em **Adicionar**.
1. Mantenha o **Escopo** configurado como **Workspace**.
1. Adicione `ryan@contoso.com` para a função **Administrador do Azure Synapse**. 
1. Em seguida, clique em **Aplicar**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>RBAC do Azure: atribuições de funções na conta de armazenamento primária

Atribuir a `ryan@contoso.com` a função de **Proprietário** na conta de armazenamento principal do workspace.
Atribua a `ryan@contoso.com` a função de **Colaborador de Dados do Azure Storage Blob** na conta de armazenamento principal do workspace.

1. Abra a conta de armazenamento principal do workspace no portal do Azure.
1. Do lado esquerdo, clique em **Controle de Acesso (IAM)** .
1. Adicione `ryan@contoso.com` à função de **Proprietário**. 
1. Adicionar `ryan@contoso.com` à função **Colaborador de Dados do Azure Storage Blob**

## <a name="dedicated-sql-pools-db_owner-role"></a>Pools de SQL dedicados: função db_owner

Atribua `ryan@contoso.com` ao **db_owner** em cada pool de SQL dedicado no workspace.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)
