---
title: Como adicionar detalhes técnicos para sua oferta de SaaS no Microsoft Partner Center
description: Saiba como fornecer detalhes técnicos para sua oferta de SaaS (software como serviço) para o Marketplace comercial da Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 28c0be40387f411286230f94c19fa23a80e650af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96746396"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Como adicionar detalhes técnicos para sua oferta de SaaS

Este artigo descreve como inserir detalhes técnicos que ajudam o Microsoft Commercial Marketplace a se conectar à sua solução. Essa conexão nos permite provisionar sua oferta para o cliente se ele optar por adquiri-lo e gerenciá-lo. Para obter mais detalhes sobre essas configurações, consulte [informações técnicas](plan-saas-offer.md#technical-information).

> [!NOTE]
> Se você optar por processar as transações de forma independente, essa opção não será exibida. Em vez disso, pule para [como vender sua oferta de SaaS](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Configurações técnicas

Na guia **configuração técnica** , você definirá os detalhes técnicos que o Marketplace comercial usa para se comunicar com seu aplicativo ou solução SaaS. 

- **URL da página de aterrissagem** (obrigatório) – defina a URL do site do SaaS (por exemplo: `https://contoso.com/signup` ) que os clientes irão parar depois de adquirir sua oferta do Marketplace comercial e disparar o processo de configuração da assinatura de SaaS recém-criada.

  > [!IMPORTANT]
  > Sua página de aterrissagem deve estar em execução 24/7. Essa é a única maneira que você será notificado sobre novas compras de suas ofertas de SaaS feitas no Marketplace comercial ou solicitações de configuração de uma assinatura ativa de uma oferta.

- **Webhook de conexão** (obrigatório) – para todos os eventos assíncronos que a Microsoft precisa enviar para você (por exemplo, a assinatura de SaaS foi cancelada), exigimos que você forneça uma URL de webhook de conexão. Iremos chamar essa URL para notificá-lo do evento.

  > [!IMPORTANT]
  > Seu webhook deve estar em execução 24/7, pois essa é a única maneira que você será notificado sobre atualizações sobre as assinaturas de SaaS de seus clientes adquiridas por meio do Marketplace comercial.

- **Azure Active Directory ID do locatário** (obrigatório) – para localizar a ID do locatário do seu aplicativo Azure Active Directory (AD do Azure), vá para a folha [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) em Azure Active Directory. Na coluna **nome de exibição** , selecione o aplicativo. Em seguida, procure o número de **ID do diretório (locatário)** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory ID do aplicativo** (obrigatório) – para localizar a [ID do aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), vá para a folha [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) em Azure Active Directory. Na coluna **nome de exibição** , selecione o aplicativo. Em seguida, procure o número de ID do aplicativo (cliente) listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

Selecione **salvar rascunho** antes de continuar para a próxima guia: visão geral do plano.

## <a name="next-steps"></a>Próximas etapas

- [Como criar planos para sua oferta de SaaS](create-new-saas-offer-plans.md).