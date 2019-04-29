---
title: Conectar-se ao Banco de Dados SQL usando C e C++ | Microsoft Docs
description: Use o código de exemplo neste início rápido para criar um aplicativo moderno com C++ e sustentado por um poderoso banco de dados relacional na nuvem com o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 00a3904bd78f3bb76266c726af28582770b23921
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723905"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Conectar-se ao Banco de Dados SQL usando C e C++

Esta postagem é destinada a desenvolvedores de C e C++ que tentam se conectar ao Banco de Dados de SQL do Azure. Ele é dividido em seções para que você possa ir direto para aquela que melhor captura seu interesse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Pré-requisitos para o tutorial do C/C++

Verifique se você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Você deve instalar os componentes de linguagem C++ para compilar e executar esse exemplo.
* [Desenvolvimento no Linux para Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Se você estiver desenvolvendo no Linux, também deverá instalar a extensão do Linux do Visual Studio.

## <a id="AzureSQL"></a>Banco de dados SQL do Azure e SQL Server em máquinas virtuais
O Azure SQL baseia-se no Microsoft SQL Server e foi projetado para fornecer uma alta disponibilidade, desempenho e serviço escalonável. Há muitos benefícios em usar o SQL Azure em vez do seu banco de dados proprietário em execução no local. Com o SQL Azure, você não precisa instalar, configurar, manter ou gerenciar o banco de dados, somente o conteúdo e a estrutura do seu banco de dados. Causas de preocupação típicas de bancos de dados como redundância e tolerância a falhas estão incluídos.

Atualmente, o Azure tem duas opções para hospedar cargas de trabalho do SQL Server: Banco de Dados SQL do Azure, banco de dados como um serviço e SQL Server em Máquinas Virtuais (VM). Não entraremos em detalhes sobre as diferenças entre esses dois, exceto que o Banco de Dados SQL do Azure é a melhor opção para novos aplicativos baseados em nuvem aproveitarem a economia de custo e otimização de desempenho que os serviços de nuvem fornecem. Se você estiver pretendendo migrar ou estender seus aplicativos locais para a nuvem, o SQL Server na máquina virtual do Azure pode funcionar melhor para você. Para manter as coisas simples neste artigo, vamos criar um Banco de Dados SQL do Azure.

## <a id="ODBC"></a>Tecnologias de acesso a dados: ODBC e OLE DB
Conectar-se ao BD SQL do Azure não é diferente e, atualmente, há duas maneiras para se conectar aos bancos de dados: ODBC (Conectividade ao Banco de Dados Aberto) e OLE DB (Banco de Dados de Vinculação e Incorporação de Objeto). Nos últimos anos, a Microsoft tem se alinhado com o [ODBC para acesso a dados relacionais nativos](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). O ODBC é relativamente simples e também muito mais rápido que o OLE DB. A única limitação é que o ODBC usa uma API de estilo C antiga.

## <a id="Create"></a>Etapa 1:  Criando seu Banco de Dados SQL do Azure
Consulte a [página de introdução](sql-database-single-database-get-started.md) para aprender a criar um banco de dados de exemplo.  Como alternativa, você pode seguir este [breve vídeo de dois minutos](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) para criar um Banco de Dados SQL do Azure usando o Portal do Azure.

## <a id="ConnectionString"></a>Etapa 2:  Obter a cadeia de conexão
Depois do Banco de Dados SQL do Azure ser configurado, você precisará executar as seguintes etapas para determinar as informações de conexão e adicionar o IP do cliente para acesso ao firewall.

No [Portal do Azure](https://portal.azure.com/), vá para a cadeia de conexão ODBC do Banco de Dados SQL do Azure usando **Mostrar cadeias de conexão do banco de dados** listado como parte da seção de visão geral do banco de dados:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Copie o conteúdo da cadeia de caracteres **ODBC (inclui Node.js) [autenticação do SQL]**. Usaremos essa cadeia de caracteres posteriormente para conectar do nosso interpretador de linha de comando ODBC C++. Essa cadeia de caracteres fornece detalhes como o driver, servidor e outros parâmetros de conexão de banco de dados.

## <a id="Firewall"></a>Etapa 3:  Adicionar o IP ao firewall
Vá para a seção de firewall para o servidor de Banco de dados e adicione seu [IP do cliente ao firewall usando estas etapas](sql-database-configure-firewall-settings.md) para verificar se é possível estabelecer uma conexão bem-sucedida:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Neste ponto, você configurou seu Banco de Dados SQL do Azure e está pronto para se conectar do código C++.

## <a id="Windows"></a>Etapa 4: Conectando de um aplicativo C/C++ no Windows
Você pode conectar facilmente ao seu [Banco de Dados SQL do Azure usando ODBC no Windows com este exemplo](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) baseado no Visual Studio. O exemplo implementa um interpretador de linha de comando do ODBC que pode ser usado para conectar ao nosso Banco de Dados SQL do Azure. Este exemplo usa o um arquivo DSN ( nome de origem do banco de dados) como um argumento de linha de comando ou cadeia de conexão detalhada copiado previamente no Portal do Azure. Abra a página de propriedade para este projeto e cole a cadeia de conexão como um argumento de comando como mostrado aqui:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Verifique se você forneceu os detalhes de autenticação adequados para seu banco de dados como parte dessa cadeia de conexão do banco de dados.

Abra o aplicativo e compile-o. Você verá a seguinte janela validando se a conexão foi bem-sucedida. Você poderá até mesmo executar alguns comandos básicos do SQL como **create table** para validar a conectividade do banco de dados:

![Comandos SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Como alternativa, você poderia criar um arquivo DSN usando o assistente que é iniciado quando nenhum argumento é fornecido. É recomendável que você também experimente usar essa opção. Você pode usar esse arquivo DSN para automação e para proteger suas configurações de autenticação:

![Criar um arquivo DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Parabéns! Agora você se conectou com êxito ao SQL Azure usando o C++ e ODBC no Windows. Você pode continuar lendo para fazer o mesmo para a plataforma Linux.

## <a id="Linux"></a>Etapa 5: Conectando de um aplicativo C/C++ no Linux
Caso ainda saiba, agora o Visual Studio permite que você desenvolva aplicativos C++ Linux também. Você pode ler sobre esse novo cenário no blog [Desenvolvimento em Visual C++ para Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/). Para compilar para Linux, você precisa de um computador remoto onde sua distribuição do Linux está sendo executada. Se você não tiver um disponível, poderá definir um rapidamente usando [máquinas virtuais Azure Linux](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para este tutorial, vamos supor que você tenha uma distribuição do Ubuntu 16.04 Linux configurada. As etapas aqui também devem aplicar ao Ubuntu 15.10, Red Hat 6 e Red Hat 7.

As etapas a seguir instalam as bibliotecas necessárias para SQL e ODBC para sua distribuição:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Inicie o Visual Studio. Em Ferramentas -> Opções -> Plataforma Cruzada -> C++ -> Gerenciador de Conexões, adicione uma conexão à sua caixa do Linux:

![Opções de Ferramentas](./media/sql-database-develop-cplusplus-simple/tools.png)

Depois de estabelecer a conexão via SSH, crie um modelo de projeto Vazio (Linux):

![Novo modelo do projeto](./media/sql-database-develop-cplusplus-simple/template.png)

Você pode adicionar um [novo arquivo de origem C e substituí-lo por este conteúdo](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Usando as APIs ODBC SQLAllocHandle, SQLSetConnectAttr e SQLDriverConnect, você deverá poder inicializar e estabelecer uma conexão com o banco de dados.
Como no exemplo do Windows ODBC, você precisará substituir a chamada SQLDriverConnect pelos detalhes dos parâmetros de cadeia de conexão do banco de dados copiados anteriormente no Portal do Azure.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

A última coisa a fazer antes de compilar é adicionar **odbc** como uma dependência de biblioteca:

![Adicionando o ODBC como uma biblioteca de entrada](./media/sql-database-develop-cplusplus-simple/lib.png)

Para iniciar o aplicativo, abra o Console do Linux no menu **Depurar**:

![Console do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Se a conexão for bem-sucedida, agora você verá o nome do banco de dados atual impresso no Console do Linux:

![Saída da Janela de Console do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Parabéns! Você concluiu com êxito o tutorial e poderá se conectar ao seu Banco de Dados SQL do Azure do C++ em plataformas Windows e Linux.

## <a id="GetSolution"></a>Obter a solução completa do tutorial de C/C++
Você pode encontrar a solução de Introdução que contém todos os exemplos nesse artigo no GitHub:

* [Exemplo de ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), baixe o exemplo do Windows C++ ODBC para se conectar ao Azure SQL
* [Exemplo de ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), baixe o exemplo do Linux C++ ODBC para se conectar ao Azure SQL

## <a name="next-steps"></a>Próximas etapas
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações na [Referência da API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)

