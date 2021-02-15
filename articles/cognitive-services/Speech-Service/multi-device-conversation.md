---
title: Conversa de vários dispositivos (visualização)-serviço de fala
titleSuffix: Azure Cognitive Services
description: A conversa de vários dispositivos facilita a criação de uma conversa de fala ou de texto entre vários clientes e coordena as mensagens que são enviadas entre elas.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932417"
---
# <a name="what-is-multi-device-conversation-preview"></a>O que é conversa com vários dispositivos (versão prévia)?

A **conversa de vários dispositivos** facilita a criação de uma conversa de fala ou de texto entre vários clientes e coordena as mensagens enviadas entre eles.

Com a **conversa com vários dispositivos**, você pode:

- Conecte vários clientes à mesma conversa e gerencie o envio e recebimento de mensagens entre eles.
- Transcrever facilmente o áudio de cada cliente e enviar a transcrição para os outros, com tradução opcional.
- Envie facilmente mensagens de texto entre clientes, com tradução opcional.

Você pode criar um recurso ou solução que funcione em uma matriz de dispositivos. Cada dispositivo pode enviar mensagens de forma independente (transcrições de áudio ou mensagens instantâneas) para todos os outros dispositivos.

Enquanto a [**transcrição de conversa**](conversation-transcription.md) funciona em um único dispositivo com uma matriz de microfone multicanal, a conversa de **vários dispositivos** é adequada para cenários com vários dispositivos, cada um com um único microfone.

>[!IMPORTANT]
> A conversa de vários dispositivos **não** dá suporte ao envio de arquivos de áudio entre clientes: somente a transcrição e/ou a tradução.

## <a name="key-features"></a>Principais recursos

