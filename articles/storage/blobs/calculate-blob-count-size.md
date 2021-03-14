---
title: Calcular contagem e tamanho de BLOB usando o inventário de armazenamento do Azure
description: Saiba como calcular a contagem e o tamanho total de BLOBs por contêiner.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 92e5b00cd655677cdc3096bc2142dfe1b704adf2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637643"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Calcular a contagem de BLOBs e o tamanho total por contêiner usando o inventário de armazenamento do Azure

Este artigo usa o recurso de inventário de armazenamento de BLOBs do Azure e o Azure Synapse para calcular a contagem de BLOBs e o tamanho total de BLOBs por contêiner. Esses valores são úteis ao otimizar o uso de blob por contêiner.

Os metadados de BLOB não estão incluídos neste método. O recurso de inventário de armazenamento de BLOBs do Azure usa a API REST de [listar BLOBs](/rest/api/storageservices/list-blobs) com parâmetros padrão. Portanto, o exemplo não dá suporte a instantâneos, contêineres ' $ ' e assim por diante.

> [!IMPORTANT]
> O inventário de blob está em **Visualização** no momento. Consulte os [termos de uso complementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

## <a name="enable-inventory-reports"></a>Habilitar relatórios de inventário

A primeira etapa neste método é [habilitar relatórios de inventário](blob-inventory.md#enable-inventory-reports) em sua conta de armazenamento. Talvez seja necessário aguardar até 24 horas depois de habilitar os relatórios de inventário para que seu primeiro relatório seja gerado.

Quando você tem um relatório de inventário para analisar, conceda a si mesmo acesso de leitura de blob ao contêiner em que reside o arquivo CSV de relatório.

1. Navegue até o contêiner com o arquivo de relatório CSV de inventário.
1. Selecione o **controle de acesso (iam)** e, em seguida, **adicione atribuições de função**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Selecione Adicionar atribuições de função":::

1. Selecione **leitor de dados de blob de armazenamento** na lista suspensa **função** .

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Adicionar a função de leitor de dados de blob de armazenamento da lista suspensa":::

1. Insira o endereço de email da conta que você está usando para executar o relatório no campo **selecionar** .

## <a name="create-an-azure-synapse-workspace"></a>Como criar um workspace do Azure Synapse

Em seguida, [crie um espaço de trabalho do Azure Synapse](/azure/synapse-analytics/get-started-create-workspace) , em que você executará uma consulta SQL para relatar os resultados do inventário.

## <a name="create-the-sql-query"></a>Criar a consulta SQL

Depois de criar o espaço de trabalho Synapse do Azure, execute as etapas a seguir.

1. Navegue até [https://web.azuresynapse.net](https://web.azuresynapse.net).
1. Selecione a guia **desenvolver** na borda esquerda.
1. Selecione o sinal de adição grande (+) para adicionar um item.
1. Selecione **script SQL**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Selecione o script SQL para criar uma nova consulta":::

## <a name="run-the-sql-query"></a>Executar a consulta SQL

1. Adicione a seguinte consulta SQL em seu espaço de trabalho Synapse do Azure para [ler o arquivo CSV de inventário](/azure/synapse-analytics/sql/query-single-csv-file#read-a-csv-file).

    Para o `bulk` parâmetro, use a URL do arquivo CSV do relatório de inventário que você deseja analisar.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Nomeie sua consulta SQL no painel Propriedades à direita.

1. Publique sua consulta SQL pressionando CTRL + S ou selecionando o botão **publicar tudo** .

1. Selecione o botão **executar** para executar a consulta SQL. A contagem de BLOBs e o tamanho total por contêiner são relatados no painel de **resultados** .

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Saída da execução do script para calcular a contagem de BLOBs e o tamanho total.":::

## <a name="next-steps"></a>Próximas etapas

- [Usar o inventário de blob do armazenamento do Azure para gerenciar dados de BLOB](blob-inventory.md)
- [Calcular o tamanho total de cobrança de um contêiner de blob](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
