---
title: Conectar a porta frontal do Azure Premium a uma origem do aplicativo Web com o link privado
titleSuffix: Azure Private Link
description: Saiba como conectar sua Azure front door Premium a um webapp de forma privada.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030572"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Conectar a porta frontal do Azure Premium a uma origem do aplicativo Web com o link privado

Este artigo irá orientá-lo sobre como configurar o SKU Premium da porta do Azure para se conectar ao seu aplicativo Web de forma privada usando o serviço de link privado do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Crie um serviço de [vínculo privado](../../private-link/create-private-link-service-portal.md) para seus servidores Web de origem.

> [!Note]
> O ponto de extremidade privado está disponível em regiões públicas para camada PremiumV2, camada PremiumV3, aplicativos Web do Windows, aplicativos Web do Linux e plano Premium do Azure Functions (às vezes chamado de plano Elástico Premium).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Habilitar o link privado para um aplicativo Web na porta de recepção Premium do Azure
 
Nesta seção, você mapeará o serviço de vínculo privado para um ponto de extremidade privado criado na rede privada da porta de frente do Azure. 

1. No perfil Premium da porta de recepção do Azure, em *configurações*, selecione **grupos de origem**.

1. Selecione o grupo de origem que contém a origem do aplicativo Web para a qual você deseja habilitar o link privado.

1. Selecione **+ Adicionar uma origem** para adicionar uma nova origem do aplicativo Web ou selecione uma origem do aplicativo Web criada anteriormente na lista.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Captura de tela da habilitação do link privado para um aplicativo Web.":::

1. Para **selecionar um recurso do Azure**, selecione **em meu diretório**. Selecione ou insira as configurações a seguir para configurar o site ao qual você deseja que o Azure front door Premium se conecte com particular.

    | Configuração | Valor |
    | ------- | ----- |
    | Região | Selecione a região que é a mesma ou mais próxima de sua origem. |
    | Tipo de recurso | Selecione **Microsoft.Web/sites**. |
    | Recurso | Selecione **myPrivateLinkService**. |
    | Sub-recurso de destino | sites |
    | Mensagem de solicitação | Personalize a mensagem ou escolha o padrão. |

1. Em seguida, selecione **Adicionar** para salvar sua configuração.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Aprovar a conexão de ponto de extremidade privada Premium do Azure da porta do aplicativo Web

1. Vá para o aplicativo Web para o qual você configurou o link privado na última seção. Selecione **rede** em **configurações**.

1. Em **Rede**, selecione **Configurar suas conexões de ponto de extremidade privado**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Captura de tela das configurações de rede em um aplicativo Web.":::

1. Selecione a solicitação de ponto de extremidade privada *pendente* da porta de início do Azure Premium e selecione **aprovar**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Captura de tela de solicitação de ponto de extremidade privada pendente.":::

1. Depois de aprovado, ele deve ser semelhante à captura de tela abaixo. Levará alguns minutos para que a conexão seja estabelecida completamente. Agora você pode acessar seu aplicativo Web do Azure front door Premium. O acesso direto ao aplicativo Web da Internet pública é desabilitado depois que o ponto de extremidade privado é habilitado.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Captura de tela da solicitação de ponto de extremidade aprovada.":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [serviço de vínculo privado com o aplicativo Web do Azure](../../app-service/networking/private-endpoint.md).