- **Transcrição em tempo real** – todos receberão uma transcrição da conversa, para que possam acompanhar o texto em tempo real ou salvá-lo para mais tarde.
- **Conversão em tempo real** – com mais de 70 [idiomas com suporte](language-support.md#text-languages) para tradução de texto, os usuários podem traduzir a conversa para suas linguagens preferenciais.
- **Transcrições legíveis** – a transcrição e a tradução são fáceis de acompanhar, com quebras de frase e pontuação.
- **Entrada de voz ou de texto** – cada usuário pode falar ou digitar em seu próprio dispositivo, dependendo dos recursos de suporte de idioma habilitados para o idioma escolhido do participante. Consulte o [suporte ao idioma](language-support.md#speech-to-text).
- **Retransmissão de mensagens** – o serviço de conversa de vários dispositivos distribuirá mensagens enviadas por um cliente para todos os outros, nos idiomas de sua escolha.
- **Identificação de mensagem** – cada mensagem que os usuários recebem na conversa será marcada com o apelido do usuário que o enviou.

## <a name="use-cases"></a>Casos de uso

### <a name="lightweight-conversations"></a>Conversas leves

É fácil criar e ingressar em uma conversa. Um usuário atuará como "host" e criará uma conversa, que gera um código de conversa de cinco letras aleatório e um código QR. Todos os outros usuários podem ingressar na conversa digitando o código de conversa ou verificando o código QR. 

Como os usuários se unem por meio do código de conversa e não são necessários para compartilhar informações de contato, é fácil criar conversações rápidas no local.

### <a name="inclusive-meetings"></a>Reuniões inclusivas

A transcrição e a tradução em tempo real podem ajudar a tornar as conversas acessíveis para pessoas que falam diferentes idiomas e/ou que são surdas ou difíceis de ouvir. Cada pessoa também pode participar ativamente da conversa, falando sobre seu idioma preferencial ou enviando mensagens instantâneas.

### <a name="presentations"></a>Apresentações

Você também pode fornecer legendas para apresentações e palestras na tela e nos próprios dispositivos dos membros do público. Depois que o público ingressa no código de conversa, ele pode ver a transcrição em seu idioma preferencial, em seu próprio dispositivo.

## <a name="how-it-works"></a>Como ele funciona

Todos os clientes usarão o SDK de fala para criar ou ingressar em uma conversa. O SDK de fala interage com o serviço de conversa de vários dispositivos, que gerencia o tempo de vida de uma conversa, incluindo a lista de participantes, as linguagens escolhidas por cada cliente e as mensagens enviadas.  

Cada cliente pode enviar áudio ou mensagens instantâneas. O serviço usará o reconhecimento de fala para converter áudio em texto e enviar mensagens instantâneas como estão. Se os clientes escolherem idiomas diferentes, o serviço irá traduzir todas as mensagens para os idiomas especificados de cada cliente.

![Diagrama de visão geral de conversa de vários dispositivos](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Visão geral da conversa, do host e do participante

Uma **conversa** é uma sessão que um usuário inicia para os outros usuários participantes ingressarem. Todos os clientes se conectam à conversa usando o **código de conversa** de cinco letras.

Cada conversa cria metadados que incluem:
-    Carimbos de data/hora de quando a conversa foi iniciada e encerrada
-    Lista de todos os participantes na conversa, que inclui o apelido escolhido de cada usuário e o idioma principal para a entrada de texto ou de fala.


Há dois tipos de usuários em uma conversa:  **host** e **participante**.

O **host** é o usuário que inicia uma conversa e quem atua como administrador dessa conversa.
- Cada conversa pode ter apenas um host
- O host deve estar conectado à conversa pela duração da conversa. Se o host sair da conversa, a conversa será encerrada para todos os outros participantes.
- O host tem alguns controles adicionais para gerenciar a conversa: 
    - Bloquear a conversa-impedir que participantes adicionais ingressem
    - Ativar mudo para todos os participantes – impedir que outros participantes enviem mensagens para a conversa, sejam transcritas de fala ou mensagens instantâneas
    - Desativar participantes individuais
    - Desativar mudo de todos os participantes
    - Desativar mudo de participantes individuais

Um **participante** é um usuário que ingressa em uma conversa.
- Um participante pode sair e reingressar na mesma conversa a qualquer momento, sem encerrar a conversa para outros participantes.
- Os participantes não podem bloquear a conversa ou mudo/desativar outros

> [!NOTE]
> Cada conversa pode ter até 100 participantes, dos quais 10 pode estar falando simultaneamente em um determinado momento.

## <a name="language-support"></a>Suporte ao idioma

Ao criar ou ingressar em uma conversa, cada usuário deve escolher um **idioma principal**: o idioma em que eles irão falar e enviar mensagens instantâneas e também o idioma em que eles verão as mensagens de outros usuários.

Há dois tipos de idiomas: somente de **fala a texto** e **texto**:
- Se o usuário escolher uma linguagem de **fala para texto** como seu idioma principal, ele poderá usar a fala e a entrada de texto na conversa.

- Se o usuário escolher uma linguagem **somente texto** , será possível usar apenas a entrada de texto e enviar mensagens instantâneas na conversa. As linguagens somente texto são os idiomas com suporte para tradução de texto, mas não de conversão de fala em texto. Você pode ver os idiomas disponíveis na página de [suporte ao idioma](./language-support.md) .

Além do idioma principal, cada participante também pode especificar idiomas adicionais para a tradução da conversa.

Abaixo está um resumo do que o usuário poderá fazer em uma conversa com vários dispositivos, com base no idioma principal escolhido.


| O que o usuário pode fazer na conversa | Conversão de fala em texto | Somente texto |
|-----------------------------------|----------------|------|
| Usar entrada de fala | ✔️ | ❌ |
| Enviar mensagens instantâneas | ✔️ | ✔️ |
| Traduza a conversa | ✔️ | ✔️ |

> [!NOTE]
> Para obter listas de idiomas de conversão de fala em texto e de texto disponíveis, consulte [idiomas com suporte](./language-support.md).



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Traduza conversas em tempo real](quickstarts/multi-device-conversation.md)