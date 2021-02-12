---
title: Como configurar um pipeline de IC / CD para o Azure Data Lake Analytics
description: Saiba como configurar a integração contínua e a implantação contínua para Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015257"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Como configurar um pipeline de IC / CD para o Azure Data Lake Analytics  

Neste artigo, você aprenderá a configurar um pipeline de integração e implantação contínua (CI / CD) para tarefas de U-SQL e bancos de dados U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Use CI / CD para trabalhos em U-SQL

As Ferramentas do Azure Data Lake para Visual Studio fornecem o tipo de projeto U-SQL que ajuda a organizar scripts U-SQL. O uso do projeto U-SQL para gerenciar seu código U-SQL facilita mais os cenários de CI / CD.

## <a name="build-a-u-sql-project"></a>Construa um projeto U-SQL

Um projeto U-SQL pode ser criado com o Microsoft Build Engine (MSBuild) passando parâmetros correspondentes. Siga as etapas neste artigo para configurar um processo de criação para um projeto U-SQL.

### <a name="project-migration"></a>Migração do projeto

Antes de configurar uma tarefa de construção para um projeto U-SQL, verifique se você possui a versão mais recente do projeto U-SQL. Abra o arquivo de projeto de U-SQL em seu editor e verificar se você tem estes itens de importação:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Se não tiver, poderá usar uma das duas opções a seguir para migrar o projeto:

- Opção 1: altere o item de importação antigo para o anterior.
- Opção 2: abra o projeto antigo nas Ferramentas do Data Lake do Azure para Visual Studio. Use uma versão mais recente do que 2.3.3000.0. O antigo modelo de projeto será atualizado automaticamente para a versão mais recente. Novos projetos criados com versões mais recentes que 2.3.3000.0 usam o novo modelo.

### <a name="get-nuget"></a>Obter o NuGet

