---
title: "Definir sua estratégia do Mobile Engagement | Microsoft Docs"
description: "Saiba como carregar e otimizar o Mobile Engagement com análises e notificações por push."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7533e318-81b9-4360-aace-b7be8225985b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: db68bd3ebf7efde1d23820445f07c83c8c27dc20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="define-your-mobile-engagement-strategy"></a>Definir sua estratégia do Mobile Engagement
*Você escreveu o seu aplicativo por um motivo: para que os usuários o usem!*

Acreditamos que você se esforce bastante tentando torná-lo um grande aplicativo que agradará muito aos usuários. Você provavelmente também investiu uma quantidade considerável de orçamento de marketing para adquirir os usuários. Mas, após o pico inicial e animador de usuários, você pode perceber que eles, lentamente, param de usar seu aplicativo. *E é para isso que o Azure Mobile Engagement serve!*: para fazer com que eles permaneçam por perto e para permitir que você melhore incrementalmente seu aplicativo por meio de testes e aprenda.

Nossa abordagem para melhorar o uso e a retenção baseia-se em envolver os usuários do aplicativo por meio de notificações por push e mensagens no aplicativo, mas de uma maneira muito especial, com mensagens e uma comunicação adaptadas a eles e de acordo com seus comportamentos no aplicativo. Nossa meta é permitir que você se comunique com o público certo no momento certo e o local certo.

Mas, para isso, você precisará começar *entendendo seus usuários*e depois criar grupos de acordo com suas ações ou características (nós a chamamos de segmentos) e criar comunicações relevantes para cada segmento.

## <a name="mobile-engagement-serves-your-objectives"></a>O Mobile Engagement atende aos seus objetivos
*Falamos sobre a retenção e o uso, mas para quê?*

Criar sua estratégia do Mobile Engagement requer analisar primeiro os objetivos e KPIs (indicadores chave de desempenho) de seu aplicativo.

Comece definindo os objetivos e KPIs que ajudam a definir seus casos de uso de envolvimento com o prisma correto.

Casos de uso são uma lista simples de campanhas que você gostaria de fazer para se comunicar com os usuários, desde uma simples Boas-vindas até a notificação de utilitário muito avançado disparada pelo seu sistema de TI. Um caso de uso bem elaborado deve incluir pelo menos as três perguntas – *O quê? Quem? Quando?*:

1. Uma designação muito breve (por exemplo, “Campanha de boas-vindas”).
2. **O quê?**: um exemplo de mensagem (por exemplo, “Estamos felizes em tê-lo conosco! Lembre-se de fazer logon para obter seu primeiro mês gratuito!"). Esta mensagem não é, de forma alguma, definitiva; você poderá alterá-la sempre que desejar, mas geralmente ela é útil para começar a pensar sobre o que queremos dizer.
3. **Quem?**: o segmento que receberá essa mensagem (por exemplo, “Todos os usuários que iniciaram o aplicativo pela primeira vez três dias atrás, visitaram a página de logon, mas que não fizeram logon”).
   * Sim, você pode fazer isso facilmente com o Azure Mobile Engagement:)
   * Lembrando que isso não precisa ser definitivo, pois você pode definir os segmentos a qualquer momento; porém, é importante definir seus critérios de segmentação logo no início para garantir que você colete os dados certos.
4. **Quando**: o tempo de sua campanha. Ele pode ser em uma determinada data ou depois de uma ação específica, com base em um gatilho. O Mobile Engagement oferece um número significativo de possibilidades para ajustar adequadamente sua comunicação.

Uma vez definidos os casos de uso e o segmento, ele fornece uma diretriz para definir os dados que devem ser coletados em um aplicativo. Essa é a função de um *“plano de marca”*. Um plano de marca permite que você garanta que a coleta de dados seja especificada para os desenvolvedores. Dessa forma, os desenvolvedores são capazes de incorporar o Mobile Engagement à configuração certa para você trabalhar suas campanhas com os dados corretos. Também é muito importante executar testes para garantir que a integração esteja correta e que ela colete aquilo que você precisa.

Com base na integração, depois que os aplicativos são publicados, você, como profissional de marketing, poderá ver sua análise em tempo real, segmentar seu público e depois começar a enviar uma notificação por push inteligente e direcionada para se envolver com os usuários finais dentro ou fora do aplicativo.

