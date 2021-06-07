---
title: Integração de controle do código-fonte
description: Experiência de DevOps de banco de dados corporativo para o pool de SQL dedicado com integração do controle do código-fonte nativo usando Azure Repos (Git e GitHub).
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 53adbc0288c28c3b18632539c3f812bbca82da92
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566147"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integração do controle do código-fonte para o pool de SQL dedicado no Azure Synapse Analytics

Este tutorial descreve como integrar seu projeto de banco de dados SSDT (SQL Server Data Tools) com controle do código-fonte.  A integração do controle do código-fonte é a primeira etapa na criação do seu pipeline de implantação e integração contínua com o recurso de pool de SQL dedicado no Azure Synapse Analytics.

## <a name="before-you-begin"></a>Antes de começar

- Inscreva-se para obter uma [organização do Azure DevOps](https://azure.microsoft.com/services/devops/)
- Percorra o tutorial [Criar e Conectar](create-data-warehouse-portal.md)
- [Instale o Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Configurar e conectar ao Azure DevOps

1. Em sua Organização do Azure DevOps, crie um projeto que hospedará seu projeto de banco de dados do SSDT por meio de um Repositório do Azure.

   ![Criar Projeto](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Criar projeto")

2. Abra o Visual Studio e conecte-se à sua organização e projeto do Azure DevOps da etapa um selecionando **Gerenciar Conexão**.

   ![Gerenciar Conexões](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Gerenciar conexões")

3. Conecte-se ao seu projeto selecionando **Gerenciar Conexões** e **Conectar-se a um projeto**.
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Conectar")


4. Localize o projeto que você criou na etapa um e selecione **Conectar**.
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Conectar")


3. Clone seu repositório do Azure DevOps de seu projeto para o computador local.

   ![Clonar repositório](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Clonar repositório")

Para obter mais informações sobre como conectar projetos usando o Visual Studio, confira [Conectar-se a projetos no Team Explorer](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true). Para obter diretrizes sobre como clonar um repositório usando o Visual Studio, examine o artigo [Clonar um repositório Git de saída](/azure/devops/repos/git/clone?tabs=visual-studio). 

## <a name="create-and-connect-your-project"></a>Criar e conectar o projeto

1. No Visual Studio, crie um novo Projeto de Banco de Dados do SQL Server com um diretório e um repositório Git local em seu **repositório clonado local**.

   ![Criar projeto](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Criar um novo projeto")  

2. Clique com o botão direito do mouse em seu sqlproject vazio e importe seu data warehouse para o projeto de banco de dados.

   ![Importar Projeto](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importar projeto")  

3. No Team Explorer no Visual Studio, confirme suas alterações no repositório Git local.

   ![Confirmar](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. Agora que você tem as alterações confirmadas localmente no repositório clonado, sincronize e envie por push suas alterações para o repositório Azure Repo em seu projeto do Azure DevOps.

   ![Sincronizar e efetuar push – preparo](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Sincronizar e efetuar push – preparo")

   ![Sincronizar e efetuar push](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Sincronizar e efetuar push")  

## <a name="validation"></a>Validação

1. Verifique se as alterações foram enviadas por push para o Repositório do Azure atualizando uma coluna de tabela em seu projeto de banco de dados no SSDT (SQL Server Data Tools) do Visual Studio.

   ![Validar coluna de atualização](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Validar coluna de atualização")

2. Confirme e envie por push a alteração do repositório local para seu Azure Repo.

   ![Efetuar push das alterações](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Enviar alterações por push")

3. Verifique se a alteração foi enviada por push em seu repositório Azure Repo.

   ![Verificar](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verificar alterações")

4. (**Opcional**) Use a Comparação de Esquemas e atualize as alterações no pool de SQL dedicado de destino usando o SSDT, para garantir que as definições de objeto no repositório do Azure e no repositório local reflitam o pool de SQL dedicado.

## <a name="next-steps"></a>Próximas etapas

- [Desenvolvimento do pool de SQL dedicado](sql-data-warehouse-overview-develop.md)