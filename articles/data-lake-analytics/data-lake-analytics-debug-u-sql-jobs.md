---
title: Depurar código C# definido pelo usuário para trabalhos U-SQL do Azure Data Lake com falha | Microsoft Docs
description: Este artigo descreve como depurar um vértice U-SQL com falha usando as Ferramentas do Azure Data Lake para Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 5417f66696191cebadc2af9c6d634419a0eb8e5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615338"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Depurar um código C# definido pelo usuário em trabalhos com falha do U-SQL

O U-SQL oferece um modelo de extensibilidade usando C#. Em scripts do U-SQL, é fácil chamar funções de C# e executar funções analíticas não compatíveis com linguagens declarativas semelhantes ao SQL. Para saber mais sobre a extensibilidade do U-SQL, consulte [Guia de programação do U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

Na prática, qualquer código pode precisar de depuração, mas é difícil depurar um trabalho distribuído com código personalizado na nuvem com arquivos de log limitados. As [Ferramentas do Azure Data Lake para Visual Studio](https://aka.ms/adltoolsvs) oferecem um recurso chamado **Falha na Depuração de Vértice**, que ajuda a depurar de forma mais fácil as falhas que ocorrem no código personalizado. Quando o trabalho do U-SQL falha, o serviço mantém o estado de falha e a ferramenta ajuda a baixar o ambiente de falha da nuvem para o computador local a fim de realizar a depuração. O download local captura todo o ambiente de nuvem, incluindo dados de entrada e código do usuário.

O vídeo a seguir demonstra a Falha na Depuração de Vértice nas Ferramentas do Azure Data Lake para Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> O Visual Studio requer as seguintes duas atualizações para usar esse recurso: [Microsoft Visual C++ 2015 redistribuível atualização 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e o [tempo de execução C Universal para Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Baixar o vértice com falha no computador local

Ao abrir um trabalho com falha nas Ferramentas do Azure Data Lake para Visual Studio, você verá uma barra amarela de alerta com mensagens de erro detalhadas na guia de erros.

1. Clique em **Download** para baixar todos os recursos e fluxos de entrada necessários. Se o download não for concluído, clique em **Tentar Novamente**.

2. Clique em **Abrir** após a conclusão do download para gerar um ambiente de depuração local. Uma nova solução de depuração será aberta e, se você tiver uma solução já existente aberta no Visual Studio, não deixe de salvá-la e fechá-la antes de depurar.

![Vértice para download do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Configurar o ambiente de depuração

> [!NOTE]
> Antes da depuração, marque a opção **Exceções do Common Language Runtime** na janela Configurações de Exceção (**Ctrl + Alt + E**).

![Configuração do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Na nova instância inicializada do Visual Studio, você pode ou não encontrar o código-fonte C# definido pelo usuário:

1. [Posso encontrar meu código-fonte na solução](#source-code-is-included-in-debugging-solution)

2. [Não posso encontrar meu código-fonte na solução](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>O código-fonte está incluído na solução de depuração

Há dois casos em que o código-fonte C# é capturado:

1. O código do usuário é definido no arquivo code-behind (normalmente denominado `Script.usql.cs` em um projeto do U-SQL).

2. O código do usuário é definido no projeto de biblioteca de classes em C# para o aplicativo U-SQL e registrado como um assembly com **informações de depuração**.

Se o código-fonte for importado para a solução, será possível usar as ferramentas de depuração do Visual Studio (inspeção, variáveis etc.) para solucionar o problema:

1. Pressione **F5** para iniciar a depuração. O código será executado até ser interrompido por uma exceção.

2. Abra o arquivo de código-fonte e defina pontos de interrupção. Em seguida, pressione **F5** para depurar o código passo a passo.

    ![Exceção de depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>O código-fonte não está incluído na solução de depuração

Se o código do usuário não está incluído no arquivo code-behind ou se você não registrou o assembly com as **informações de depuração**, o código-fonte não é incluído automaticamente na solução de depuração. Nesse caso, serão necessárias mais etapas para adicionar o código-fonte:

1. Clique com o botão direito do mouse em **Solução “VertexDebug” > Adicionar > Projeto Existente...** para encontrar o código-fonte do assembly e adicionar o projeto à solução de depuração.

    ![Adicionar projeto de depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Obter o caminho da pasta do projeto **FailedVertexDebugHost**. 

3. Clique com o botão direito do mouse no **projeto de código-fonte do assembly adicionado > Propriedades**, selecione a guia **Compilar** à esquerda e cole o final do caminho copiado com \bin\debug como **Saída > Caminho de saída**. O caminho de saída final é como `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Definir caminho pdb da depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Depois de aplicar essas configurações, comece a depurar com a tecla **F5** e os pontos de interrupção. Você também pode usar as ferramentas de depuração do Visual Studio (inspeção, variáveis, etc.) para solucionar o problema.

> [!NOTE]
> Recompile o projeto de código-fonte do assembly sempre depois de modificar o código para gerar os arquivos .pdb atualizados.

## <a name="resubmit-the-job"></a>Reenviar o trabalho

Após a depuração, se o projeto for concluído com êxito, a janela de saída mostrará a seguinte mensagem:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Depuração do U-SQL do Azure Data Lake Analytics bem sucedida](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Para reenviar o trabalho com falha:

1. Para trabalhos com soluções code-behind, copie o código C# para o arquivo de origem code-behind (geralmente, `Script.usql.cs`).

2. Para trabalhos com assemblies, clique com o botão direito do mouse no projeto de código-fonte do assembly na solução de depuração e registre os assemblies .dll atualizados no catálogo do Azure Data Lake.

3. Envie novamente o trabalho do U-SQL.

## <a name="next-steps"></a>Próximas etapas

- [Guia de programação do U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos do Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testar e depurar trabalhos U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Como solucionar problemas de um trabalho recorrente anormal](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
