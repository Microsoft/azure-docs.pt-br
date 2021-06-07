---
title: 'Início Rápido: criar um workspace do Azure Synapse'
description: Crie um workspace do Synapse seguindo as etapas neste guia.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d38f1f294f60b73e8f1e69169a75333eb175c9f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600149"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Início Rápido: Criar um workspace do Synapse
Este início rápido descreve as etapas necessárias para criar um workspace do Azure Synapse usando o portal do Azure.

## <a name="create-a-synapse-workspace"></a>Criar um workspace do Synapse

1. Abra o [portal do Azure](https://portal.azure.com) e, na parte superior, procure por **Synapse**.
1. Nos resultados da pesquisa, em **Serviços**, selecione **Azure Synapse Analytics**.
1. Selecione **Adicionar** para criar um workspace.
1. Na guia **Noções Básicas**, dê um nome exclusivo ao workspace. Neste documento, usaremos o **myworkspace**
1. Você precisa de uma conta do ADLSGEN2 para criar um workspace. A opção mais simples é criar uma. Se você quiser reutilizar uma existente, será necessário executar algumas configurações adicionais. 
1. OPÇÃO 1 Criação de uma conta do ADLSGEN2 
    1. Em **Selecione Data Lake Storage Gen 2**, clique em **Criar** e nomeie-a **contosolake**.
    1. Em **Selecione Data Lake Storage Gen 2**, clique em **Sistema de Arquivos** e nomeie-a **usuários**.
1. OPÇÃO 2 Confira as instruções **Preparar uma Conta de Armazenamento** na parte inferior deste documento.
1. Seu workspace do Azure Synapse usará essa conta de armazenamento como a conta de armazenamento "primária" e o contêiner para armazenar dados do workspace. O workspace armazena dados em tabelas do Apache Spark. Ele armazena logs de aplicativo do Spark em uma pasta chamada **/synapse/nomedoworkspace**.
1. Selecione **Examinar + criar** > **Criar**. Seu workspace fica pronto em alguns minutos.

> [!NOTE]
> Depois de criar seu workspace do Azure Synapse, você não poderá mover o workspace para outro locatário do Azure Active Directory. Se você fizer isso por meio de migração de assinatura ou outras ações, poderá perder o acesso aos artefatos no workspace.
> Além disso, no momento, não é possível criar um workspace do Synapse Analytics em uma assinatura do [CSP (Provedor de Soluções de Nuvem)](/partner-center/csp-overview).

## <a name="open-synapse-studio"></a>Abrir o Synapse Studio

Após criar o workspace do Azure Synapse, você tem duas maneiras de abrir o Synapse Studio:

* Abra o workspace do Synapse no [portal do Azure](https://portal.azure.com). Na parte superior da seção **Visão Geral**, selecione **Iniciar o Synapse Studio**.
* Acesse `https://web.azuresynapse.net` e entre no seu workspace.

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Preparar uma conta de armazenamento existente para uso com o Azure Synapse Analytics

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Navegue até uma conta de armazenamento ADLSGEN2 existente
1. Selecione **Controle de acesso (IAM)** no painel esquerdo. Depois, atribua as seguintes funções ou verifique se elas já estão atribuídas:
    * Atribua-se à função de **Proprietário**.
    * Atribua-se à função de **Proprietário de Dados do Storage Blob**.
1. No painel esquerdo, selecione **Contêineres** e crie um contêiner.
1. Você pode dar qualquer nome ao contêiner. Neste documento, chamaremos o contêiner de **usuários**.
1. Aceite a configuração padrão **Nível de acesso público** e, em seguida, selecione **Criar**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Configurar o acesso à conta de armazenamento em seu workspace

Identidades gerenciadas para seu workspace do Azure Synapse talvez já tenham acesso à conta de armazenamento. Siga estas etapas para se certificar:

1. Abra o [portal do Azure](https://portal.azure.com) e a conta de armazenamento primária escolhida para seu workspace.
1. Selecione **Controle de acesso (IAM)** no painel esquerdo.
1. Atribua as funções a seguir ou verifique se elas já estão atribuídas. Usamos o mesmo nome para a identidade do workspace e o nome do workspace.
    * Para a função de **Colaborador de Dados do Storage Blob** na conta de armazenamento, atribua **myworkspace** como a identidade do workspace.
    * Atribua **myworkspace** como o nome do workspace.

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

* [Criar um pool de SQL dedicado](quickstart-create-sql-pool-studio.md) 
* [Criar um Pool do Apache Spark sem servidor](quickstart-create-apache-spark-pool-portal.md)
* [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)