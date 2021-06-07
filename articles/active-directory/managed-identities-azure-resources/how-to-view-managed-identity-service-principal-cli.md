---
title: Exibir a entidade de serviço de uma identidade gerenciada-CLI do Azure-Azure AD
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92892036"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. É possível usar essa identidade para autenticar qualquer serviço que dê suporte à autenticação do Azure AD, sem a necessidade de ter credenciais em seu código. 

Neste artigo, você aprende como exibir a entidade de serviço de uma identidade gerenciada usando CLI do Azure.

Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com as identidades gerenciadas dos recursos do Azure, confira [O que são as identidades gerenciadas dos recursos do Azure?](overview.md).

- Habilite a [identidade designada pelo sistema em uma máquina virtual](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) ou [aplicativo](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

Este comando a seguir demonstra como exibir a entidade de serviço de uma VM ou aplicativo com identidade gerenciada habilitada. Substitua `<Azure resource name>` pelos seus próprios valores.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como gerenciar entidades de serviço do Azure AD usando CLI do Azure, consulte [az ad sp](/cli/azure/ad/sp).
