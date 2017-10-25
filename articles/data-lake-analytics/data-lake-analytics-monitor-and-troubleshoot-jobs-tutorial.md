---
title: Solucionar problemas em trabalhos do Azure Data Lake Analytics usando o Portal do Azure | Microsoft Docs
description: "Saiba como usar o Portal do Azure para solucionar problemas com trabalhos da Análise do Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: b9c7453cc0a94f70d0098ed83e5f127832065a62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure
Saiba como usar o Portal do Azure para solucionar problemas com trabalhos da Análise do Data Lake.

Neste tutorial, você criará um problema de arquivo de origem ausente e usará o Portal do Azure para solucionar o problema.

## <a name="submit-a-data-lake-analytics-job"></a>Enviar um trabalho da Análise Data Lake

Envie o seguinte trabalho U-SQL:

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
O arquivo de origem definido no script é **/Samples/Data/SearchLog.tsv1**, em que ele deverá ser **/Samples/Data/SearchLog.tsv**.


## <a name="troubleshoot-the-job"></a>Solucionar problemas no trabalho

**Para ver todos os trabalhos**

1. No portal do Azure, clique em **Microsoft Azure** no canto superior esquerdo.
2. Clique no bloco com o nome da conta da Análise Data Lake.  O resumo do trabalho é mostrado no bloco **Gerenciamento de Trabalhos** .

    ![Gerenciamento de trabalhos da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    O Gerenciamento do trabalho mostra rapidamente o status do trabalho. Observe que há um trabalho com falha.
3. Clique no bloco **Gerenciamento de Trabalhos** para ver os trabalhos. Os trabalhos são categorizados como **Em execução**, **Em fila** e **Concluído**. Você deverá ver o trabalho com falha na seção **Concluído** . Ele deve ser primeiro na lista. Quando tiver vários trabalhos, é possível clicar em **Filtrar** para ajudar a encontrar os trabalhos.

    ![Trabalhos de filtragem da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Clique no trabalho com falha na lista para abrir os detalhes do trabalho em uma nova folha:

    ![Trabalho com falha da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observe o botão **Enviar novamente** . Depois de corrigir o problema, você poderá enviar novamente o trabalho.
5. Clique na parte realçada na captura de tela anterior para abrir os detalhes do erro.  Você verá algo semelhante ao seguinte:

    ![Detalhes do trabalho com falha da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Isso indica que a pasta de origem não foi encontrada.
6. Clique em **Duplicar Script**.
7. Atualize o caminho **FROM** para o seguinte:

    "/Samples/Data/SearchLog.tsv"
8. Clique em **Enviar Trabalho**.

## <a name="see-also"></a>Confira também
* [Visão geral da Análise Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução ao U-SQL da Análise Azure Data Lake usando o Visual Studio](data-lake-analytics-u-sql-get-started.md)
* [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
