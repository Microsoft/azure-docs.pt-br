---
title: Cenários de preços para chamada (voz/vídeo) e chat
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre o modelo de preços dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 779fa577b25bd4f2aa92aa8b8cc1244a58bdf1ae
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126183"
---
# <a name="pricing-scenarios"></a>Cenários de preços

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


No geral, os preços dos Serviços de Comunicação do Azure são baseados em um modelo pago conforme o uso.

## <a name="voicevideo-calling-and-screen-sharing"></a>Chamada de voz/vídeo e compartilhamento de tela

Os Serviços de Comunicação do Azure permitem adicionar chamadas de voz/vídeo e compartilhamento de tela aos seus aplicativos. Você pode inserir a experiência em seus aplicativos usando as bibliotecas de clientes JavaScript, Objective-C (Apple), Java (Android) ou .NET. Confira nossa [lista completa de bibliotecas de clientes disponíveis](./sdk-options.md).

### <a name="pricing"></a>Preços

Os serviços de chamada e de compartilhamento de tela são cobrados por minuto por participante a US$ 0,004 por participante por minuto para chamadas de grupo. Para entender os vários fluxos de chamada possíveis, confira [esta página](./call-flows.md).

Cada participante da chamada contará na cobrança de cada minuto em que estiver conectado à chamada. Isso se aplica independentemente de o usuário estar realizando uma chamada de vídeo, uma chamada de voz ou um compartilhamento de tela.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Exemplo de preço: agrupar chamada de áudio/vídeo usando bibliotecas de clientes JS e iOS

Alice fez uma chamada em grupo com seus colegas, Bob e Charlie. Alice e Bob usaram as bibliotecas de clientes JS; Charlie usou as bibliotecas de clientes do iOS.

- A chamada dura um total de 60 minutos.
- Alice e Bob participaram de toda a chamada. Alice ativou o vídeo por cinco minutos e compartilhou a tela por 23 minutos. Bob ficou com o vídeo ativado durante toda a chamada (60 minutos) e compartilhou a tela por 12 minutos.
- Charlie saiu da chamada após 43 minutos. Charlie usou áudio e vídeo durante todo o tempo em que participou (43 minutos).

**Cálculos de custo**

- 2 participantes x 60 minutos x US$ 0,004 por participante por minuto = US$ 0,48 [vídeo e áudio são cobrados à mesma taxa]
- 1 participante x 43 minutos x US$ 0,004 por participante por minuto = US$ 0,172 [vídeo e áudio são cobrados à mesma taxa]

**Custo total para a chamada em grupo** : US$ 0,48 + US$ 0,172 = US$ 0,652

## <a name="chat"></a>Chat

Com os Serviços de Comunicação, você pode aprimorar seu aplicativo com a capacidade de enviar e receber mensagens de chat entre dois ou mais usuários. As bibliotecas de clientes de chat estão disponíveis para JavaScript, .NET, Python e Java. Confira [esta página para saber mais sobre bibliotecas de cliente](./sdk-options.md)

### <a name="price"></a>Preço

Você será cobrado US$ 0,0008 para cada mensagem de chat enviada.

### <a name="pricing-example-chat-between-two-users"></a>Exemplo de preço: chat entre dois usuários 

Geeta inicia uma conversa de chat com Emily para compartilhar uma atualização e envia cinco mensagens. O chat dura 10 minutos, em que Geeta e Emily enviam outras 15 mensagens cada.

**Cálculos de custo** 
- Número de mensagens enviadas (5 + 15 + 15) x US$ 0,0008 = US$ 0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Exemplo de preço: chat em grupo com vários usuários 

Charlie inicia uma conversa de chat com seus amigos Casey e Jasmine para planejar as férias. Eles conversam por um tempo, em que Charlie, Casey e Jasmine enviam 20, 30 e 18 mensagens, respectivamente. Eles percebem que uma amiga deles, Rosa, pode estar interessada em participar da viagem também, então a adicionam à conversa de chat e compartilham todo o histórico de mensagens com ela. 

Rosa vê as mensagens e começa a conversar. Enquanto isso, Casey recebe uma chamada e decide acompanhar a conversa mais tarde. Charlie, Jasmine e Rosa decidem as datas da viagem e enviam outras 30, 25 e 35 mensagens, respectivamente.

**Cálculos de custo** 

- Número de mensagens enviadas (20 + 30 + 18 + 30 + 25 + 35) x US$ 0,0008 = US$ 0,1264


## <a name="telephony-and-sms"></a>Telefonia e SMS

## <a name="price"></a>Preço 

Os serviços de telefonia são cobrados por minuto, enquanto o SMS é cobrado por mensagem. O preço é determinado pelo tipo e pela localização do número que você está usando, bem como pelo destino das suas chamadas e mensagens SMS.

### <a name="telephone-number-leasing"></a>Aluguel de número de telefone

Os valores de aluguel de número de telefone são cobradas antecipadamente e são recorrentes a cada mês:

|Tipo de número   |Valor mensal   |
|--------------|-----------|
|Local (Estados Unidos)     |US$ 1/mês        |
|Chamada gratuita (Estados Unidos) |US$ 2/mês |


### <a name="telephone-calling"></a>Chamada telefônica

A chamada telefônica tradicional (chamada que ocorre pela rede telefônica pública comutada) está disponível com o preço pago conforme o uso para números de telefone localizados nos Estados Unidos. O preço é uma cobrança por minuto com base no tipo de número usado e no destino da chamada. Os detalhes de preços para os destinos de chamada mais populares estão incluídos na tabela abaixo. Confira a [lista de preços detalhada](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) para obter uma lista completa de destinos.


#### <a name="united-states-calling-prices"></a>Preços da chamada nos Estados Unidos

Os seguintes preços incluem impostos e valores de comunicação obrigatórios até 30 de junho de 2021:

|Tipo de número   |Para fazer chamadas   |Para receber chamadas|
|--------------|-----------|------------|
|Local     |A partir de US$ 0,013/min       |US$ 0,0085/min        |
|Chamada gratuita |US$ 0,013/min   |US$ 0,0220/min |

#### <a name="other-calling-destinations"></a>Outros destinos de chamada

Os seguintes preços incluem impostos e valores de comunicação obrigatórios até 30 de junho de 2021:

|Fazer chamadas para   |Preço por minuto|
|-----------|------------|
|Canada     |A partir de US$ 0,013/min   |
|United Kingdom     |A partir de US$ 0,015/min   |
|Alemanha     |A partir de US$ 0,015/min   |
|França     |A partir de US$ 0,016/min   |


### <a name="sms"></a>SMS

O SMS oferece preços pagos conforme o uso. O preço é um custo por mensagem baseado no destino da mensagem. As mensagens podem ser enviadas por números de telefone de chamada gratuita para números de telefone localizados nos Estados Unidos. Observe que os números de telefone locais (geográficos) não podem ser usados para enviar mensagens SMS.

Os seguintes preços incluem impostos e valores de comunicação obrigatórios até 30 de junho de 2021:

|País   |Enviar mensagens|Receber mensagens|
|-----------|------------|------------|
|EUA (chamada gratuita)    |US$ 0,0075/mensagem   | US$ 0,0075/mensagem |
