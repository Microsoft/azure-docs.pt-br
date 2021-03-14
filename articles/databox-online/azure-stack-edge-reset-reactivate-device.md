---
title: Redefinir e reativar seu dispositivo pro Edge Azure Stack | Microsoft Docs
description: Saiba como apagar os dados de e reativar seu dispositivo Azure Stack Edge pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2020
ms.author: alkohli
ms.openlocfilehash: 95fbe5dc4a934fee10c558f640bc24f261203a33
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102443484"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Redefinir e reativar o Azure Stack dispositivo pro Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como redefinir, reconfigurar e reativar um dispositivo Azure Stack Edge pro se você estiver tendo problemas com o dispositivo ou precisar iniciar de novo por algum outro motivo.

Depois de redefinir o dispositivo para remover os dados, você precisará reativar o dispositivo como um novo recurso. A redefinição de um dispositivo remove a configuração do dispositivo, portanto, você precisará reconfigurar o dispositivo por meio da interface do usuário da Web local.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Apagar os dados dos discos de dados no dispositivo
> * Reativar o dispositivo criando um novo pedido, reconfigurando o dispositivo e ativando-o

## <a name="reset-data-from-the-device"></a>Redefinir dados do dispositivo

Para apagar os dados dos discos de dados do seu dispositivo, você precisa redefinir o dispositivo.

Antes de redefinir, crie uma cópia dos dados locais no dispositivo, se necessário. Você pode copiar os dados do dispositivo para um contêiner do Armazenamento do Microsoft Azure.

>[!IMPORTANT]
> A redefinição do dispositivo apagará todos os dados e cargas de trabalho locais do seu dispositivo e isso não poderá ser revertido. Redefina o dispositivo somente se você quiser iniciar o novamente com o dispositivo.

Você pode redefinir seu dispositivo na interface do usuário da Web local ou no PowerShell. Para obter instruções sobre o PowerShell, consulte [redefinir seu dispositivo](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>Reativar dispositivo

Depois de redefinir o dispositivo, você precisará reativar o dispositivo como um novo recurso. Depois de inserir um novo pedido, você precisará reconfigurar e reativar o novo recurso.

Para reativar o dispositivo existente, siga estas etapas:

1. Crie um novo pedido para o dispositivo existente seguindo as etapas em [criar um novo recurso](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource). Na guia **endereço para entrega** , selecione **já tenho um dispositivo**.

   ![Não especifique nenhum novo dispositivo no endereço de envio](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Obtenha a chave de ativação](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key).

1. [Conecte-se ao dispositivo](azure-stack-edge-gpu-deploy-connect.md).

1. [Configure a rede para o dispositivo](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Definir configurações do dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Configurar certificados](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Ative o dispositivo](azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [se conectar a um dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-connect.md).
