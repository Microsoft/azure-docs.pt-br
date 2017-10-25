---
title: Carregar dados do SQL Server no SQL Data Warehouse do Azure (SSIS) | Microsoft Docs
description: "Mostra a você como criar um pacote do SSIS (SQL Server Integration Services) para mover dados de uma ampla variedade de fontes de dados para o SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Carregar dados do SQL Server no Azure SQL Data Warehouse (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Crie um pacote SSIS (SQL Server Integration Services) para carregar dados do SQL Server no SQL Data Warehouse do Azure. Opcionalmente, você pode reestruturar, transformar e limpar os dados conforme eles passam pelo fluxo de dados do SSIS.

Neste tutorial, você irá:

* Criar um novo projeto do Integration Services no Visual Studio.
* Conectar-se a fontes de dados, incluindo SQL Server (como uma fonte) e o SQL Data Warehouse (como um destino).
* Criar um pacote do SSIS que carregue os dados da fonte para o destino.
* Executar o pacote do SSIS para carregar os dados.

Este tutorial usa o SQL Server como a fonte de dados. O SQL Server pode estar em execução local ou em uma máquina virtual do Azure.

## <a name="basic-concepts"></a>Conceitos básicos
O pacote é a unidade de trabalho no SSIS. Os pacotes relacionados são agrupados em projetos. Você cria projetos e pacotes de design no Visual Studio com o SQL Server Data Tools. O processo de design é um processo visual no qual você arrasta e solta componentes da Caixa de Ferramentas para a superfície de design, conecta-os e define suas propriedades. Depois de concluir seu pacote, você pode, opcionalmente, implantá-lo no SQL Server para gerenciamento, monitoramento e segurança abrangentes.

## <a name="options-for-loading-data-with-ssis"></a>Opções de carregamento de dados com o SSIS
O SSIS (SQL Server Integration Services) é um conjunto flexível de ferramentas que fornece uma variedade de opções para se conectar a e carregar dados no SQL Data Warehouse.

