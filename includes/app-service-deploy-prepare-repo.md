---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836734"
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
| Python | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| Trabalhos Web | _\<job_name > / run. \<extensão >_ sob _App\_dados/trabalhos/continuous_ para WebJobs contínuos, ou _aplicativo\_dados/trabalhos/triggered_ do disparada Trabalhos Web. Para obter mais informações, consulte [documentação de WebJobs Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funções | Confira [Implantação contínua para Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar a implantação, você pode incluir um arquivo *.deployment* na raiz do repositório. Para obter mais informações, consulte [personalizar implantações](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se você desenvolver no Visual Studio, permita que o [Visual Studio cria um repositório para você](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). O projeto é imediatamente pronto para ser implantado usando o Git.
>

