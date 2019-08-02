---
title: Consultar Apache Hive com driver ODBC e PowerShell – Azure HDInsight
description: Use o driver ODBC do Microsoft Hive e o PowerShell para consultar clusters do Apache Hive no Azure HDInsight.
keywords: hive,odbc do hive,powershell
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: hrasheed
ms.openlocfilehash: b02c865e953861b5ac396538fdd0f0623b0e5428
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486059"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Tutorial: Consultar o Apache Hive com ODBC e PowerShell

Os drivers ODBC da Microsoft fornecem uma maneira flexível de interagir com tipos diferentes de fonte de dados, inclusive o Apache Hive. Você pode escrever código em linguagens de script, como o PowerShell, que usam os drivers ODBC para abrir uma conexão com o seu cluster do Hive, passar uma consulta de sua escolha e exibir os resultados.

Neste tutorial, você executará as seguintes tarefas:

> [!div class="checklist"]
> * Baixar e instalar o driver ODBC do Microsoft Hive
> * Criar uma fonte de dados ODBC do Apache Hive vinculada ao seu cluster
> * Consultar informações de exemplo no seu cluster usando o PowerShell

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter os seguintes itens:

* Um cluster de Consulta Interativa no HDInsight. Para criar um, confira [Introdução ao Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Selecione **Consulta Interativa** como o tipo de cluster.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive

Baixe e instale o [Driver ODBC do Microsoft Hive](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados ODBC do Apache Hive

As etapas a seguir mostram como criar uma fonte de dados ODBC do Apache Hive.

1. No Windows, navegue até **Iniciar** > **Ferramentas Administrativas do Windows** > **Fontes de Dados ODBC (32 bits)/(64 bits)** .  Uma janela **Administrador de Fonte de Dados ODBC** é aberta.

    ![Administrador de fonte de dados ODBC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Configurar um DSN usando o Administrador de Fonte de Dados ODBC")

1. Na guia **DSN de Usuário**, selecione **Adicionar** para abrir a janela **Criar Nova Fonte de Dados**.

1. Selecione **Driver ODBC do Microsoft Hive** e selecione **Concluir** para abrir a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.

1. Digite ou selecione os valores a seguir:

   | Propriedade | DESCRIÇÃO |
   | --- | --- |
   |  Nome da fonte de dados |Forneça um nome para a sua fonte de dados |
   |  Hosts |Digite `CLUSTERNAME.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Porta |Use **443**.|
   |  Banco de dados |Use **padrão**. |
   |  Mecanismo |Selecione **Serviço do Microsoft Azure HDInsight** |
   |  Nome do Usuário |Insira o nome de usuário HTTP do cluster HDInsight. O nome de usuário padrão é **admin**. |
   |  Senha |Insira a senha do usuário do cluster HDInsight. Marque a caixa de seleção **Salvar Senha (Criptografada)** .|

1. Opcional: Selecione **Opções Avançadas**.  

   | Parâmetro | DESCRIÇÃO |
   | --- | --- |
   |  Use Consulta Nativa |Quando selecionado, o driver ODBC NÃO tenta converter TSQL em HiveQL. Use essa opção somente se você tiver plena certeza de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
   |  Linhas buscadas por bloco |Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, Comprimento da coluna binária e Escala da coluna decimal |Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles farão com que informações incorretas sejam retornadas devido à perda de precisão e truncamento. |

    ![Opções avançadas](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Opções de configuração de DSN avançadas")

1. Selecione **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, o resultado do teste mostrará **SUCESSO**.  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.  

1. Selecione **OK** para fechar a janela **Administrador de Fonte de Dados ODBC**.  

## <a name="query-data-with-powershell"></a>Consultar dados com o PowerShell

O script do PowerShell a seguir é uma função usada pelo ODBC para consultar um cluster Hive.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

O trecho de código a seguir usa a função acima para executar uma consulta no cluster Consulta Interativa que você criou no início do tutorial. Substitua `DATASOURCENAME` pelo **Nome da Fonte de Dados** que você especificou na tela **Configuração de DSN do Driver ODBC do Microsoft Hive**. Quando solicitado a fornecer credenciais, digite o nome de usuário e a senha que você inseriu em **Nome de usuário de logon do cluster** e **Senha de logon do cluster** quando criou o cluster.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o cluster HDInsight e a conta de armazenamento. Para fazer isso, selecione o grupo de recursos em que o cluster foi criado e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o driver ODBC do Microsoft Hive e o PowerShell para recuperar dados do seu cluster Consulta Interativa do Azure HDInsight.

> [!div class="nextstepaction"]
> [Conectar o Excel ao Apache Hive usando ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
