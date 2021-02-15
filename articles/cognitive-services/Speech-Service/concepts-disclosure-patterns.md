---
title: Padrões de design de divulgação
titleSuffix: Azure Cognitive Services
description: Padrões de design e práticas recomendadas para divulgação.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 425096bf7cb75ae3560c06cbcd32778536dc1952
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537333"
---
# <a name="disclosure-design-patterns"></a>Padrões de design de divulgação
Agora que você&#39;foi determinado o [nível certo de divulgação](concepts-disclosure-guidelines.md#disclosure-assessment) para sua experiência de voz sintética, ele&#39;um bom momento para explorar os padrões de design potenciais.
## <a name="overview"></a>Visão geral
Há um espectro de padrões de design de divulgação que você pode aplicar à sua experiência de voz sintética. Se o resultado da sua avaliação de divulgação foi ' alta divulgação ', recomendamos a [**divulgação explícita**](#explicit-disclosure), o que significa comunicar as origens da voz sintética imediatamente. A [**divulgação implícita**](#implicit-disclosure) inclui indicações e padrões de interação que beneficiam experiências de voz se os níveis de divulgação necessários são altos ou baixos.
![Espectro de padrões de divulgação](media/responsible-ai/disclosure-patterns/affordances.png)






| Padrões de divulgação explícitos                                                                                                                                                                                    | Padrões de divulgação implícita                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Introdução transparente](#transparent-introduction)<br> [Introdução transparente verbal](#verbal-transparent-introduction)<br>  [Autor explícito](#explicit-byline)<br>  [Personalização e calibragem](#customization-and-calibration)<br> [Divulgação de pais](#parental-disclosure)<br> [Fornecendo oportunidades para saber mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Divulgação de capacidade](#capability-disclosure)<br>[Sugestões e comentários implícitos](#implicit-cues--feedback)<br> [Transparência da conversa](#conversational-transparency) |



Use o gráfico a seguir para consultar diretamente os padrões que se aplicam à sua voz sintética. Algumas das outras condições neste gráfico também podem se aplicar ao seu cenário:<br/>



| Se sua experiência de voz sintética... | Recomendações | Padrões de design |
| --- | --- | --- |
| Requer alta divulgação  | Use pelo menos um padrão explícito e indicações implícitas até o início para ajudar os usuários a criarem associações. |[Divulgação explícita](#explicit-disclosure)<br>[Divulgação implícita](#implicit-disclosure)  |
| Requer baixa divulgação | A divulgação pode ser mínima ou desnecessária, mas pode se beneficiar de alguns padrões implícitos. | [Divulgação de capacidade](#capability-disclosure)<br>[Transparência da conversa](#conversational-transparency)  |
| Tem um alto nível de envolvimento | Crie a longo prazo e ofereça vários pontos de entrada para divulgação ao longo do percurso do usuário. É altamente recomendável ter uma experiência de integração. | [Introdução transparente](#transparent-introduction)<br>[Personalização e calibragem](#customization-and-calibration)<br>[Divulgação de capacidade](#capability-disclosure) |
| Inclui filhos como o público-alvo principal | Direcione os pais como o público de divulgação principal e garanta que eles possam comunicar efetivamente a divulgação para os filhos.  | [Divulgação de pais](#parental-disclosure)<br>[Introdução transparente verbal](#verbal-transparent-introduction)<br> [Divulgação implícita](#implicit-disclosure)<br> [Transparência da conversa](#conversational-transparency)  |
| Inclui usuários cegos ou pessoas com deficiência visual como o público-alvo principal  | Ser inclusivo de todos os usuários e garantir que qualquer forma de divulgação Visual tenha associado um texto alternativo ou efeitos sonoros. Aderir aos padrões de acessibilidade para taxa de contraste e tamanho de exibição. Use indicações de auditoria para comunicar a divulgação.  | [Introdução transparente verbal](#verbal-transparent-introduction) <br>[Indicações de auditoria](#implicit-cues--feedback)<br>[Indicações de Haptic](#implicit-cues--feedback)<br>[Transparência da conversa](#conversational-transparency)<br>[Padrões de acessibilidade](https://www.microsoft.com/accessibility) |
| É sem tela, sem dispositivo ou usa voz como o modo de interação principal ou somente | Use indicações de auditoria para comunicar a divulgação. | [Introdução transparente verbal](#verbal-transparent-introduction) <br> [Indicações de auditoria](#implicit-cues--feedback)  |
| Potencialmente inclui vários usuários/ouvintes (por exemplo, assistente pessoal em vários residências)  | Lembre-se de vários contextos de usuário e níveis de compreensão e oferta de várias oportunidades de divulgação na jornada do usuário.  | [Introdução transparente (usuário de retorno)](#transparent-introduction)<br> [Fornecendo oportunidades para saber mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Transparência da conversa](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Divulgação explícita
Se sua experiência de voz sintética exigir uma divulgação alta, é melhor usar pelo menos um dos padrões explícitos a seguir para declarar claramente a natureza sintética.
### <a name="transparent-introduction"></a>Introdução transparente

Antes que a experiência de voz comece, apresente o assistente digital ao ser totalmente transparente sobre as origens de sua voz e seus recursos. O melhor momento para usar esse padrão é ao integrar um novo usuário ou ao introduzir novos recursos a um usuário de retorno. A implementação de indicações implícitas durante uma introdução ajuda os usuários a formar um modelo mental sobre a natureza sintética do agente digital.

#### <a name="first-time-user-experience"></a>Primeira experiência do usuário

![Introdução transparente durante a primeira experiência de execução](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*A voz sintética é introduzida durante a integração de um novo usuário.*

Recomendações
- Descreva que a voz é artificial (por exemplo, &quot; digital &quot; )
- Descrever o que o agente é capaz de fazer
- Declarar explicitamente as origens de&#39;s de voz
- Oferecer um ponto de entrada para saber mais sobre a voz sintética

#### <a name="returning-user-experience"></a>Retornando a experiência do usuário

Se um usuário ignorar a experiência de integração, continue a oferecer pontos de entrada para a experiência de introdução transparente até que o usuário dispare a voz pela primeira vez.
<br/>

![Introdução transparente durante a experiência do usuário de retorno](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Forneça um ponto de entrada consistente para a experiência de voz sintética. Permita que o usuário retorne à experiência de integração quando disparar a voz pela primeira vez em qualquer ponto do percurso do usuário.*


### <a name="verbal-transparent-introduction"></a>Introdução transparente verbal

Um aviso falado informando que as origens do assistente digital&#39;s Voice são explícitas o suficiente para alcançar a divulgação. Esse padrão é melhor para cenários de alta divulgação em que a voz é o único modo de interação disponível.
<br/>

![Introdução transparente falada verbalmente](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Use uma introdução transparente quando houver momentos na experiência do usuário em que você já pode introduzir ou atribuir uma pessoa&#39;s Voice.*


![Introdução transparente falada verbalmente na primeira pessoa](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Para obter mais transparência, o ator de voz pode divulgar as origens da voz sintética na primeira pessoa.*

### <a name="explicit-byline"></a>Autor explícito

Use esse padrão se o usuário estiver interagindo com um player de áudio ou um componente interativo para disparar a voz.


![Subtítulo explícito em um cenário de mídia de notícias](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Um subtítulo explícito é a atribuição de onde veio a voz.*

Recomendações

- Ponto de entrada da oferta para saber mais sobre a voz sintetizada

### <a name="customization-and-calibration"></a>Personalização e calibragem

Fornecer aos usuários o controle sobre como o assistente digital responde a eles (ou seja, como os sons de voz).  Quando um usuário interage com um sistema em seus próprios termos e com metas específicas em mente, por definição, eles já compreenderam que&#39;s não é uma pessoa real.

#### <a name="user-control"></a>Controle de Usuário

Ofereça opções que tenham um impacto significativo e perceptível sobre a experiência de voz sintética.

![Preferências do usuário](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*As preferências do usuário permitem que os usuários personalizem e aprimorem sua experiência.*

Recomendações

- Permitir que os usuários personalizem a voz (por exemplo, selecione o tipo de idioma e voz)
- Fornecer aos usuários uma maneira de ensinar o sistema a responder à sua voz exclusiva (por exemplo, calibragem de voz, comandos personalizados)
- Otimizar para interações contextuais ou geradas pelo usuário (por exemplo, lembretes)

#### <a name="persona-customization"></a>Personalização de persona

Oferecem maneiras de personalizar o assistente digital&#39;s Voice. Se a voz for baseada em um celebridade ou uma pessoa amplamente reconhecível, considere o uso de introduções visuais e faladas quando os usuários visualizarem a voz.

![Personalização de voz](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*A oferta da capacidade de selecionar a partir de um conjunto de vozes ajuda a transmitir a natureza artificial.*

Recomendações
- Permitir que os usuários visualizem o som de cada voz
- Usar uma introdução autêntica para cada voz
- Pontos de entrada da oferta para saber mais sobre a voz sintetizada

### <a name="parental-disclosure"></a>Divulgação de pais

Além de estar em conformidade com as normas de COPPA, forneça divulgação para os pais se seu público-alvo principal for crianças pequenas e seu nível de exposição for alto. Para usos confidenciais, considere a possibilidade de obter a experiência até que um adulto confirme o uso da voz sintética. Incentive os pais a comunicar a mensagem com seus filhos.

![Divulgação para pais](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Uma introdução transparente otimizada para pais garante que um adulto tenha consciência da natureza sintética da voz antes que um filho interaja com ela.*

Recomendações

- Direcionar os pais como o público-alvo principal para divulgação
- Incentive os pais a comunicar a divulgação para seus filhos
- Pontos de entrada da oferta para saber mais sobre a voz sintetizada
- Portão a experiência fazendo com que os pais &quot; tenham uma pergunta de proteção simples &quot; para mostrar que eles lerem a divulgação

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Fornecendo oportunidades para saber mais sobre como a voz foi feita

Ofereça pontos de entrada sensíveis ao contexto a uma página, pop-up ou site externo que fornece mais informações sobre a tecnologia de voz sintética. Por exemplo, você pode retonar um link para saber mais durante a integração ou quando o usuário solicitar mais informações durante a conversa.

![Ponto de entrada para saber mais](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Exemplo de um ponto de entrada para oferecer a oportunidade de saber mais sobre a voz sintetizada.*

Quando um usuário solicita mais informações sobre a voz sintética, o objetivo principal é instruí-los sobre as origens da voz sintética e ser transparente sobre a tecnologia.

![Fornecer aos usuários mais informações sobre voz sintética](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Mais informações podem ser oferecidas em um site de ajuda do site externo.*

Recomendações

- Simplifique conceitos complexos e evite usar o legalese e o jargão técnico
- Não encontram esse conteúdo em termos de privacidade e de instruções de uso
- Manter o conteúdo conciso e usar imagens quando disponível

## <a name="implicit-disclosure"></a>Divulgação implícita

A consistência é a chave para alcançar a divulgação implicitamente durante o percurso do usuário. O uso consistente de indicações visuais e auditivas em dispositivos e modos de interação pode ajudar a criar associações entre padrões implícitos e divulgação explícita.

![Consistência de indicações implícitas](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Sugestões de & de indicações implícitas

O anthropomorphism pode manifestar de maneiras diferentes, desde a representação visual real do agente até a voz, sons, padrões de luz, formas saltadoras ou até mesmo a vibração de um dispositivo. Ao definir sua pessoa, utilize indicações implícitas e padrões de comentários em vez de visar um avatar muito humano. Essa é uma maneira de minimizar a necessidade de divulgação mais explícita.

![Dicas visuais e comentários](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Essas indicações ajudam a anthropomorphizer o agente sem ser muito humano. Eles também podem se tornar mecanismos de divulgação efetivos por conta própria quando usados consistentemente ao longo do tempo.*

Considere os diferentes modos de interações de sua experiência ao incorporar os seguintes tipos de indicações:

| Indicações visuais                                                                                                                                                               | Indicações de auditoria                                                      | Indicações de Haptic |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Indicações em tempo real responsivas (por exemplo, animações)<br> Indicações de não tela (por exemplo, luzes e padrões em um dispositivo)<br>  | Sonic (por exemplo, um breve som distintivo, série de observações musicais) | Vibração   |

### <a name="capability-disclosure"></a>Divulgação de capacidade

A divulgação pode ser obtida implicitamente definindo expectativas precisas para o que o assistente digital é capaz de fazer. Forneça comandos de exemplo para que os usuários possam aprender a interagir com o assistente digital e oferecer ajuda contextual para saber mais sobre a voz sintética durante os estágios iniciais da experiência.

![Exemplo de respostas padrão a uma conversa que você pode criar.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Transparência da conversa

Quando as conversas se enquadram em caminhos inesperados, considere a criação de respostas padrão que podem ajudar a redefinir as expectativas, reforçar a transparência e direcionar os usuários para caminhos bem-sucedidos. Também há oportunidades para usar a divulgação explícita em conversa.

![Manipulando caminhos inesperados](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
As perguntas desligadas ou &quot; pessoais &quot; direcionadas para o agente são um bom momento para lembrar os usuários da natureza sintética do agente e direcioná-los para que se envolvam adequadamente ou Redirecione-os para uma pessoa real.

![Tratamento de perguntas de tarefas](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Quando divulgar

Há muitas oportunidades de divulgação durante o percurso do usuário. Design para o primeiro uso, segundo uso, enésimo uso..., mas também adotar momentos de &quot; falha &quot; para realçar a transparência – como quando o sistema cometer um erro ou quando o usuário descobre uma limitação dos recursos do agente&#39;s.

![Oportunidades de divulgação em toda a jornada do usuário](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Exemplo de um percurso de usuário do assistente digital padrão destacando várias oportunidades de divulgação.

### <a name="up-front"></a>Início

O momento ideal para a divulgação é a primeira vez que uma pessoa interage com a voz sintética.Em um cenário de assistente de voz pessoal, isso seria durante a integração ou na primeira vez que o usuário praticamente não caixa a experiência. Em outros cenários, pode ser a primeira vez que uma voz sintética lê o conteúdo em um site ou na primeira vez que um usuário interage com um caractere virtual.

- [Introdução transparente](#transparent-introduction)
- [Divulgação de capacidade](#capability-disclosure)
- [Personalização e calibragem](#customization-and-calibration)
- [Indicações implícitas](#implicit-cues--feedback)

### <a name="upon-request"></a>Mediante solicitação

Os usuários devem ser capazes de acessar facilmente informações adicionais, preferências de controle e receber comunicação transparente a qualquer momento durante o percurso do usuário quando solicitado.

- [Fornecendo oportunidades para saber mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Personalização e calibragem](#customization-and-calibration)
- [Transparência da conversa](#conversational-transparency)

### <a name="continuously"></a>Constantemente

Use os padrões de design implícitos que melhoram a experiência do usuário continuamente.

- [Divulgação de capacidade](#capability-disclosure)
- [Indicações implícitas](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Quando o sistema falha

Use a divulgação como uma oportunidade de falhar normalmente.

- [Transparência da conversa](#conversational-transparency)
- [Fornecendo oportunidades para saber mais sobre como a voz foi feita](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Entrega para humanos](#conversational-transparency)

## <a name="additional-resources"></a>Recursos adicionais
- [Diretrizes de bot da Microsoft](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Diretrizes de design da Cortana](/cortana/voice-commands/voicecommand-design-guidelines)
- [Diretrizes de design de fala do Microsoft Windows UWP](/windows/uwp/design/input/speech-interactions)
- [Diretrizes de comando de voz do Microsoft Windows Mixed Reality](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="see-also"></a>Confira também

* [Divulgação para talento de voz](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Diretrizes para a implantação responsável da tecnologia de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)
* [Como divulgar](concepts-disclosure-guidelines.md)