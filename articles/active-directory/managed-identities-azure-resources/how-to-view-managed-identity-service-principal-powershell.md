---
title: Exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell-Azure AD
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15ccc0faa4d74a2ef95aca00a6257f27b9a209c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611938"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende a exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Caso ainda não tenha uma conta do Azure, [inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite a [identidade designada pelo sistema em uma máquina virtual](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) ou [aplicativo](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) e, em seguida, entre no Azure usando `Connect-AzAccount`.

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

O comando a seguir demonstra como exibir a entidade de serviço de um aplicativo ou VM com identidade atribuída pelo sistema habilitada. Substitua `<Azure resource name>` pelos seus próprios valores.

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <Azure resource name>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como exibir entidades de serviço do Azure AD usando o PowerShell, confira [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).
