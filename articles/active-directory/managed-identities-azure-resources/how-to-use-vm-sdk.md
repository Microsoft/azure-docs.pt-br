---
title: Usar identidades gerenciadas em uma VM do Azure com SDKs do Azure-Azure AD
description: Códigos de exemplo para usar SDKs do Azure com uma VM do Azure que gerencia identidades para recursos do Azure.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb5f1d81874c8303d095aec320f60cc9ae6a7dc
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360557"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure com SDKs do Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece uma lista de exemplos de SDK, que demonstram o uso do suporte do respectivo SDK do Azure para identidades gerenciadas para recursos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Todos os scripts/código de exemplos neste artigo assumem que o cliente está executando em uma VM com identidades gerenciadas para recursos do Azure habilitadas. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar identidades gerenciadas para recursos do Azure em uma VM, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) ou um dos artigos variantes (usando PowerShell, CLI, um modelo ou um SDK do Azure). 

## <a name="sdk-code-samples"></a>Exemplos de código do SDK

| .             | Exemplo de código |
| --------------- | ----------- |
| .NET            | [Implantar um modelo do Azure Resource Manager de uma VM do Windows usando identidades gerenciadas para recursos do Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Chamar serviços do Azure de uma VM do Linux usando identidades gerenciadas para recursos do Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Gerenciar recursos usando identidades gerenciadas para recursos do Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Usar identidades gerenciadas para recursos do Azure para autenticar simplesmente de dentro de uma VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gerenciar recursos de uma VM com identidades gerenciadas para recursos do Azure habilitadas](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Próximas etapas

- Consulte [Azure SDKs](https://azure.microsoft.com/downloads/) (SDKs do Azure) para ver a lista completa de recursos de SDK do Azure, incluindo downloads da biblioteca, documentação e mais.
- Para habilitar identidades gerenciadas para recursos do Azure em uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure l](qs-configure-portal-windows-vm.md).








