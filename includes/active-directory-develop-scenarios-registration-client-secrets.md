---
title: Arquivo de inclusão
description: arquivo de inclusão para páginas de aterrissagem do cenário de cliente confidencial (daemon, aplicativo Web, API da Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: cd37880be6d518105e880b93a0bd748f7c729d88
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286323"
---
## <a name="registration-of-secrets-or-certificates"></a>Registro de segredos ou certificados

Assim como para qualquer aplicativo cliente confidencial, você precisa registrar um segredo ou certificado. Você pode registrar os segredos do aplicativo por meio da experiência interativa no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)ou usando as ferramentas de linha de comando (como o PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registrando segredos do cliente usando o portal de registro de aplicativos

O gerenciamento de credenciais do cliente ocorre na página **certificados & segredos** de um aplicativo:

![imagem](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- o segredo do aplicativo (também chamado de segredo do cliente) é gerado pelo AD do Azure, durante o registro do aplicativo cliente confidencial. Essa geração acontece quando você seleciona **novo segredo do cliente**. Nesse ponto, você deve copiar a cadeia de caracteres secreta na área de transferência para uso em seu aplicativo, antes de selecionar **salvar**. Essa cadeia de caracteres não será mais apresentada.
- o certificado é carregado no registro do aplicativo usando o botão **carregar certificado** . O Azure AD dá suporte apenas a certificados registrados diretamente no aplicativo e não seguem cadeias de certificados.

Para obter detalhes, [consulte início rápido: Configurar um aplicativo cliente para acessar APIs Web | Adicionar credenciais ao seu aplicativo](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>Registrando segredos do cliente usando o PowerShell

Como alternativa, você pode registrar seu aplicativo com o Azure AD usando ferramentas de linha de comando. O exemplo [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) mostra como registrar um segredo de aplicativo ou um certificado com um aplicativo do Azure AD:

- Para obter detalhes sobre como registrar um segredo de aplicativo, consulte [AppCreationScripts/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Para obter detalhes sobre como registrar um certificado com o aplicativo, consulte [AppCreationScripts-withCert/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
