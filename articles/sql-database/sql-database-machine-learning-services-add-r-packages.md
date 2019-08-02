---
title: Adicionar um pacote R aos Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo explica como instalar um pacote R que ainda não foi instalado nos Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: f82408a6aaa7cf3a492f3036a6db5d8666b6f160
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598043"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Adicionar um pacote R aos Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Este artigo explica como adicionar um pacote R aos Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar o [R](https://www.r-project.org) e o [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) em seu computador local. R está disponível para Windows, macOS e Linux. Este artigo pressupõe que você está usando o Windows.

- Este artigo inclui um exemplo de como usar o [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou o [SSMS](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio) para executar um script R no Banco de Dados SQL do Azure. É possível executar scripts R usando outro gerenciamento de banco de dados ou ferramentas de consulta, mas este exemplo pressupõe o Azure Data Studio ou o SSMS.
   
> [!NOTE]
> Não é possível instalar um pacote executando um script R usando **sp_execute_external_script** no Azure Data Studio ou no SSMS. Só é possível instalar e remover pacotes usando a linha de comando do R e o RStudio como descrito neste artigo. Após a instalação do pacote, é possível acessar as funções de pacote em um script R usando **sp_execute_external_script**.

## <a name="list-r-packages"></a>Listar pacotes de R

A Microsoft fornece inúmeros pacotes R pré-instalados com os Serviços do Machine Learning em seu banco de dados SQL do Azure.
É possível ver uma lista de pacotes R instalados executando o seguinte comando no Azure Data Studio ou no SSMS.

1. Abra o Azure Data Studio ou o SSMS e conecte-se ao Banco de Dados SQL do Azure.

1. Execute o comando a seguir:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

A saída deve ser semelhante ao seguinte.

**Resultados**

![Pacotes instalados em R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Adicionar um pacote com sqlmlutils

Se for necessário usar um pacote que ainda não foi instalado em seu Banco de Dados SQL do Azure, será possível instalá-lo usando [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** é um pacote criado para ajudar os usuários a interagir com bancos de dados SQL (SQL Server e Banco de Dados SQL do Azure) e a executar código R ou Python no SQL de um cliente do R ou do Python. No momento, apenas a versão de R de **sqlmlutils** no Banco de Dados SQL do Azure é compatível.

No exemplo a seguir, você instalará o pacote **[glue](https://cran.r-project.org/web/packages/glue/)** que pode formatar e interpolar cadeias de caracteres. Essas etapas instalam **sqlmlutils** e **RODBCext** (um pré-requisito de **sqlmlutils**) e adicionam um pacote **glue**.

### <a name="install-sqlmlutils"></a>Instalar **sqlmlutils**

1. Baixe o arquivo zip **sqlmlutils** mais recente de https://github.com/Microsoft/sqlmlutils/tree/master/R/dist para seu computador local. Não é necessário descompactar o arquivo.

1. Abra um **Prompt de Comando** e execute os comandos a seguir para instalar **RODBCext** e **sqlmlutils** em seu computador local. Substitua o caminho completo para o arquivo zip **sqlmlutils** baixado (o exemplo pressupõe que o arquivo está na pasta Documentos).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    A saída que você vir deverá ser semelhante ao seguinte.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Caso receba o erro "'R' não será reconhecido como um comando interno ou externo, programa operável ou arquivo em lotes", provavelmente significa que o caminho até R.exe não está incluído em sua variável de ambiente **PATH** no Windows. É possível adicionar o caminho até a variável de ambiente ou navegue até a pasta no prompt de comando (por exemplo, `cd C:\Program Files\R\R-3.5.3\bin`) e, em seguida, repita o comando.

### <a name="add-the-package"></a>Adicionar o pacote

1. Abra RStudio e crie um arquivo **R Script**. 

1. Use o código R a seguir para instalar o pacote **glue** usando **sqlmlutils**. Substitua suas próprias informações de conexão do Banco de Dados SQL do Azure.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > O **escopo** pode ser **PÚBLICO** ou **PRIVADO**. O escopo público é útil para o administrador de banco de dados poder instalar pacotes que todos os usuários podem usar. O escopo privado disponibiliza o pacote apenas para o usuário que o instala. Se você não especificar o escopo, o escopo padrão será **PRIVADO**.

### <a name="verify-the-package"></a>Verificar o pacote

Verifique se o pacote **glue** foi instalado executando o script R a seguir no RStudio. Use a mesma **conexão** definida na etapa anterior.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Resultados**

![Conteúdo da tabela RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Usar o pacote

Após a instalação do pacote, será possível usá-lo em um script R por meio de **sp_execute_external_script**.

1. Abra o Azure Data Studio ou o SSMS e conecte-se ao Banco de Dados SQL do Azure.

1. Execute o comando a seguir:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    Você verá o resultado a seguir na guia **Mensagens**.

    **Resultados**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Remover o pacote

Se desejar remover o pacote, execute o seguinte script R no RStudio. Use a mesma **conexão** que você definiu anteriormente.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Outra maneira de instalar um pacote R em seu banco de dados SQL do Azure é carregá-lo de um fluxo de bytes usando a instrução T-SQL **CREATE EXTERNAL LIBRARY**. Confira [Criar uma biblioteca de um fluxo de bytes](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) na documentação de referência [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Serviços de Machine Learning do Banco de Dados SQL do Azure com o R (versão prévia), confira os seguintes artigos.

- [Serviços de Machine Learning do Banco de Dados SQL do Azure com o R (versão prévia)](sql-database-machine-learning-services-overview.md)
- [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
- [Trabalhar usando dados do R e do SQL nos Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)