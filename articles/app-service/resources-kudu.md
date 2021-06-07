---
title: Visão geral do serviço kudu
description: Saiba mais sobre o mecanismo que capacita a implantação contínua no serviço de aplicativo e seus recursos.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608968"
---
# <a name="kudu-service-overview"></a>Visão geral do serviço kudu

Kudu é o mecanismo por trás de vários recursos do [Azure app serviço](overview.md) relacionado à implantação baseada em controle do código-fonte e outros métodos de implantação, como o Dropbox e o onedrive Sync. 

## <a name="access-kudu-for-your-app"></a>Acesse o kudu para seu aplicativo
Sempre que você criar um aplicativo, o serviço de aplicativo criará um aplicativo complementar para ele protegido por HTTPS. Este aplicativo kudu pode ser acessado em:

- O aplicativo não está na camada isolada: `https://<app-name>.scm.azurewebsites.net`
- Aplicativo na camada isolada (Ambiente do Serviço de Aplicativo): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Para obter mais informações, consulte [acessando o serviço kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Recursos do kudu

O kudu fornece informações úteis sobre o aplicativo do serviço de aplicativo, como:

- Configurações de aplicativo
- Cadeias de conexão
- Variáveis de ambiente
- Variáveis de servidor
- Cabeçalhos HTTP

Ele também fornece outros recursos, como:

- Execute comandos no [console do kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Baixe os despejos de diagnóstico do IIS ou os logs do Docker.
- Gerencie processos e extensões de site do IIS.
- Adicionar WebHooks de implantação para APS do Windows.
- Permitir interface do usuário de implantação ZIP com `/ZipDeploy` .
- Gera [scripts de implantação personalizados](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Permite o acesso com a [API REST](https://github.com/projectkudu/kudu/wiki/REST-API).

## <a name="more-resources"></a>Mais recursos

Kudu é um [projeto](https://github.com/projectkudu/kudu)de software livre e tem sua documentação em [kudu wiki](https://github.com/projectkudu/kudu/wiki).

