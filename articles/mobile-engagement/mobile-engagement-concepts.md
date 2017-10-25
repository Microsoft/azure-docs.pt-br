---
title: Conceitos do Mobile Engagement | Microsoft Docs
description: Conceitos do Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8d19abd1-0a6c-4772-9fa5-5e99980ac5da
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8450651528007b4527366b89a6ad7615169f93c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-concepts"></a>Conceitos do Azure Mobile Engagement
O Mobile Engagement define alguns conceitos comuns a todas as plataformas com suporte. Este artigo descreve resumidamente os conceitos.

Este artigo é um bom começo, se você for novo no Mobile Engagement. Além disso, leia a documentação específica da plataforma que você está usando, como ela refinará os conceitos descritos neste artigo com mais detalhes e exemplos, bem como possíveis limitações.

## <a name="devices-and-users"></a>Dispositivos e usuários
O Mobile Engagement identifica os usuários gerando um identificador exclusivo para cada dispositivo. Esse identificador é chamado de identificador de dispositivo (ou `deviceid`). Ele é gerado de forma que a execução de todos os aplicativos do mesmo dispositivo compartilha o mesmo identificador de dispositivo.

Implicitamente, significa que o Mobile Engagement considera que um dispositivo deve pertencer a um usuário e, assim, os usuários e dispositivos são conceitos equivalentes.

## <a name="sessions-and-activities"></a>Sessões e atividades
Uma sessão é um uso do aplicativo executado por um usuário, desde o momento em que ele começa a usá-lo até quando ele para.

Uma atividade é o uso de uma determinada subpeça do aplicativo executado por um usuário (geralmente é uma tela, mas ele pode ser qualquer coisa adequada para o aplicativo).

Um usuário só pode executar uma atividade por vez.

Uma atividade é identificada por um nome (limitado a 64 caracteres) e pode, opcionalmente, inserir alguns dados adicionais (no limite de 1024 bytes).

As sessões são computadas automaticamente da sequência de atividades executadas pelos usuários. Uma sessão começa quando o usuário começa a sua primeira atividade e para quando ele termina sua última atividade. Isso significa que as sessões não precisam ser iniciadas ou paradas explicitamente. Em vez disso, as atividades são explicitamente iniciadas ou interrompidas. Se nenhuma atividade for relatada, nenhuma sessão será relatada.

## <a name="events"></a>Eventos
Os Eventos são usados para relatar ações de instantâneas (como um botão pressionado ou artigos lidos por usuários).

Um evento pode estar relacionado à sessão atual, como um trabalho em execução ou pode ser um evento independente.

Um evento é identificado por um nome (limitado a 64 caracteres) e pode, opcionalmente, inserir alguns dados adicionais (no limite de 1024 bytes).

## <a name="error"></a>Erro
Os Erros são usados para relatar problemas detectados corretamente pelo aplicativo (como ações incorretas do usuário ou falhas nas chamadas à API).

Um erro pode estar relacionado à sessão atual, como um trabalho em execução ou pode ser um erro independente.

Um erro é identificado por um nome (limitado a 64 caracteres) e pode, opcionalmente, inserir alguns dados adicionais (no limite de 1024 bytes).

## <a name="job"></a>Trabalho
Os Trabalhos são usados para relatar ações que tenham uma duração (como a duração das chamadas de API, exibir o tempo de anúncios, a duração das tarefas em segundo plano ou a duração das ações do usuário).

Um trabalho não está relacionado a uma sessão, pois a tarefa pode ser executada em segundo plano, sem qualquer interação do usuário.

Um trabalho é identificado por um nome (limitado a 64 caracteres) e pode, opcionalmente, inserir alguns dados adicionais (no limite de 1024 bytes).

## <a name="crash"></a>Falha
As Falhas são emitidas automaticamente pelo SDK do Mobile Engagement para falhas de aplicativo de relatório em que os problemas não detectados pelo aplicativo o fazem falhar.

## <a name="application-information"></a>Informações do aplicativo
As informações do aplicativo (ou as informações do aplicativo) são usadas para marcar usuários, por exemplo, para associar alguns dados aos usuários de um aplicativo (semelhante aos cookies da web, exceto que as informações do aplicativo sejam armazenadas no lado do servidor na plataforma do Azure Mobile Engagement).

As informações de um aplicativo podem ser registradas usando a API do SDK do Mobile Engagement ou usando a API do dispositivo da plataforma do Mobile Engagement.

As informações de um aplicativo são um par chave/valor associado a um dispositivo. A chave é o nome das informações do aplicativo (limitado a 64 letras ASCII [a-zA-Z], números [0-9] e sublinhados [_]). O valor (limitado a 1024 caracteres) pode ser qualquer cadeia de caracteres, inteiro, data (aaaa-MM-dd) ou booliano (verdadeiro ou falso).

Qualquer número de informações de aplicativo pode ser associado a um dispositivo, dentro dos limites definidos pelos termos de preços do Mobile Engagement. Para uma determinada chave, o Mobile Engagement controla apenas o conjunto mais recente de valor (nenhum histórico). Definir ou alterar o valor das informações de um aplicativo força o Mobile Engagement a reavaliar o conjunto de critérios de público definido nessas informações do aplicativo (se houver), o que significa que essas informações de aplicativo podem ser usadas para os pushes do gatilho em tempo real.

## <a name="extra-data"></a>Dados extras
Os dados extras (ou extras) são alguns dados arbitrários que podem ser anexados a trabalhos, atividades, erros e eventos.

Os extras são estruturados de forma semelhante aos objetos JSON: são feitos de de uma árvore de pares chave/valor. As chaves são limitadas a 64 letras ASCII [a-zA-Z], números [0-9] e sublinhados [_]) e o tamanho total de extras é limitado a 1024 caracteres (uma vez codificados em JSON pelo SDK do Mobile Engagement).

A árvore inteira de pares chave/valor é armazenada como um objeto JSON. No entanto, somente o primeiro nível de chaves/valores é decomposto para estar diretamente acessível para algumas funções avançadas como Segmentos (por exemplo, você pode definir facilmente um segmento chamado “Fãs de ficção científica” composto por todos os usuários que enviaram pelo menos 10 vezes o evento chamado "content_viewed" com a chave extra "content_type" definida como o valor "scifi" no último mês). Ele, portanto, é altamente recomendável para enviar apenas extras feitos de listas simples de pares chave/valor usando valores escalares (por exemplo, cadeias de caracteres, datas, números inteiros ou booliano).

## <a name="next-steps"></a>Próximas etapas
* [Visão geral do SDK do Windows Universal para o Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
* [Visão geral do SDK do Windows Phone Silverlight para o Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
* [SDK do iOS para o Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
* [SDK do Android do Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)

