---
title: incluir arquivo
description: arquivo de inclusão para páginas de aterrissagem do cenário de cliente confidencial (daemon, aplicativo Web, API da Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995992"
---
## <a name="add-a-client-secret-or-certificate"></a>Adicionar um certificado ou segredo do cliente

Assim como ocorre com qualquer aplicativo cliente confidencial, você precisa adicionar um segredo ou certificado para agir como *as credenciais* desse aplicativo, para que ele possa autenticar-se como ele mesmo, sem a interação do usuário.

Você pode adicionar credenciais ao registro do aplicativo cliente usando o [portal do Azure](#add-client-credentials-by-using-the-azure-portal) ou uma ferramenta de linha de comando como o [PowerShell](#add-client-credentials-by-using-powershell).

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Adicionar credenciais de cliente usando o portal do Azure

Para adicionar credenciais ao registro de aplicativo do seu aplicativo cliente confidencial, siga as etapas em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](../articles/active-directory/develop/quickstart-register-app.md) para o tipo de credencial que você deseja adicionar:

* [Adicionar um segredo do cliente](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Adicionar um certificado](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Adicionar credenciais de cliente usando o PowerShell

Como alternativa, você pode adicionar credenciais ao registrar seu aplicativo com a plataforma de identidade da Microsoft usando o PowerShell.

O exemplo de código [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) no GitHub mostra como adicionar um segredo de aplicativo ou certificado ao registrar um aplicativo:

- Para obter detalhes sobre como adicionar um **segredo do cliente** com o PowerShell, consulte [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Para obter detalhes sobre como adicionar um **certificado** com o PowerShell, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
