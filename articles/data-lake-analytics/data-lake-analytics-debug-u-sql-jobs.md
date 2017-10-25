---
title: Depurar trabalhos do U-SQL | Microsoft Docs
description: "Saiba como depurar um vértice com falha do U-SQL usando o Visual Studio."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Depurar um código C# definido pelo usuário em trabalhos com falha do U-SQL

O U-SQL fornece um modelo de extensibilidade usando o C# e, portanto, você pode escrever seu código para adicionar funcionalidade, como um extrator ou redutor personalizado. Para saber mais, consulte o [Guia de programação do U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). Na prática, qualquer código pode precisar de depuração e sistemas de Big Data podem fornecer apenas informações limitadas de depuração em tempo de execução como arquivos de log.

As Ferramentas do Azure Data Lake para Visual Studio fornecem um recurso chamado **Falha na Depuração de Vértice**, que permite clonar um trabalho com falha da nuvem para o computador local para depuração. O clone local captura todo o ambiente de nuvem, incluindo dados de entrada e código do usuário.

O vídeo a seguir demonstra a Falha na Depuração de Vértice nas Ferramentas do Azure Data Lake para Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> O Visual Studio exige as seguintes duas atualizações, caso elas ainda não estejam instaladas: [Microsoft Visual C++ 2015 Redistributable Atualização 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e [Tempo de Execução C Universal do Windows](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Baixar o vértice com falha no computador local

Ao abrir um trabalho com falha nas Ferramentas do Azure Data Lake para Visual Studio, você verá uma barra amarela de alerta com mensagens de erro detalhadas na guia de erros.

1. Clique em **Download** para baixar todos os recursos e fluxos de entrada necessários. Se o download não for concluído, clique em **Tentar Novamente**.

2. Clique em **Abrir** após a conclusão do download para gerar um ambiente de depuração local. Uma nova instância do Visual Studio com uma solução de depuração é criada e aberta automaticamente.

![Vértice para download do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

Os trabalhos podem incluir arquivos de origem code-behind ou assemblies registrados e esses dois tipos têm diferentes cenários de depuração.

- [Depurar um trabalho com falha com code-behind](#debug-job-failed-with-code-behind)
- [Depurar um trabalho com falha com assemblies](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Depurar um trabalho com falha com code-behind

Se um trabalho do U-SQL falhar e o trabalho incluir o código do usuário (geralmente chamado `Script.usql.cs` em um projeto do U-SQL), esse código-fonte será importado para a solução de depuração.  Nela, você pode usar as ferramentas de depuração do Visual Studio (inspeção, variáveis, etc.) para solucionar o problema.

> [!NOTE]
> Antes da depuração, marque a opção **Exceções do Common Language Runtime** na janela Configurações de Exceção (**Ctrl + Alt + E**).

![Configuração do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Pressione **F5** para executar o código code-behind. Ele será executado até que seja interrompido por uma exceção.

2. Abra o arquivo `ADLTool_Codebehind.usql.cs` e defina pontos de interrupção e, em seguida, pressione **F5** para depurar o código passo a passo.

    ![Exceção de depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Depurar trabalho em falha com assemblies

Se você usar assemblies registrados no script do U-SQL, o sistema não poderá obter o código-fonte automaticamente. Nesse caso, adicione manualmente os arquivos do código-fonte dos assemblies à solução.

### <a name="configure-the-solution"></a>Configurar a solução

1. Clique com o botão direito do mouse em **Solução “VertexDebug” > Adicionar > Projeto Existente...** para encontrar o código-fonte dos assemblies e adicionar o projeto à solução de depuração.

    ![Adicionar projeto de depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Clique com o botão direito do mouse em **LocalVertexHost > Propriedades** na solução e copie o caminho do **Diretório de Trabalho**.

3. Clique com o botão direito do mouse em **projeto de código-fonte do assembly > Propriedades**, selecione a guia **Compilar** à esquerda e, em seguida, cole o caminho copiado como **Saída > Caminho de saída**.

    ![Definir caminho pdb da depuração do U-SQL do Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Pressione **Ctrl+Alt+E** e marque **Exceções de Common Language Runtime** na janela Configurações de exceção.

### <a name="start-debug"></a>Iniciar depuração

1. Clique com o botão direito do mouse em **projeto de código-fonte do assembly > Recompilar** para gerar arquivos .pdb no diretório de trabalho `LocalVertexHost`.

2. Pressione **F5** e o projeto será executado até que seja interrompido por uma exceção. Talvez você veja a seguinte mensagem de aviso, que poderá ser ignorada. Pode levar um minuto para que a tela de depuração seja exibida.

    ![Aviso do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Abra o código-fonte e defina pontos de interrupção e, em seguida, pressione **F5** para depurar o código passo a passo.

Você também pode usar as ferramentas de depuração do Visual Studio (inspeção, variáveis, etc.) para solucionar o problema.

> [!NOTE]
> Recompile o projeto de código-fonte do assembly sempre depois de modificar o código para gerar os arquivos .pdb atualizados.

Após a depuração, se o projeto for concluído com êxito, a janela de saída mostrará a seguinte mensagem:

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Depuração do U-SQL do Azure Data Lake Analytics bem sucedida](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Reenviar o trabalho

Depois de concluir a depuração, reenvie o trabalho com falha.

1. Para trabalhos com soluções code-behind, copie o código C# para o arquivo de origem code-behind (geralmente, `Script.usql.cs`).
2. Para trabalhos com assemblies, registre os assemblies .dll atualizados no banco de dados do ADLA:
    1. No Gerenciador de Servidores ou no Cloud Explorer, expanda o nó **conta do ADLA > Bancos de dados**.
    2. Clique com o botão direito do mouse em **Assemblies** e registre os novos assemblies .dll no banco de dados do ADLA: ![assembly de registro de depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Reenvie o trabalho.

## <a name="next-steps"></a>Próximas etapas

- [Guia de programação do U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos do Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Tutorial: desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