### <a name="use-cases-to-get-started"></a>Casos de uso para começar
1. Estratégia de boas-vindas: crie várias campanhas de notificação por push com base no comportamento do usuário final na inicialização do aplicativo para envolver novamente D+2/5/10/15 após a primeira sessão e aumentar a primeira a retenção de execução.
2. Promova um novo conteúdo (recurso, artigo/vídeo ou produto) de acordo com o comportamento do usuário final para enviar as informações apenas para os usuários finais que têm mais probabilidade de se envolverem.
3. Avalie o aplicativo: Tenha como meta menos de 1% de sua base de usuários com maior probabilidade de avaliar o aplicativo como 5 estrelas na loja.
4. Aumente as assinaturas: promova conteúdo valioso para usuários finais que ainda não os viram para aumentar as assinaturas.
5. Tutorial: o tutorial não é mais obrigatório para todos. Por que não compilar excelentes tutoriais no aplicativo e depois dispará-los por meio de mensagens no aplicativo apenas se o usuário parecer não estar usando o aplicativo ou ter dificuldades ao usar um recurso?

## <a name="why-do-you-need-analytics-to-engage"></a>Por que você precisa de análise para o envolvimento?
Como você pode perceber neste ponto, fazer uma notificação de envio de difusão apenas não é suficiente. O principal conceito do Mobile Engagement é ajudar os profissionais de marketing e os desenvolvedores a envolver os usuários finais certos, no momento certo e no lugar certo. Para saber os três principais conceitos, é essencial reunir as análises de seu aplicativo e, em seguida, usá-las para o público-alvo do segmento. Isso também é ainda mais eficiente quando os segmentos de comportamento complementam os dados de seu banco de dados ou CRM ou de um canal de distribuição. O Mobile Engagement permite recuperar dados de qualquer lugar e usa-o para o público-alvo certo.

Para ser o mais contextual possível quando envolver seu público, é crucial ter conhecimento sobre o comportamento dos usuários finais, para saber o status em tempo real. A coleta de dados permite que os profissionais de marketing se concentrem no que realmente importa para realizar casos de uso e atingir os objetivos da estratégia de envolvimento. Atingir os objetivos descritos acima também é a razão pela qual a prática recomendada é, de fato, não reunir tudo na análise, mas apenas aquilo que permita que você se concentre naquilo que quer saber e nos casos de uso. Essa é uma boa maneira de começar, experimentar, testar e aprender a usar a solução, bem como abordar uma notificação por push inteligente e aumentar a retenção de um aplicativo para colocá-lo no nível de uma história de sucesso.

> [!NOTE]
> Lembre-se: uma quantidade excessiva de dados destroem os dados!
> 
> 

### <a name="use-cases-and-best-practices"></a>Casos de uso e práticas recomendadas
Nas próximas seções, abordaremos brevemente alguns dos principais casos de uso que aconteceram com nossos clientes para dar a você algumas noções básicas.

#### <a name="media"></a>Mídia
Colete o tipo de conteúdo que é consumido pelo usuário final e depois segmente o público de acordo com esse comportamento para ter como alvo tipos específicos de conteúdo apenas para um público que provavelmente o consumirá. Evita gerar um spam para todos os usuários e garante uma retenção melhor.

#### <a name="m-commerce"></a>M-comércio
Colete as categorias de produtos mais visitadas dentro do aplicativo e o público alvo para promover um desconto ou um novo produto nessa categoria que o usuário final terá mais probabilidade de comprar. Objetivo de aumentar a receita. Novamente, o objetivo é evitar spam!

#### <a name="gaming"></a>Jogos
Colete o nível de jogo de um usuário final e o tempo gasto em um determinado período para ter como alvo o público que poderia ficar estagnado e que teria mais probabilidade de passar para um próximo nível com uma oferta de bônus.

Comunique eventos específicos com um incentivo aos usuários que não jogaram por algum tempo para tentar incentivá-los a voltar.

#### <a name="retail"></a>Varejo
Colete os produtos ou as marcas que um determinado público tem mais probabilidade de consumir de acordo com seus favoritos ou comportamentos e conduza-o à sua loja para aumentar as receitas de compras.

#### <a name="banking"></a>Serviços bancários
Colete dados dos usuários finais que criaram uma conta na primeira inicialização do aplicativo. Tenha como objetivo implantar uma estratégia de boas-vindas com uma notificação por push direcionada e aumente o número de assinaturas de conta.

