---
title: Conectar a Azure front door Premium a uma origem de balanceador de carga interna com link privado
titleSuffix: Azure Private Link
description: Saiba como conectar sua Azure front door Premium a um balanceador de carga interno.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803641"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Conectar a Azure front door Premium a uma origem de balanceador de carga interna com link privado

Este artigo orientará você sobre como configurar a SKU Premium da porta do Azure para se conectar à origem do balanceador de carga interno usando o serviço de vínculo privado do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Crie um [serviço de vínculo privado](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Habilitar o link privado para um balanceador de carga interno
 
Nesta seção, você mapeará o serviço de vínculo privado para um ponto de extremidade privado criado na rede privada da porta de frente do Azure. 

1. No perfil Premium da porta de recepção do Azure, em *configurações*, selecione **grupos de origem**.

1. Selecione o grupo de origem para o qual você deseja habilitar o link privado para o balanceador de carga interno.

1. Selecione **+ Adicionar uma origem** para adicionar uma origem de balanceador de carga interna.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Captura de tela da habilitação do link privado para um balanceador de carga interno.":::

1. Para **selecionar um recurso do Azure**, selecione **em meu diretório**. Selecione ou insira as configurações a seguir para configurar o site ao qual você deseja que o Azure front door Premium se conecte com particular.

    | Setting | Valor |
    | ------- | ----- |
    | Região | Selecione a região que é a mesma ou mais próxima de sua origem. |
    | Tipo de recurso | Selecione **Microsoft.Network/privateLinkServices**. |
    | Recurso | Selecione seu link privado vinculado ao balanceador de carga interno. |
    | Sub-recurso de destino | Deixe em branco. |
    | Mensagem de solicitação | Personalize a mensagem ou escolha o padrão. |

1. Em seguida, selecione **Adicionar** e **Atualizar** para salvar sua configuração.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Aprovar conexão de ponto de extremidade privada da conta de armazenamento

1. Vá para o centro de links privado e selecione **serviços de link privado**. Em seguida, selecione o nome do seu link privado.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Captura de tela da lista de links particulares.":::

1. Selecione **conexões de ponto de extremidade privado** em *configurações*.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Captura de tela da página Visão geral do link privado.":::

1. Selecione a solicitação de ponto de extremidade privada *pendente* da porta de início do Azure Premium e selecione **aprovar**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Captura de tela de aprovação pendente para o link privado.":::

1. Depois de aprovado, ele deve ser semelhante à captura de tela abaixo. Levará alguns minutos para que a conexão seja estabelecida completamente. Agora você pode acessar o balanceador de carga interno da porta Premium do Azure.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Captura de tela da solicitação de link privado aprovada.":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [serviço de vínculo privado](../../private-link/private-link-service-overview.md).
