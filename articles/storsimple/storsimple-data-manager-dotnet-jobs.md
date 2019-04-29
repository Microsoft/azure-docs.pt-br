---
title: Usar o SDK do .NET para trabalhos do Gerenciador de Dados do Microsoft Azure StorSimple | Microsoft Docs
description: Saiba como usar o SDK do .NET para iniciar trabalhos do Gerenciador de Dados StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 80f01a926b94deebab59f8ef91bfc36a4600b5f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632259"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Use o SDK do .NET para iniciar a transformação de dados

## <a name="overview"></a>Visão geral

Este artigo explica como você pode usar o recurso de transformação de dados dentro do serviço do Gerenciador de Dados StorSimple para transformar os dados do dispositivo StorSimple. Os dados transformados, em seguida, são consumidos por outros serviços do Azure na nuvem.

Você pode inicializar um trabalho de transformação de dados de duas maneiras:

- Usar o SDK .NET
- Usar o Runbook de Automação do Azure
 
  Este artigo fornece detalhes sobre como criar um aplicativo de console .NET de exempo para iniciar um trabalho de transformação de dados e, em seguida, acompanhá-lo até a conclusão. Para saber mais sobre como iniciar a transformação de dados por meio da Automação do Azure, acesse [Usar o runbook de Automação do Azure para acionar os trabalhos de transformação de dados](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:
*   Um computador em execução:

    - Visual Studio 2012, 2013, 2015 ou 2017.

    - PowerShell do Azure. [Baixar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Uma definição de trabalho configurada corretamente no Gerenciador de Dados do StorSimple dentro de um grupo de recursos.
*   Todas as dlls necessárias. Baixe essas dlls do [repositório GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) script do repositório do GitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

Execute as seguintes etapas para usar o .NET e iniciar um trabalho de transformação de dados.

1. Para recuperar os parâmetros de configuração, execute as etapas a seguir:
    1. Baixe o `Get-ConfigurationParams.ps1` do script do repositório GitHub no local `C:\DataTransformation`.
    1. Execute o script `Get-ConfigurationParams.ps1` do repositório GitHub. Digite o seguinte comando: 

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Você pode passar em todos os valores para ActiveDirectoryKey e AppName.

2. Esse script gera como saída os seguintes valores:
    * ID do cliente
    * ID do locatário
    * Chave do Active Directory (a mesma inserida acima)
    * ID da assinatura

        ![Saída de script dos parâmetros de configuração](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Usando o Visual Studio 2012, 2013 ou 2015, crie um aplicativo de console do .NET em C#.

    1. Inicie o **Visual Studio 2012/2013/2015**.
    1. Selecione **Arquivo > Novo > Projeto**.

        ![Criar um projeto 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selecione **Instalado > Modelo > Visual C# > Aplicativo de Console**.
    3. Digite **DataTransformationApp** como **Nome**.
    4. Selecione **C:\DataTransformation** como **Local**.
    6. Clique em **OK** para criar o projeto.

        ![Criar um projeto 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Agora, adicione todas as dlls que estão na pasta [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) como **Referências** no projeto que você criou. Para adicionar os arquivos dll, execute o seguinte procedimento:

   1. No Visual Studio, vá para **Modo de Exibição > Gerenciador de Soluções**.
   2. Clique na seta à esquerda do projeto do Aplicativo de Transformação de Dados. Clique em **Referências** e clique com o botão direito do mouse em **Adicionar Referência**.
    
       ![Adicionar dlls 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Navegue até o local da pasta de pacotes, selecione todas as dlls e clique em **Adicionar**e em **OK**.

       ![Adicionar dlls 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adicione as seguintes declarações **using** ao arquivo de origem (Program.cs) no projeto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. O código a seguir inicializa a instância do trabalho de transformação de dados. Adicione isso ao **Método principal**. Substitua os valores dos parâmetros de configuração como obtido anteriormente. Conecte os valores do **Nome do Grupo de Recursos** e **Nome do Recurso**. O **Nome do Grupo de Recursos** está associado ao Gerenciador de Dados StorSimple no qual a definição do trabalho foi configurada. O **Nome do Recurso** é o nome do seu serviço do Gerenciador de Dados StorSimple.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Especifique os parâmetros com os quais a definição de trabalho precisa ser executada

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OU)

    Se você quiser alterar os parâmetros de definição de trabalho durante o tempo de execução, adicione o seguinte código:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Após a inicialização, adicione o código a seguir para disparar um trabalho de transformação de dados na definição de trabalho. Use o plug-in do **Nome de Definição do Trabalho** apropriado.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Depois de o código ser colado, compile a solução. Aqui está uma captura de tela do snippet de código para inicializar a instância de trabalho de transformação de dados.

   ![Snippet de código para inicializar o trabalho de transformação de dados](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Esse trabalho transforma os dados que combinam com o diretório raiz e o arquivo filtra dentro do volume do StorSimple e o coloca no compartilhamento especificado/compartilhamento de arquivo. Quando um arquivo é transformado, uma mensagem é adicionada à fila de armazenamento (na mesma conta de armazenamento que do contêiner/compartilhamento de arquivo) com o mesmo nome da definição de trabalho. Essa mensagem pode ser usada como um disparador para iniciar o processamento do arquivo.

10. Depois que o trabalho é disparado, você pode usar o código a seguir para acompanhar o trabalho até a conclusão. Não é obrigatório adicionar esse código para a execução do trabalho.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Aqui está uma captura de tela do exemplo de código inteiro usado para disparar o trabalho usando o .NET.

    ![Snippet completo do código para disparar um trabalho do .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Próximas etapas

[Use a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md).
