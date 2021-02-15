---
title: Usar o Python para consultar um banco de dados
description: Este tópico mostra como usar o Python para criar um programa que se conecta a um banco de dados no Banco de Dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: 8fb6d319cacf85630b2c400cd18d14487725f925
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703953"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Início Rápido: Usar o Python para consultar um banco de dados
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Neste início rápido, você usará o Python para se conectar ao Banco de Dados SQL do Azure, à Instância Gerenciada de SQL do Azure ou ao banco de dados SQL do Synapse e usará instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Um banco de dados no qual você executará uma consulta.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 e software relacionado

  # <a name="macos"></a>[macOS](#tab/macos)

  Para instalar o Homebrew e o Python, o driver ODBC e o SQLCMD e o driver do Python para SQL Server, use as etapas **1.2**, **1.3** e **2.1** em [criar aplicativos Python usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Para obter mais informações, confira [Driver ODBC da Microsoft no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Para instalar o Python e outros pacotes necessários, use `sudo apt-get install python python-pip gcc g++ build-essential`.

  Para instalar o driver ODBC, o SQLCMD e o driver do Python para SQL Server, confira [configurar um ambiente para desenvolvimento em Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Para obter mais informações, confira [Driver ODBC da Microsoft no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Para instalar o Python, o driver ODBC, o SQLCMD e o driver do Python para SQL Server, confira [configurar um ambiente para desenvolvimento em Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Para obter mais informações, confira [Driver ODBC da Microsoft](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---
Para explorar ainda mais o Python e o banco de dados no Banco de Dados SQL do Azure, confira [Bibliotecas do Banco de Dados SQL do Azure para Python](/python/api/overview/azure/sql), o [repositório pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e um [exemplo de pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Criar um código para consultar o banco de dados 

1. Em um editor de texto, crie um novo arquivo chamado *sqltest.py*.  
   
1. Adicione o código seguinte: Obtenha as informações de conexão da seção de pré-requisitos e substitua seus valores por \<server>, \<database>, \<username> e \<password>.
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o comando a seguir:

   ```cmd
   python sqltest.py
   ```

1. Verifique se os bancos de dados e seus agrupamentos são retornados e feche a janela Comando.

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure](design-first-database-tutorial.md)
- [Drivers Python da Microsoft para SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Central de desenvolvedores do Python](https://azure.microsoft.com/develop/python/?v=17.23h)
