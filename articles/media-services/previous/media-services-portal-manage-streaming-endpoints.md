---
title: Gerenciar pontos de extremidade de streaming com o portal do Azure | Microsoft Docs
description: Este tópico mostra como gerenciar pontos de extremidade de streaming usando o portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 1775bbb2913f6b1a985ca7ec9e89bafed42fd0e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61129659"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gerenciar pontos de extremidade de streaming com o portal do Azure 

Este artigo mostra como usar o portal do Azure para gerenciar pontos de extremidade de streaming. 

>[!NOTE]
>Certifique-se de examinar o artigo [Visão geral](media-services-streaming-endpoints-overview.md). 

Para obter informações sobre como dimensionar o ponto de extremidade de streaming, consulte [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a name="start-managing-streaming-endpoints"></a>Começar a gerenciar pontos de extremidade de streaming 

Para começar a gerenciar pontos de extremidade de streaming para sua conta, faça o indicado a seguir.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na folha **Configurações**, selecione **Pontos de extremidade de streaming**.
   
    ![ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Você será cobrado apenas quando seu ponto de extremidade de streaming estiver em estado de execução.

## <a name="adddelete-a-streaming-endpoint"></a>Adicionar/excluir um ponto de extremidade de streaming

>[!NOTE]
>O ponto de extremidade de streaming não pode ser excluído.

Para adicionar\excluir um ponto de extremidade de streaming usando o portal do Azure, faça o seguinte:

1. Para adicionar um ponto de extremidade de streaming, clique no **+ Ponto de extremidade** na parte superior da página. 

    Você talvez queira ter vários Pontos de Extremidade de Streaming se planeja ter diferentes CDNs ou uma CDN e acesso direto.

2. Para excluir um ponto de extremidade de streaming, pressione o botão **Excluir** .      
3. Clique no botão **Iniciar** para iniciar o ponto de extremidade de streaming.
   
    ![ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Configurando o ponto de extremidade de streaming
O Ponto de Extremidade de Streaming permite que você configure as seguintes propriedades:

* Controle de acesso
* controle de cache
* Políticas de acesso entre sites

Para obter informações detalhadas sobre essas propriedades, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Quando a CDN está habilitada, não é possível acessar o IP de acesso. O IP de acesso só será aplicável quando não há uma CDN.

Você pode configurar o ponto de extremidade de streaming fazendo o seguinte:

1. Selecione o ponto de extremidade de streaming que você quer configurar.
2. Clique em **Configurações**.

A seguir há uma breve descrição dos campos.

![Ponto de Extremidade de Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Política de cache máximo: usada para configurar o tempo de vida de cache para ativos atendidos por esse ponto de extremidade de streaming. Se nenhum valor for definido, o padrão será usado. Os valores padrão também podem ser definidos diretamente no armazenamento do Azure. Se a CDN do Azure estiver habilitada para o ponto de extremidade de streaming, você não deverá definir o valor da política de cache para menos de 600 segundos.  
2. Endereços IP permitidos: usado para especificar endereços IP que terão permissão para se conectar ao ponto de extremidade de streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar. Os endereços IP podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), um intervalo de IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22') ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede em notação decimal com ponto (por exemplo, '10.0.0.1(255.255.255.0)').
3. Configuração de autenticação de cabeçalho de assinatura da Akamai: usado para especificar como a solicitação de autenticação de cabeçalho de assinatura de servidores da Akamai está configurada. A data de validade é no formato UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Dimensionar seu ponto de extremidade de streaming

Para saber mais, confira [este artigo](media-services-portal-scale-streaming-endpoints.md).

## <a id="enable_cdn"></a>Habilitar a integração da CDN do Azure

Quando você cria uma nova conta, a integração padrão da CDN do Azure para Ponto de Extremidade de Streaming é habilitada por padrão.

Se quiser desabilitar/habilitar a CDN depois, o ponto de extremidade de streaming deverá estar no estado **interrompido**. Para que a integração da CDN do Azure seja habilitada e as alterações estejam ativas em todos os POPs da CDN talvez sejam necessárias até duas horas. No entanto, é possível iniciar o ponto de extremidade de streaming e o fluxo sem interrupções do ponto de extremidade de streaming e, assim que a integração estiver concluída, o fluxo é fornecido a partir da CDN. Durante o período de provisionamento seu ponto de extremidade de streaming estará no estado **iniciando** e você pode observar a degradação do desempenho.

A integração da CDN é habilitada em todos os datacenters do Azure, exceto nas regiões da China e do Governo Federal.

Assim que estiver habilitada, as configurações **Controle de Acesso**, **Nome do host personalizado e **Autenticação de assinatura Akama** serão desabilitadas.
 
> [!IMPORTANT]
> A integração dos Serviços de Mídia do Azure à CDN do Azure é implementada da **Verizon na CDN do Azure** para pontos de extremidade de streaming padrão. Os pontos de extremidade de streaming Premium podem ser configurados usando todos os **tipos de preço e provedores da CDN do Azure**. Para obter mais informações sobre os recursos da CDN do Azure, consulte [Visão geral da CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Considerações adicionais

* Quando CDN está habilitado para um ponto de extremidade de streaming, os clientes não podem solicitar o conteúdo diretamente a partir da origem. Se você precisar da capacidade de testar seu conteúdo com ou sem CDN, poderá criar outro ponto de extremidade de streaming no qual a CDN não esteja habilitada.
* O nome de host do ponto de extremidade de streaming permanece o mesmo depois de habilitar o CDN. Você não precisa fazer nenhuma alteração ao seu fluxo de trabalho de serviços de mídia depois que o CDN for habilitado. Por exemplo, se o nome de host do ponto de extremidade de streaming for strasbourg.streaming.mediaservices.windows.net, depois de habilitar o CDN, o mesmo nome de host é usado.
* Para novos pontos de extremidade de streaming, é possível habilitar a CDN simplesmente criando um novo ponto de extremidade; para pontos de extremidade de streaming existentes, será necessário primeiro parar o ponto de extremidade e, em seguida, habilitar/desabilitar a CDN.
* O ponto de extremidade de streaming padrão pode ser configurado apenas usando o **provedor de CDN padrão da Verizon** no portal clássico do Azure. No entanto, você pode habilitar outros provedores de CDN do Azure usando APIs REST.

## <a name="configure-cdn-profile"></a>Configurar perfil de CDN

Você pode configurar o perfil de CDN escolhendo o botão **Gerenciar CDN** na parte superior.

![ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

