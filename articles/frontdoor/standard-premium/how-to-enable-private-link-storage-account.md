---
title: Conectar a porta frontal do Azure Premium a uma origem da conta de armazenamento com link privado
titleSuffix: Azure Private Link
description: Saiba como conectar sua Azure front door Premium a uma conta de armazenamento de forma privada.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201661"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Conectar a porta frontal do Azure Premium a uma origem da conta de armazenamento com link privado

Este artigo irá orientá-lo sobre como configurar a SKU Premium da porta do Azure para se conectar à sua origem da conta de armazenamento de forma privada usando o serviço de vínculo privado do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Habilitar o link privado para uma conta de armazenamento
 
Nesta seção, você mapeará o serviço de vínculo privado para um ponto de extremidade privado criado na rede privada da porta de frente do Azure. 

1. No perfil Premium da porta de recepção do Azure, em *configurações*, selecione **grupos de origem**.

1. Selecione o grupo de origem que contém a origem da conta de armazenamento para a qual você deseja habilitar o link privado.

1. Selecione **+ Adicionar uma origem** para adicionar uma nova origem da conta de armazenamento ou selecione uma origem da conta de armazenamento criada anteriormente na lista.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Captura de tela da habilitação do link privado para uma conta de armazenamento.":::

1. Para **selecionar um recurso do Azure**, selecione **em meu diretório**. Selecione ou insira as configurações a seguir para configurar o site ao qual você deseja que o Azure front door Premium se conecte com particular.

    | Configuração | Valor |
    | ------- | ----- |
    | Região | Selecione a região que é a mesma ou mais próxima de sua origem. |
    | Tipo de recurso | Selecione **Microsoft. Storage/storageAccounts**. |
    | Recurso | Selecione sua conta de armazenamento. |
    | Sub-recurso de destino | Você pode selecionar *blob* ou *Web*. |
    | Mensagem de solicitação | Personalize a mensagem ou escolha o padrão. |

1. Em seguida, selecione **Adicionar** para salvar sua configuração.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Aprovar conexão de ponto de extremidade privada da conta de armazenamento

1. Acesse a conta de armazenamento para a qual você configurou o link privado na última seção. Selecione **rede** em **configurações**.

1. Em **rede**, selecione **conexões de ponto de extremidade privadas**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Captura de tela das configurações de rede em um aplicativo Web.":::

1. Selecione a solicitação de ponto de extremidade privada *pendente* da porta de início do Azure Premium e selecione **aprovar**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Captura de tela da solicitação de ponto de extremidade particular do armazenamento pendente.":::

1. Depois de aprovado, ele deve ser semelhante à captura de tela abaixo. Levará alguns minutos para que a conexão seja estabelecida completamente. Agora você pode acessar sua conta de armazenamento na porta de recepção do Azure Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Captura de tela da solicitação de ponto de extremidade de armazenamento aprovado.":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [serviço de vínculo privado com a conta de armazenamento](../../storage/common/storage-private-endpoints.md).
