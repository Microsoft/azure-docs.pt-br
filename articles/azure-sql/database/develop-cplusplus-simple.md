---
title: Conectar-se ao Banco de Dados SQL usando C e C++
description: Use o código de exemplo neste início rápido para criar um aplicativo moderno com C++ e sustentado por um poderoso banco de dados relacional na nuvem com o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: 0460317a47a1cf01707990b6f92532d4ade01439
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643283"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Conectar-se ao Banco de Dados SQL usando C e C++
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Esta postagem destina-se a desenvolvedores C e C++ tentando se conectar ao banco de dados SQL do Azure. Ele é dividido em seções para que você possa ir direto para aquela que melhor captura seu interesse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Pré-requisitos para o tutorial do C/C++

Verifique se você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Do Visual Studio](https://www.visualstudio.com/downloads/). Você deve instalar os componentes de linguagem C++ para compilar e executar esse exemplo.
* [Desenvolvimento no Linux para Visual Studio](/cpp/linux/). Se você estiver desenvolvendo no Linux, também deverá instalar a extensão do Linux do Visual Studio.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Banco de dados SQL do Azure e SQL Server em máquinas virtuais

O banco de dados SQL do Azure foi criado em Microsoft SQL Server e foi projetado para fornecer um serviço de alta disponibilidade, de alto desempenho e escalonável. Há muitos benefícios em usar o SQL do Azure em seu banco de dados proprietário em execução no local. Com o Azure SQL, você não precisa instalar, configurar, manter ou gerenciar seu banco de dados, mas apenas o conteúdo e a estrutura do banco de dados. Causas de preocupação típicas de bancos de dados como redundância e tolerância a falhas estão incluídos.

Atualmente, o Azure tem duas opções para hospedar cargas de trabalho do SQL Server: banco de dados SQL do Azure, banco de dados como serviço e SQL Server em máquinas virtuais (VM). Não entraremos em detalhes sobre as diferenças entre essas duas, exceto pelo fato de o banco de dados SQL do Azure ser a melhor opção para novos aplicativos baseados em nuvem aproveitarem a economia de custos e a otimização de desempenho que os serviços de nuvem fornecem. Se você estiver pretendendo migrar ou estender seus aplicativos locais para a nuvem, o SQL Server na máquina virtual do Azure pode funcionar melhor para você. Para manter as coisas simples para este artigo, vamos criar um banco de dados SQL do Azure.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Tecnologias de acesso a dados: ODBC e OLE DB

Conectar-se ao banco de dados SQL do Azure não é diferente e atualmente há duas maneiras de se conectar a bancos de dados: ODBC (conectividade de banco de dados aberta) e OLE DB (vinculação de objeto e banco de dados de incorporação). Nos últimos anos, a Microsoft tem se alinhado com o [ODBC para acesso a dados relacionais nativos](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access). O ODBC é relativamente simples e também muito mais rápido que o OLE DB. A única limitação é que o ODBC usa uma API de estilo C antiga.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Etapa 1: Criando seu Banco de Dados SQL do Azure

Consulte a [página de introdução](single-database-create-quickstart.md) para aprender a criar um banco de dados de exemplo.  Como alternativa, você pode seguir este [pequeno vídeo de dois minutos](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) para criar um banco de dados SQL do Azure usando o portal do Azure.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Etapa 2: Obter a cadeia de conexão

Depois que o banco de dados SQL do Azure tiver sido provisionado, você precisará executar as etapas a seguir para determinar as informações de conexão e adicionar o IP do cliente para acesso ao firewall.

Em [portal do Azure](https://portal.azure.com/), vá para a cadeia de conexão ODBC do banco de dados SQL do Azure usando as **cadeias de conexão mostrar banco de dados** listadas como parte da seção de visão geral do seu banco de dados:

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Copie o conteúdo da cadeia de caracteres **ODBC (inclui Node.js) [autenticação do SQL]**. Usaremos essa cadeia de caracteres posteriormente para conectar do nosso interpretador de linha de comando ODBC C++. Essa cadeia de caracteres fornece detalhes como o driver, servidor e outros parâmetros de conexão de banco de dados.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Etapa 3: Adicionar o IP do firewall

Vá para a seção de firewall do seu servidor e adicione o [IP do cliente ao firewall usando estas etapas](firewall-configure.md) para garantir que possamos estabelecer uma conexão bem-sucedida:

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

Neste ponto, você configurou seu banco de dados SQL do Azure e está pronto para se conectar do seu código C++.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Etapa 4: Conectando de um aplicativo Windows C/C++

Você pode se conectar facilmente ao [banco de dados SQL do Azure usando ODBC no Windows usando este exemplo](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) que se baseia no Visual Studio. O exemplo implementa um interpretador de linha de comando ODBC que pode ser usado para se conectar ao nosso banco de dados SQL do Azure. Este exemplo usa o um arquivo DSN ( nome de origem do banco de dados) como um argumento de linha de comando ou cadeia de conexão detalhada copiado previamente no Portal do Azure. Abra a página de propriedade para este projeto e cole a cadeia de conexão como um argumento de comando como mostrado aqui:

![DSN Propsfile](./media/develop-cplusplus-simple/props.png)

Verifique se você forneceu os detalhes de autenticação adequados para seu banco de dados como parte dessa cadeia de conexão do banco de dados.

Abra o aplicativo e compile-o. Você verá a seguinte janela validando se a conexão foi bem-sucedida. Você poderá até mesmo executar alguns comandos básicos do SQL como **create table** para validar a conectividade do banco de dados:

![Comandos SQL](./media/develop-cplusplus-simple/sqlcommands.png)

Como alternativa, você poderia criar um arquivo DSN usando o assistente que é iniciado quando nenhum argumento é fornecido. É recomendável que você também experimente usar essa opção. Você pode usar esse arquivo DSN para automação e para proteger suas configurações de autenticação:

![Criar um arquivo DSN](./media/develop-cplusplus-simple/datasource.png)

Parabéns! Agora você se conectou com êxito ao SQL Azure usando o C++ e ODBC no Windows. Você pode continuar lendo para fazer o mesmo para a plataforma Linux.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Etapa 5: Conectando de um aplicativo Linux C/C++

Caso você ainda não tenha ouvido as notícias, o Visual Studio agora também permite que você desenvolva o aplicativo C++ Linux. Você pode ler sobre esse novo cenário no blog [Desenvolvimento em Visual C++ para Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/). Para compilar para Linux, você precisa de um computador remoto onde sua distribuição do Linux está sendo executada. Se você não tiver um disponível, poderá configurar um rapidamente usando [máquinas virtuais do Azure do Linux](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para este tutorial, vamos supor que você tenha uma distribuição do Ubuntu 16.04 Linux configurada. As etapas aqui também devem aplicar ao Ubuntu 15.10, Red Hat 6 e Red Hat 7.

As etapas a seguir instalam as bibliotecas necessárias para SQL e ODBC para sua distribuição:

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Inicie o Visual Studio. Em Ferramentas -> Opções -> Plataforma Cruzada -> C++ -> Gerenciador de Conexões, adicione uma conexão à sua caixa do Linux:

![Opções de Ferramentas](./media/develop-cplusplus-simple/tools.png)

Depois de estabelecer a conexão via SSH, crie um modelo de projeto Vazio (Linux):

![Novo modelo do projeto](./media/develop-cplusplus-simple/template.png)

Você pode adicionar um [novo arquivo de origem C e substituí-lo por este conteúdo](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Usando as APIs ODBC SQLAllocHandle, SQLSetConnectAttr e SQLDriverConnect, você deverá poder inicializar e estabelecer uma conexão com o banco de dados.
Como no exemplo do Windows ODBC, você precisará substituir a chamada SQLDriverConnect pelos detalhes dos parâmetros de cadeia de conexão do banco de dados copiados anteriormente no Portal do Azure.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

A última coisa a fazer antes de compilar é adicionar **odbc** como uma dependência de biblioteca:

![Adicionando o ODBC como uma biblioteca de entrada](./media/develop-cplusplus-simple/lib.png)

Para iniciar o aplicativo, abra o Console do Linux no menu **Depurar**:

![Console do Linux](./media/develop-cplusplus-simple/linuxconsole.png)

Se a conexão for bem-sucedida, agora você verá o nome do banco de dados atual impresso no Console do Linux:

![Saída da Janela de Console do Linux](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Parabéns! Você concluiu com êxito o tutorial e agora pode se conectar ao banco de dados SQL do Azure do C++ em plataformas Windows e Linux.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Obter a solução completa do tutorial de C/C++

Você pode encontrar a solução de Introdução que contém todos os exemplos nesse artigo no GitHub:

* [Exemplo de ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), baixe o exemplo do Windows C++ ODBC para se conectar ao Azure SQL
* [Exemplo de ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), baixe o exemplo do Linux C++ ODBC para se conectar ao Azure SQL

## <a name="next-steps"></a>Próximas etapas

* Examine a [visão geral do desenvolvimento do banco de dados SQL](develop-overview.md)
* Mais informações na [Referência da API ODBC](/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Recursos adicionais

* [Padrões de design para aplicativos SaaS multilocatário com o banco de dados SQL do Azure](saas-tenancy-app-design-patterns.md)
* Explore todos os [recursos do banco de dados SQL](https://azure.microsoft.com/services/sql-database/)