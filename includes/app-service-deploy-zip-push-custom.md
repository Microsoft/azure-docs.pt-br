---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018404"
---
## <a name="deployment-customization"></a>Personalização da implantação

O processo de implantação presume que o arquivo .zip em que você efetua push contém um aplicativo pronto para execução. Por padrão, nenhuma personalização é executada. Para habilitar os mesmos processos de compilação que você obtém com a integração contínua, adicione o seguinte para as configurações do aplicativo:

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Quando você usar a implantação por push do .zip, essa configuração é **falsa** por padrão. O padrão é **verdadeiro** para implantações de integração contínua. Quando definido como **verdadeiras**, as configurações de implantação são usadas durante a implantação. Você pode configurar essas configurações como configurações de aplicativo ou em um arquivo de configuração de .deployment localizado na raiz do seu arquivo .zip. Para obter mais informações, consulte [Repositório e configurações relacionadas à implantação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) na referência de implantação.