---
title: Gerenciar uma conta do Video Indexer
titlesuffix: Azure Media Services
description: Este artigo mostra como gerenciar uma conta do Video Indexer conectada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 1515a026ae297a960f220a97449d2258c0b75e58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553567"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Gerenciar uma conta do Video Indexer conectada ao Azure

Este artigo demonstra como gerenciar uma conta do Video Indexer que está conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure.

> [!NOTE]
> Você precisa ser o proprietário da conta do Video Indexer para fazer os ajustes na configuração da conta discutidos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Conecte sua conta do Video Indexer ao Azure, conforme descrito em [Conectado ao Azure](connect-to-azure.md). 

Certifique-se de seguir os [Pré-requisitos](connect-to-azure.md#prerequisites) e veja as [Considerações](connect-to-azure.md#considerations) neste artigo.

## <a name="examine-account-settings"></a>Examinar as configurações de conta

Esta seção examina as configurações da sua conta do Video Indexer.

Para exibir as configurações:

1. Clique no ícone do usuário no canto superior direito e selecione **Configurações**.

    ![Configurações](./media/manage-account-connected-to-azure/select-settings.png)

2. Na página **Configurações**, selecione a guia **Conta**.

Se sua conta do Video Indexer estiver conectada ao Azure, você verá o seguinte:

* O nome da conta de Serviços de Mídia do Azure subjacente.
* O número de trabalhos de indexação em execução e na fila.
* O número e tipo de unidades reservadas alocadas.

Se sua conta precisar de alguns ajustes, você verá avisos e erros relevantes sobre a configuração da sua conta na página **Configurações**. As mensagens contêm links para locais exatos no portal do Azure em que você precisa fazer alterações. Para obter mais informações, consulte a seção de [erros e avisos](#errors-and-warnings) a seguir.

## <a name="auto-scale-reserved-units"></a>Unidades reservada de dimensionamento automático

A página **Configurações** permite que você defina o dimensionamento automático de RU (unidades reservadas) para mídia. Se a opção estiver **Ativa**, você poderá alocar o número máximo de RUs e garantir que o Video Indexer iniciará/parará RUs automaticamente. Com essa opção, você não paga dinheiro extra pelo tempo ocioso, mas também não espera a conclusão dos trabalhos de indexação por um longo período quando a carga de indexação é alta.

O dimensionamento automático não ajusta a escala abaixo de 1 RU ou acima do limite padrão da conta dos Serviços de Mídia. Para aumentar o limite, crie uma solicitação de serviço. Para saber mais sobre as cotas e limitações e sobre como abrir um tíquete de suporte, consulte [Cotas e limitações](../../media-services/previous/media-services-quotas-and-limitations.md).

![Inscrição](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Erros e avisos

Se sua conta precisar de alguns ajustes, você verá avisos e erros relevantes sobre a configuração da sua conta na página **Configurações**. As mensagens contêm links para locais exatos no portal do Azure em que você precisa fazer alterações. Esta seção fornece mais detalhes sobre o erro e mensagens de aviso.

* Grade de Eventos

    Você precisa registrar o provedor de recursos do EventGrid usando o portal do Azure. No [portal do Azure](https://portal.azure.com/), vá para **Assinaturas** > [assinatura] > **ResourceProviders** > **Microsoft.EventGrid**. Se não estiver no estado **Registrado**, clique em **Registrar**. Demora alguns minutos para se registrar. 

* Ponto de Extremidade de Streaming

    Verifique se a conta de Serviços de Mídia subjacente tem o padrão **Ponto de Extremidade de Streaming** em um estado iniciado. Caso contrário, você não poderá assistir a vídeos dessa conta de Serviços de Mídia ou no Video Indexer.

* Unidades reservadas de mídia 

    Você deve alocar unidades reservadas de mídia em seu recurso de serviço de mídia para indexar vídeos. Para obter o desempenho ideal da indexação, é recomendável alocar pelo menos 10 Unidades Reservadas S3. Para obter informações de preço, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-steps"></a>Próximas etapas

É possível interagir programaticamente com a conta de avaliação e/ou com as contas do Video Indexer que estão conectadas ao Azure, seguindo as instruções em: [Usar APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao se conectar ao Azure.
