---
title: "Testar e depurar trabalhos do U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake | Microsoft Docs"
description: "Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio e o SDK para U-SQL do Azure Data Lake a fim de testar e depurar trabalhos do U-SQL em sua estação de trabalho local."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Testar e depurar trabalhos do U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake

Você pode usar as Ferramentas do Azure Data Lake para Visual Studio e SDK para U-SQL do Azure Data Lake a fim de executar trabalhos de U-SQL na sua estação de trabalho da mesma forma que faz no serviço Azure Data Lake. Esses dois recursos de execução local economizam tempo no teste e na depuração de seus trabalhos de U-SQL.

## <a name="understand-the-data-root-folder-and-the-file-path"></a>Compreender a pasta raiz de dados e o caminho do arquivo

Tanto a execução local quanto o SDK para U-SQL exigem uma pasta raiz de dados. A pasta raiz de dados é um "repositório local" para a conta de computador local. Ela equivale à conta do Azure Data Lake Store de uma conta do Data Lake Analytics. Alternar para uma pasta raiz de dados diferente é como alternar para uma conta de repositório diferente. Se você quiser acessar dados compartilhados normalmente com pastas raiz de dados diferentes, deverá usar caminhos absolutos em seus scripts. Ou criar links simbólicos de sistema de arquivo (por exemplo, **mklink** em NTFS) na pasta raiz de dados para apontar para os dados compartilhados.

A pasta raiz de dados é usada para:

- Armazenar metadados, incluindo bancos de dados, tabelas, TVFs (funções com valor de tabela) e assemblies.
- Pesquisar os caminhos de entrada e saída que são definidos como caminhos relativos no U-SQL. Usar caminhos relativos facilita a implantação dos projetos U-SQL no Azure.

Você pode usar um caminho relativo e um caminho absoluto local em scripts U-SQL. O caminho relativo é relativo ao caminho da pasta raiz de dados especificado. Recomendamos que você use "/" como o separador de caminho para tornar seus scripts compatíveis com o lado do servidor. A seguir, alguns exemplos de caminhos relativos e os respectivos caminhos absolutos equivalentes. Nesses exemplos, C:\LocalRunDataRoot é a pasta raiz de dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Usar execução local no Visual Studio

As Ferramentas do Data Lake para Visual Studio proporcionam experiência de execução local do U-SQL no Visual Studio. Usando esse recurso, você pode:

- Executar um script U-SQL localmente com os assemblies de C#.
- Depurar um assembly de C# localmente.
- Criar, exibir e excluir catálogos do U-SQL (bancos de dados locais, assemblies, esquemas e tabelas) no Gerenciador de servidores. Você também pode encontrar o catálogo local no Gerenciador de Servidores.

    ![Catálogo local da execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

O instalador das Ferramentas do Data Lake cria uma pasta C:\LocalRunRoot para ser usada como a pasta raiz de dados padrão. O paralelismo da execução local padrão é 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Para configurar a execução local no Visual Studio

1. Abra o Visual Studio.
2. Abra o **Gerenciador de Servidores**.
3. Expanda **Azure** > **Data Lake Analytics**.
4. Clique no menu **Data Lake** e, em seguida, em **Opções e Configurações**.
5. Na árvore à esquerda, expanda **Azure Data Lake** e, em seguida, **Geral**.

    ![Definir configurações de execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

É necessário um projeto U-SQL do Visual Studio para realizar a execução local. Essa parte é diferente de executar scripts U-SQL no Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Para executar um script U-SQL localmente
1. No Visual Studio, abra o projeto U-SQL.   
2. Clique com o botão direito do mouse em um script U-SQL no Gerenciador de Soluções e clique em **Enviar Script**.
3. Selecione **(Local)** como a conta do Analytics para executar o script localmente.
Você pode também clicar na conta **(Local)** na parte superior da janela do script e clicar em **Enviar** (ou usar as teclas de acesso CTRL+F5).

    ![Enviar trabalhos de execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblies do C# localmente

É possível depurar assemblies do C# sem os enviar nem os registrar para o Serviço Azure Data Lake Analytics. Você pode definir pontos de interrupção no arquivo code-behind e em um projeto do C# referenciado.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Para depurar o código local no arquivo code-behind

1. Definir pontos de interrupção no arquivo code-behind.
2. Pressione F5 para depurar o script localmente.

> [!NOTE]
   > O procedimento a seguir só funciona no Visual Studio 2015. No Visual Studio mais antigo, talvez seja necessário adicionar manualmente os arquivos pdb.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Para depurar o código local em um projeto do C# referenciado

1. Crie um projeto de Assembly do C# e compile-o para gerar o dll de saída.
2. Registre o dll usando uma instrução U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Definir pontos de interrupção no código C#.
4. Pressione F5 para depurar o script referenciando a dll do C# localmente.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Usar execução local no SDK para U-SQL do Data Lake

Além de executar scripts U-SQL localmente usando o Visual Studio, você pode usar o SDK para U-SQL do Azure Data Lake para executá-los localmente com interfaces de programação e linha de comando. Você pode dimensionar seu teste local U-SQL com eles.

Saiba mais sobre o [SDK do U-SQL do Azure Data Lake](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Próximas etapas

* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver detalhes do trabalho, confira [Usar o Navegador de Trabalhos e o Modo de Exibição de Trabalho para trabalhos do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
