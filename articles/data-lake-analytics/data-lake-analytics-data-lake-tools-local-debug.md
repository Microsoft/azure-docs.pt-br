---
title: Depurar o código do Azure Data Lake Analytics localmente
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para depurar trabalhos do U-SQL em sua estação de trabalho local.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472984"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Depurar o código do Azure Data Lake Analytics localmente

Você pode usar Ferramentas do Azure Data Lake para Visual Studio para executar e depurar código do Azure Data Lake Analytics em sua estação de trabalho local, assim como no serviço do Azure Data Lake Analytics.

Saiba [como executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblies do C# localmente

Você pode depurar assemblies do C# sem enviar e registrá-los para o serviço Azure Data Lake Analytics. Você pode definir pontos de interrupção no arquivo code-behind e em um projeto do C# referenciado.

### <a name="debug-local-code-in-a-code-behind-file"></a>Depurar o código local no arquivo code-behind

1. Definir pontos de interrupção no arquivo code-behind.
2. Selecione **F5**para depurar o script localmente.

> [!NOTE]
   > O procedimento a seguir só funciona no Visual Studio 2015. Nas versões mais antigas do Visual Studio, talvez seja necessário adicionar manualmente os arquivos **PDB**.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Depurar o código local em um projeto do C# referenciado

1. Criar um projeto de assembly do C# e compilar para gerar o arquivo **DLL** de saída.
2. Registre o arquivo **DLL** usando uma instrução U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Definir pontos de interrupção no código C#.
4. Selecione **F5** para depurar o script referenciando o arquivo **DLL** C# localmente.


## <a name="next-steps"></a>Próximas etapas

- Para ver um exemplo de consulta mais complexa, consulte [Analisar logs de site usando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Para ver detalhes do trabalho, confira [Usar o Navegador de Trabalhos e o Modo de Exibição de Trabalho para trabalhos do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
