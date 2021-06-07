---
title: 'Início Rápido: Criar um pool de SQL dedicado usando o Synapse Studio'
description: Crie um pool de SQL dedicado usando o Synapse Studio seguindo as etapas deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3644891f12a6475ec9cfec51f572df4742481e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541837"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Início Rápido: Criar um pool de SQL dedicado usando o Synapse Studio

O Azure Synapse Analytics oferece vários mecanismos de análise para ajudar você a ingerir, transformar, modelar e analisar seus dados. Um pool de SQL dedicado oferece recursos de computação e armazenamento baseados em T-SQL. Após a criação de um pool de SQL dedicado no workspace do Azure Synapse, os dados poderão ser carregados, modelados, processados e entregues para obter insights analíticos mais rapidamente.

Este início rápido descreve as etapas para criar um pool de SQL dedicado em um workspace do Azure Synapse usando o Synapse Studio.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Workspace do Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até o workspace do Synapse

1. Navegue até o workspace do Azure Synapse em que o pool de SQL dedicado será criado digitando o nome do serviço (ou o nome do recurso diretamente) na barra de pesquisa.

    ![Barra de pesquisa do portal do Azure com workspaces do Synapse digitados.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Na lista de workspaces, digite o nome (ou parte do nome) do workspace a ser aberto. Para este exemplo, vamos usar um workspace chamado **contosoanalytics**.

    ![Lista de workspaces do Synapse filtrados para mostrar os que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio

1. Na visão geral do workspace, selecione a **URL da Web do workspace** para iniciar o Synapse Studio.

    ![Visão geral do workspace do Azure Synapse no portal do Azure com a URL da Web do workspace realçada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Criar um pool de SQL dedicado no Synapse Studio

1. Na home page do Synapse Studio, navegue até o **Hub de Gerenciamento** no painel de navegação esquerdo selecionando o ícone **Gerenciar**.

    ![Home page do Synapse Studio com a seção Hub de Gerenciamento realçada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Uma vez no Hub de Gerenciamento, navegue até a seção **Pools de SQL** para ver a lista atual de pools de SQL disponíveis no workspace.

    ![Hub de Gerenciamento do Synapse Studio com a navegação dos pools de SQL selecionada](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selecione o comando **+ Novo** e o novo assistente de criação de pool de SQL será exibido. 

    ![Listagem de pools de SQL do Hub de Gerenciamento do Synapse Studio.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Insira os seguintes detalhes na guia **Informações Básicas**:

    | Configuração | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome do pool de SQL** | contosoedw | Esse é o nome que o pool de SQL dedicado terá. |
    | **Nível de desempenho** | DW100c | Defina isso com o menor tamanho para reduzir os custos deste início rápido |

    ![Fluxo de criação de pools de SQL – guia Básico.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Observe que há limitações específicas para os nomes que os pools de SQL dedicados podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 caracteres ou menos, não conter palavras reservadas e ser exclusivos no workspace.

4. Na próxima guia, **Configurações adicionais**, selecione **nenhum** para provisionar o pool de SQL sem dados. Deixe a ordenação padrão conforme selecionada.

    Se você quiser restaurar o pool de SQL dedicado com base em um ponto de restauração, selecione **Ponto de restauração**. Para obter mais informações sobre como executar uma restauração, confira [Instruções: restaurar um pool de SQL dedicado existente](backuprestore/restore-sql-pool.md)

    ![Fluxo de criação de pool de SQL – guia Configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Por enquanto não adicionaremos marcas, assim, selecione **Examinar + criar**.

1. Na guia **Revisar + criar**, verifique se os detalhes estão corretos com base no que foi inserido anteriormente e clique em **criar**. 

    ![Fluxo de criação do pool de SQL – guia Examinar configurações.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Neste ponto, o fluxo de provisionamento de recursos será iniciado.

1. Após a conclusão do provisionamento, ao navegar de volta para o workspace, aparecerá uma nova entrada para o pool de SQL recém-criado.

    ![Fluxo de criação do pool de SQL – provisionamento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Depois que o pool de SQL dedicado for criado, ele estará disponível no workspace para carregar dados, processar fluxos, ler do lake etc.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Limpar um pool de SQL dedicado usando o Synapse Studio    

Siga as etapas abaixo para excluir o pool de SQL dedicado do workspace usando o Synapse Studio.
> [!WARNING]
> A exclusão de um pool de SQL dedicado removerá o mecanismo de análise do workspace. Não será mais possível se conectar ao pool e todas as consultas, os pipelines e os scripts que usam esse pool de SQL dedicado deixarão de funcionar.

Caso deseje excluir o pool de SQL dedicado, faça o seguinte:

1. Navegue até os pools de SQL no Hub de Gerenciamento no Synapse Studio.
1. Selecione as reticências no pool de SQL dedicado a ser excluído (nesse caso, **contosoedw**) para mostrar os comandos do pool de SQL dedicado:

    ![Listagem de pools de SQL, com o pool recém-criado selecionado.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Selecione **delete**.
1. Confirme a exclusão e selecione o botão **Delete**.
1. Quando o processo for concluído com sucesso, o pool de SQL dedicado não será mais listado nos recursos do workspace.

## <a name="next-steps"></a>Próximas etapas
 
- Confira [Início Rápido: Criar um notebook do Apache Spark](quickstart-apache-spark-notebook.md).
- Confira [Início Rápido: criar um pool de SQL dedicado usando o portal do Azure](quickstart-create-sql-pool-portal.md).
