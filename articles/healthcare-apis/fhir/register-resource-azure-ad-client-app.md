---
title: Registrar um aplicativo de recurso no Azure AD – API do Azure para FHIR
description: Registrar um aplicativo de recurso (ou API) no Azure Active Directory, para que os aplicativos cliente possam solicitar acesso ao recurso ao autenticar.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017560"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrar um aplicativo de recurso no Azure Active Directory

Neste artigo, você aprenderá a registrar um aplicativo de recurso (ou API) no Azure Active Directory. Um aplicativo de recurso é uma representação Azure Active Directory da API do servidor FHIR e os aplicativos cliente podem solicitar acesso ao recurso durante a autenticação. O aplicativo de recurso também é conhecido como o *público* em linguagem OAuth.

## <a name="azure-api-for-fhir"></a>API do Azure para FHIR

Se você estiver usando a API do Azure para FHIR, um aplicativo de recurso será criado automaticamente quando você implantar o serviço. Contanto que você esteja usando a API do Azure para FHIR no mesmo locatário Azure Active Directory em que está implantando seu aplicativo, você pode ignorar este guia e, em vez disso, implantar sua API do Azure para FHIR para começar.

Se você estiver usando um locatário de Azure Active Directory diferente (não associado à sua assinatura), poderá importar a API do Azure para o aplicativo de recurso FHIR em seu locatário com o PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

ou você pode usar CLI do Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Servidor FHIR para o Azure

Se você estiver usando o servidor de FHIR de software livre para o Azure, siga as etapas no [repositório do GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) para registrar um aplicativo de recurso. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como registrar um aplicativo de recurso no Azure Active Directory. Em seguida, Registre seu aplicativo cliente confidencial.
 
>[!div class="nextstepaction"]
>[Registrar aplicativo cliente confidencial](register-confidential-azure-ad-client-app.md)