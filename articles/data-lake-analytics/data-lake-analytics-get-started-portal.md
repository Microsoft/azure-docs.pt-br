---
title: Introdução ao Azure Data Lake Analytics usando o Portal do Azure
description: Use o portal do Azure para criar uma conta do Azure Data Lake Analytics e enviar um trabalho do U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 25d58bdc5791de868c6302b4d2763fa34e98af17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615034"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Introdução ao Azure Data Lake Analytics usando o Portal do Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Este artigo descreve como usar o portal do Azure para criar contas do Azure Data Lake Analytics, definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos ao serviço do Data Lake Analytics.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter uma **assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta da Análise Data Lake

Agora, você criará uma conta do Data Lake Analytics e do Azure Data Lake Storage Gen1 ao mesmo tempo.  Esta etapa é simples e demora apenas 60 segundos para ser concluída.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso** >  **Dados + Análise** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens:
   * **Nome**: Nome de sua conta do Data Lake Analytics (somente letras minúsculas e números são permitidos).
   * **Assinatura**: Escolha a assinatura do Azure usada para a conta da análise.
   * **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo.
   * **Local**. Selecione um datacenter do Azure para a conta da Análise Data Lake.
   * **Data Lake Storage Gen1**: Siga as instruções para criar uma nova conta do Data Lake armazenamento Gen1, ou selecione um existente. 
4. Opcionalmente, selecione um tipo de preço para sua conta Data Lake Analytics.
5. Clique em **Criar**. 


## <a name="your-first-u-sql-script"></a>Seu primeiro script U-SQL

O texto a seguir é um script U-SQL muito simples. Ele define um pequeno conjunto de dados dentro do script e, em seguida, grava esse conjunto de dados na conta do Data Lake Storage Gen1 padrão como um arquivo chamado `/data.csv`.

```
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

## <a name="submit-a-u-sql-job"></a>Enviar um trabalho do U-SQL

1. Na conta do Data Lake Analytics, selecione **Novo Trabalho**.
2. Cole o texto do script U-SQL anterior. Dê um nome para o trabalho. 
3. Selecione o botão **Enviar** para iniciar o trabalho.   
4. Monitore o **Status** do trabalho e aguarde até que o status do trabalho mude para **Bem-sucedido**.
5. Selecione a guia **Dados** e, em seguida, selecione a guia **Saídas**. Selecione o arquivo de saída denominado `data.csv` e exiba os dados de saída.

## <a name="see-also"></a>Consulte também

* Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).
