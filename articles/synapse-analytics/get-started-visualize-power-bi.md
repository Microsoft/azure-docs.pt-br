---
title: 'Tutorial: Introdução ao Azure Synapse Analytics: visualizar dados do workspace com o Power BI'
description: Neste tutorial, você aprenderá a criar um workspace do Power BI, vincular seu workspace do Azure Synapse e criar um conjunto de dados do Power BI que utiliza os dados no workspace do Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 03/25/2021
ms.openlocfilehash: 829daaa9c739657528a9085201c61c88635931d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608435"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar os dados com o Power BI

Neste tutorial, você aprenderá a criar um workspace do Power BI, vincular seu workspace do Azure Synapse e criar um conjunto de dados do Power BI que utiliza os dados no workspace do Azure Synapse. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, [instale o Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="overview"></a>Visão geral

Com base nos dados de táxi de Nova York, criamos conjuntos de dados agregados em duas tabelas:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Você pode vincular um workspace do Power BI ao seu workspace do Azure Synapse. Essa funcionalidade permite que você obtenha dados com facilidade em seu workspace do Power BI. Você pode editar os relatórios de Power BI diretamente no seu workspace do Azure Synapse. 

### <a name="create-a-power-bi-workspace"></a>Criar um workspace do Power BI

1. Entre no [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Clique em **Workspaces** e selecione **Criar um workspace**. Crie um workspace do Power BI chamado **NYCTaxiWorkspace1** ou outro nome semelhante, pois esse nome precisa ser exclusivo.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Vincular seu workspace do Azure Synapse ao novo workspace do Power BI

1. No Synapse Studio, vá para **Gerenciar** > **Serviços Vinculados**.
1. Selecione **Novo** > **Conectar-se ao Power BI**.
1. Defina **Nome** como **NYCTaxiWorkspace1**.
1. Defina **Nome do workspace** como o workspace do Power BI criado acima, semelhante a **NYCTaxiWorkspace1**.
1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Criar um conjunto de dados do Power BI que use dados em seu workspace do Azure Synapse

1. No Synapse Studio, acesse **Desenvolver** > **Power BI**.
1. Navegue até **NYCTaxiWorkspace1** > **Conjuntos de dados do Power BI** e selecione **Novo conjunto de dados do Power BI**. Clique em **Iniciar**.
1. Selecione a fonte de dados **SQLPOOL1** e clique em **Continuar**.
1. Clique em **Baixar** para baixar o arquivo .pbids para o arquivo **NYCTaxiWorkspace1SQLPOOL1.pbids**. Clique em **Continue**.
1. Abra o arquivo **.pbids** baixado. O Power BI Desktop será aberto e se conectará automaticamente ao **SQLDB1** no workspace do Azure Synapse.
1. Se uma caixa de diálogo chamada **Banco de dados do SQL Server** for exibida:
    1. Selecione **Conta Microsoft**.
    1. Selecione **Entrar** e entre na conta.
    1. Selecione **Conectar**.
1. Depois que a caixa de diálogo **Navegador** for aberta, verifique a tabela **PassengerCountStats** e selecione **Carregar**.
1. Depois que a caixa de diálogo **Configurações de conexão** for exibida, selecione **DirectQuery** > **OK**.
1. Selecione o botão **Relatório** à esquerda.
1. Em **Visualizações**, clique no ícone do gráfico de linhas para adicionar um **Gráfico de linhas** ao relatório.
    1. Em **Campos**, arraste a coluna **PassengerCount** para **Visualizações** > **Eixo**.
    1. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Visualizações** > **Valores**.
1. Na guia **Página Inicial**, selecione **Publicar**.
1. Selecione **Salvar** para salvar as alterações.
1. Escolha o nome do arquivo **PassengerAnalysis.pbix** e, em seguida, selecione **Salvar**.
1. Na janela **Publicar no Power BI**, em **Selecionar um destino**, escolha o **NYCTaxiWorkspace1** e clique em **Selecionar**.
1. Aguarde a conclusão da publicação. 

### <a name="configure-authentication-for-your-dataset"></a>Configurar autenticação para o conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e escolha **Entrar**.
1. No lado esquerdo, em **Workspaces**, selecione o workspace **NYCTaxiWorkspace1**.
1. Dentro desse workspace, localize um conjunto de dados chamado de **Análise de Passageiros** e um relatório chamado **Análise de Passageiros**.
1. Passe o mouse sobre o conjunto de dados **PassengerAnalysis**, selecione o botão de reticências (...) e, em seguida, selecione **Configurações**.
1. Em **Credenciais da fonte de dados**, clique em **Editar**, defina o **Método de autenticação** como **OAuth2** e selecione **Entrar**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar um relatório no Synapse Studio

1. Volte para o Synapse Studio e selecione **Fechar e atualizar**.
1. Acesse o hub **Desenvolver**.
1. À direita da camada do **Power BI**, clique no botão de reticências (...) e clique em **Atualizar** para atualizar o nó **Relatórios do Power BI**.
1. Agora, em **Power BI**, você deve ver:
    * Em **NYCTaxiWorkspace1** > **Conjunto de dados do Power BI**, um novo conjunto de dados chamado **PassengerAnalysis**.
    * Em **NYCTaxiWorkspace1** > **Relatórios do Power BI**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis**. O relatório é aberto e você pode editá-lo diretamente no Synapse Studio.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar](get-started-monitor.md)
                                 

