---
title: Usar identidades gerenciadas para recursos no Azure Cloud Shell
description: Autenticação de código com MSI no Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: 0fb19524079f84e92e1ddbc98a61917026492663
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89469891"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Use identidades gerenciadas para recursos do Azure no Azure Cloud Shell

O Azure Cloud Shell suporta autorização com identidades gerenciadas para recursos do Azure. Utilize essa opção para recuperar tokens de acesso para se comunicar com segurança com os serviços do Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Sobre as identidades gerenciadas dos recursos do Azure
Um desafio comum ao criar aplicativos de nuvem é como gerenciar com segurança as credenciais que precisam estar em seu código para autenticar para serviços de nuvem. No Cloud Shell você precisará autenticar a recuperação do Key Vault para uma credencial que um script possa ser necessário.

Identidades gerenciadas para recursos do Azure facilita a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

## <a name="acquire-access-token-in-cloud-shell"></a>Adquirir o token de acesso no Cloud Shell

Execute os seguintes comandos para definir o token de acesso do MSI como uma variável de ambiente `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Manipulando a expiração do token

O subsistema de MSI local armazena em cache os tokens. Portanto, você pode chamá-lo sempre que desejar e uma chamada durante a transmissão para resultados do Azure AD somente se:
- ocorrer um cache ignorado devido a não haver token no cache
- o token está expirado

Se você armazenar em cache o token em seu código, deverá estar preparado para lidar com cenários em que o recurso indique que o token expirou.

Para tratar erros de token, visite a [página MSI sobre tokens de acesso MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#error-handling).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre MSI](../active-directory/managed-identities-azure-resources/overview.md)  
[Aquisição de tokens de acesso da MSI VMs](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)