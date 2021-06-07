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
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491955"
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
    

    |**Ação**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Instale o driver ODBC, o SQLCMD e o driver Python para o SQL Server|Use as etapas **1.2**, **1.3** e **2.1** em [Criar aplicativos Python usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Isso também instalará o Homebrew e o Python.       |[Configurar um ambiente para desenvolvimento de Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Configurar um ambiente para desenvolvimento de Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Instalar o Python e outros pacotes necessários|    |Use `sudo apt-get install python python-pip gcc g++ build-essential`.|    |
    |Informações adicionais|[Driver ODBC da Microsoft no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Driver ODBC da Microsoft no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Driver ODBC da Microsoft no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



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
