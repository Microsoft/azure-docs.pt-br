---
title: Use o Redgate para carregar dados para o Data Warehouse do Azure | Microsoft Docs
description: "Saiba como usar o Data Platform Studio da Redgate para cenários de data warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: a38b237d5bfc0450c1ca79b53a5784dbb9bf8602
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-with-redgate-data-platform-studio"></a>Carregar dados com o Data Platform Studio da Redgate
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Fábrica de dados](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

Este tutorial mostra como usar o [Data Platform Studio da Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) para mover dados de um SQL Server local para o SQL Data Warehouse do Azure. O Data Platform Studio aplica-se as correções de compatibilidade e otimizações mais apropriada, portanto, é a maneira mais rápida de começar a usar o SQL Data Warehouse.

> [!NOTE]
> [Redgate](http://www.red-gate.com) é um antigo parceiro da Microsoft que oferece várias ferramentas de SQL Server. Esse recurso no Data Platform Studio ficou disponível gratuitamente para uso comercial e não comercial.
> 
> 

## <a name="before-you-begin"></a>Antes de começar
### <a name="create-or-identify-resources"></a>Criar ou identificar recursos
Antes de iniciar este tutorial, você precisa ter os recursos a seguir:

* **Banco de dados do SQL Server local**: os dados que você deseja importar para o SQL Data Warehouse precisam vir de um SQL Server local (versão 2008R2 ou superior). Data Platform Studio não pode importar dados diretamente de um Banco de Dados SQL do Azure ou de arquivos de texto.
* **Conta de Armazenamento do Azure**: o Data Platform Studio prepara os dados no Armazenamento de Blobs do Azure antes de carregá-los no SQL Data Warehouse. A conta de armazenamento deve estar usando o modelo de implantação "Gerenciador de recursos" (o padrão), em vez do modelo de implantação "Clássico". Se você não tiver uma conta de armazenamento, aprenda como criar uma conta de armazenamento. 
* **SQL Data Warehouse**: este tutorial move os dados do SQL Server local para o SQL Data Warehouse, por isso você precisa ter um data warehouse online. Se você ainda não tiver um data warehouse, saiba como criar um Azure SQL Data Warehouse.

> [!NOTE]
> O desempenho é aprimorado se a conta de armazenamento e o data warehouse são criados na mesma região.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Etapa 1: Entrar no Data Platform Studio com sua conta do Azure
Abra seu navegador da Web e navegue até o site do [Data Platform Studio](https://www.dataplatformstudio.com/). Entrar com a mesma conta do Azure que você usou para criar o data warehouse a conta de armazenamento. Se seu endereço de email está associado a uma conta corporativa ou de estudante e a uma conta da Microsoft, certifique-se de escolher a conta que tenha acesso aos seus recursos.

> [!NOTE]
> Se esta for sua primeira vez usando o Data Platform Studio, você precisará conceder a permissão de aplicativo para gerenciar os recursos do Azure.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>Etapa 2: Iniciar o assistente de importação
Na tela principal do DPS, selecione o link Importar para Azure SQL Data Warehouse para iniciar o assistente de importação.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Etapa 3: Instalar o gateway do Data Platform Studio
Para se conectar ao banco de dados do SQL Server local, você precisa instalar o Gateway do DPS. O gateway é um agente cliente que fornece acesso ao seu ambiente local, extrai os dados e carrega-os em sua conta de armazenamento. Os dados nunca passam por servidores da Redgate. Instalar o Gateway:

1. Clique no link **Criar Gateway**
2. Baixe e instale o Gateway usando o instalador fornecido

![][2]

> [!NOTE]
> O Gateway pode ser instalado em qualquer computador com acesso à rede para o banco de dados do SQL Server de origem. Ele acessa o banco de dados do SQL Server usando a autenticação do Windows com as credenciais do usuário atual.
> 
> 

Uma vez instalado, o status de Gateway é alterado para Conectado e você pode selecionar Avançar.

## <a name="step-4-identify-the-source-database"></a>Etapa 4: Identificar o banco de dados de origem
Na caixa de texto *Insira o nome do servidor*, digite o nome do servidor que hospeda seu banco de dados e selecione **Avançar**. Em seguida, no menu suspenso, selecione o banco de dados do qual você deseja importar dados.

![][3]

O DPS inspeciona o banco de dados para as tabelas a serem importadas. Por padrão, o DPS importa todas as tabelas no banco de dados. Você pode marcar ou desmarcar tabelas expandindo o link de todas as tabelas. Selecione o botão Avançar para continuar.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Etapa 5: Escolha uma conta de armazenamento a fim de preparar os dados
O DPS solicitará um local para transferir os dados. Escolha uma conta de armazenamento existente na sua assinatura e selecione **Avançar**.

> [!NOTE]
> O DPS criará um novo contêiner blob na conta de armazenamento escolhida e use uma pasta distinta para cada importação.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Etapa 6: Criar um data warehouse
Em seguida, selecione o banco de dados online [Azure SQL Data Warehouse](http://aka.ms/sqldw) para importar os dados. Depois de selecionar o banco de dados, você precisa inserir as credenciais para se conectar ao banco de dados e selecionar **Avançar**.

![][5]

> [!NOTE]
> O DPS mescla as tabelas de dados de origem no data warehouse. O DPS avisa se o nome da tabela requer a substituição das tabelas existentes no data warehouse. Você, opcionalmente, poderá excluir quaisquer objetos existentes no data warehouse, clicando em excluir todos os objetos existentes antes da importação.
> 
> 

## <a name="step-7-import-the-data"></a>Etapa 7: Importar dados
O DPS confirma que você deseja importar os dados. Basta clicar no botão de iniciar importação para iniciar a importação de dados.

![][6]

O DPS exibe uma visualização que mostra o andamento da extração e carregamento de dados do SQL Server local e o andamento da importação no SQL Data Warehouse.

![][7]

Depois que a importação for concluída, o DPS exibe um resumo da importação de dados e um relatório de alteração das correções de compatibilidade que foram executadas.

![][8]

## <a name="next-steps"></a>Próximas etapas
Para explorar seus dados no SQL Data Warehouse, inicie por meio da exibição:

* [Consultar o Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Visualizar os dados com o Power BI][Visualize data with Power BI]

Para saber mais sobre o Data Platform Studio da Redgate:

* [Visite a página inicial do DPS](http://www.dataplatformstudio.com/)
* [Assista a uma demonstração do DPS no Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Para obter uma visão geral de outras maneiras de migrar e carregar os dados no SQL Data Warehouse, consulte:

* [Migrar sua solução para o SQL Data Warehouse][Migrate your solution to SQL Data Warehouse]
* [Carregar dados no SQL Data Warehouse do Azure](sql-data-warehouse-overview-load.md)

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
