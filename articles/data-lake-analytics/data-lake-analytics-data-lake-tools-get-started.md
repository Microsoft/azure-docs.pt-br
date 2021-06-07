---
title: Consulta Azure Data Lake Analytics-Visual Studio
description: Saiba como instalar ferramentas do Data Lake para o Visual Studio e como desenvolver e testar scripts U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d3812cb27d2d9ea7a49038f566f8b4cc5d779172
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95241685"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Desenvolvimento de scripts U-SQL usando as ferramentas do Data Lake para Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

O Azure Data Lake Analytics e as Ferramentas do Stream Analytics incluem a funcionalidade relacionada a dois serviços do Azure, Azure Data Lake Analytics e Azure Stream Analytics. Para obter mais informações sobre os cenários de Azure Stream Analytics, consulte [Azure Stream Analytics Tools for Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Este artigo descreve como usar o Visual Studio para criar contas de Azure Data Lake Analytics. Você pode definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos para o serviço de data Lake Analytics. Para obter mais informações sobre Data Lake Analytics, consulte [Azure data Lake Analytics visão geral](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Recomendamos que você atualize para Ferramentas do Azure Data Lake para Visual Studio versão 2.3.3000.4 ou posterior. As versões anteriores não estão disponíveis para download e foram preteridas.
>
> 1. Verifique se você estiver usando uma versão anterior ao 2.3.3000.4 do Azure Data Lake Tools para Visual Studio.
>
>    ![Verifique a versão da ferramenta](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Se sua versão for uma versão anterior do 2.3.3000.4, atualize o Azure Data Lake Tools para Visual Studio visitando o Centro de download:
>    - [Para o Visual Studio 2017 e 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Para Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: há suporte para todas as edições, exceto Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **SDK do Microsoft Azure para .NET** versão 2.7.1 ou posterior. Instale-o usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta do **Data Lake Analytics**. Para criar uma conta, confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio

Este tutorial requer que Ferramentas do Data Lake Tools para Visual Studio esteja instalado. Para obter mais informações, consulte [instalar o data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Conecte-se com uma conta do Azure Data Lake Analytics

1. Abra o Visual Studio.

1. Abra o **Data Lake Analytics Explorer** selecionando **Exibir**  >  **Data Lake Analytics Explorer**.

1. Clique com o botão direito do mouse em **Azure** e selecione **conectar-se a Microsoft Azure assinatura**. Em **entrar em sua conta**, siga as instruções.

1. Em **Gerenciador de servidores**, selecione **Data Lake Analytics do Azure**  >  . Você verá uma lista das suas contas do Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Escreve seu primeiro script U-SQL

O texto a seguir é um script U-SQL simples. Ele define um pequeno conjunto de dados e grava o conjunto de dados no repositório padrão do Data Lake Store como um arquivo chamado `/data.csv`.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Enviar um trabalho da Análise Data Lake

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

1. Selecione o tipo de **projeto U-SQL** e, em seguida, selecione **Avançar**. Em **configurar seu novo projeto**, selecione **criar**.

   O Visual Studio cria uma solução que contém um arquivo **script. usql** .

1. Cole o script de [escrever seu primeiro script U-SQL](#write-your-first-u-sql-script) na janela **script. usql** .

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em **script. Usql** e selecione **Enviar script**.

1. Em **Enviar trabalho**, escolha sua conta de data Lake Analytics e selecione **Enviar**.

   ![Enviar projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Após o envio do trabalho, a guia **Exibição do trabalho** é aberta para mostrar o andamento do trabalho.

* **Resumo do trabalho** mostra o resumo do trabalho.
* **Grafo do Trabalho** visualiza o andamento do trabalho.
* **Operações de metadados** mostra todas as ações que foram executadas no catálogo de U-SQL.
* **Dados** mostra todas as entradas e saídas.
* **Histórico de estado** mostra os detalhes da linha do tempo e do estado.
* A **análise de au** mostra quantas AUS foram usadas no trabalho e exploram simulações de diferentes estratégias de alocação de au.
* **Diagnósticos** fornece uma análise avançada para otimização de desempenho e a execução do trabalho.

![Grafo de desempenho de trabalho do Data Lake Analytics do U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Para ver o status do trabalho mais recente e atualizar a tela, selecione **Atualizar**.

## <a name="check-job-status"></a>Verificar o status do trabalho

1. Em **Gerenciador de servidores**, selecione **Data Lake Analytics do Azure**  >  .

1. Expanda o nome da conta do Data Lake Analytics.

1. Clique duas vezes em **Trabalhos**.

1. Selecione o trabalho enviado anteriormente.

## <a name="see-the-job-output"></a>Veja a saída do trabalho

1. Em **Gerenciador de servidores**, navegue até o trabalho que você enviou.

1. Clique na guia **Dados**.

1. Na guia **Saídas do trabalho**, selecione o arquivo `"/data.csv"`.

## <a name="next-steps"></a>Próximas etapas

* [Execute scripts U-SQL em sua estação de trabalho para teste e depuração](data-lake-analytics-data-lake-tools-local-run.md)
* [Depurar código C# em trabalhos U-SQL usando as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
