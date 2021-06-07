---
title: Perguntas frequentes sobre implantação – Serviço de Aplicativo do Azure | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre a implantação para o recurso Aplicativos Web do Serviço de Aplicativo do Azure.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88080466"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Perguntas frequentes sobre implantação de Aplicativos Web no Azure

Este artigo apresenta respostas para perguntas frequentes sobre problemas de implantação do [recurso Aplicativos Web do Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Estou começando a usar os aplicativos Web do Serviço de Aplicativo. Como fazer para publicar meu código?

Estas são algumas opções para publicar seu código do aplicativo Web:

*   Implante usando o Visual Studio. Se você tiver a solução Visual Studio, clique com o botão direito do mouse no projeto de aplicativo Web e, em seguida, selecione **Publicar**.
*   Implante usando um cliente FTP. No portal do Azure, baixe o perfil de publicação do aplicativo Web no qual você deseja implantar o código. Em seguida, carregue os arquivos em \site\wwwroot usando as mesmas credenciais FTP do perfil de publicação.

Para obter mais informações, consulte [Implantar seu aplicativo no Serviço de Aplicativo](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Recebi uma mensagem de erro ao tentar implantar por meio do Visual Studio. Como fazer resolver esse erro?

Se você receber a seguinte mensagem de erro, talvez você esteja usando uma versão mais antiga do SDK: “Erro durante a implantação do recurso 'YourResourceName' no grupo de recursos 'YourResourceGroup': MissingRegistrationForLocation: a assinatura não está registrada para o tipo de recurso 'components' na localização 'EUA Central'. Registre-se novamente para esse provedor para ter acesso a esse local. " 

Para resolver esse erro, atualize para o [último SDK](https://azure.microsoft.com/downloads/). Se você receber essa mensagem e tiver o último SDK, envie uma solicitação de suporte.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Como fazer para implantar um aplicativo ASP.NET por meio do Visual Studio no Serviço de Aplicativo?
<a id="deployasp"></a>

O tutorial [criar seu primeiro aplicativo web ASP.net no Azure em cinco minutos](quickstart-dotnetcore.md) mostra como implantar um aplicativo Web ASP.net em um aplicativo Web no serviço de aplicativo usando o Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quais são os diferentes tipos de credenciais de implantação?

O Serviço de Aplicativo dá suporte a dois tipos de credenciais para a implantação local do Git e a implantação de FTP/S. Para obter mais informações sobre como configurar as credenciais de implantação, consulte [Configurar as credenciais de implantação para o Serviço de Aplicativo](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Qual é a estrutura de arquivos ou de diretórios de meu aplicativo Web do Serviço de Aplicativo?

Para obter informações sobre a estrutura de arquivos do aplicativo do Serviço de Aplicativo, consulte [Estrutura de arquivos no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Como fazer para resolver o “Erro de FTP 550 – não há espaço suficiente em disco” quando tento usar o FTP para meus arquivos?

Se você vir essa mensagem, é provável que você esteja executando uma cota de disco no plano de serviço para seu aplicativo Web. Talvez você precise escalar verticalmente para uma camada de serviço superior de acordo com suas necessidades de espaço em disco. Para obter mais informações sobre planos de preços e limites de recursos, consulte [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Como fazer para configurar a implantação contínua em meu aplicativo Web do Serviço de Aplicativo?

É possível configurar a implantação contínua por meio de vários recursos, incluindo o Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox e outros repositórios Git. Essas opções estão disponíveis no portal. [Implantação contínua para o Serviço de Aplicativo](deploy-continuous-deployment.md) é um tutorial útil que explica como configurar a implantação contínua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Como fazer para solucionar problemas com a implantação contínua do GitHub e do Bitbucket?

Para obter ajuda sobre como investigar problemas com a implantação contínua do GitHub ou do Bitbucket, consulte [Investigando a implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Não consigo usar o FTP em meu site nem publicar meu código. Como resolver isso?

Para resolver problemas de FTP:

1. Verifique se você está inserindo o nome de host e as credenciais corretas. Para obter informações detalhadas sobre os diferentes tipos de credenciais e como usá-los, consulte [Credenciais de implantação](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Verifique se as portas FTP não estão bloqueadas por um firewall. As portas devem ter essas configurações:
    * Porta de conexão de controle FTP: 21
    * Porta de conexão de dados FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Como fazer para publicar meu código no Serviço de Aplicativo?

O Guia de início rápido do Azure foi projetado para ajudá-lo a implantar seu aplicativo usando a pilha de implantação e o método de sua escolha. Para usar o Início rápido, no portal do Azure, vá para o serviço do seu aplicativo, em **Implantação**, selecione **Início Rápido**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Por que meu aplicativo às vezes reinicia após a implantação no Serviço de Aplicativo?

Para saber mais sobre as circunstâncias nas quais uma implantação de aplicativo pode resultar em uma reinicialização, consulte [Implantação versus problemas de runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Como o artigo descreve, o Serviço de Aplicativo implanta arquivos na pasta wwwroot. Ele nunca reinicia o aplicativo diretamente.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Como integrar o código do Azure DevOps com o Serviço de Aplicativo?

Você tem duas opções para usar a implantação contínua com o Azure DevOps:

*   Use um projeto Git. Conecte-se por meio do serviço de aplicativo usando o centro de implantação.
*   Use um projeto TFVC (Controle de Versão do Team Foundation). Implante usando o agente de build do Serviço de Aplicativo.

A implantação contínua de código para essas duas opções depende dos fluxos de trabalho existentes do desenvolvedor e dos procedimentos de check-in. Para obter mais informações, confira estes tópicos: 

*   [Implementar a implantação contínua do aplicativo em um site do Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Configurar uma organização do Azure DevOps para que ela possa implantar em um aplicativo Web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Como fazer para usar o FTP ou o FTPS para implantar meu aplicativo no Serviço de Aplicativo?

Para obter informações sobre como usar o FTP ou o FTPS para implantar o aplicativo Web no Serviço de Aplicativo, consulte [Implantar o aplicativo no Serviço de Aplicativo usando o FTP/S](deploy-ftp.md).
