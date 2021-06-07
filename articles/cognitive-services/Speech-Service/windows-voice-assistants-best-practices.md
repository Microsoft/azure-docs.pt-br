---
title: Assistentes de voz no Windows-diretrizes de design
titleSuffix: Azure Cognitive Services
description: Diretrizes para práticas recomendadas ao criar uma experiência de agente de voz.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92165138"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Experiências do assistente de design para Windows 10

Os assistentes de voz desenvolvidos no Windows 10 devem implementar as diretrizes de experiência do usuário abaixo para fornecer as melhores experiências possíveis para ativação de voz no Windows 10. Este documento orientará os desenvolvedores na compreensão do trabalho principal necessário para que um assistente de voz se integre ao shell do Windows 10.

## <a name="contents"></a>Sumário

- [Resumo de exibições de ativação de voz com suporte no Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Resumo dos requisitos](#requirements-summary)
- [Práticas recomendadas para boas experiências de escuta](#best-practices-for-good-listening-experiences)
- [Diretrizes de design para ativação de voz no aplicativo](#design-guidance-for-in-app-voice-activation)
- [Diretrizes de design para ativação de voz acima do bloqueio](#design-guidance-for-voice-activation-above-lock)
- [Diretrizes de design para visualização de ativação de voz](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Resumo de exibições de ativação de voz com suporte no Windows 10

O Windows 10 infere uma experiência de ativação para o contexto do cliente com base no contexto do dispositivo. A tabela de resumo a seguir é uma visão geral de alto nível das diferentes exibições disponíveis quando a tela está ativada.

| Exibição (disponibilidade) | Contexto de dispositivo | Meta do cliente | Aparece quando | Necessidades de design |
| --- | --- | --- | --- | --- |
| **No aplicativo (19H1)** | Abaixo do bloqueio, o assistente tem foco | Interagir com o aplicativo assistente | O assistente processa a solicitação no aplicativo | Principal experiência de escuta no modo de exibição no aplicativo |
| **Bloqueio acima (19H2)** | Acima do bloqueio, não autenticado | Interagir com o assistente, mas de uma distância | O sistema está bloqueado e o assistente solicita ativação | Visuais de tela inteira para a interface do usuário de campos distantes. Implemente políticas descartáveis para não bloquear o desbloqueio. |
| **Visualização de ativação de voz (20H1)** | Abaixo do bloqueio, o assistente não tem foco | Interaja com o assistente, mas de forma menos invasiva | O sistema está abaixo do Lock and Assistant solicita ativação em segundo plano | Tela mínima. Redimensione ou passe para a exibição do aplicativo principal conforme necessário. |

## <a name="requirements-summary"></a>Resumo dos requisitos

O esforço mínimo é necessário para acessar as diferentes experiências. No entanto, os assistentes precisam implementar as diretrizes de design corretas para cada exibição. Esta tabela abaixo fornece uma lista de verificação dos requisitos que devem ser seguidos.

| **Exibição de ativação de voz** | **Resumo de requisitos do assistente** |
| --- | --- |
| **No aplicativo** | <ul><li>Processar a solicitação no aplicativo</li><li>Fornece indicadores de interface do usuário para Estados de escuta</li><li>A interface do usuário se adapta à medida que os tamanhos das janelas mudam</li></ul> |
| **Acima do bloqueio** | <ul><li>Detectar estado de bloqueio e solicitar ativação</li><li>Não fornecer UX sempre persistente que bloquearia o acesso à tela de bloqueio do Windows</li><li>Fornecer visuais de tela inteira e uma experiência de voz inicial</li><li>Honra as diretrizes descartadas abaixo</li><li>Siga as considerações de segurança e privacidade abaixo</li></ul> |
| **Visualização de ativação de voz** | <ul><li>Detectar estado de desbloqueio e solicitar ativação em segundo plano</li><li>Desenhar o mínimo de UX de escuta no painel de visualização</li><li>Desenhe um X fechado no canto superior direito e autodescarte e interrompa o streaming de áudio quando pressionado</li><li>Redimensione ou passe para a exibição do aplicativo assistente principal, conforme necessário, para fornecer respostas</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Práticas recomendadas para boas experiências de escuta

Os assistentes devem criar uma experiência de escuta para fornecer comentários críticos para que o cliente possa entender o estado do assistente. Abaixo estão alguns Estados possíveis a serem considerados ao criar uma experiência de assistente. Essas são apenas sugestões possíveis, não as diretrizes obrigatórias.

- O assistente está disponível para entrada de fala
- O assistente está no processo de ativação (uma palavra-chave ou o botão MIC)
- O assistente está transmitindo ativamente o áudio para a nuvem do assistente
- O assistente está pronto para o cliente começar a falar
- O assistente está ouvindo que as palavras estão sendo consideradas
- O assistente entende que o cliente está falando
- O assistente está processando e preparando uma resposta
- O assistente está respondendo

Mesmo que os Estados mudem rapidamente, vale a pena considerar fornecer UX para Estados, pois as durações são variáveis no ecossistema do Windows. Comentários visuais, bem como breves Chimes de áudio ou chirps, também chamados &quot; &quot; de earcons, podem fazer parte da solução. Da mesma forma, as placas visuais combinadas com descrições de áudio fazem boas opções de resposta.

## <a name="design-guidance-for-in-app-voice-activation"></a>Diretrizes de design para ativação de voz no aplicativo

Quando o aplicativo assistente tem foco, a intenção do cliente é claramente interagir com o aplicativo, de modo que todas as experiências de ativação de voz devem ser tratadas pela exibição do aplicativo principal. Este modo de exibição pode ser redimensionado pelo cliente. Para ajudar a explicar as interações do shell do assistente, o restante deste documento usa o exemplo concreto de um assistente de serviço financeiro chamado contoso. Neste e em diagramas subsequentes, o que o cliente diz aparecerá em bolhas de fala à esquerda, com respostas de assistente em bolhas de desenho à direita.

**Exibição no aplicativo. Estado inicial quando a ativação de voz começa:** 
 ![ a captura de tela mostrando o aplicativo assistente de finanças da Contoso aberto para a tela padrão. Uma bolha de fala de desenho à direita diz "contoso".](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Exibição no aplicativo. Após a ativação de voz bem-sucedida, a experiência de escuta começa:** ![ captura de tela do assistente de voz no Windows enquanto o assistente de voz está ouvindo](media/voice-assistants/windows_voice_assistant/listening.png)

**Exibição no aplicativo. Todas as respostas permanecem na experiência do aplicativo.** ![ Captura de tela do assistente de voz no Windows como respostas do assistente](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Diretrizes de design para ativação de voz acima do bloqueio

Disponível com o 19H2, os assistentes criados na plataforma de ativação do Windows Voice estão disponíveis para responder ao bloqueio acima.

### <a name="customer-opt-in"></a>Aceitação do cliente

A ativação de voz acima do bloqueio é sempre desabilitada por padrão. Os clientes optam por meio das configurações do Windows>privacidade>ativação de voz. Para obter detalhes sobre como monitorar e solicitar essa configuração, consulte o [Guia de implementação de bloqueio acima](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Não é uma substituição de tela de bloqueio

Embora as notificações ou outros pontos de integração de tela de bloqueio de aplicativo padrão permaneçam disponíveis para o assistente, a tela de bloqueio do Windows sempre define a experiência inicial do cliente até que ocorra uma ativação de voz. Depois que a ativação de voz for detectada, o aplicativo assistente aparecerá temporariamente acima da tela de bloqueio. Para evitar a confusão do cliente, quando ativo acima do bloqueio, o aplicativo assistente nunca deve apresentar a interface do usuário para solicitar qualquer tipo de credenciais ou fazer logon.

![Captura de tela de um bloqueio do Windows](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Experiência de bloqueio acima após a ativação de voz

Quando a tela estiver ativada, o aplicativo assistente ficará em tela inteira sem nenhuma barra de título acima da tela de bloqueio. Visuais maiores e descrições fortes de voz com uma interface principal de voz forte permitem casos em que o cliente está muito longe de ler a interface do usuário ou tem suas mãos ocupadas com outra tarefa (não de PC).

Quando a tela permanece desativada, o aplicativo assistente pode reproduzir um earcon para indicar que o assistente está sendo ativado e fornecer uma experiência somente de voz.

![Captura de tela do assistente de voz acima do bloqueio](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Políticas de descarte

O assistente deve implementar as orientações descartadas nesta seção para facilitar o logon dos clientes na próxima vez que desejarem usar seu computador Windows. Abaixo estão os requisitos específicos, que o assistente deve implementar:

- **Todas as telas do assistente que mostram o bloqueio acima devem conter um X** no canto superior direito que ignora o assistente.
- **Pressionar qualquer tecla também deve ignorar o aplicativo assistente**. Entrada de teclado é um sinal de aplicativo de bloqueio tradicional no qual o cliente deseja fazer logon. Portanto, qualquer entrada de teclado/texto não deve ser direcionada para o aplicativo. Em vez disso, o aplicativo deve ser disparado automaticamente quando a entrada do teclado é detectada, para que o cliente possa fazer logon facilmente em seu dispositivo.
- **Se a tela for desativada, o aplicativo deverá descartá-la automaticamente.** Isso garante que, na próxima vez que o cliente usar seu PC, a tela de logon estará pronta e aguardando.
- Se o aplicativo estiver &quot; em uso &quot; , ele poderá continuar acima do bloqueio. &quot;em uso &quot; constitui qualquer entrada ou saída. Por exemplo, ao transmitir música ou vídeo, o aplicativo pode continuar acima do bloqueio. &quot;O acompanhamento &quot; e outras etapas de caixa de diálogo de multiativação têm permissão para manter o aplicativo acima do bloqueio.
- Os **detalhes de implementação sobre como ignorar o aplicativo** podem ser encontrados [no guia de implementação de bloqueio acima](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Captura de tela mostrando a exibição de bloqueio acima do aplicativo assistente de finanças da contoso.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Captura de tela de uma área de trabalho mostrando o Windows Lock Screen.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>Considerações de segurança de privacidade &amp; acima do bloqueio

Muitos PCs são portáteis, mas nem sempre dentro do alcance do cliente. Elas podem ser brevemente deixadas em salas de Hotel, estações de avião ou espaços de trabalho, onde outras pessoas têm acesso físico. Se os assistentes que estão habilitados acima do bloqueio não estiverem preparados, eles poderão se tornar sujeitos à classe dos chamados &quot; ataques [Maids nocivos](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot; .

Portanto, os assistentes devem seguir as orientações desta seção para ajudar a manter a experiência segura. A interação acima do bloqueio ocorre quando o usuário do Windows não está autenticado. Isso significa que, em geral, a **entrada para o assistente também deve ser tratada como não autenticada**.

- Os assistentes devem **implementar uma lista de habilidades permitidas para identificar as habilidades que são confirmadas de forma segura e** segura para serem acessadas acima do bloqueio.
- As tecnologias de ID do palestrante podem desempenhar uma função para aliviar alguns riscos, mas a ID do palestrante não é uma substituição adequada para a autenticação do Windows.
- A lista de habilidades permitidas deve considerar três classes de ações ou habilidades:

| **Classe de ação** | **Descrição** | **Exemplos (não é uma lista completa)** |
| --- | --- | --- |
| Seguro sem autenticação | Informações de uso geral ou comando e controle do aplicativo básico | &quot;Qual é o tempo? &quot; , &quot; Jogue a próxima faixa&quot; |
| Seguro com ID do palestrante | Risco de representação, revelando informações pessoais. | &quot;O que&#39;s meu próximo compromisso? &quot; , &quot; examine minha lista de compras &quot; , &quot; responda à chamada&quot; |
| Seguro somente após a autenticação do Windows | Ações de alto risco que um invasor poderia usar para danificar o cliente | &quot;Comprar mais supermercado &quot; , &quot; excluir meu compromisso (importante) &quot; , &quot; Enviar uma mensagem de texto (Mean) &quot; , &quot; iniciar uma página da Web do (perigoso)&quot; |

Para o caso da Contoso, informações gerais sobre informações de ações públicas são seguras sem autenticação. Informações específicas do cliente, como o número de compartilhamentos de propriedade, provavelmente são seguras com a ID do palestrante. No entanto, comprar ou vender ações nunca deve ser permitido sem a autenticação do Windows.

Para proteger ainda mais a experiência, os **weblinks ou outras inicializações de aplicativo para aplicativo sempre serão bloqueadas pelo Windows até que o cliente entre.** Como última mitigação de Resort, a Microsoft se reserva o direito de remover um aplicativo da lista permitida de assistentes habilitados se um problema sério de segurança não for resolvido em tempo hábil.

## <a name="design-guidance-for-voice-activation-preview"></a>Diretrizes de design para visualização de ativação de voz

Abaixo do bloqueio, quando o aplicativo assistente _não tem foco_ , o Windows fornece uma interface de usuário de ativação de voz menos invasiva para ajudar a manter o cliente em fluxo. Isso é especialmente verdadeiro para o caso de ativações falsas que seriam altamente perturbadoras se iniciarem o aplicativo completo. A ideia principal é que cada assistente tem outra página inicial no Shell, o ícone da barra de tarefas do assistente. Quando ocorre a solicitação de ativação em segundo plano, é exibida uma exibição pequena acima do ícone da barra de tarefas do assistente. Os assistentes devem fornecer uma pequena experiência de escuta nesta tela. Depois de processar as solicitações, os assistentes podem optar por redimensionar essa exibição para mostrar uma resposta no contexto ou para entregar sua exibição do aplicativo principal para mostrar visuais maiores e mais detalhados.

- Para ficar mínimo, a visualização não tem uma barra de título, portanto, **o assistente deve desenhar um X no canto superior direito para permitir que os clientes ignorem a exibição.** Consulte [fechar o aplicativo](windows-voice-assistants-implementation-guide.md#closing-the-application) para que as APIs específicas chamem quando o botão ignorar for pressionado.
- Para dar suporte a visualizações de ativação de voz, os assistentes podem convidar os clientes a fixar o assistente na barra de tarefas durante a primeira execução.

**Visualização de ativação de voz: estado inicial**

O assistente contoso tem uma página inicial na barra de tarefas: seu ícone de espiral, circular.

![Captura de tela do assistente de voz no Windows como um ícone de barra de tarefas pré-ativação](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**À medida que a ativação progride**, o assistente solicita ativação em segundo plano. O assistente recebe um pequeno painel de visualização (largura padrão 408 e altura: 248). Se a ativação de voz do lado do servidor determinar que o sinal era um falso positivo, essa exibição poderá ser ignorada para interrupção mínima.

![Captura de tela do assistente de voz no Windows na exibição compacta ao verificar a ativação](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Quando a ativação final é confirmada**, o assistente apresenta seu UX de escuta. O assistente deve sempre desenhar um dispensar X no canto superior direito da visualização de ativação de voz.

![Captura de tela do assistente de voz no Windows ouvindo na exibição compacta](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

As **respostas rápidas** podem ser mostradas na visualização de ativação de voz. Um TryResizeView permitirá que os assistentes solicitem tamanhos diferentes.

![Captura de tela do assistente de voz no Windows respondendo na exibição compacta](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Entrega**. A qualquer momento, o assistente pode entregar a sua exibição principal do aplicativo para fornecer mais informações, diálogos ou respostas que exigem mais espaço na tela. Veja a seção [transição da exibição compacta para a exibição completa](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) para obter detalhes de implementação.

![Capturas de tela do assistente de voz no Windows antes e depois da expansão da exibição compacta](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Comece a desenvolver seu assistente de voz](how-to-windows-voice-assistants-get-started.md)