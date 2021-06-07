---
title: Passo a passo do Emparelhamento direto
titleSuffix: Azure
description: Passo a passo do Emparelhamento direto
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592374"
---
# <a name="direct-peering-walkthrough"></a>Passo a passo do Emparelhamento direto

Esta seção explica as etapas que você precisa seguir para configurar e gerenciar um emparelhamento direto.

## <a name="create-a-direct-peering"></a>Criar um emparelhamento Direto
> [!div class="mx-imgBorder"]
> ![Fluxo de trabalho de emparelhamento direto e Estados de conexão](./media/direct-peering.png)

As etapas a seguir devem ser seguidas para provisionar um emparelhamento direto:
1. Examine a [política de emparelhamento](https://peering.azurewebsites.net/peering) da Microsoft para entender os requisitos de emparelhamento direto.
1. Siga as instruções em [criar ou modificar um emparelhamento direto](howto-direct-powershell.md) para enviar uma solicitação de emparelhamento.
1. Depois de enviar uma solicitação de emparelhamento, a Microsoft entrará em contato com o endereço de email registrado para fornecer LOA (carta de autorização) ou para outras informações.
1. Quando a solicitação de emparelhamento é aprovada, o estado da conexão muda para ProvisioningStarted.
1. Você precisa:
    1. concluir a fiação de acordo com o LOA
    1. (opcionalmente) executar o teste de link usando 169.254.0.0/16
    1. Configure a sessão BGP e, em seguida, notifique-nos.
1. A Microsoft provisiona a sessão BGP com a política negar tudo e validar de ponta a ponta.
1. Se for bem-sucedido, você receberá uma notificação informando que o estado da conexão de emparelhamento está ativo.
1. O tráfego será permitido por meio do novo emparelhamento.

Observe que os Estados de conexão não devem ser confundidos com os Estados de sessão [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) padrão.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Converter um Emparelhamento direto herdado para o recurso do Azure
As etapas a seguir devem ser seguidas para converter um emparelhamento direto herdado para o recurso do Azure:
1. Siga as instruções em [converter um emparelhamento direto herdado para o recurso do Azure](howto-legacy-direct-powershell.md)
1. Depois de enviar a solicitação de conversão, a Microsoft examinará a solicitação e entrará em contato com você, se necessário.
1. Depois de aprovado, você verá seu emparelhamento direto com um estado de conexão como ativo.

## <a name="deprovision-direct-peering"></a>Desprovisionar emparelhamento direto
Entre em contato com a equipe de [emparelhamento da Microsoft](mailto:peering@microsoft.com) para desprovisionar o emparelhamento direto.

Quando um emparelhamento direto for definido para desprovisionamento, você verá o estado da conexão como **PendingRemove**

> [!NOTE]
> Se você executar o cmdlet do PowerShell para excluir o emparelhamento direto quando ConnectionState for ProvisioningStarted ou ProvisioningCompleted, a operação falhará.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [pré-requisitos para configurar o emparelhamento com a Microsoft](prerequisites.md).
