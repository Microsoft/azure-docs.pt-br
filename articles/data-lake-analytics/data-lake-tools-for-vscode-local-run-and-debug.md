---
title: Executar U-SQL e depurar localmente usando as Ferramentas do Azure Data Lake para Visual Studio Code
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code para executar e depurar trabalhos U-SQL localmente.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 765bcaab0f91e097be827bfa6e8f505ef5330d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814214"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Executar U-SQL e depurar localmente no Visual Studio Code
Este artigo descreve como executar trabalhos U-SQL em um computador de desenvolvimento local para acelerar as fases iniciais de codificação ou para depurar o código localmente no Visual Studio Code. Para obter instruções sobre como usar as Ferramentas do Azure Data Lake para Visual Studio Code, confira [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Apenas Windows instalações de ferramentas do Azure Data Lake para Visual Studio dão suporte a ação para executar o U-SQL localmente e depurar o U-SQL localmente. Instalações no macOS e sistemas operacionais baseados em Linux não dão suporte a esse recurso.

## <a name="set-up-the-u-sql-local-run-environment"></a>Configurar o ambiente de execução local do U-SQL

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos e, em seguida, insira **ADL: Baixe o pacote de execução Local** para baixar os pacotes.  

   ![Baixar os pacotes de Dependência de LocalRun do ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Localize os pacotes de dependência do caminho mostrado no painel **Saída** abaixo e então instale o BuildTools e o Win10SDK 10240. Aqui está um caminho de exemplo:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Localizar os pacotes de dependência](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Para instalar **BuildTools**, clique em visualcppbuildtools_full.exe na pasta LocalRunDependency e siga as instruções do assistente.   

    ![Instalar BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Para instalar **Win10SDK 10240**, clique em sdksetup.exe na pasta LocalRunDependency/Win10SDK_10.0.10240_2 e siga as instruções do assistente.  

    ![Instalar Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Defina a variável de ambiente. Defina a variável de ambiente **SCOPE_CPP_SDK** como:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Inicie o serviço de execução local e envie o trabalho U-SQL para uma conta local 
Para o usuário pela primeira vez, use **ADL: Baixe o pacote de execução Local** para baixar pacotes de execução local, se você ainda não tiver [configurar o ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos e, em seguida, insira **ADL: Iniciar o serviço de execução Local**.   
2. Selecione **Aceito** para aceitar os termos da Licença de Software da Microsoft pela primeira vez. 

   ![Aceite os Termos de Licença para Software Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. O console cmd é aberto. Aos usuários de primeira viagem, será necessário inserir **3** e insira um caminho de pasta local para os dados de entrada e saída. Para outras opções, basta simplesmente usar os valores padrão. 

   ![Ferramentas do Data Lake para Visual Studio Code cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecione Ctrl + Shift + P para abrir a paleta de comandos, digite **ADL: Enviar trabalho**e, em seguida, selecione **Local** para enviar o trabalho para sua conta local.

   ![Ferramentas do Data Lake para Visual Studio Code selecionar local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Depois de enviar o trabalho, você poderá exibir os detalhes de envio. Para exibir os detalhes do envio, selecione **jobUrl** na janela **Saída**. Você também pode exibir o status de envio de trabalho do console do cmd. Digite **7** no console do cmd se você quiser saber mais detalhes do trabalho.

   ![Ferramentas do Data Lake para Visual Studio Code saída de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Ferramentas do Data Lake para Visual Studio Code status do cmd de execução local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Iniciar uma depuração local para o trabalho de U-SQL  
Para o usuário novato:

1. Use **ADL: Baixe o pacote de execução Local** para baixar pacotes de execução local, se você ainda não tiver [configurar o ambiente de execução local U-SQL](#set-up-the-u-sql-local-run-environment).
2. Instale o .NET Core SDK 2.0 conforme sugerido na caixa de mensagem, se não instalado.
 
  ![lembrete instala Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instalar o C# para Visual Studio Code como sugerido na caixa de mensagem se não instalado. Clique em **instale** para continuar e, em seguida, reinicie o VSCode.

    ![Lembrete para instalar o C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Siga as etapas abaixo para executar a depuração local:
  
1. Selecione Ctrl + Shift + P para abrir a paleta de comandos e, em seguida, insira **ADL: Iniciar o serviço de execução Local**. O console cmd é aberto. Verifique se **DataRoot** está definido.
2. Defina um ponto de interrupção no code-behind do C#.
3. Voltar ao editor de scripts, o botão direito do mouse e selecione **ADL: Depuração local**.
    
   ![Ferramentas do Data Lake para Visual Studio Code resultado da depuração local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Próximas etapas
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Desenvolver U-SQL com Python, R e CSharp para Azure Data Lake Analytics no VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Introdução à Análise Data Lake usando o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução à Análise Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Usar as Ferramentas do Data Lake para Visual Studio para desenvolver aplicativos do U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar o catálogo do Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
