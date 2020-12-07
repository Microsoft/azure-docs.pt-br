---
title: 'Início rápido: Introdução – criar um workspace do Azure Synapse'
description: Neste tutorial, você aprenderá a criar um workspace do Azure Synapse, um pool de SQL dedicado e um Pool do Apache Spark sem servidor.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/21/2020
ms.openlocfilehash: 949992418cb19c5c69ed8da92e9c2f0fc5f2f791
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445422"
---
# <a name="creating-a-synapse-workspace"></a>Como criar um workspace do Azure Synapse

Neste tutorial, você aprenderá a criar um workspace do Azure Synapse, um pool de SQL dedicado e um Pool do Apache Spark sem servidor. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisará ter acesso a um grupo de recursos no qual tenha a função **Proprietário**. Crie o workspace do Synapse nesse grupo de recursos.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Criar um workspace do Azure Synapse no portal do Azure

1. Abra o [portal do Azure](https://portal.azure.com) e, na parte superior, procure por **Synapse**.
1. Nos resultados da pesquisa, em **Serviços**, selecione **Azure Synapse Analytics**.
1. Selecione **Adicionar** para criar um workspace.
1. Em **Conceitos básicos**, insira a **Assinatura**, o **Grupo de recursos**, a **Região** preferenciais e escolha um nome para o workspace. Neste tutorial, usaremos **myworkspace**.
1. Navegue até **Selecionar Data Lake Storage Gen 2**. 
1. Clique em **Criar Novo** e nomeie a conta como **contosolake**.
1. Clique em **Sistema de Arquivos** e nomeie a conta como **usuários**. Isso criará um contêiner chamado **usuários**
1. O workspace usará essa conta de armazenamento como a conta de armazenamento "primária" para os logs do aplicativo e as tabelas do Spark.
1. Selecione **Examinar + criar** > **Criar**. Seu workspace fica pronto em alguns minutos.

> [!NOTE]
> Para habilitar recursos de workspace de um pool de SQL dedicado existente (antigo SQL DW), veja [Como habilitar um workspace para seu pool de SQL dedicado (antigo SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Abrir o Synapse Studio

Após criar o workspace do Azure Synapse, você tem duas maneiras de abrir o Synapse Studio:

* Abra o workspace do Synapse no [portal do Azure](https://portal.azure.com). Na parte superior da seção **Visão Geral**, selecione **Iniciar o Synapse Studio**.
* Acesse `https://web.azuresynapse.net` e entre no seu workspace.

## <a name="create-a-dedicated-sql-pool"></a>Criar um pool de SQL dedicado

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools de SQL**.
1. Selecione **Novo**
1. Em **Nome do pool de SQL**, selecione **SQLPOOL1**
1. Em **Nível de desempenho**, escolha **DW100C**
1. Selecione **Examinar + criar** > **Criar**. Seu pool de SQL dedicado estará pronto em alguns minutos. O pool de SQL dedicado é associado a um banco de dados do pool de SQL dedicado também chamado **SQLPOOL1**.

Um pool de SQL dedicado consome recursos faturáveis desde que ele esteja ativo. Você pode pausar o pool posteriormente para reduzir custos.

> [!NOTE] 
> Quando você criar um pool de SQL dedicado (antigo SQL DW) em seu workspace, a página de provisionamento do pool de SQL dedicado será aberta. O provisionamento ocorrerá no SQL Server lógico.

## <a name="create-a-serverless-apache-spark-pool"></a>Criar um Pool do Apache Spark sem servidor

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools do Apache Spark**.
1. Selecione **Novo** 
1. Em **Nome do Pool do Apache Spark**, insira **Spark1**.
1. Em **Tamanho do nó**, insira **Pequeno**.
1. Em **Número de nós**, defina o mínimo como 3 e o máximo como 3
1. Selecione **Examinar + criar** > **Criar**. Seu Pool do Apache Spark estará pronto em alguns segundos.

O Pool do Spark informa o Azure Synapse sobre quantos recursos do Spark serão usados. Você paga somente pelos recursos que usa. Quando você parar ativamente de usar o pool, os recursos atingirão o tempo limite automaticamente e serão reciclados.

## <a name="the-built-in-serverless-sql-pool"></a>O pool de SQL sem servidor interno

Todo workspace é fornecido com um pool de SQL sem servidor predefinido chamado **Interno**. Esse pool não pode ser excluído. Os pools de SQL sem servidor permitem que você use o SQL sem precisar reservar a capacidade com pools de SQL dedicados. Ao contrário dos pools de SQL dedicados, a cobrança de um pool de SQL sem servidor se baseia no volume de dados examinados para executar a consulta, não no número de capacidade alocada para executá-lo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise por meio de um pool de SQL dedicado](get-started-analyze-sql-pool.md)
