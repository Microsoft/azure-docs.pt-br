---
title: Usar o SDK do .NET para trabalhos do Gerenciador de Dados do Microsoft Azure StorSimple | Microsoft Docs
description: "Saiba como usar o SDK do .NET para iniciar trabalhos do Gerenciador de Dados StorSimple (visualização particular)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>Usar o SDK do .net para iniciar a transformação de dados (visualização particular)

## <a name="overview"></a>Visão geral

Este artigo explica como você pode usar o recurso de transformação de dados dentro do serviço do Gerenciador de Dados StorSimple para transformar os dados do dispositivo StorSimple. Os dados transformados, em seguida, são consumidos por outros serviços do Azure na nuvem. O artigo também tem um passo a passo para ajudar a criar um aplicativo de console do .NET de exemplo a fim de iniciar um trabalho de transformação de dados e acompanhar a sua conclusão.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:
*   Um sistema com o Visual Studio 2012, 2013, 2015 ou 2017 instalado.
*   Azure Powershell instalado. [Baixar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Definições de configuração para inicializar o trabalho de Transformação de Dados (as instruções para obter essas configurações estão incluídas aqui).
*   Uma definição de trabalho que foi configurada corretamente em um recurso de dados híbrido dentro de um grupo de recursos.
*   Todas as dlls necessárias. Baixe essas dlls do [repositório GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   Um `Get-ConfigurationParams.ps1`[script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) do repositório GitHub.

## <a name="step-by-step"></a>Passo a passo

Execute as seguintes etapas para usar o .NET e iniciar um trabalho de transformação de dados.

1. Para recuperar os parâmetros de configuração, execute as etapas a seguir:
    1. Baixe o `Get-ConfigurationParams.ps1` do script do repositório GitHub no local `C:\DataTransformation`.
    1. Execute o script `Get-ConfigurationParams.ps1` do repositório GitHub. Digite o seguinte comando: 

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Você pode passar em todos os valores para ActiveDirectoryKey e AppName.


2. Esse script gera como saída os seguintes valores:
    * Id do Cliente
    * ID do locatário
    * Chave do Active Directory (a mesma inserida acima)
    * ID da assinatura

3. Usando o Visual Studio 2012, 2013 ou 2015, crie um aplicativo de console do .NET em C#.

    1. Inicie o **Visual Studio 2012/2013/2015**.
    1. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
    2. Expanda **Modelos** e selecione **Visual C#**.
    3. Selecione **Aplicativo de console** na lista de tipos de projetos à direita.
    4. Digite **DataTransformationApp** como **Nome**.
    5. Selecione **C:\DataTransformation** como **Local**.
    6. Clique em **OK** para criar o projeto.

4.  Agora, adicione todas as DLLs que estão na pasta [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) como **Referências** no projeto que você criou. Para baixar os arquivos dll, faça o seguinte:

    1. No Visual Studio, vá para **Modo de Exibição > Gerenciador de Soluções**.
    1. Clique na seta à esquerda do projeto do Aplicativo de Transformação de Dados. Clique em **Referências** e clique com o botão direito do mouse em **Adicionar Referência**.
    2. Navegue até o local da pasta de pacotes, selecione todas as DLLs e clique em **Adicionar**e em **OK**.

5. Adicione as seguintes declarações **using** ao arquivo de origem (Program.cs) no projeto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. O código a seguir inicializa a instância do trabalho de transformação de dados. Adicione isso ao **Método principal**. Substitua os valores dos parâmetros de configuração como obtido anteriormente. Conecte os valores de **Nome do Grupo de Recursos** e **Nome do Recurso de Dados Híbrido**. O **Nome do Grupo de Recursos** é aquele que hospeda o Recurso de Dados Híbrido no qual a definição de trabalho foi configurada.

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

    ```

9. Esse trabalho carrega os arquivos correspondentes presentes no diretório raiz do volume do StorSimple no contêiner especificado. Quando um arquivo é carregado, uma mensagem é descartada da fila (na mesma conta de armazenamento que o contêiner) com o mesmo nome da definição de trabalho. Essa mensagem pode ser usada como um disparador para iniciar o processamento do arquivo.

10. Depois que o trabalho é disparado, adicione o código a seguir para acompanhar o trabalho até a conclusão.

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


## <a name="next-steps"></a>Próximas etapas

[Use a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md).
