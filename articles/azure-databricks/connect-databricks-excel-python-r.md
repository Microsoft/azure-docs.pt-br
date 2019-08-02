---
title: 'Conecte-se ao Azure Databricks do Excel, Python ou R '
description: Saiba como usar o driver Simba para conectar o Azure Databricks para o Excel, Python ou R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: 1805f04d7833dea180847defadd865cb23e9df62
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340847"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Conecte-se ao Azure Databricks do Excel, Python ou R

Neste artigo, você aprenderá a usar o driver ODBC Databricks para conectar o Azure Databricks com linguagem R, Python ou Microsoft Excel. Uma vez estabelecida a conexão, você pode acessar os dados no Azure Databricks dos clientes de R, Python ou Excel. Você também pode usar os clientes para analisar mais profundamente os dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um workspace do Azure Databricks, um cluster Spark e dados de exemplo associados ao seu cluster. Se você ainda não tiver esses pré-requisitos, conclua o guia de início rápido em [Executar um trabalho do Spark no Azure Databricks usando o portal do Azure](quickstart-create-databricks-workspace-portal.md).

* Baixar o driver ODBC Databricks da [Página de download do driver de Databricks](https://databricks.com/spark/odbc-driver-download). Instale a versão de 32 bits ou 64 bits dependendo do aplicativo de onde você deseja se conectar com o Azure Databricks. Por exemplo, para se conectar do Excel, instale a versão de 32 bits do driver. Para se conectar do R e Python, instale a versão de 64 bits do driver.

* Configure um token de acesso pessoal no Databricks. Para obter instruções, consulte o [Gerenciamento de Token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Definir um DSN

Um nome de fonte de dados (DSN) contém as informações sobre uma fonte de dados específica. Um driver ODBC precisa desse DSN para se conectar a uma fonte de dados. Nesta seção, você configura um DSN que pode ser usado com o driver ODBC Databricks para se conectar ao Azure Databricks de clientes como o Microsoft Excel, Python ou R.

1. No workspace do Azure Databricks, navegue até o cluster do Databricks.

    ![Abrir o cluster do Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Abrir o cluster do Databricks")

2. Sob o guia **Configuração**, clique no guia **JDBC/ODBC** e copie os valores para o **Nome do host do servidor** e **Caminho HTTP**. Você precisa desses valores para concluir as etapas neste artigo.

    ![Obter a configuração do Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Obter a configuração do Databricks")

3. Em seu computador, inicie o aplicativo **Fontes de dados ODBC** (32 bits ou 64 bits), dependendo do aplicativo. Para se conectar do Excel, use a versão de 32 bits. Para se conectar do R e Python, use a versão de 64 bits.

    ![Inicialize o ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Inicialize o aplicativo ODBC")

4. Sob o guia **DSN do usuário**, clique em **Adicionar**. Na caixa de diálogo **Criar Nova Fonte de Dados**, selecione **Driver ODBC do Simba Spark** e clique em **Concluir**.

    ![Inicialize o ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Inicialize o aplicativo ODBC")

5. Na caixa de diálogo **Driver ODBC Simba Spark**, forneça os valores a seguir:

    ![Configurar o DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Configurar o DSN")

    A tabela a seguir fornece informações sobre os valores a fornecer na caixa de diálogo.
    
    |Campo  | Value  |
    |---------|---------|
    |**Nome da fonte de dados**     | Forneça um nome para a fonte de dados.        |
    |**Host(s)**     | Forneça o valor que você copiou do workspace do Databricks para *nome do host do servidor*.        |
    |**Porta**     | Inserir *443*.        |
    |**Mecanismo de** > **Autenticação**     | Selecione *Nome de usuário e senha*.        |
    |**Nome de usuário**     | Insira o *token*.        |
    |**Senha**     | Forneça o valor do token que você copiou do workspace do Databricks. |
    
    Execute as seguintes etapas adicionais na caixa de diálogo de configuração de DSN.
    
    * Clique em **Opções de HTTP**. Na caixa de diálogo que é aberta, cole o valor para o *Caminho de HTTP* que você copiou do workspace do Databricks. Clique em **OK**.
    * Clique nas **Opções do SSL**. Na caixa de diálogo que é aberta, selecione a caixa de seleção **Habilitar SSL**. Clique em **OK**.
    * Clique em **Testar** para testar a conexão no Databricks do Azure. Clique em **OK** para salvar a configuração.
    * Na caixa de diálogo **Administrador de Fonte de Dados de ODBC**, clique em **OK**.

Agora você tem o seu DSN configurado. Nas próximas seções, você deve usar esse DSN para se conectar ao Azure Databricks do Excel, Python ou R.

## <a name="connect-from-microsoft-excel"></a>Conectar-se do Microsoft Excel

Nesta seção, você extrairá dados do Azure Databricks para o Microsoft Excel usando o DSN que você criou anteriormente. Antes de começar, certifique-se de que o Microsoft Excel esteja instalado em seu computador. Você pode usar uma versão de avaliação do Excel a partir do [link de avaliação do Microsoft Excel](https://products.office.com/excel).

1. Abra uma pasta de trabalho em branco no Microsoft Excel. Da faixa de opções de **Dados**, clique em **Obter dados**. Clique em **De Outras Fontes** e em **Do ODBC**.

    ![Inicie o ODBC do Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Inicie o ODBC do Excel")

2. Na caixa de diálogo **Do ODBC**, selecione o DSN que você criou anteriormente e clique em **OK**.

    ![Selecione o DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Selecione o DSN")

3. Se as credenciais forem solicitadas a você, para o nome de usuário, digite **token**. Para a senha, forneça o valor do token que você recuperou do workspace de Databricks.

    ![Forneça as credenciais para o Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Selecione DSN")

4. Na janela do navegador, selecione a tabela no Databricks que você deseja carregar para o Excel e, em seguida, clique em **Carregar**. 

    ![Carregar dta no Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Carregar dta no Excel")

Uma vez que você tiver os dados na sua pasta de trabalho do Excel, você pode realizar operações analíticas nela.

## <a name="connect-from-r"></a>Conecte-se do R

> [!NOTE]
> Esta seção fornece informações sobre como integrar um cliente do R Studio em execução na área de trabalho com o Azure Databricks. Para obter instruções sobre como usar o Studio R no próprio cluster do Azure Databricks, consulte [R Studio no Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

Nesta seção, você deve usar uma linguagem R IDE para os dados de referência disponíveis no Azure Databricks. Antes de começar, você deve ter os itens a seguir instalados no seu computador.

* Um IDE para a linguagem R. Este artigo usa RStudio para área de trabalho. Você pode instalá-lo a partir do [download do Studio R](https://www.rstudio.com/products/rstudio/download/).
* Se você usa o RStudio para área de trabalho como seu IDE, instale também o Microsoft R Client [https://aka.ms/rclient/](https://aka.ms/rclient/). 

Abra o RStudio e siga as etapas a seguir:

- Referência ao pacote `RODBC`. Isso permite que você se conecte ao Azure Databricks usando o DSN que você criou anteriormente.
- Estabeleça uma conexão usando o DSN.
- Execute uma consulta SQL nos dados no Azure Databricks. No snippet de código a seguir, *radio_sample_data* é uma tabela que já existe no Azure Databricks.
- Execute algumas operações na consulta para verificar a saída. 

O snippet de código a seguir executa essas tarefas:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Conecte-se do Python

Nesta seção, você deve usar um IDE Python (como IDLE) para os dados de referência disponíveis no Azure Databricks. Antes de começar, você precisará atender aos seguintes pré-requisitos:

* Instalar o Python a partir [daqui](https://www.python.org/downloads/). Instalar o Python deste link também instala IDLE.

* A partir de um prompt de comando no computador, instale o pacote `pyodbc`. Execute o comando a seguir:

      pip install pyodbc

Abra o IDLE e siga as etapas a seguir:

- Importar o pacote `pyodbc`. Isso permite que você se conecte ao Azure Databricks usando o DSN que você criou anteriormente.
- Estabeleça uma conexão usando o DSN que você criou anteriormente.
-  Execute uma consulta SQL usando a conexão criada por você. No snippet de código a seguir, *radio_sample_data* é uma tabela que já existe no Azure Databricks.
- Execute algumas operações na consulta para verificar a saída.

O snippet de código a seguir executa essas tarefas:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as fontes de onde você pode importar dados para o Azure Databricks, consulte [Fontes de dados para o Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