MSBuild não fornece suporte interno para projetos U-SQL. Para obter esse suporte, você precisa adicionar uma referência para sua solução ao pacote [Microsoft.Azure.DataLake.USQL.SD](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet que adiciona o serviço de idioma necessário.

Para adicionar a referência de pacote NuGet, clique com o botão direito do mouse na solução no Visual Studio Solution Explorer e escolha **Gerenciar pacotes NuGet**. Ou você pode adicionar um arquivo chamado `packages.config` na pasta de solução e colocar o seguinte conteúdo nele:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>Gerenciar as referências do banco de dados U-SQL

Scripts U-SQL em um projeto U-SQL podem ter instruções de consulta para objetos de banco de dados U-SQL. Nesse caso, você precisará referenciar o projeto de banco de dados U-SQL correspondente que inclui a definição de objetos antes de compilar o projeto de U-SQL. Um exemplo é quando você consulta uma tabela de U-SQL ou fazer referência a um assembly.

Saiba mais sobre [projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
> A instrução DROP pode causar uma exclusão acidental. Para habilitar a instrução DROP, você precisa especificar explicitamente os argumentos do MSBuild. O **AllowDropStatement** habilitará a operação DROP não relacionada a dados, como drop assembly e DROP TABLE Value function. O **AllowDataDropStatement** habilitará a operação DROP relacionada a dados, como DROP TABLE e drop Schema. Você precisa habilitar AllowDropStatement antes de usar AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Construa um projeto U-SQL com a linha de comando do MSBuild

Primeiro, migre o projeto e obtenha o pacote NuGet. Em seguida, chame a linha de comando do MSBuild padrão com os seguintes argumentos adicionais para compilar seu projeto de U-SQL:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

A definição e os valores dos argumentos são os seguintes:

- **USQLSDKPath = \<U-SQL Nuget package> \build\runtime**. Este parâmetro refere-se ao caminho de instalação do pacote NuGet para o serviço de linguagem U-SQL.
- **USQLTargetType = mesclagem ou SyntaxCheck**:

  - **Mesclar**. Modo de mesclagem compila arquivos code-behind. Os exemplos são **. CS**, **. py**, e **. r** arquivos. Ele incorpora a biblioteca de códigos definida pelo usuário resultante no script U-SQL. Exemplos são um código binário, Python ou R da dll.

  - **SyntaxCheck**. O modo SyntaxCheck primeiro mescla os arquivos code-behind no script U-SQL. Em seguida, ele compila o script U-SQL para validar seu código.

- **DataRoot = \<DataRoot path>**. DataRoot é necessária apenas para o modo SyntaxCheck. Quando ele cria o script com o modo SyntaxCheck, o MSBuild verifica as referências aos objetos do banco de dados no script. Antes de criar, configure um ambiente local correspondente que contenha os objetos referenciados do banco de dados U-SQL na pasta DataRoot da máquina de compilação. Você também pode gerenciar essas dependências de banco de dados ao [fazer referência a um projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild verifica somente as referências de objeto de banco de dados, não arquivos.

- **EnableDeployment = true** ou **falso**. EnableDeployment indica se é permitido implantar bancos de dados U-SQL referenciados durante o processo de compilação. Se você fizer referência a um projeto de banco de dados U-SQL e consumir os objetos de banco de dados em seu script U-SQL, defina esse parâmetro como **true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Integração contínua por meio do Azure Pipelines

Além da linha de comando, você também poderá usar o Build do Visual Studio ou uma tarefa do MSBuild para compilar projetos de U-SQL no Azure Pipelines. Para configurar um pipeline de construção, certifique-se de adicionar duas tarefas no pipeline de construção: uma tarefa de restauração do NuGet e uma tarefa do MSBuild.

![Tarefa do MSBuild para um projeto de U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Adicione uma tarefa de restauração do NuGet para obter o pacote NuGet referenciado pela solução que inclui `Azure.DataLake.USQL.SDK`, para que o MSBuild possa localizar os destinos da linguagem U-SQL. Defina **Advanced**  >  o **diretório de destino** avançado como `$(Build.SourcesDirectory)/packages` se desejar usar o exemplo de argumentos do MSBuild diretamente na etapa 2.

   ![Tarefa de restauração do NuGet para um projeto de U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Defina os argumentos do MSBuild nas ferramentas de compilação do Visual Studio ou em uma tarefa do MSBuild, conforme mostrado no exemplo a seguir. Ou, você pode definir variáveis para esses argumentos no pipeline de build do Azure Pipelines.

   ![Definir as variáveis do MSBuild de CI/CD para um projeto de U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>Resultado da compilação do projeto U-SQL

Depois de executar uma compilação, todos os scripts no projeto U-SQL são criados e gerados em um arquivo zip chamado `USQLProjectName.usqlpack`. A estrutura de pastas em seu projeto é mantida na saída de compilação zipada.

> [!NOTE]
> Os arquivos code-behind para cada script U-SQL serão mesclados como uma instrução embutida para a saída da compilação do script.

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O Azure Data Lake fornece projetos de teste para scripts U-SQL e C# UDO / UDAG / UDF:

- Saiba como [Adicionar casos de teste para scripts U-SQL e código C# estendido](data-lake-analytics-cicd-test.md#test-u-sql-scripts)

- Saiba como [executar casos de testes no Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Implantar um trabalho de U-SQL

Após verificar o código por meio do processo de build e teste, você poderá enviar trabalhos do U-SQL diretamente do Azure Pipelines por meio de uma tarefa do Azure PowerShell. Você também pode implantar o script no Azure Data Lake Store ou no armazenamento de Blob do Azure e [executar os trabalhos agendados por meio do Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Enviar trabalhos do U-SQL por meio do Azure Pipelines

A compilação de saída do projeto U-SQL é um arquivo zip chamado **USQLProjectName.usqlpack**. O arquivo zip inclui todos os scripts U-SQL no projeto. É possível usar a [tarefa do Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) no Pipelines com o exemplo de script a seguir do PowerShell para enviar tarefas de U-SQL diretamente do Azure Pipelines.

```powershell
<#
   This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and submit all scripts one-by-one.

   Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.

   Example :
      USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

   return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
   $usqlFiles = GetUsqlFiles

   Write-Output "$($usqlFiles.Count) jobs to be submitted..."

   # Submit each usql script and wait for completion before moving ahead.
   foreach ($usqlFile in $usqlFiles)
   {
      $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

      Write-Output "Submitting job for '{$usqlFile}'"

      $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism

      LogJobInformation $jobToSubmit

      Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
      $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
      LogJobInformation $jobResult
   }
}

Function LogJobInformation($jobInfo)
{
   Write-Output "************************************************************************"
   Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
   Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
   Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
   Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
   Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
   Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
   Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
   if ($item -ne $null)
   {
      return $item
   }
   return ""
}

Function Main()
{
   Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
   Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
   Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

   Write-Output "Starting USQL script deployment..."

   SubmitAnalyticsJob

   Write-Output "Finished deployment..."
}

Main
```

>[!NOTE]
> Os comandos: `Submit-AzDataLakeAnalyticsJob` e `Wait-AzDataLakeAnalyticsJob` são ambos Azure PowerShell cmdlets para Azure data Lake Analytics na estrutura de Azure Resource Manager. Você necessáriosá uma estação de trabalho com Azure PowerShell instalado. Você pode consultar a [lista](/powershell/module/Az.DataLakeAnalytics) de comandos para obter mais comandos e exemplos.
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Implantar trabalhos do U-SQL por meio do Azure Data Factory

Você pode enviar trabalhos do U-SQL diretamente do Azure Pipelines. Ou você pode carregar os scripts criados para o armazenamento do Azure Data Lake Store ou do Azure Blob e [executar os trabalhos agendados pelo Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

Use a [tarefa do Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) no Azure Pipelines com o exemplo de script a seguir do PowerShell para carregar scripts U-SQL em uma conta do Azure Data Lake Store:

```powershell
<#
   This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.

   Example :
      FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
   Write-Host "************************************************************************"
   Write-Host "Uploading files to $ADLSName"
   Write-Host "***********************************************************************"

   $usqlScripts = GetUsqlFiles

   $files = @(get-childitem $usqlScripts -recurse)
   foreach($file in $files)
   {
      Write-Host "Uploading file: $($file.Name)"
      Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
   }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD para um banco de dados U-SQL

O Azure Data Lake Tools para Visual Studio fornece modelos de projeto de banco de dados U-SQL que ajudam a desenvolver, gerenciar e implantar bancos de dados U-SQL. Saiba mais sobre um [projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Compilar o projeto de banco de dados U-SQL

### <a name="get-the-nuget-package"></a>Obtenha o pacote do NuGet

O MSBuild não oferece suporte interno para projetos de banco de dados U-SQL. Para obter essa habilidade, você precisa adicionar uma referência para sua solução ao pacote [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet que adiciona o serviço de idioma necessário.

Para adicionar a referência de pacote do NuGet, clique com botão direito a solução no Gerenciador de soluções do Visual Studio. Escolha **Gerenciar Pacotes NuGet**. Em seguida, procure e instale o pacote do NuGet. Ou você pode adicionar um arquivo chamado **packages.config** na pasta da solução e colocar o seguinte conteúdo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Construa o U-SQL em um projeto de banco de dados com a linha de comando do MSBuild

Para construir seu projeto de banco de dados U-SQL, chame a linha de comando padrão do MSBuild e passe a referência do pacote NuGet do U-SQL SDK como um argumento adicional. Consulte o seguinte exemplo: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

O argumento `USQLSDKPath=<U-SQL Nuget package>\build\runtime` refere-se ao caminho de instalação do pacote NuGet para o serviço de linguagem U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Integração contínua com o Azure Pipelines

Além da linha de comando, você poderá usar o Build do Visual Studio ou uma tarefa do MSBuild para compilar projetos de banco de dados U-SQL no Azure Pipelines. Para configurar uma tarefa de construção, certifique-se de adicionar duas tarefas no pipeline de construção: uma tarefa de restauração do NuGet e uma tarefa do MSBuild.

   ![Tarefa CI / CD MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Inclua uma tarefa de restauração do NuGet para obter o pacote NuGet referenciado pela solução, que inclui `Azure.DataLake.USQL.SDK`, para que o MSBuild possa localizar os destinos da linguagem U-SQL. Defina **Advanced**  >  o **diretório de destino** avançado como `$(Build.SourcesDirectory)/packages` se desejar usar o exemplo de argumentos do MSBuild diretamente na etapa 2.

   ![Tarefa CI / CD MSBuild para um projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Defina os argumentos do MSBuild nas ferramentas de compilação do Visual Studio ou em uma tarefa do MSBuild, conforme mostrado no exemplo a seguir. Ou, você pode definir variáveis para esses argumentos no pipeline de build do Azure Pipelines.

   ![Definir variáveis CI / CD MSBuild para um projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>Resultado da compilação do projeto de banco de dados U-SQL

A saída de build do projeto de banco de dados U-SQL é um pacote de implantação do banco de dados U-SQL nomeado com o sufixo `.usqldbpack`. O pacote `.usqldbpack` é um arquivo zip que inclui todas as instruções DDL em um único script U-SQL em uma pasta DDL. Inclui todos os arquivos **.dlls** e arquivos adicionais para montagem em uma pasta temporária.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Funções de valor de tabela de teste e procedimentos armazenados

Adicionar casos de teste para funções com valor de tabela e procedimentos armazenados diretamente não é suportado atualmente. Como solução alternativa, você pode criar um projeto U-SQL com scripts U-SQL que chame essas funções e gravar casos de teste para eles. Execute as etapas a seguir para configurar casos de teste para funções com valor de tabela e procedimentos armazenados definidos no projeto de banco de dados U-SQL:

1. Crie um projeto U-SQL para fins de teste e grave scripts U-SQL chamando as funções com valor de tabela e procedimentos armazenados.

1. Adicione uma referência de banco de dados ao projeto U-SQL. Para obter a função com valor de tabela e a definição de procedimento armazenado, é necessário referenciar o projeto de banco de dados que contém a instrução DDL. Saiba mais sobre [referências de banco de dados](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

1. Adicione casos de teste para scripts de U-SQL que chamam procedimentos armazenados e funções com valor de tabela. Aprenda como [adicionar casos de teste para scripts U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Implantar banco de dados U-SQL por meio do Azure Pipelines

`PackageDeploymentTool.exe` fornece as interfaces de programação e de linha de comando que ajudam a implementar pacotes de implementação do banco de dados U-SQL, **.usqldbpack**. O SDK está incluído no [pacote NuGet do U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), localizado em **build / runtime / PackageDeploymentTool.exe**. Usando `PackageDeploymentTool.exe`, você pode implantar bancos de dados U-SQL no Azure Data Lake Analytics e contas locais.

> [!NOTE]
>
> Atualmente, o suporte à linha de comando do PowerShell e suporte à tarefa de lançamento do Azure Pipelines para implantação do banco de dados U-SQL estão pendentes.
>

Execute as etapas a seguir para configurar uma tarefa de implantação de banco de dados no Azure Pipelines:

1. Adicione uma tarefa de script do PowerShell em um pipeline de criação ou lançamento e execute o seguinte script do PowerShell. Esta tarefa ajuda a obter dependências do SDK do Azure para `PackageDeploymentTool.exe` e `PackageDeploymentTool.exe`. Você pode definir os parâmetros **-AzureSDK** e **-DBDeploymentTool** para carregar as dependências e a ferramenta de implementação em pastas específicas. Passar o **- AzureSDK** caminho `PackageDeploymentTool.exe` como o **- AzureSDKPath** parâmetro na etapa 2.

   ```powershell
   <#
      This script is used for getting dependencies and SDKs for U-SQL database deployment.
      PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.

      Example :
          GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
   #>

   param (
      [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
      [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
      [string]$workingfolder = "" # Folder to execute these command lines
   )

   if ([string]::IsNullOrEmpty($workingfolder))
   {
      $scriptpath = $MyInvocation.MyCommand.Path
      $workingfolder = Split-Path $scriptpath
   }
   cd $workingfolder

   echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
   echo "Downloading required packages..."

   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
   iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

   echo "Extracting packages..."

   Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
   Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
   Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
   Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
   Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

   echo "Copy required DLLs to output folder..."

   mkdir $AzureSDK -Force
   mkdir $DBDeploymentTool -Force
   copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
   copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
   copy USQLSDK\build\runtime\*.* $DBDeploymentTool
   ```

2. Adicionar um **tarefa de linha de comando** em um pipeline de build ou versão e preencha o script chamando `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` está localizado sob a definido **$DBDeploymentTool** pasta. O script de exemplo é o seguinte: 

   - Implante um banco de dados U-SQL localmente:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - Use o modo de autenticação interativa para implantar um banco de dados U-SQL em uma conta do Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - Use a autenticação **secreta** para implantar um banco de dados U-SQL em uma conta do Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - Use a autenticação **certFile** para implantar um banco de dados U-SQL em uma conta do Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Descrições de parâmetro PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Parâmetros comuns

| Parâmetro | Descrição | Valor padrão | Obrigatório |
|---------|-----------|-------------|--------|
|Pacote|O caminho do pacote de implantação do banco de dados U-SQL a ser implantado.|null|true|
|Banco de dados|O nome do banco de dados a ser implantado ou criado.|master|false|
|LogFile|O caminho do arquivo para registro. Padrão para saída padrão (console).|null|false|
|LogLevel|Nível de registro: Verbose, Normal, Aviso ou Erro.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parâmetro para implantação local

|Parâmetro|Descrição|Valor padrão|Obrigatório|
|---------|-----------|-------------|--------|
|DataRoot|O caminho da pasta raiz de dados local.|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parâmetros para a implantação do Azure Data Lake Analytics

|Parâmetro|Descrição|Valor padrão|Obrigatório|
|---------|-----------|-------------|--------|
|Conta|Especifica qual conta do Azure Data Lake Analytics será implantada pelo nome da conta.|null|true|
|ResourceGroup|O nome do grupo de recursos do Azure para a conta do Azure Data Lake Analytics.|null|true|
|SubscriptionId|O ID de assinatura do Azure para a conta do Azure Data Lake Analytics.|null|true|
|Locatário|O nome do inquilino é o nome de domínio do Azure AD (Azure Active Directory). Encontre-o na página de gerenciamento de assinaturas no portal do Azure.|null|true|
|AzureSDKPath|Encontre-o na página de gerenciamento de assinaturas no portal do Azure.|null|true|
|Interativo|Se deve ou não usar o modo interativo para autenticação.|false|false|
|ClientId|O ID do aplicativo do Azure AD necessário para autenticação não interativa.|null|Obrigatório para autenticação não interativa.|
|Secrete|O segredo ou senha para autenticação não interativa. Deve ser usado apenas em um ambiente confiável e seguro.|null|Necessário para autenticação não interativa; caso contrário, use o SecreteFile.|
|SecreteFile|O arquivo salva o segredo ou a senha para autenticação não interativa. Certifique-se de mantê-lo legível apenas pelo usuário atual.|null|Necessário para autenticação não interativa ou use o segredo.|
|CertFile|O arquivo salva a certificação X.509 para autenticação não interativa. O padrão é usar a autenticação de segredo do cliente.|null|false|
| JobPrefix | O prefixo para a implementação do banco de dados de um trabalho DDL do U-SQL. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>Próximas etapas

- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Execute o script U-SQL em seu computador local](data-lake-analytics-data-lake-tools-local-run.md).
- [Projeto de banco de dados U-SQL Use para desenvolver o banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).