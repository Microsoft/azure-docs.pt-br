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
ms.openlocfilehash: 5c8c4808e735a872b31deb6855646dae90c407c9
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487671"
---
# <a name="pricing-scenarios"></a>Cenários de preços

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


No geral, os preços dos Serviços de Comunicação do Azure são baseados em um modelo pago conforme o uso. Os preços nos exemplos a seguir são para fins ilustrativos e podem não refletir os preços mais recentes do Azure.

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

**Custo total para a chamada em grupo**: US$ 0,48 + US$ 0,172 = US$ 0,652

### <a name="pricing-example-a-user-of-the-communication-services-js-client-library-joins-a-scheduled-microsoft-teams-meeting"></a>Exemplo de preço: Um usuário da biblioteca de clientes JS dos Serviços de Comunicação entra em uma reunião agendada do Microsoft Teams

Alice é uma médica que está se reunindo com o paciente dela, Bob. A Alice vai entrar na visita do aplicativo da área de trabalho Teams. Bob receberá um link para entrar usando o site do prestador de serviços de saúde, que se conecta à reunião usando a biblioteca de clientes JS dos Serviços de Comunicação. Bob usará o celular dele para entrar na reunião usando um navegador da Web (iPhone com Safari). O chat estará disponível durante a visita virtual.

- A chamada dura 30 minutos no total.
- Alice e Bob participaram de toda a chamada. Alice ativa o vídeo dela cinco minutos depois que a chamada é iniciada e compartilha a tela por 13 minutos. Bob fica com o vídeo ativado em toda a chamada.
- Alice envia cinco mensagens, Bob responde com três.


**Cálculos de custo**

- 1 participante (Bob) x 30 minutos x US$ 0,004 por participante por minuto = US$ 0,12 [vídeo e áudio são cobrados na mesma taxa]
- 1 participante (Alice) x 30 minutos x $0 por participante por minuto = $0*.
- 1 participante (Bob) x 3 mensagens de chat x US$ 0,0008 = US$ 0,0024.
- 1 participante (Alice) x 5 mensagens de chat x US$ 0 = US$ 0*.

*A participação de Alice é coberta pela licença do Teams dela. Sua fatura do Azure mostrará os minutos e as mensagens de chat que os usuários da equipe trocaram com os usuários dos Serviços de Comunicação para sua conveniência, mas esses minutos e mensagens provenientes do cliente do Teams não terão custo.

**Custo total para a visita**:
- entrada do usuário usando a biblioteca de clientes JS dos Serviços de Comunicação: US$ 0,12 + US$ 0,0024 = US$ 0,1224
- Entrada do usuário no aplicativo da área de trabalho Teams: US$ 0 (coberto pela licença do Teams)


## <a name="chat"></a>Chat

Com os Serviços de Comunicação, você pode aprimorar seu aplicativo com a capacidade de enviar e receber mensagens de chat entre dois ou mais usuários. As bibliotecas de clientes de chat estão disponíveis para JavaScript, .NET, Python e Java. Confira [esta página para saber mais sobre bibliotecas de cliente](./sdk-options.md)

### <a name="price"></a>Preço

Você será cobrado US$ 0,0008 para cada mensagem de chat enviada.

### <a name="pricing-example-chat-between-two-users"></a>Exemplo de preço: chat entre dois usuários

Geeta inicia uma conversa de chat com Emily para compartilhar uma atualização e envia cinco mensagens. O chat dura 10 minutos. Geeta e Emily enviam mais 15 mensagens cada uma.

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
