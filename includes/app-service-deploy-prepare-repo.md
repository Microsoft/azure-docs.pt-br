---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df987d1e13cb5330842fbab41dae96b24b581ddb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765526"
---
## <a name="prepare-your-repository"></a>Preparar o repositório

Para obter os builds automáticos do servidor de build Kudu do serviço de aplicativo do Azure, certifique-se de que a raiz do repositório tenha os arquivos corretos em seu projeto.

| Tempo de execução | Arquivos do diretório raiz |
|-|-|
| ASP.NET (somente Windows) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Ruby (somente Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ ou _package.json_ com um script de início |
| Python (somente Windows) | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| Trabalhos Web | _\<nome_trabalho>/run.\<extension>_ em _App\_Data/jobs/continuous_ (para WebJobs contínuos) ou _App\_Data/jobs/triggered_ (para WebJobs disparados). Para obter mais informações, consulte [documentação de WebJobs Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Confira [Implantação contínua para Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Para personalizar a implantação, você pode incluir um arquivo _.deployment_ na raiz do repositório. Para obter mais informações, consulte [personalizar implantações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se você desenvolver no Visual Studio, permita que o [Visual Studio cria um repositório para você](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). O projeto é imediatamente pronto para ser implantado usando o Git.
>
>

