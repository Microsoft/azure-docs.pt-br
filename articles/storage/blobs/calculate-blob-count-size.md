---
title: Calcular a contagem e o tamanho do blob usando o inventário de Armazenamento do Azure
description: Saiba como calcular a contagem e o tamanho total de blobs por contêiner.
services: storage
author: twooley
ms.author: twooley
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: e752d40ce2f237c2ab08bac2e71133cd06ec40e4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277174"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Calcule a contagem de blobs e o tamanho total por contêiner usando o inventário de Armazenamento do Azure

Este artigo usa o recurso de inventário de Armazenamento de Blobs do Azure e o Azure Synapse para calcular a contagem e o tamanho total dos blobs por contêiner. Esses valores são úteis ao otimizar o uso de blobs por contêiner.

Os metadados de blobs não estão incluídos nesse método. O recurso de inventário de Armazenamento de Blobs do Azure usa a API REST [Listar Blobs](/rest/api/storageservices/list-blobs) com os parâmetros padrão. Portanto, o exemplo não dá suporte a instantâneos, contêineres '$', entre outros.

> [!IMPORTANT]
> No momento, o inventário de blobs está em **VERSÃO PRÉVIA**. Veja os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter termos legais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

## <a name="enable-inventory-reports"></a>Habilitar relatórios de inventário

A primeira etapa neste método é [habilitar relatórios de inventário](blob-inventory.md#enable-inventory-reports) na sua conta de armazenamento. Talvez seja necessário aguardar até 24 horas depois de habilitar os relatórios de inventário para que o seu primeiro relatório seja gerado.

Quando você tem um relatório de inventário para analisar, conceda a si mesmo acesso de leitura de blob ao contêiner no qual o arquivo CSV de relatório reside.

1. Navegue até o contêiner com o arquivo de relatório CSV de inventário.
1. Selecione **Controle de Acesso (IAM)** e **Adicionar atribuições de função**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Selecione Adicionar atribuições de função":::

1. Selecione **Leitor de Dados do Blob de Armazenamento** na lista suspensa **Função**.

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Adicionar a função de Leitor de Dados do Blob de Armazenamento da lista suspensa":::

1. Insira o endereço de email da conta que você está usando para executar o relatório no campo **Selecionar**.

## <a name="create-an-azure-synapse-workspace"></a>Como criar um workspace do Azure Synapse

Em seguida, [crie um workspace do Azure Synapse](../../synapse-analytics/get-started-create-workspace.md), em que você executará uma consulta SQL para relatar os resultados do inventário.

## <a name="create-the-sql-query"></a>Criar a consulta SQL

Depois de criar o workspace do Azure Synapse, execute as etapas a seguir.

1. Navegue até [https://web.azuresynapse.net](https://web.azuresynapse.net).
1. Selecione a guia **Desenvolver** na borda esquerda.
1. Selecione o sinal de adição grande (+) para adicionar um item.
1. Selecione **Script SQL**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Selecione o script SQL para criar uma consulta":::

## <a name="run-the-sql-query"></a>Executar a consulta SQL

1. Adicione a consulta SQL a seguir no seu workspace do Azure Synapse para [ler o arquivo CSV de inventário](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    No parâmetro `bulk`, use a URL do arquivo CSV do relatório de inventário que você deseja analisar.

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

1. Nomeie a sua consulta SQL no painel de propriedades à direita.

1. Publique a sua consulta SQL pressionando CTRL+S ou selecionando o botão **Publicar tudo**.

1. Selecione o botão **Executar** para executar a consulta SQL. A contagem de blobs e o tamanho total por contêiner são relatados no painel **Resultados**.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Saída da execução do script para calcular a contagem de blobs e o tamanho total.":::

## <a name="next-steps"></a>Próximas etapas

- [Usar o inventário de blobs do Armazenamento do Azure para gerenciar dados de blob](blob-inventory.md)
- [Calcular o tamanho total de cobrança de um contêiner de blob](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)