---
title: Anotações de versão para o Application Insights | Microsoft Docs
description: Adicione uma implantação ou compile marcadores para seus gráficos do gerenciador de métricas no Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 776efd56aaa523d1c2621c51cba0446a42bb7411
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461905"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações sobre gráficos de métricas no Application Insights

As anotações mostram onde você implantou uma nova compilação ou outros eventos significativos. As anotações facilitam a visualização se as alterações tiveram algum efeito sobre o desempenho do aplicativo. Eles podem ser criados automaticamente pelo sistema [Azure pipelines](/azure/devops/pipelines/tasks/) Build. Você também pode criar anotações para sinalizar eventos desejados criando-as no PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Anotações de versão com Azure Pipelines Build

As anotações de versão são um recurso do serviço Azure Pipelines baseado em nuvem do Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão Annotations (uma vez)

Para poder criar anotações de versão, você precisará instalar uma das muitas extensões do Azure DevOps disponíveis no Visual Studio Marketplace.

1. Entre no seu projeto [DevOps do Azure](https://azure.microsoft.com/services/devops/) .
   
1. Na página [extensão de anotações](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) de Visual Studio Marketplace versão, selecione sua organização do Azure DevOps e, em seguida, selecione **instalar** para adicionar a extensão à sua organização do DevOps do Azure.
   
   ![Selecione uma organização do Azure DevOps e, em seguida, selecione instalar.](./media/annotations/1-install.png)
   
Você só precisa instalar a extensão uma vez para sua organização DevOps do Azure. Agora você pode configurar anotações de versão para qualquer projeto em sua organização.

### <a name="configure-release-annotations"></a>Criar anotações de versão

Crie uma chave de API separada para cada um dos modelos de versão de Azure Pipelines.

1. Entre no [portal do Azure](https://portal.azure.com) e abra o recurso Application insights que monitora seu aplicativo. Ou, se você não tiver uma, [crie um novo recurso de Application insights](./app-insights-overview.md).
   
1. Abra a guia **acesso à API** e copie a **ID de Application insights**.
   
   ![Em acesso à API, copie a ID do aplicativo.](./media/annotations/2-app-id.png)

1. Em uma janela separada do navegador, abra ou crie o modelo de versão que gerencia suas implantações de Azure Pipelines.
   
1. Selecione **Adicionar tarefa** e, em seguida, selecione a Application insights tarefa de **anotação de liberação** no menu.
   
   ![Selecione Adicionar tarefa e selecione Application Insights anotação de versão.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A tarefa de anotação de versão atualmente dá suporte apenas a agentes baseados no Windows; Ele não será executado no Linux, no macOS ou em outros tipos de agentes.
   
1. Em **ID do aplicativo**, Cole a id de Application insights que você copiou da guia **acesso à API** .
   
   ![Cole a ID de Application Insights](./media/annotations/4-paste-app-id.png)
   
1. De volta à janela de **acesso Application insights API** , selecione **criar chave de API**. 
   
   ![Na guia acesso à API, selecione criar chave de API.](./media/annotations/5-create-api-key.png)
   
1. Na janela **criar chave de API** , digite uma descrição, selecione **anotações de gravação** e, em seguida, selecione **gerar chave**. Copie a nova chave.
   
   ![Na janela criar chave de API, digite uma descrição, selecione anotações de gravação e, em seguida, selecione gerar chave.](./media/annotations/6-create-api-key.png)
   
1. Na janela modelo de versão, na guia **variáveis** , selecione **Adicionar** para criar uma definição de variável para a nova chave de API.

1. Em **nome**, insira `ApiKey` e, em **valor**, Cole a chave de API que você copiou da guia **acesso à API** .
   
   ![Na guia variáveis do Azure DevOps, selecione Adicionar, nomeie a variável ApiKey e cole a chave de API em valor.](./media/annotations/7-paste-api-key.png)
   
1. Selecione **salvar** na janela principal do modelo de liberação para salvar o modelo.


   > [!NOTE]
   > Os limites para chaves de API são descritos na [documentação limites da taxa da API REST](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Exibir anotações


   > [!NOTE]
   > As anotações de versão não estão disponíveis no painel de métricas do Application Insights

Agora, sempre que você usar o modelo de versão para implantar uma nova versão, uma anotação será enviada para Application Insights. As anotações podem ser exibidas nos seguintes locais:

O painel de **uso** em que você também pode criar anotações de versão manualmente:

![Captura de tela do gráfico de barras com o número de visitas do usuário exibidas em um período de horas. As anotações de versão aparecem como marcas de seleção verdes acima do gráfico indicando o momento em que uma liberação ocorreu](./media/annotations/usage-pane.png)

Em qualquer consulta de pasta de trabalho baseada em log na qual a visualização exibe o tempo ao longo do eixo x.

![Captura de tela de painel de pastas de trabalho com consulta baseada em log de série temporal com anotações exibidas](./media/annotations/workbooks-annotations.png)

Para habilitar anotações em sua pasta de trabalho, acesse **Configurações avançadas** e selecione **Mostrar anotações**.

![Captura de tela do menu de configurações avançadas com as palavras mostrar anotações realçadas com uma marca de seleção ao lado da configuração para habilitá-la.](./media/annotations/workbook-show-annotations.png)

Selecione qualquer marcador de anotação para abrir detalhes sobre a versão, incluindo o solicitante, o Branch de controle do código-fonte, o pipeline de liberação e o ambiente.

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas no PowerShell
Você pode usar o script do PowerShell do CreateReleaseAnnotation do GitHub para criar anotações de qualquer processo que desejar, sem usar o DevOps do Azure.

1. Faça uma cópia local de CreateReleaseAnnotation.ps1:

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. Use as etapas no procedimento anterior para obter sua ID de Application Insights e criar uma chave de API na guia **acesso à api** Application insights.
   
1. Chame o script do PowerShell com o código a seguir, substituindo os espaços reservados entre colchetes angulares por seus valores. Os `-releaseProperties` são opcionais. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Você pode modificar o script, por exemplo, para criar anotações para o passado.

## <a name="next-steps"></a>Próximas etapas

* [Criar um item de trabalho](./diagnostic-search.md#create-work-item)
* [Automação com o PowerShell](./powershell.md)

