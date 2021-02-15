---
title: Atribuir um acesso de identidade gerenciada a um recurso usando o portal do Azure-Azure AD
description: Instruções passo a passo para atribuir uma identidade gerenciada em um acesso de recurso a outro recurso, usando o portal do Azure.
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
ms.openlocfilehash: 6584754edf3ff7ae31c3b9ace72baf16459dbc44
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359979"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Atribuir um acesso de identidade gerenciada a um recurso usando o portal do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Após configurar um recurso do Azure com uma identidade gerenciada, será possível conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este artigo mostra como conceder acesso de identidade gerenciada de uma máquina virtual do Azure ou conjunto de dimensionamento de máquinas virtuais a uma conta de armazenamento do Azure, usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usar o RBAC do Azure para atribuir um acesso de identidade gerenciada a outro recurso

Depois de habilitar a identidade gerenciada em um recurso do Azure, como uma [VM do Azure](qs-configure-portal-windows-vm.md) ou um [conjunto de dimensionamento de máquinas virtuais do Azure](qs-configure-portal-windows-vmss.md):

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure na qual você configurou a identidade gerenciada.

2. Navegue até o recurso desejado no qual você deseja modificar o controle de acesso. Neste exemplo, estamos fornecendo um acesso à máquina virtual do Azure para uma conta de armazenamento, portanto, navegamos até a conta de armazenamento.

3. Selecione a página **IAM (Controle de Acesso)** do recurso e selecione **+ Adicionar atribuição de função**. Em seguida, especifique a **Função** , **Atribuir acesso** e especifique a **Assinatura** correspondente. Na área de critérios de pesquisa, você verá o recurso. Selecione o recurso e selecione **Salvar**. 

   ![Captura de tela de controle de acesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Próximas etapas

- [Identidade gerenciada para visão geral dos recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md).
- Para habilitar a identidade gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure](qs-configure-portal-windows-vmss.md).


