---
title: "Como usar as ferramentas Stream Analytics do Visual Studio para configurar o processo de implantação e integração contínua | Microsoft Docs"
description: "Tutorial sobre como usar as ferramentas Stream Analytics do Visual Studio para configurar o processo de implantação e integração contínua"
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 947266dc94babab21556da26d8cc8c917cf81c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-the-continuous-integration-and-deployment-process"></a>Usar as ferramentas Stream Analytics do Visual Studio para configurar o processo de implantação e integração contínua
Neste tutorial, você aprende a usar as ferramentas Stream Analytics do Visual Studio para configurar o processo de implantação e integração contínua.

A versão mais recente (2.3.0000.0 ou superior) das [ferramentas de Stream Analytics para Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio) adiciona o suporte para **MSBuild**. 

Há também um pacote NuGet recém-lançado [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Ele fornece as ferramentas de MSBuild, execução local e implantação que dão suporte ao processo de implantação e integração contínua de projetos do Stream Analytics para Visual Studio. 
> [!NOTE] 
O pacote NuGet só pode ser usado com o 2.3.0000.0 ou versão superior das ferramentas de Stream Analytics para Visual Studio. Se você tiver projetos criados em versões anteriores das ferramentas do Visual Studio, basta abri-los com o 2.3.0000.0 ou versão superior e salvar. Assim, você terá os novos recursos habilitados. 

[Saiba como usar o Stream Analytics Tools para Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="msbuild"></a>MSBuild
Assim como com a experiência padrão do MSBuild para Visual Studio, para compilar um projeto, você pode clicar com o botão direito no projeto e escolhe **Compilar**, ou usar **MSBuild** no pacote NuGet na linha de comando.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Após a compilação bem-sucedida de um projeto do Stream Analytics para Visual Studio, ele gera os dois arquivos de modelo do Azure Resource Manager a seguir na pasta **bin/[Depuração/Varejo]/Implantar**: 

Arquivo de modelo do Azure Resource Manager.
*       [ProjectName].JobTemplate.json 

Arquivo de parâmetros do Azure Resource Manager.
*       [ProjectName].JobTemplate.parameters.json   

Os parâmetros padrão no arquivo parameters.json das configurações em seu projeto do Visual Studio. Se você deseja implantar em outro ambiente, basta substituir os parâmetros adequadamente. 
> [!NOTE] 
Para todas as credenciais, os valores padrão são definidos como nulo. Eles **PRECISAM** ser definidos antes de implantá-los na nuvem.
```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre [Como implantar com o arquivo de modelo do Azure Resource Manager e o Azure PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy) e [Como usar um objeto como um parâmetro em um modelo do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Ferramenta de linha de comando

### <a name="build-the-project"></a>Compilar o projeto
No pacote NuGet, há uma ferramenta de linha de comando chamada **SA.exe**. Ela dá suporte à compilação do projeto, teste local em uma máquina aleatória, que você pode usar em seu processo de integração contínua e de entrega contínua. 

Por padrão, os arquivos de implantação são colocados no diretório atual. Você pode especificar o caminho de saída pelo parâmetro -OutputPath.

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testar o script localmente

Se o projeto tiver especificado arquivos de entrada locais no Visual Studio, você pode executar o teste de script automatizado usando o comando *localrun*. O resultado da saída é colocado no diretório atual.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-job-definition-file-to-use-with-stream-analytics-power-shell"></a>Gere o arquivo de definição de trabalho para usar com o Power Shell do Stream Analytics.

O comando *arm* usa o modelo de trabalho e os arquivos de parâmetro de modelo do trabalho gerados por meio da compilação como entrada. Em seguida, os combina em um arquivo JSON de definição de trabalho que pode ser usado com a API do PowerShell do Stream Analytics.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```

```
Example
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


