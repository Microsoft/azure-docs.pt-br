---
title: Tipos de número de telefone nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba como usar com eficácia diferentes tipos de números de telefone para SMS e telefonia.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 66edf35f292f5b681578d49b8be784de66e4d904
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936522"
---
# <a name="phone-number-types-in-azure-communication-services"></a>Tipos de número de telefone nos Serviços de Comunicação do Azure

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

> [!IMPORTANT]
> A disponibilidade do número de telefone atualmente está restrita a assinaturas pagas do Azure que têm um endereço para cobrança nos Estados Unidos e a recursos dos Serviços de Comunicação que têm um local de dados nos EUA. Números de telefone não podem ser adquiridos em contas de avaliação ou usando créditos gratuitos do Azure. Para obter mais informações, visite a seção [qualificação para assinatura](#azure-subscriptions-eligibility) deste documento.


Os Serviços de Comunicação do Azure permitem que você use números de telefone para fazer chamadas de voz e enviar mensagens SMS com a PSTN (Rede Telefônica Pública Comutada). Neste documento, examinaremos os tipos de número de telefone, as opções de configuração e a disponibilidade de região para planejar sua solução de telefonia e SMS usando os Serviços de Comunicação.

## <a name="azure-subscriptions-eligibility"></a>Qualificação das assinaturas do Azure

Para adquirir um número de telefone, você precisa ter uma assinatura paga do Azure. Os números de telefone não podem ser adquiridos em contas de avaliação ou por créditos gratuitos do Azure.

A disponibilidade do número de telefone atualmente está restrita a assinaturas do Azure que têm um endereço para cobrança nos Estados Unidos e a recursos dos Serviços de Comunicação que têm um local de dados nos EUA.


## <a name="number-types-and-features"></a>Tipos de número e recursos
Os Serviços de Comunicação oferecem dois tipos de números de telefone: **local** e **gratuito**.

### <a name="local-numbers"></a>Números locais
Os números locais (geográficos) são números de telefone de 10 dígitos que consistem nos códigos de área locais dos Estados Unidos. Por exemplo, `+1 (206) XXX-XXXX` é um número local com o código de área `206`. Esse código de área é atribuído à cidade de Seattle. Esses números de telefone geralmente são usados por indivíduos e empresas locais. Os Serviços de Comunicação do Azure oferecem números locais nos Estados Unidos. Esses números podem ser usados para fazer chamadas telefônicas, mas não para enviar mensagens SMS.

### <a name="toll-free-numbers"></a>Números de chamada gratuita
Os números de chamada gratuita são números de telefone de 10 dígitos com códigos de área distintos que podem ser chamados em qualquer número de telefone gratuito. Por exemplo, `+1 (800) XXX-XXXX` é um número de chamada gratuita na região América do Norte. Geralmente, esses números de telefone são usados para fins de atendimento ao cliente. Os Serviços de Comunicação do Azure oferecem números de chamada gratuita nos Estados Unidos. Esses números podem ser usados para fazer chamadas telefônicas e enviar mensagens SMS. Os números de chamada gratuita não podem ser usados por pessoas e só podem ser atribuídos a aplicativos.

#### <a name="choosing-a-phone-number-type"></a>Como escolher um tipo de número de telefone

Se o seu número de telefone for usado por um aplicativo (por exemplo, para fazer chamadas ou enviar mensagens em nome do seu serviço), você poderá selecionar um número de chamada gratuita ou local (geográfico). Selecione um número de chamada gratuita se o seu aplicativo estiver enviando mensagens SMS e/ou fazendo chamadas.

Se ele estiver sendo usado por uma pessoa (por exemplo, um usuário do seu aplicativo de chamada), o número de telefone local (geográfico) precisará ser usado.

A tabela a seguir resume esses tipos de números de telefone:

| Tipo de número de telefone | Exemplo                              | Disponibilidade do país    | Funcionalidade de número de telefone |Caso de uso comum                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Local (geográfico)        | +1 (código de área local) XXX XX XX  | EUA                      | Chamada (saída) | Como atribuir números de telefone aos usuários nos seus aplicativos  |
| Chamada gratuita         | \+ 1 (*código* de área gratuita) XXX XX XX | EUA                      | Chamada (de saída), SMS (entrada/saída)| Como atribuir números de telefone a sistemas IVR (resposta interativa de voz)/bots, aplicativos de SMS                                        |


### <a name="phone-number-capabilities-in-azure-communication-services"></a>Funcionalidades de número de telefone nos Serviços de Comunicação do Azure

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

Para a maioria dos números de telefone, permitimos que você configure um conjunto de funcionalidades "a la carte". Essas funcionalidades podem ser selecionados à medida que você arrenda seus números de telefone nos Serviços de Comunicação do Azure.

As funcionalidades disponíveis dependem do país em que você está operando, no caso de uso e no tipo de número de telefone escolhido. Esses recursos variam por país devido a requisitos regulatórios. Os Serviços de Comunicação do Azure oferecem as seguintes funcionalidades de números de telefone:

- **SMS de saída unidirecional** Essa opção permite que você envie mensagens SMS para os usuários. Isso pode ser útil em cenários de notificação e autenticação de dois fatores.
- **SMS de entrada e saída bidirecional** Essa opção permite que você envie e receba mensagens dos usuários usando números de telefone. Isso pode ser útil em cenários de atendimento ao cliente.
- **Chamada telefônica de saída unidirecional** Essa opção permite que você faça chamadas para os usuários e configure a ID de Chamadas das chamadas de saída feitas pelo serviço. Isso pode ser útil em cenários de notificação de voz e atendimento ao cliente.

## <a name="countryregion-availability"></a>Disponibilidade do país/da região

A tabela a seguir mostra em que local você pode adquirir diferentes tipos de números de telefone, juntamente com os recursos de SMS e chamada de entrada e saída associados a esses tipos de números de telefone.

|Tipo de número| Adquirir números em | Fazer chamadas para                                        | Receber chamadas de                                    |Enviar mensagens para       | Receber mensagens de |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Local (geográfico)  | EUA                 | EUA, Canadá, Reino Unido, Alemanha, França. +mais*| EUA, Canadá, Reino Unido, Alemanha, França. +mais* |Não disponível| Não disponível |
| Chamada gratuita | EUA                 | EUA                                                   | EUA                                                    |EUA                | EUA |

*Para obter mais detalhes sobre os destinos e os preços das chamadas, veja a [página de preços](../pricing.md).


## <a name="next-steps"></a>Próximas etapas

### <a name="quickstarts"></a>Inícios rápidos

- [Obter um número de telefone](../../quickstarts/telephony-sms/get-phone-number.md)
- [Fazer uma chamada](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Enviar um SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Documentação conceitual

- [Conceitos de voz e vídeo](../voice-video-calling/about-call-types.md)
- [Conceitos de telefonia](./telephony-concept.md)
- [Fluxos de chamadas](../call-flows.md)
- [Preços](../pricing.md)
