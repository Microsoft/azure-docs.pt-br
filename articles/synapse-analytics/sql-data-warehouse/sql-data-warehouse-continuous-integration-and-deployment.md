---
title: Integração e implantação contínuas para o pool SQL dedicado
description: Experiência de DevOps de banco de dados de classe empresarial para pool dedicado de SQL no Azure Synapse Analytics com suporte interno para integração e implantação contínuas usando o Azure Pipelines.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cb80f2d21246a75d41fc5753e72995d409d5c6b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119179"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integração e implantação contínuas para o pool SQL dedicado no Azure Synapse Analytics

Este tutorial simples descreve como integrar seu projeto de banco de dados SSDT (SQL Server Data Tools) ao Azure DevOps e aproveitar o Azure Pipelines para configurar a integração e a implantação contínuas. Este tutorial é a segunda etapa para criar seu pipeline de implantação e integração contínua para data warehousing.

## <a name="before-you-begin"></a>Antes de começar

- Percorra o [tutorial de integração de controle do código-fonte](sql-data-warehouse-source-control-integration.md)

- Configurar e conectar ao Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Integração contínua ao build do Visual Studio

1. Navegue até o Azure Pipelines e crie um pipeline de build.

      ![Novo pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Novo Pipeline")

2. Selecione o repositório de código-fonte (Git do Azure Repos) e selecione o modelo de aplicativo da área de trabalho do .NET.

      ![Configuração do pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Configuração do pipeline")

3. Edite seu arquivo YAML para usar o pool adequado do seu agente. Seu arquivo YAML deve ter a seguinte aparência:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Neste ponto, você tem um ambiente simples em que qualquer check-in em sua ramificação principal do repositório do controle do código-fonte deve disparar automaticamente uma compilação bem-sucedida do Visual Studio do seu projeto de banco de dados. Valide se a automação está funcionando de ponta a ponta fazendo uma alteração no projeto de banco de dados local e fazendo check-in dessa alteração para sua ramificação principal.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Implantação contínua com a tarefa de implantação do Azure Synapse Analytics (ou banco de dados)

1. Adicione uma nova tarefa usando a [tarefa de implantação do Banco de Dados SQL do Azure](/azure/devops/pipelines/targets/azure-sqldb) e preencha os campos obrigatórios para se conectar ao seu data warehouse de destino. Quando essa tarefa é executada, o DACPAC gerado com base no processo de build anterior é implantado no data warehouse de destino. Você também pode usar a [tarefa de implantação do Azure Synapse Analytics](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Tarefa de implantação](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Tarefa de implantação")

2. Se você estiver usando um agente auto-hospedado, certifique-se de definir sua variável de ambiente para usar o SqlPackage.exe correto para o Azure Synapse Analytics. O caminho deve ter a seguinte aparência:

      ![Variável de ambiente](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Variável de ambiente")

   C:\Arquivos de Programas (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Execute e valide seu pipeline. Você pode fazer alterações localmente e check-in do controle do código-fonte que deve gerar um build e uma implantação automáticos.

## <a name="next-steps"></a>Próximas etapas

- Explore a [arquitetura do pool de SQL dedicado (anteriormente conhecido como SQL DW)](massively-parallel-processing-mpp-architecture.md)
- [Criar rapidamente um pool SQL dedicado (anteriormente conhecido como SQL DW)](create-data-warehouse-portal.md)
- [Carregar dados de amostra](./load-data-from-azure-blob-storage-using-copy.md)
- Explore os [Vídeos](sql-data-warehouse-videos.md)