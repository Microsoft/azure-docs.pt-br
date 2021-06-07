---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: c29c7abf0964edb7a5427d8c2cfe62e066d13bfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900745"
---
Antes de começar, verifique se:

* Sua assinatura do Microsoft Azure está habilitada para um recurso do Azure Stack Edge. Use uma assinatura compatível, como [EA (Contrato Enterprise) da Microsoft](https://azure.microsoft.com/overview/sales-number/), [Programa do CSP (Provedor de Soluções na Nuvem)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Você tem acesso de proprietário ou colaborador no nível do grupo de recursos para os recursos do Azure Stack Edge/Gateway de Armazenamento do Azure, Hub IoT e Armazenamento do Azure.

  * Para criar qualquer recurso do Azure Stack Edge, você deverá ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. Você também precisará verificar se o provedor `Microsoft.DataBoxEdge` está registrado. Para obter informações sobre como realizar o registro, acesse [Registrar o provedor de recursos](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Para criar qualquer recurso do Hub IoT, verifique se o provedor Microsoft.Devices está registrado. Para obter informações sobre como realizar o registro, acesse [Registrar o provedor de recursos](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Para criar um recurso da conta de armazenamento, novamente, você precisará ter acesso de colaborador ou superior com escopo no nível do grupo de recursos. O Armazenamento do Azure é, por padrão, um provedor de recursos registrado.
* Você tem acesso de usuário ou administrador à API do Azure Active Directory Graph. Para obter mais informações, confira [API do Azure Active Directory Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.