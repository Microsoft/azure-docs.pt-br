---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: aed23bf5008f850b19a1e5a8c7b0ff21f329eb3a
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560480"
---
## <a name="prepare-your-repository"></a>Preparar o repositório

Para obter builds automáticos do servidor de build do Serviço de Aplicativo do Azure, verifique se a raiz do repositório tem os arquivos corretos no projeto.

| Runtime | Arquivos do diretório raiz |
|-|-|
| ASP.NET (somente Windows) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Ruby (somente Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ ou _package.json_ com um script de início |
| Python | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ em _App\_Data/jobs/continuous_ para WebJobs contínuos ou _App\_Data/jobs/triggered_ para WebJobs disparados. Para mais informações, confira a [Documentação de WebJobs do Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Confira [Implantação contínua para Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar a implantação, você pode incluir um arquivo *.deployment* na raiz do repositório. Para mais informações, confira [Personalizar implantações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [Script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se você desenvolver no Visual Studio, permita que o [Visual Studio cria um repositório para você](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio&preserve-view=true). O projeto estará imediatamente pronto para ser implantado usando o Git.
>

