---
title: Analisar logs de sites usando o Azure Data Lake Analytics | Microsoft Docs
description: "Saiba como analisar os logs de site usando a Análise Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 52d19297ae5c34f9daf5e42250a53a78e0168192
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analisar logs de site usando o Azure Data Lake Analytics
Saiba como analisar os logs do site usando a Análise Data Lake, principalmente para descobrir quais referenciadores resultaram em erros ao tentar visitar o site.

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2015 ou Visual Studio 2013**.
* **[Ferramentas do Data Lake para Visual Studio](http://aka.ms/adltoolsvs)**.

    Quando as Ferramentas do Data Lake para Visual Studio estiverem instaladas, você verá um item **Data Lake** no menu **Ferramentas** do Visual Studio:

    ![Menu U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Conhecimento básico sobre o Data Lake Analytics e sobre as Ferramentas do Data Lake para o Visual Studio**. Para começar. confira:

  * [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Uma conta da Análise Data Lake.**  Confira [Create an Azure Data Lake Analytics account](data-lake-analytics-get-started-portal.md) (Criar uma conta do Azure Data Lake Analytics).
* **Instale os dados de exemplo.** No Portal do Azure, abra sua conta do Data Lake Analytics e clique em **Scripts de Exemplo** no menu à esquerda e, em seguida, clique em **Copiar Dados de Exemplo**. 

## <a name="connect-to-azure"></a>Conecte-se ao Azure
Antes de criar e testar qualquer script U-SQL, é preciso se conectar ao Azure.

**Para conectar-se à Análise Data Lake**

1. Abra o Visual Studio.
2. Clique em **Data Lake > Opções e configurações**.
3. Clique em **Entrar** ou **Alterar Usuário**, se alguém estiver conectado, e siga as instruções.
4. Clique em **OK** para fechar a caixa de diálogo Opções e Configurações.

**Para procurar suas contas da Análise Data Lake**

1. No Visual Studio, abra o **Gerenciador de Servidores** pressionando **CTRL+ALT+S**.
2. No **Gerenciador de Servidores**, expanda **Azure** e **Data Lake Analytics**. Você deverá ver uma lista das suas contas da Análise Data Lake, caso haja alguma. Não é possível criar contas da Análise Data Lake a partir do estúdio. Para criar uma conta, confira [Introdução ao Azure Data Lake Analytics usando o Portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução ao Azure Data Lake Analytics usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Desenvolver aplicativos U-SQL
Um aplicativo U-SQL é basicamente um script U-SQL. Para saber mais sobre o U-SQL, consulte [Introdução ao U-SQL](data-lake-analytics-u-sql-get-started.md).

Você pode adicionar operadores definidos pelo usuário a este aplicativo.  Para obter mais informações, veja [Desenvolver operadores U-SQL definidos pelo usuário para trabalhos da Análise do Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Para criar e enviar um trabalho da Análise Data Lake**

1. Clique em **Arquivo > Novo > Projeto**.
2. Selecione o tipo Projeto do U-SQL.

    ![novo projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Clique em **OK**. O Visual Studio cria uma solução com um arquivo Script.usql.
4. Insira o seguinte script no arquivo Script.usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Para entender o U-SQL, consulte [Introdução à linguagem da Análise do Date Lake U-SQL](data-lake-analytics-u-sql-get-started.md).    
5. Adicione um novo script U-SQL ao seu projeto e digite o seguinte:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Volte para o primeiro script U-SQL e, ao lado do botão **Enviar** , especifique a conta de análise.
7. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e, então, clique em **Compilar Script**. Verifique os resultados no painel Saída.
8. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e, então, clique em **Enviar Script**.
9. Verifique se a **Conta do Analytics** é uma conta na qual você pode executar o trabalho e clique em **Enviar**. Os resultados do envio e o link do trabalho ficarão disponíveis na janela de resultados das Ferramentas do Date Lake para Visual Studio quando o envio for concluído.
10. Aguarde até o trabalho ser concluído com sucesso.  Se houver falha no trabalho, é provável que esteja faltando o arquivo original.  Confira a seção Pré-requisitos deste tutorial. Para obter mais informações sobre solução de problemas, veja [Monitorar e solucionar problemas com trabalhos da Análise do Azure Data Lake](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Quando o trabalho for concluído, você deverá ver a seguinte tela:

    ![a Análise Data Lake analisa logs de site de blogs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Agora, repita as etapas 7 a 10 para **Script1.usql**.

**Para ver a saída do trabalho**

1. No **Gerenciador de Servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda sua conta do Data Lake Analytics, expanda **Contas de Armazenamento**, clique com o botão direito do mouse na conta de Armazenamento padrão do Data Lake e clique em **Gerenciador**.
2. Clique duas vezes em **Exemplos** para abrir a pasta e, então, clique duas vezes em **Saídas**.
3. Clique duas vezes em **UnsuccessfulResponsees.log**.
4. Também é possível clicar duas vezes no arquivo de saída no modo de exibição de gráficos do trabalho a fim de navegar diretamente até a saída.

## <a name="see-also"></a>Confira também
Para começar a usar a Análise Data Lake com ferramentas diferentes, consulte:

* [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução à Análise Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução à Análise Data Lake usando o SDK do .NET](data-lake-analytics-get-started-net-sdk.md)
