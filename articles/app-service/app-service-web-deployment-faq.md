---
title: "Perguntas frequentes sobre implantação de aplicativos Web do Azure | Microsoft Docs"
description: "Obtenha respostas para perguntas frequentes sobre a implantação para o recurso Aplicativos Web do Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: genli
ms.openlocfilehash: daecef7962ddee2f5e4b6a36c418619285f3ba5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Perguntas frequentes sobre implantação de Aplicativos Web no Azure

Este artigo apresenta respostas para perguntas frequentes sobre problemas de implantação do [recurso Aplicativos Web do Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Estou começando a usar os aplicativos Web do Serviço de Aplicativo. Como fazer para publicar meu código?

Estas são algumas opções para publicar seu código do aplicativo Web:

*   Implante usando o Visual Studio. Se você tiver a solução Visual Studio, clique com o botão direito do mouse no projeto de aplicativo Web e, em seguida, selecione **Publicar**.
*   Implante usando um cliente FTP. No portal do Azure, baixe o perfil de publicação do aplicativo Web no qual você deseja implantar o código. Em seguida, carregue os arquivos em \site\wwwroot usando as mesmas credenciais FTP do perfil de publicação.

Para obter mais informações, consulte [Implantar seu aplicativo no Serviço de Aplicativo](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Recebi uma mensagem de erro ao tentar implantar por meio do Visual Studio. Como resolver isso?

Se você receber a seguinte mensagem de erro, talvez você esteja usando uma versão mais antiga do SDK: “Erro durante a implantação do recurso 'YourResourceName' no grupo de recursos 'YourResourceGroup': MissingRegistrationForLocation: a assinatura não está registrada para o tipo de recurso 'components' na localização 'EUA Central'. Registre-se novamente nesse provedor para ter acesso a essa localização”. 

Para resolver esse erro, atualize para o [último SDK](https://azure.microsoft.com/downloads/). Se você receber essa mensagem e tiver o último SDK, envie uma solicitação de suporte.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Como fazer para implantar um aplicativo ASP.NET por meio do Visual Studio no Serviço de Aplicativo?
<a id="deployasp"></a>

O tutorial [Criar seu primeiro aplicativo Web ASP.NET no Azure em cinco minutos](app-service-web-get-started-dotnet.md) mostra como implantar um aplicativo Web ASP.NET em um aplicativo Web no Serviço de Aplicativo usando o Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quais são os diferentes tipos de credenciais de implantação?

O Serviço de Aplicativo dá suporte a dois tipos de credenciais para a implantação local do Git e a implantação de FTP/S. Para obter mais informações sobre como configurar as credenciais de implantação, consulte [Configurar as credenciais de implantação para o Serviço de Aplicativo](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Qual é a estrutura de arquivos ou de diretórios de meu aplicativo Web do Serviço de Aplicativo?

Para obter informações sobre a estrutura de arquivos do aplicativo do Serviço de Aplicativo, consulte [Estrutura de arquivos no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Como fazer para resolver o “Erro de FTP 550 – não há espaço suficiente em disco” quando tento usar o FTP para meus arquivos?

Se você receber essa mensagem, é provável que você esteja atingindo uma cota de disco no plano de serviço do aplicativo Web. Talvez você precise escalar verticalmente para uma camada de serviço superior de acordo com suas necessidades de espaço em disco. Para obter mais informações sobre planos de preços e limites de recursos, consulte [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Como fazer para configurar a implantação contínua em meu aplicativo Web do Serviço de Aplicativo?

Configure a implantação contínua por meio de vários recursos, incluindo o Visual Studio Team Services, OneDrive, GitHub, Bitbucket, Dropbox e outros repositórios Git. Essas opções estão disponíveis no portal. [Implantação contínua para o Serviço de Aplicativo](app-service-continuous-deployment.md) é um tutorial útil que explica como configurar a implantação contínua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Como fazer para solucionar problemas com a implantação contínua do GitHub e do Bitbucket?

Para obter ajuda sobre como investigar problemas com a implantação contínua do GitHub ou do Bitbucket, consulte [Investigando a implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Não consigo usar o FTP em meu site nem publicar meu código. Como resolver isso?

Para resolver problemas de FTP:

1. Verifique se você está inserindo o nome do host e as credenciais corretas. Para obter informações detalhadas sobre os diferentes tipos de credenciais e como usá-los, consulte [Credenciais de implantação](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Verifique se as portas FTP não estão bloqueadas por um firewall. As portas devem ter essas configurações:
    * Porta de conexão de controle FTP: 21
    * Porta de conexão de dados FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Como fazer para publicar meu código no Serviço de Aplicativo?

O Guia de início rápido do Azure foi projetado para ajudá-lo a implantar seu aplicativo usando a pilha de implantação e o método de sua escolha. Para usar o Guia de início rápido, no portal do Azure, acesse **Configurações** > **Implantação de Aplicativo**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Por que meu aplicativo às vezes reinicia após a implantação no Serviço de Aplicativo?

Para saber mais sobre as circunstâncias nas quais uma implantação de aplicativo pode resultar em uma reinicialização, consulte [Implantação versus problemas de tempo de execução](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Como o artigo descreve, o Serviço de Aplicativo implanta arquivos na pasta wwwroot. Ele nunca reinicia o aplicativo diretamente.

## <a name="how-do-i-integrate-visual-studio-team-services-code-with-app-service"></a>Como fazer para integrar o código do Visual Studio Team Services ao Serviço de Aplicativo?

Você tem duas opções para usar a implantação contínua com o Visual Studio Team Services:

*   Use um projeto Git. Conecte-se por meio do Serviço de Aplicativo usando as opções de implantação desse repositório.
*   Use um projeto TFVC (Controle de Versão do Team Foundation). Implante usando o agente de build do Serviço de Aplicativo.

A implantação contínua de código para essas duas opções depende dos fluxos de trabalho existentes do desenvolvedor e dos procedimentos de check-in. Para obter mais informações, consulte estes artigos: 

*   [Implementar a implantação contínua do aplicativo em um site do Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Configurar uma conta do Visual Studio Team Services para que ela possa ser implantada em um aplicativo Web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Como fazer para usar o FTP ou o FTPS para implantar meu aplicativo no Serviço de Aplicativo?

Para obter informações sobre como usar o FTP ou o FTPS para implantar o aplicativo Web no Serviço de Aplicativo, consulte [Implantar o aplicativo no Serviço de Aplicativo usando o FTP/S](app-service-deploy-ftp.md).
