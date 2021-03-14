---
title: Substituir seu dispositivo pro Edge Azure Stack | Microsoft Docs
description: Descreve como obter um dispositivo Azure Stack Edge pro de substituição.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2196c9463569dc43092b38de58e0103104efed0c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443464"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Substitua seu dispositivo pro Edge Azure Stack

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como substituir seu dispositivo pro Edge Azure Stack. Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. 


Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Abrir um tíquete de suporte para o problema de hardware
> * Criar um novo pedido para um dispositivo de substituição no portal do Azure
> * Instalar, ativar o dispositivo de substituição
> * Retornar o dispositivo original

## <a name="open-a-support-ticket"></a>Abrir um tíquete de suporte

Se o dispositivo existente tiver uma falha de hardware, abra um tíquete de suporte seguindo estas etapas:

1. Abra um tíquete de suporte com Suporte da Microsoft indicando que você deseja devolver o dispositivo. Selecione o tipo de problema de **hardware do Azure Stack Edge pro** e escolha o subtipo de **problemas de hardware** .  

    ![Abrir tíquete de suporte](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Um engenheiro de Suporte da Microsoft entrará em contato com você para determinar se uma FRU (unidade de substituição de campo) pode corrigir o problema e está disponível para essa instância. Se uma FRU não estiver disponível ou o dispositivo precisar de uma atualização de hardware, o suporte o guiará a fazer um novo pedido e devolver o dispositivo antigo.

## <a name="create-a-new-order"></a>Criar um novo pedido

Crie um novo recurso para a ativação do seu dispositivo de substituição seguindo as etapas em [criar um novo recurso](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

> [!NOTE]
> Não há suporte para a ativação de um dispositivo de substituição em relação a um recurso existente. O novo recurso é considerado um novo pedido. Você começará a ser cobrado 14 dias depois que o dispositivo for enviado para você.

## <a name="install-and-activate-the-replacement-device"></a>Instalar e ativar o dispositivo de substituição

Siga estas etapas para instalar e ativar o dispositivo de substituição:

1. [Instale seu dispositivo](azure-stack-edge-deploy-install.md).
2. [Ative seu dispositivo](azure-stack-edge-deploy-connect-setup-activate.md) com o novo recurso que você criou anteriormente.

## <a name="return-your-existing-device"></a>Retornar o dispositivo existente

Siga todas as etapas para devolver o dispositivo original:

1. [Apague os dados no dispositivo](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Inicie o retorno de dispositivo](azure-stack-edge-return-device.md#initiate-device-return) para o dispositivo original.
3. [Agende uma retirada](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Depois que o dispositivo for recebido na Microsoft, você poderá [excluir o recurso](azure-stack-edge-return-device.md#delete-the-resource) associado ao dispositivo retornado.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [retornar um dispositivo Azure Stack Edge pro](azure-stack-edge-return-device.md).