1. Use um destino do ADO NET para conectar-se ao SQL Data Warehouse. Este tutorial usa um Destino do ADO NET porque ele é o que tem o menor número de opções de configuração.
2. Use um Destino OLE DB para conectar-se ao SQL Data Warehouse. Esta opção pode fornecer desempenho um pouco melhor do que o Destino ADO NET.
3. Use a Tarefa de Upload de Blob do Azure para preparar os dados no Armazenamento de Blobs do Azure. Em seguida, use a tarefa Executar SQL do SSIS para iniciar um script do Polybase que carrega os dados no SQL Data Warehouse. Essa opção oferece o melhor desempenho das três opções listadas aqui. Para obter a Tarefa de Upload de Blobs do Azure, baixe o [Feature Pack do Microsoft SQL Server 2016 Integration Services para o Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Para saber mais sobre o Polybase, consulte o [Guia do PolyBase][PolyBase Guide].

## <a name="before-you-start"></a>Antes de começar
Para acompanhar este tutorial, você precisará:

1. **SSIS (SQL Server Integration Services)**. O SSIS é um componente do SQL Server e requer uma versão de avaliação ou uma versão licenciada do SQL Server. Para obter uma versão de avaliação da Visualização do SQL Server 2016, consulte [Avaliações do SQL Server][SQL Server Evaluations].
2. **Visual Studio**. Para obter o Visual Studio Community Edition gratuito, consulte [Visual Studio Community][Visual Studio Community].
3. **SSDT (SQL Server Data Tools) para Visual Studio**. Para obter o SQL Server Data Tools para Visual Studio, consulte [Baixar o SSDT (SQL Server Data Tools)][Download SQL Server Data Tools (SSDT)].
4. **Dados de exemplo**. Este tutorial usa dados de exemplo armazenados no SQL Server no banco de dados de exemplo do AdventureWorks como os dados de origem a serem carregados no SQL Data Warehouse. Para obter o banco de dados de exemplo do AdventureWorks, consulte [Bancos de Dados de Exemplo do AdventureWorks 2014][AdventureWorks 2014 Sample Databases].
5. **Um banco de dados e permissões do SQL Data Warehouse**. Este tutorial conecta-se a uma instância do SQL Data Warehouse e carrega dados nela. Você precisa ter permissões para criar uma tabela e para carregar dados.
6. **Uma regra de firewall**. Antes que você possa carregar dados no SQL Data Warehouse, você precisará criar uma regra de firewall no SQL Data Warehouse com o endereço IP do computador local.

## <a name="step-1-create-a-new-integration-services-project"></a>Etapa 1: Criar um novo projeto do Integration Services
1. Inicie o Visual Studio.
2. No menu **Arquivo**, selecione **Novo | Projeto**.
3. Navegue até os tipos de projeto **Instalado | Modelos | Business Intelligence | Integration Services** .
4. Selecione **Projeto do Integration Services**. Forneça valores para **Nome** e **Local** e, em seguida, selecione **OK**.

O Visual Studio abre e cria um novo projeto do SSIS (Integration Services). Em seguida, o Visual Studio abre o designer para o único novo pacote SSIS (Package.dtsx) no projeto. Você vê as seguintes áreas de tela:

* À esquerda, a **Caixa de Ferramentas** dos componentes do SSIS.
* No meio, a superfície de design, com várias guias. Normalmente, você usa pelo menos as guias **Fluxo de Controle** e **Fluxo de Dados**.
* À direita, os painéis **Gerenciador de Soluções** e **Propriedades**.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Etapa 2: Criar o fluxo de dados básico
1. Arraste uma Tarefa de Fluxo de Dados da Caixa de Ferramentas para o centro da superfície de design (na guia **Fluxo de Controle** ).
   
    ![][02]
2. Clique duas vezes na Tarefa de Fluxo de Dados para alternar para a guia Fluxo de Dados.
3. Na lista Outras Fontes na Caixa de Ferramentas, arraste uma Fonte do ADO.NET para a superfície de design. Com o adaptador de fonte ainda selecionado, altere seu nome para **Fonte do SQL Server** no painel **Propriedades**.
4. Na lista Outros Destinos na caixa de ferramentas, arraste um Destino ADO.NET para a superfície de design na Fonte do ADO.NET. Com o adaptador de destino ainda selecionado, altere seu nome para **Destino do SQL DW** no painel **Propriedades**.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Etapa 3: Configurar o adaptador de fonte
1. Clique duas vezes no adaptador de fonte para abrir o **Editor de Fonte do ADO.NET**.
   
    ![][03]
2. Na guia **Gerenciador de Conexões** do **Editor de Fonte do ADO.NET**, clique no botão **Novo** ao lado da lista **Gerenciador de conexões ADO.NET** para abrir a caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET** e crie as configurações de conexão para o banco de dados do SQL Server do qual este tutorial carrega os dados.
   
    ![][04]
3. Na caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET**, clique no botão **Novo** para abrir a caixa de diálogo **Gerenciador de Conexões** e criar uma nova conexão de dados.
   
    ![][05]
4. Na caixa de diálogo **Gerenciador de Conexões** , realize as ações descritas a seguir.
   
   1. Para **Provedor**, selecione o Provedor de Dados SqlClient.
   2. Para **Nome do servidor**, insira o nome do SQL Server.
   3. Na seção **Fazer logon no servidor** , selecione ou insira as informações de autenticação.
   4. Na seção **Conectar-se a um banco de dados** , selecione o banco de dados de exemplo AdventureWorks.
   5. Clique em **Testar conexão**.
      
       ![][06]
   6. Na caixa de diálogo que relata os resultados do teste de conexão, clique em **OK** para retornar à caixa de diálogo **Gerenciador de Conexões**.
   7. Na caixa de diálogo **Gerenciador de Conexões**, clique em **OK** para retornar à caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET**.
5. Na caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET**, clique em **OK** para retornar ao **Editor de Fonte do ADO.NET**.
6. No **Editor de Fonte do ADO.NET**, na lista **Nome da tabela ou exibição**, selecione a tabela **Sales.SalesOrderDetail**.
   
    ![][07]
7. Clique em **Visualização** para ver as primeiras 200 linhas de dados na tabela de origem na caixa de diálogo **Visualizar Resultados da Consulta**.
   
    ![][08]
8. Na caixa de diálogo **Visualizar Resultados da Consulta**, clique em **Fechar** para retornar para o **Editor de Fonte do ADO.NET**.
9. No **Editor de Fonte do ADO.NET**, clique em **OK** para concluir a configuração da fonte de dados.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Etapa 4: Conectar o adaptador de fonte ao adaptador de destino
1. Selecione o adaptador de fonte na superfície de design.
2. Selecione a seta azul que se estende do adaptador de fonte e arraste-a para o editor de destino até que ela se encaixe.
   
    ![][10]
   
    Em um pacote SSIS típico, você pode usar um número de outros componentes da Caixa de Ferramentas do SSIS entre a fonte e o destino para reestruturar, transformar e limpar seus dados conforme eles passam pelo fluxo de dados do SSIS. Para manter esse exemplo o mais simples possível, estamos conectando a fonte diretamente ao destino.

## <a name="step-5-configure-the-destination-adapter"></a>Etapa 5: Configurar o adaptador de destino
1. Clique duas vezes no adaptador de destino para abrir o **Editor de Destino do ADO.NET**.
   
    ![][11]
2. Na guia **Gerenciador de Conexões** do **Editor de Destino do ADO.NET**, clique no botão **Novo** ao lado da lista **Gerenciador de conexões** para abrir a caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET** e crie as configurações de conexão para o banco de dados do SQL Data Warehouse no qual este tutorial carrega os dados.
3. Na caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET**, clique no botão **Novo** para abrir a caixa de diálogo **Gerenciador de Conexões** e criar uma nova conexão de dados.
4. Na caixa de diálogo **Gerenciador de Conexões** , realize as ações descritas a seguir.
   1. Para **Provedor**, selecione o Provedor de Dados SqlClient.
   2. Para **Nome do servidor**, digite o nome do SQL Data Warehouse.
   3. Na seção **Fazer logon no servidor**, selecione **Usar autenticação do SQL Server** e insira as informações de autenticação.
   4. Na seção **Conectar-se a um banco de dados** , selecione um banco de dados do SQL Data Warehouse existente.
   5. Clique em **Testar conexão**.
   6. Na caixa de diálogo que relata os resultados do teste de conexão, clique em **OK** para retornar à caixa de diálogo **Gerenciador de Conexões**.
   7. Na caixa de diálogo **Gerenciador de Conexões**, clique em **OK** para retornar à caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET**.
5. Na caixa de diálogo **Configurar Gerenciador de Conexões do ADO.NET**, clique em **OK** para retornar ao **Editor de Destino do ADO.NET**.
6. No **Editor de Destino do ADO.NET**, clique em **Novo** ao lado da lista **Usar uma tabela ou exibição** para abrir a caixa de diálogo **Criar Tabela** para criar uma nova tabela de destino com uma lista de colunas que corresponde à tabela de origem.
   
    ![][12a]
7. Na caixa de diálogo **Criar Tabela** , realize as ações a seguir.
   
   1. Altere o nome da tabela de destino para **SalesOrderDetail**.
   2. Remova a coluna **rowguid** . Não há suporte para o tipo de dados **uniqueidentifier** no SQL Data Warehouse.
   3. Altere o tipo de dados da coluna **LineTotal** para **money**. Não há suporte para o tipo de dados **decimal** no SQL Data Warehouse. Para obter informações sobre tipos de dados com suporte, consulte [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Clique em **OK** para criar a tabela e retornar para o **Editor de Destino do ADO.NET**.
8. No **Editor de Destino do ADO.NET**, selecione a guia **Mapeamentos** para ver como as colunas na origem são mapeadas para as colunas no destino.
   
    ![][13]
9. Clique em **OK** para concluir a configuração da fonte de dados.

## <a name="step-6-run-the-package-to-load-the-data"></a>Etapa 6: Executar o pacote para carregar os dados
Execute o pacote clicando no botão **Iniciar** na barra de ferramentas ou selecionando uma das opções **Executar** no menu **Depurar**.

O pacote começa a executar e você vê rodas amarelas girando para indicar atividade, bem como o número de linhas processadas até o momento.

![][14]

Quando a execução do pacote for concluída, você verá marcas de seleção verdes para indicar êxito, bem como o número total de linhas de dados carregados da fonte para o destino.

![][15]

Parabéns! Você usou com êxito o SQL Server Integration Services para carregar dados no SQL Data Warehouse do Azure.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o fluxo de dados do SSIS. Comece por aqui: [Fluxo de Dados][Data Flow].
* Saiba como depurar e solucionar problemas de seus pacotes direto do ambiente de design. Comece por aqui: [Solução de problemas de ferramentas para o desenvolvimento de pacotes][Troubleshooting Tools for Package Development].
* Saiba como implantar seus pacotes e projetos do SSIS no servidor do Integration Services ou em outro local de armazenamento. Comece por aqui: [Implantação de projetos e pacotes][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
