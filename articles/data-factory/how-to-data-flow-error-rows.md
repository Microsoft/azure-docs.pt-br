---
title: Manipular linhas de erro com fluxos de dados de mapeamento em Azure Data Factory
description: Saiba como lidar com erros de truncamento do SQL em Azure Data Factory usando fluxos de dados de mapeamento.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: a7a03ff1a58f50f16ebefce48b9e2772a16a011a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386332"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Manipular linhas de erro de truncamento do SQL no mapeamento de Data Factory fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um cenário comum no Data Factory ao usar o mapeamento de fluxos de dados é gravar seus dados transformados em um banco de dado no banco de dados SQL do Azure. Nesse cenário, uma condição de erro comum que deve ser evitada é o truncamento de coluna possível.

Há dois métodos principais para lidar com erros de identificadores normais ao gravar dados no coletor de banco de dados no ADF:

* Defina a [manipulação de linha de erro](./connector-azure-sql-database.md#error-row-handling) do coletor como "continuar se houver erro" ao processar dados do banco de dado. Esse é um método de interceptação automatizada que não requer lógica personalizada em seu fluxo de dados.
* Como alternativa, siga as etapas abaixo para fornecer o log de colunas que não se ajustarão a uma coluna de cadeia de caracteres de destino, permitindo que o fluxo de dados continue.

> [!NOTE]
> Ao habilitar a manipulação de linha de erro automática, em oposição ao método abaixo de escrever sua própria lógica de tratamento de erro, haverá uma pequena penalidade de desempenho incorrida pelo e etapa adicional tomada pelo ADF para executar uma operação de duas fases para interceptar erros.

## <a name="scenario"></a>Cenário

1. Temos uma tabela de banco de dados de destino que tem uma ```nvarchar(5)``` coluna chamada "Name".

2. Dentro de nosso fluxo de dados, queremos mapear títulos de filmes de nosso coletor para a coluna "nome" de destino.

    ![Fluxo de dados de filme 1](media/data-flow/error4.png)
    
3. O problema é que o título do filme não se encaixa em uma coluna do coletor que pode conter apenas 5 caracteres. Ao executar esse fluxo de dados, você receberá um erro como este: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Este vídeo percorre um exemplo de como configurar a lógica de tratamento de linha de erro em seu fluxo de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Como criar com base nessa condição

1. Nesse cenário, o comprimento máximo da coluna "nome" é de cinco caracteres. Portanto, vamos adicionar uma transformação de divisão condicional que nos permitirá registrar linhas com "títulos" com mais de cinco caracteres e, ao mesmo tempo, permitir o restante das linhas que podem caber nesse espaço para gravar no banco de dados.

    ![divisão condicional](media/data-flow/error1.png)

2. Essa transformação de divisão condicional define o comprimento máximo de "título" como cinco. Qualquer linha menor ou igual a cinco será inserida no ```GoodRows``` fluxo. Qualquer linha que tenha mais de cinco será inserida no ```BadRows``` fluxo.

3. Agora, precisamos registrar as linhas que falharam. Adicione uma transformação de coletor ao ```BadRows``` fluxo para registro em log. Aqui, vamos "mapear automaticamente" todos os campos para que tenhamos registro em log do registro de transação completo. Essa é uma saída de arquivo CSV delimitada por texto para um único arquivo no armazenamento de BLOBs. Vamos chamar o arquivo de log "badrows.csv".

    ![Linhas inadequadas](media/data-flow/error3.png)
    
4. O fluxo de dados concluído é mostrado abaixo. Agora, podemos dividir as linhas de erro para evitar os erros de truncamento do SQL e colocar essas entradas em um arquivo de log. Enquanto isso, as linhas bem-sucedidas podem continuar a gravar em nosso banco de dados de destino.

    ![fluxo de dados completo](media/data-flow/error2.png)

5. Se você escolher a opção de tratamento de linhas de erro na transformação do coletor e definir "linhas de erro de saída", o ADF gerará automaticamente uma saída de arquivo CSV de seus dados de linha junto com as mensagens de erro relatadas pelo driver. Você não precisa adicionar essa lógica manualmente ao fluxo de dados com essa opção alternativa. Haverá uma pequena penalidade de desempenho incorrida com essa opção para que o ADF possa implementar uma metodologia de duas fases para interceptar erros e fazer o log delas.

    ![fluxo de dados completo com linhas de erro](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>Próximas etapas

* Compile o restante da lógica de fluxo de dados usando as [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.