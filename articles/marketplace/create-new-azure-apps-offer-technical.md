---
title: Como adicionar detalhes técnicos para sua oferta de Aplicativo Azure
description: Saiba como adicionar detalhes técnicos para sua oferta de aplicativo do Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369933"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Como adicionar detalhes técnicos para sua oferta de Aplicativo Azure

Este artigo descreve como inserir detalhes técnicos que ajudam o Microsoft Commercial Marketplace a se conectar à sua solução. Essa conexão nos permite provisionar sua oferta para o cliente se ele optar por adquiri-lo e gerenciá-lo.

Conclua esta seção somente se sua oferta incluir um aplicativo gerenciado que emitirá eventos de medição usando as [APIs de cobrança limitada do Marketplace](partner-center-portal/marketplace-metering-service-apis.md) e terá um serviço que será autenticado com um token de segurança do Azure AD. Para obter mais informações, consulte [estratégias de autenticação do serviço de medição do Marketplace](partner-center-portal/marketplace-metering-service-authentication.md) nas diferentes opções de autenticação.

## <a name="technical-configuration-offer-level"></a>Configuração técnica (nível de oferta)

A guia **configuração técnica** se aplica a você somente se você criar um aplicativo gerenciado que emita eventos de medição usando as [APIs de cobrança limitada do Marketplace](partner-center-portal/marketplace-metering-service-apis.md). Nesse caso, conclua as etapas a seguir. Caso contrário, vá para [as próximas etapas](#next-steps). 

Para obter mais informações sobre esses campos, consulte [planejar uma oferta de aplicativo Azure para o Marketplace comercial](plan-azure-application-offer.md#technical-configuration).

1. Na guia **configuração técnica** , forneça a **id de locatário Azure Active Directory** e **Azure Active Directory ID de aplicativo** usada para validar a conexão entre nossos dois serviços está por trás de uma comunicação autenticada.

1. Selecione **salvar rascunho** antes de continuar para a próxima guia: visão geral do plano.

## <a name="next-steps"></a>Próximas etapas

- [Como criar planos para sua oferta de Aplicativo Azure](create-new-azure-apps-offer-plans.md)
