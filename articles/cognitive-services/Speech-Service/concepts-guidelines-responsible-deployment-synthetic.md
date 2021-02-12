---
title: Diretrizes para a implantação responsável da tecnologia de voz sintética
titleSuffix: Azure Cognitive Services
description: Diretrizes de design geral da Microsoft para o uso da tecnologia de voz sintética. Elas foram desenvolvidas em estudos que a Microsoft realizou com talento de voz, consumidores, bem como indivíduos com fala autoimunes para guiar o desenvolvimento responsável da voz sintética.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 9a7a8868497433ea0de8f2f8b32f8e8fbaa497eb
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537177"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Diretrizes para a implantação responsável da tecnologia de voz sintética

Neste artigo, você aprenderá sobre as diretrizes de design gerais da Microsoft para o uso da tecnologia de voz sintética. Essas diretrizes foram desenvolvidas em estudos que a Microsoft realizou com talento de voz, consumidores e indivíduos com autoimunes de fala para orientar o desenvolvimento responsável de vozes sintéticas.

Para a implantação da tecnologia de fala sintética, as diretrizes a seguir se aplicam na maioria dos cenários.

### <a name="disclose-when-the-voice-is-synthetic"></a>Divulgar quando a voz for sintética
A divulgação de uma voz é gerada pelo computador não apenas minimiza o risco de resultados prejudiciais de deconcepção, mas também aumenta a confiança na organização que fornece a voz. Saiba mais sobre [como divulgar](concepts-disclosure-guidelines.md).

A Microsoft exige que seus clientes divulguem a natureza sintética da voz neural personalizada para seus usuários. 
* Certifique-se de fornecer uma divulgação adequada para as audiências, especialmente ao usar a voz de uma pessoa bem conhecida-as pessoas fazem seu julgamento sobre informações com base na pessoa que a entrega, seja de forma consciente ou inconsciente.  Por exemplo, a divulgação pode ser compartilhada verbalmente no início de uma difusão. Para obter mais informações, visite os [padrões de divulgação](concepts-disclosure-patterns.md).   
* Considere a divulgação adequada para os pais ou outras partes com casos de uso projetados para pequenos e filhos – se o seu caso de uso for destinado a pequenos ou filhos, você precisará garantir que os pais ou tutores jurídicos sejam capazes de entender a divulgação sobre o uso de mídia sintética e tomar a decisão certa para os pequenos ou filhos se usarem a experiência. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecione os tipos de voz apropriados para seu cenário
Considere cuidadosamente o contexto de uso e os possíveis danos associados ao uso de voz sintética. Por exemplo, vozes sintéticas de alta fidelidade podem não ser apropriadas em cenários de alto risco, como para mensagens pessoais, transações financeiras ou situações complexas que exigem adaptação humana ou empatia. 

Os usuários também podem ter expectativas diferentes para tipos de voz. Por exemplo, ao ouvir as notícias confidenciais lidas por uma voz sintética, alguns usuários preferem um tom mais empática e humano, enquanto outros preferem uma voz não polarizada. Considere testar seu aplicativo para entender melhor as preferências do usuário.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seja transparente sobre recursos e limitações
É mais provável que os usuários tenham mais expectativas ao interagir com agentes de voz sintéticos de alta fidelidade. Quando os recursos do sistema não atendem a essas expectativas, a confiança pode sofrer e pode resultar em experiências desagradáveis ou até mesmo prejudiciais.

### <a name="provide-optional-human-support"></a>Fornecer suporte humano opcional
Em cenários ambíguos e transacionais (por exemplo, um Call Center de suporte), os usuários nem sempre confiam em um agente de computador para responder adequadamente às suas solicitações. O suporte humano pode ser necessário nessas situações, independentemente da qualidade realista da voz ou do recurso do sistema.

## <a name="considerations-for-voice-talent"></a>Considerações sobre talento de voz
Ao trabalhar com talento de voz, como atores de voz, para criar vozes sintéticas, a diretriz abaixo se aplica.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Obtenha um consentimento significativo do talento de voz
Os talentos de voz devem ter controle sobre seu modelo de voz (como e onde serão usados) e ser compensado por seu uso. A Microsoft exige que os clientes de voz personalizados obtenham permissão explícita por escrito de seus talentos de voz para criar uma voz sintética e seu contrato com os talentos de voz contemplarndo a duração, o uso e as limitações de conteúdo.  Se você estiver criando uma voz sintética de uma pessoa bem conhecida, deverá fornecer uma maneira para a pessoa por trás da voz para editar ou aprovar o conteúdo.

Alguns talentos de voz não sabem dos possíveis usos mal-intencionados da tecnologia e devem ser instruídos por proprietários do sistema sobre os recursos da tecnologia. A Microsoft exige que os clientes compartilhem a divulgação da Microsoft [para talento de voz](/legal/cognitive-services/speech-service/disclosure-voice-talent) com talento de voz diretamente ou por meio do representante autorizado de talentos de voz que descreve como as vozes sintéticas são desenvolvidas e operam em conjunto com os serviços de texto para fala.

## <a name="considerations-for-those-with-speech-disorders"></a>Considerações para aqueles com autoimunes de fala
Ao trabalhar com indivíduos com o Speech autoimunes, para criar ou implantar a tecnologia de voz sintética, as diretrizes a seguir se aplicam.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornecer diretrizes para estabelecer contratos
Forneça diretrizes para estabelecer contratos com indivíduos que usam voz sintética para assistência em termos de fala. O contrato deve considerar a especificação das partes que possuem a voz, a duração de uso, os critérios de transferência de propriedade, os procedimentos para excluir a fonte de voz e como impedir o acesso não autorizado. Além disso, habilite a transferência de propriedade de fonte de voz após morte para membros da família, se a permissão tiver sido fornecida.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Considerar inconsistências em padrões de fala
Para pessoas com autoimunes de fala que registram suas próprias fontes de voz, inconsistências no seu padrão de fala (slurring ou incapacidade de pronunciar determinadas palavras) podem complicar o processo de gravação. Nesses casos, a tecnologia de voz sintética e as sessões de gravação devem acomodá-las (ou seja, fornecer quebras e número adicional de sessões de gravação).

### <a name="allow-modification-over-time"></a>Permitir modificação ao longo do tempo
Indivíduos com autoimunes de fala para fazer atualizações em sua voz sintética para refletir a expiração (por exemplo, um filho que atinja Puberty). Os indivíduos também podem ter preferências estilísticos que mudam ao longo do tempo e talvez queiram fazer alterações de Tom, acentuação ou outras características de voz.


## <a name="see-also"></a>Confira também

* [Divulgação para talento de voz](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Como divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)