### <a name="how-to-create-a-great-tag-plan"></a>Como criar um bom plano de marca?
Um plano de marca deve ser como uma descrição do caminho de usuário ou um tipo de fluxo de trabalho do aplicativo, fornecendo todas as marcas necessárias (dados) que devem ser coletadas para que se tenha uma análise suficiente para entender o comportamento do usuário e segmentar corretamente a base de usuários. Isso não é um processo técnico. Portanto, os comerciantes são capazes de especificar os dados que desejam coletar, com base em sua estratégia do Mobile Engagement.

O requisito mínimo é marcar pelo menos todas as telas (chamadas de *atividades* no Mobile Engagement) de um aplicativo. Isso ajuda a determinar a trajetória do usuário.

Uma atividade pode inserir *eventos* que coletam informações sobre ações, como clicar em um botão. Isso permite coletar as interações realizadas no aplicativo. Portanto, os profissionais de marketing conseguem saber qual tela os usuários estão visitando e o que eles estão fazendo.

`Jobs` são ações com uma duração. Isso é muito útil para o profissional de marketing entender quanto tempo leva para um usuário criar uma conta ou fazer logon, por exemplo. Isso também pode ser útil para os desenvolvedores monitorarem quanto tempo levará para chamar um serviço Web.

`Errors` também podem ser monitorados para saber se os usuários estão tendo problemas em seu aplicativo. Por exemplo, ter problemas de conexão com frequência.

Todos esses tipos de dados podem ser aumentados com parâmetros (*informações extra* no Mobile Engagement) que permitem que você colete dados dinâmicos do aplicativo. Isso é importante para possibilitar uma segmentação refinada. Por exemplo, os profissionais de marketing podem segmentar um usuário de acordo com o tipo de conteúdo que ele consumiu. O tipo de conteúdo serão as informações dinâmicas de uma atividade ou de um evento.

*Informações do aplicativo* são dados que permitem confirmar o status do aplicativo ou do usuário em tempo real. Isso também ajuda a categorizar uma base de público e segmentá-la rapidamente. Por exemplo, elas podem usar um status de verdadeiro/falso que indica se o usuário está fazendo logon ou não, ou usar sua data de expiração da assinatura.

#### <a name="example-of-tags"></a>Exemplo de marcas
*Caso de uso: segmente o comportamento do público para ter como alvo o usuário final certo para o conteúdo certo da notificação por push*

1. Envie uma notificação por push para promover uma categoria de produto: colete dados de comportamento para segmentar um público de acordo com a categoria de produto que ele visitou X vezes em um determinado período ou um item específico que ele adicionou a um carrinho. Os dados coletados permitirão segmentar e depois enviar uma notificação por push ao público certo.
2. Avalie o aplicativo: colete dados de acordo com o conteúdo compartilhado pelo público em uma rede social. Tem como objetivo segmentar o público determinando os *embaixadores* de seu aplicativo. Quando estiver usando uma notificação por push no aplicativo, os embaixadores são o melhor público de seu aplicativo para solicitar uma avaliação de 5 estrelas do seu aplicativo na loja.
   
   ![][1]

*Caso de uso: dados declarativos*

1. Notícias sobre o alerta de segmento: colete dados declarativos para o público do segmento com base nas suas preferências. Ele permite enviar notificação por push de um tópico específico que realmente interessa a um público específico.
2. Público do segmento com base no status de logon. Colete dados para saber se um usuário está conectado ou se criou uma conta. Ajuda a ter como alvo os usuários finais que ainda não fizeram logon e envia uma notificação por push para incentivá-los a converter.
   ![][2]

### <a name="next-steps"></a>Próximas etapas
* Visite [Conceitos do Mobile Engagement] para saber mais sobre os conceitos básicos do Mobile Engagement.
* Visite [Criar um aplicativo do Mobile Engagement](mobile-engagement-create.md) para criar uma nova Coleção de Aplicativos do Mobile Engagement no Azure e começar a gerenciar seus aplicativos com o portal do Mobile Engagement.
* Visite [Práticas recomendadas](mobile-engagement-getting-started-best-practices.md) para conhecer os detalhes.
* Visite o [cenário de Aplicativo de Jogos](mobile-engagement-gaming-scenario.md) para saber mais sobre a implementação do Mobile Engagement com um aplicativo de jogos de exemplo. 
* Visite o [cenário de Aplicativo de Mídia](mobile-engagement-media-scenario.md) para saber mais sobre a implementação do Mobile Engagement com um aplicativo de mídia de exemplo. 
* Visite os [Tutoriais] para saber mais sobre a implementação.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Conceitos do Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Tutoriais]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

