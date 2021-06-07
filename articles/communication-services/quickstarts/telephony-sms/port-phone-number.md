---
title: Início Rápido – Portar um número de telefone para os Serviços de Comunicação do Azure
description: Saiba como portar um número de telefone para seu recurso dos Serviços de Comunicação
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729904"
---
# <a name="quickstart-port-a-phone-number"></a>Início Rápido: Portar um número de telefone

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Comece a usar os Serviços de Comunicação do Azure portando seu número de telefone para o recurso dos Serviços de Comunicação do Azure. Números para chamada gratuita e geográficos baseados nos Estados Unidos se qualificam para portabilidade. Para obter mais informações sobre os tipos de números de telefone, visite a [documentação conceitual sobre números de telefone](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Um recurso ativo dos Serviços de Comunicação.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Reunir os detalhes do recurso do Azure

Antes de iniciar uma solicitação de portabilidade, navegue até o portal do Azure e selecione seu recurso dos Serviços de Comunicação. Com o painel `Overview` selecionado, clique no link `JSON View` no canto superior direito:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Captura de tela mostrando a seleção da Exibição JSON.":::

Registre a **ID do Azure** e a **ID de Recurso Imutável** de seu recurso:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Captura de tela mostrando a seleção das propriedades JSON.":::

## <a name="initiate-the-port-request"></a>Iniciar a solicitação de portabilidade

Números para chamada gratuita e geográficos baseados nos Estados Unidos se qualificam para portabilidade. Use um dos seguintes formulários para enviar sua solicitação de portabilidade:

- Para números com chamada gratuita: [Solicitação de portabilidade de número com chamada gratuita](https://aka.ms/acs-port-form-tollfree)
- Para números geográficos baseados nos EUA: [Solicitação de portabilidade de número geográfico](https://aka.ms/acs-port-form-geographic)

Quando terminar, envie o formulário de solicitação de portabilidade preenchido para acsporting@microsoft.com. A linha do assunto do email deve começar com "ACS Port-In Request".

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Obter os metadados de seu recurso dos Serviços de Comunicação
> * Enviar uma solicitação para portar seu número de telefone

> [!div class="nextstepaction"]
> [Enviar SMS](../telephony-sms/send.md)
> [Introdução às chamadas](../voice-video-calling/getting-started-with-calling.md)
