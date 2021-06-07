---
title: Gerenciar uma conta do Video Indexer
titleSuffix: Azure Media Services
description: Saiba como gerenciar uma conta de Video Indexer conectada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 25025efe5254442c4862563ce329cfae69e7d033
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898436"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gerenciar uma conta do Video Indexer conectada ao Azure

Este artigo demonstra como gerenciar uma conta de Video Indexer que está conectada à sua assinatura do Azure e uma conta dos serviços de mídia do Azure.

> [!NOTE]
> Você precisa ser o proprietário da conta do Video Indexer para fazer os ajustes na configuração da conta discutidos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Conecte sua conta do Video Indexer ao Azure, conforme descrito em [Conectado ao Azure](connect-to-azure.md).

Certifique-se de seguir os [Pré-requisitos](connect-to-azure.md#prerequisites-for-connecting-to-azure) e veja as [Considerações](connect-to-azure.md#azure-media-services-considerations) neste artigo.

## <a name="examine-account-settings"></a>Examinar as configurações de conta

Esta seção examina as configurações da sua conta do Video Indexer.

Para exibir as configurações:

1. Clique no ícone de usuário no canto superior direito e selecione **configurações**.

    ![Configurações no Video Indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. Na página **Configurações**, selecione a guia **Conta**.

Se sua conta do indexador de vídeos estiver conectada ao Azure, você verá o seguinte:

* O nome da conta de Serviços de Mídia do Azure subjacente.
* O número de trabalhos de indexação em execução e na fila.
* O número e o tipo de unidades reservadas alocadas.

Se sua conta precisar de alguns ajustes, você verá erros relevantes e avisos sobre a configuração da sua conta na página **configurações** . As mensagens contêm links para locais exatos no portal do Azure em que você precisa fazer alterações. Para obter mais informações, consulte a seção de [erros e avisos](#errors-and-warnings) a seguir.

## <a name="repair-the-connection-to-azure"></a>Reparar a conexão com o Azure

Na caixa de diálogo **atualizar conexão com os serviços de mídia do Azure** da página [Video indexer](https://www.videoindexer.ai/) , você será solicitado a fornecer valores para as seguintes configurações:

|Configuração|Descrição|
|---|---|
|ID da assinatura do Azure|O ID da assinatura pode ser recuperado no portal do Azure. Clique em **todos os serviços** no painel esquerdo e pesquise por "assinaturas". Selecione **Assinaturas** e escolha a ID desejada na lista das assinaturas.|
|Nome do grupo de recursos dos Serviços de Mídia do Azur.|O nome do grupo de recursos no qual você criou a conta de Serviços de Mídia.|
|ID do aplicativo|A ID do aplicativo do Azure AD (com permissões para a conta de serviços de mídia especificada) que você criou para esta Video Indexer conta. <br/><br/>Para obter a ID do aplicativo, navegue até portal do Azure. Na conta dos serviços de mídia, escolha sua conta e acesse **acesso à API**. Selecione **conectar-se à API dos serviços de mídia com a entidade de serviço**  ->  **aplicativo Azure ad**. Copie os parâmetros relevantes.|
|Chave do aplicativo|A chave de aplicativo do Azure AD associada à sua conta dos serviços de mídia especificada acima. <br/><br/>Para obter a chave do aplicativo, navegue até portal do Azure. Na conta dos serviços de mídia, escolha sua conta e acesse **acesso à API**. Selecione **conectar-se à API dos serviços de mídia com entidade de serviço**  ->  **gerenciar certificados de aplicativo**  ->  **& segredos**. Copie os parâmetros relevantes.|

## <a name="autoscale-reserved-units"></a>Fazer o dimensionamento automático de unidades reservadas

A página **configurações** permite que você defina o dimensionamento automático de ru (unidades reservadas de mídia). Se a opção estiver **Ativa**, você poderá alocar o número máximo de RUs e garantir que o Video Indexer iniciará/parará RUs automaticamente. Com essa opção, você não paga dinheiro extra por tempo ocioso, mas também não espera que os trabalhos de indexação sejam concluídos muito tempo quando a carga de indexação é alta.

O dimensionamento automático não é dimensionado abaixo de 1 RU ou acima do limite padrão da conta dos serviços de mídia. Para aumentar o limite, crie uma solicitação de serviço. Para saber mais sobre as cotas e limitações e sobre como abrir um tíquete de suporte, consulte [Cotas e limitações](../../media-services/previous/media-services-quotas-and-limitations.md).

![Unidades reservadas para autoescala Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Erros e avisos

Se sua conta precisar de alguns ajustes, você verá avisos e erros relevantes sobre a configuração da sua conta na página **Configurações**. As mensagens contêm links para locais exatos no portal do Azure em que você precisa fazer alterações. Esta seção fornece mais detalhes sobre o erro e mensagens de aviso.

* EventGrid

    Você precisa registrar o provedor de recursos do EventGrid usando o portal do Azure. No [portal do Azure](https://portal.azure.com/), acesse **assinaturas** > [Subscription] > **ResourceProviders**  >  **Microsoft. EventGrid**. Se não estiver no estado **registrado** , selecione **registrar**. Demora alguns minutos para se registrar.

* ponto de extremidade de streaming

    Verifique se a conta de Serviços de Mídia subjacente tem o padrão **Ponto de Extremidade de Streaming** em um estado iniciado. Caso contrário, você não poderá assistir a vídeos desta conta de serviços de mídia ou em Video Indexer.

* Unidades reservadas para mídia

    Você deve alocar unidades reservadas de mídia em seu recurso de serviço de mídia para indexar vídeos. Para obter o desempenho ideal da indexação, é recomendável alocar pelo menos 10 Unidades Reservadas S3. Para obter informações de preço, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Próximas etapas

Você pode interagir de forma programática com sua conta de avaliação ou Video Indexer contas conectadas ao Azure seguindo as instruções em: [usar APIs](video-indexer-use-apis.md).

Use o mesmo usuário do AD do Azure que você usou ao se conectar ao Azure.
