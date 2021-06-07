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
ms.date: 03/17/2021
ms.openlocfilehash: a4fa902268d9a19cd0003a2fdaa4c5e58989a4ff
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218933"
---
# <a name="creating-a-synapse-workspace"></a>Como criar um workspace do Azure Synapse

Neste tutorial, você aprenderá a criar um workspace do Azure Synapse, um pool de SQL dedicado e um Pool do Apache Spark sem servidor. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisará ter acesso a um grupo de recursos no qual tenha a função **Proprietário**. Crie o workspace do Synapse nesse grupo de recursos.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Criar um workspace do Azure Synapse no portal do Azure

### <a name="start-the-process"></a>Iniciar o processo
1. Abra o [portal do Azure](https://portal.azure.com) e insira **Synapse** na barra de pesquisa sem pressionar Enter.
1. Nos resultados da pesquisa, em **Serviços**, selecione **Azure Synapse Analytics**.
1. Selecione **Adicionar** para criar um workspace.

## <a name="basics-tab--project-details"></a>Guia Informações Básicas > Detalhes do Projeto
Preencha os campos a seguir:

1. **Assinatura** – Escolha qualquer assinatura.
1. **Grupo de recursos** – Use qualquer grupo de recursos.
1. **Grupo de recursos gerenciados** – deixe em branco.

## <a name="basics-tab--workspace-details"></a>Guia Informações Básicas > Detalhes do Workspace
Preencha os campos a seguir:

1. **Nome do workspace** – Escolha qualquer nome exclusivo globalmente. Neste tutorial, usaremos **myworkspace**.
1. **Região** – Escolha qualquer região.

Em **Selecionar o Data Lake Storage Gen 2**:

1. Em **Nome da conta**, clique em **Criar** e nomeie a nova conta de armazenamento **contosolake** ou outro nome semelhante, pois esse nome precisa ser exclusivo.
1. Em **Nome do sistema de arquivos**, clique em **Criar** e nomeie-o **usuários**. Isso criará um contêiner de armazenamento chamado **usuários**. O workspace usará essa conta de armazenamento como a conta de armazenamento "primária" para os logs do aplicativo e as tabelas do Spark.
1. Marque a caixa "Atribuir a função Colaborador de Dados do Blob de Armazenamento a mim mesmo na conta do Data Lake Storage Gen2". 

## <a name="completing-the-process"></a>Conclusão do processo
Selecione **Examinar + criar** > **Criar**. Seu workspace fica pronto em alguns minutos.

> [!NOTE]
> Para habilitar recursos de workspace de um pool de SQL dedicado existente (antigo SQL DW), veja [Como habilitar um workspace para seu pool de SQL dedicado (antigo SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Abrir o Synapse Studio

Após criar o workspace do Azure Synapse, você tem duas maneiras de abrir o Synapse Studio:

* Abra o workspace do Azure Synapse no [portal do Azure](https://portal.azure.com) e, na seção **Visão geral** do workspace do Azure Synapse, selecione **Abrir** na caixa Abrir Synapse Studio.
* Acesse `https://web.azuresynapse.net` e entre no seu workspace.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise com um pool de SQL sem servidor](get-started-analyze-sql-on-demand.md)
