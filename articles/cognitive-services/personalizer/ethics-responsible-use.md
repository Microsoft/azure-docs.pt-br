---
title: Ética e uso responsável – Personalizador
titleSuffix: Azure Cognitive Services
description: O objetivo dessas diretrizes é ajudar você a implementar a personalização de uma maneira que ajude você a criar uma relação de confiança em sua empresa e no seu serviço. Pare para pesquisar novamente, aprender e discutir o impacto da personalização nas vidas das pessoas. Em caso de dúvida, procure orientação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: f565d95f8270612a8d83dd44a1e1bb895d1a4373
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662780"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Diretrizes para a implementação responsável do Personalizador

Para que as pessoas e a sociedade percebam todo o potenciam da IA, as implementações precisam ser criadas de maneira que ganhem a confiança daqueles que adicionam IA aos seus aplicativos e dos usuários desses aplicativos criados com IA. O objetivo dessas diretrizes é ajudar você a implementar o Personalizador de uma maneira a criar uma relação de confiança em sua empresa e no seu serviço. Pare para pesquisar novamente, aprender e discutir o impacto da personalização nas vidas das pessoas. Em caso de dúvida, procure orientação.

Essas diretrizes não foram criadas como orientação jurídica, e você deve verificar separadamente se o seu aplicativo cumpre os desenvolvimentos rápidos na lei nessa área e em seu setor.

Além disso, ao criar seu aplicativo usando o Personalizador, você deverá considerar uma ampla variedade de responsabilidades que se tem ao desenvolver qualquer sistema de IA centrado em dados, incluindo ética, privacidade, segurança, inclusão, transparência e responsabilidade. Leia mais sobre isso na seção [Leitura recomendada](#recommended-reading).

É possível usar o conteúdo a seguir como uma lista de verificação de iniciante, além de personalizá-lo e refiná-lo de acordo com o seu cenário. Este documento tem duas seções principais: a primeira dedica-se a destacar as considerações de uso responsável ao escolher cenários, recursos e recompensas para o Personalizador. A segunda usa um conjunto de valores nos quais a Microsoft acredita e que devem ser considerados ao criar sistemas de IA, além de fornecer sugestões acionáveis e riscos sobre como o uso do Personalizador os influencia. 


## <a name="your-responsibility"></a>Sua responsabilidade

Todas as diretrizes para a implementação responsável são desenvolvidas considerando que os desenvolvedores e as empresas que usam o Personalizador são responsáveis pelos efeitos do uso desses algoritmos na sociedade. Se você estiver desenvolvendo um aplicativo que sua organização implantará, deverá reconhecer sua função e responsabilidade por sua operação e como ele afeta as pessoas. Se você estiver criando um aplicativo para ser implantado por um terceiro, cheguem a um acordo sobre quem é o responsável pelo comportamento do aplicativo e documente esse acordo.

A confiança é construída com base na noção de compromissos atendidos: considere seus usuários, a sociedade e a estrutura legal nas quais seus aplicativos funcionam para identificar compromissos explícitos e implícitos que eles podem ter.

A Microsoft está continuamente despendendo esforços em suas ferramentas e documentos para ajudar você a tomar medidas em relação a essas responsabilidades. [Forneça comentários à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se você acredita que as ferramentas, recursos do produto e documentos adicionais ajudariam você a implementar essas diretrizes para usar o Personalizador.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Fatores para implementar o Personalizador com responsabilidade

A implementação do Personalizador pode ser de grande valor para seus usuários e para a sua empresa. Para implementar o Personalizador com responsabilidade, comece considerando as seguintes diretrizes ao:

* Escolher casos de uso para aplicar a Personalização.
* Criar [funções de recompensa](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Escolher quais [recursos](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) sobre o contexto e possíveis ações que você usará para a personalização.


## <a name="choosing-use-cases-for-personalizer"></a>Escolher casos de uso para o Personalizador

Usar um serviço que aprende a personalizar conteúdo e interfaces do usuário é útil. Isso também poderá ser aplicado de maneira indevida se a personalização criar efeitos colaterais negativos no mundo real, incluindo se os usuários não estiverem cientes da personalização do conteúdo. 

Exemplos de usos do Personalizador com maior potencial para efeitos colaterais negativos ou uma falta de transparência incluem cenários em que a “recompensa” depende de muitos fatores complexos de longo prazo que, quando muito simplificados em uma recompensa imediata, podem ter resultados desfavoráveis para indivíduos. Eles tendem a ser considerados opções “consequentes” ou opções que envolvem um risco de danos. Por exemplo: 


* **Finanças**: personalizar ofertas sobre produtos de empréstimo, de seguro e financeiros, em que fatores de risco baseiam-se nos dados que os indivíduos não conhecem, não podem obter nem podem disputar. 
* **Educação**: personalizar classificações para cursos escolares e instituições educacionais em que as recomendações podem propagar propensões e reduzir o conhecimento dos usuários de outras opções.
* **Democracia e participação cívica**: personalizar conteúdo para usuários com a meta de influenciar opiniões é consequencial e manipulador.
* **Avaliação de recompensa de terceiros**: personalizar itens em que a recompensa é baseada em uma avaliação de terceiros posterior dos usuários, em vez de ter uma recompensa gerada pelo próprio comportamento do usuário.
* **Intolerância com a exploração**: qualquer situação em que o comportamento de exploração do Personalizador pode causar danos.

Ao escolher casos de uso para o Personalizador:

* Inicie o processo de criação considerando como a personalização ajuda os seus usuários.
* Considere as consequências negativas no mundo real se alguns itens não forem classificados para os usuários devido a padrões ou à exploração da personalização.
* Considere ciclos de profecia autocumprida. Isso poderá acontecer se uma recompensa de personalização treinar um modelo para que ele possa excluir subsequentemente ainda mais um grupo demográfico do acesso ao conteúdo relevante. Por exemplo, a maioria das pessoas em um bairro de baixa renda não obtém uma oferta de seguro premium, e praticamente ninguém no bairro tende a ver a oferta.
* Salve cópias de modelos e políticas de aprendizado caso seja necessário reproduzir o Personalizador no futuro. É possível fazer isso periodicamente ou a cada período de atualização do modelo.
* Considere o nível de exploração adequado para o espaço e como usá-lo como uma ferramenta para mitigar os efeitos de “câmara de ressonância”.


## <a name="selecting-features-for-personalizer"></a>Como selecionar recursos para o Personalizador

A personalização de conteúdo depende se você tem informações úteis sobre o conteúdo e o usuário. Tenha em mente que, para alguns aplicativos e setores, algumas informações do usuário podem ser consideradas direta ou indiretamente discriminatórias e potencialmente ilícitas.

Considere o efeito destas informações:

* **Dados demográficos do usuário**: informações relativas a sexo, gênero, idade, etnia, religião: essas informações podem não ser permitidas em determinados aplicativos por motivos regulatórios, e talvez não seja ético personalizar com relação a eles, porque a personalização propagaria generalizações e propensões. Um exemplo dessa propagação de propensão é a postagem de um trabalho de engenharia não ser mostrada para públicos-alvo mais velhos ou com base no gênero.
* **Informações de localidade**: em muitos lugares do mundo, informações sobre o local (como CEP, código postal ou nome do bairro) podem estar altamente correlacionadas a renda, etnia e religião.
* **Percepção do usuário sobre imparcialidade**: mesmo nos casos em que seu aplicativo estiver tomando decisões sensatas, considere o efeito de os usuários perceberem que o conteúdo exibido em seu aplicativo muda da maneira como parece estar correlacionado às informações que seriam discriminatórias.
* **Propensão não intencional de informações**:  há tipos de propensões que podem ser introduzidas usando informações que só afetam um subconjunto da população. Isso requererá atenção extra se as informações estiverem sendo geradas de modo algorítmico, por exemplo, ao usar a análise de imagem para extrair itens em uma análise de texto ou de imagem para descobrir entidades no texto. Conscientize-se sobre as características dos serviços que você usa para criar informações.

Aplique as seguintes práticas ao escolher as informações a serem enviadas em contextos e ações para o Personalizador:

* Considere a legalidade e a ética de usar determinadas informações para alguns aplicativos e se informações que aparentam ser ingênuas podem atrair outras pessoas que você queira ou deva evitar.
* Seja transparente com os usuários informando que algoritmos e análises de dados estão sendo usados para personalizar as opções que eles veem.
* Pergunte-se: meus usuários se importariam e ficariam felizes se eu usasse essas informações para personalizar o conteúdo para eles? Eu me sentiria confortável mostrando a eles como a decisão foi tomada para destacar ou ocultar determinados itens?
* Use dados comportamentais em vez de dados de classificação ou de segmentação com base em outras características. Informações demográficas foram tradicionalmente usadas por varejistas por motivos históricos (os atributos demográficos pareciam simples de coletar e de dar seguimento antes da era digital), mas questione qual é a relevância das informações demográficas quando você tiver dados reais de interação, contextuais e históricos que se relacionam mais estritamente às preferências e à identidade dos usuários.
* Considere como impedir a falsificação de informações por usuários mal-intencionados que, se exploradas em grandes números, podem levar ao treinamento do Personalizador de maneira indevida para perturbar, constranger e importunar determinadas classes de usuários. 
* Quando adequado e viável, crie seu aplicativo para permitir que seus usuários aceitem ou recusem que determinadas informações pessoais sejam usadas. Elas poderiam ser agrupadas como “Informações sobre o local”, “Informações sobre o dispositivo”, “Histórico de compras” etc.


## <a name="computing-rewards-for-personalizer"></a>Como calcular recompensas para o Personalizador

O Personalizador procura melhorar a opção de qual ação recompensar com base na pontuação de recompensa fornecida pela lógica de negócios do seu aplicativo.

Uma pontuação de recompensa bem construída agirá como um substituto de curto prazo para uma meta de negócios vinculada à missão de uma organização.

Por exemplo, recompensar com base em cliques fará o Serviço do Personalizador buscar cliques às custas de todo o restante, mesmo se o que foi clicado estiver distraindo ou não estiver vinculado a um resultado de negócios.

Como um exemplo de contraste, um site de notícias pode querer definir recompensas vinculadas a algo mais significativo do que cliques, como “O usuário ficou tempo suficiente para ler o conteúdo?” "Ele clicou nos artigos ou referências relevantes?". Com o Personalizador, é fácil vincular métricas a recompensas de maneira estrita. Contudo, tenha cuidado para não confundir a participação do usuário de curto prazo com bons resultados.

### <a name="unintended-consequences-from-reward-scores"></a>Consequências não intencionais de pontuações de recompensa
As pontuações de recompensa podem ser criadas com a melhor das intenções, mas ainda podem criar consequências inesperadas ou resultados não intencionais sobre como o Personalizador classifica o conteúdo. 

Considere os exemplos a seguir:

* Recompensar a personalização de conteúdo de vídeo com base no percentual da duração do vídeo assistida provavelmente tenderia a classificar vídeos menores.
* Recompensar compartilhamentos de mídia social, sem análise de sentimento de como ele é compartilhado ou o conteúdo em si, pode levar à classificação de conteúdo ofensivo, não moderado ou provocativo, o que tende a incitar muita “participação”, mas agrega pouco valor.
* Recompensar a ação sobre elementos de interface do usuário que os usuários não esperam mudar pode interferir na usabilidade e na capacidade de previsão da interface do usuário, em que os botões estão surpreendentemente mudando o local ou a finalidade sem avisar, dificultando que determinados grupos de usuário permaneçam produtivos.

Implemente estas melhores práticas:

* Execute testes offline com seu sistema usando diferentes abordagens de recompensa para entender o impacto e os efeitos colaterais.
* Avalie suas funções de recompensa e pergunte-se como uma pessoa extremamente ingênua mudaria sua interpretação e atingiria resultados indesejáveis com ela.


## <a name="responsible-design-considerations"></a>Considerações sobre o design responsável

Veja abaixo as áreas de criação para implementações responsáveis de IA. Saiba mais sobre essa estrutura no [O futuro computado](https://news.microsoft.com/futurecomputed/).

![Valores de IA do Futuro Computado](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Responsabilidade
*As pessoas que criam e implantam Sistemas de IA devem ser responsáveis por como seus sistemas operam*. 

* Crie diretrizes internas sobre como implementar o Personalizador, documentar e comunicá-lo à sua equipe, executivos e fornecedores.
* Realize revisões periódicas de como as pontuações de recompensa são computadas, execute avaliações offline para ver quais recursos estão afetando o Personalizador e use os resultados para eliminar características desnecessárias.
* Comunique claramente a seus usuários como o Personalizador é usado, com que finalidade e com quais dados.
* Arquive informações e ativos (como modelos, políticas de aprendizado e outros dados) que o Personalizador usa para funcionar, para poder reproduzir os resultados.

### <a name="transparency"></a>Transparência
*Os sistemas de IA devem ser compreensíveis*. Com o Personalizador:

* *Forneça aos usuários informações sobre como o conteúdo foi personalizado.* Por exemplo, é possível mostrar aos usuários um botão rotulado `Why These Suggestions?` mostrando quais recursos principais do usuário e ações desempenharam um papel nos resultados do Personalizador.
* Verifique se os termos de uso mencionam que você usará informações sobre usuários e seu comportamento para personalizar a experiência.

### <a name="fairness"></a>Imparcialidade
*Os Sistemas de IA devem tratar todas as pessoas com imparcialidade*.

* Não use o Personalizador para casos de uso em que os resultados são de longo prazo, consequenciais ou que envolvam dano real.
* Não use informações inadequadas com as quais você pode personalizar o conteúdo ou que possam propagar propensões indesejadas. Por exemplo, pessoas com circunstâncias financeiras semelhantes devem ver as mesmas recomendações personalizadas para produtos financeiros.
* Entenda as propensões que podem existir nas informações originárias de editores, de ferramentas algorítmicas ou dos próprios usuários.

### <a name="reliability-and-safety"></a>Confiabilidade e segurança
*Os Sistemas de IA devem ter um desempenho seguro e confiável*. Para o Personalizador:

* *Não forneça ações para o Personalizador que não devem ser escolhidas*. Por exemplo, filmes inadequados devem ser filtrados das ações de personalização ao fazer uma recomendação para um usuário anônimo ou menor de idade.
* *Gerencie o modelo do Personalizador como um ativo de negócios*.  Considere com que frequência você salva e faz backup do modelo e de políticas de aprendizado por trás do loop do Personalizador e, de outra forma, trate-o como um importante ativo de negócios. A reprodução de resultados passados é importante para a melhoria da auto-auditoria e da medição.
* *Forneça canais para obter comentários diretos dos usuários*. Além de codificar verificações de segurança para verificar se apenas os públicos-alvo certos veem o conteúdo certo, forneça um mecanismo de comentários para os usuários relatarem conteúdo que possa ser surpreendente ou perturbador. Principalmente se o seu conteúdo vier de usuários ou de terceiros, considere usar o Microsoft Content Moderator ou ferramentas adicionais para revisar e validar o conteúdo.
* *Execute avaliações offline frequentes*. Isso ajudará a monitorar tendências e verificar se a eficácia é conhecida.
* *Estabeleça um processo para detectar e tomar medidas sobre manipulação mal-intencionada*. Há atores que aproveitarão o aprendizado de máquina e a capacidade dos sistemas de IA para aprender com seu ambiente a mudar o curso do seu resultado em direção às metas. Se o uso do Personalizador estiver em condições de influenciar escolhas importantes, verifique se você tem um meio adequado para detectar e mitigar essas classes de ataques, incluindo análise humana em circunstâncias adequadas.

### <a name="security-and-privacy"></a>Segurança e privacidade
*Os Sistemas de IA devem ser seguros e respeitar a privacidade*. Ao usar o Personalizador:

* *Informe os usuários com antecedência sobre os dados coletados e como eles são usados e obtenha o consentimento deles antecipadamente*, seguindo as normas locais e do setor.
* *Forneça controles de usuário que protegem a privacidade.* Para aplicativos que armazenam informações pessoais, considere fornecer um botão fácil de encontrar para funções como: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

Em alguns casos, eles podem ser exigidos por lei. Considere as vantagens e as desvantagens de retreinar modelos periodicamente para que eles não contenham rastreamentos de dados excluídos.

### <a name="inclusiveness"></a>Inclusão
*Atenda a uma ampla variedade de necessidades e de experiências humanas*.
* *Forneça experiências personalizadas para interfaces habilitadas para acessibilidade.* A eficiência proveniente da boa personalização (aplicada para reduzir a quantidade de esforço, movimentação e repetição desnecessária em interações) pode ser especialmente benéfica para pessoas com deficiências.
* *Ajuste o comportamento do aplicativo ao contexto*. É possível usar o Personalizador para acabar com a ambiguidade entre intenções em um bot de chat, por exemplo, pois a interpretação correta pode ser contextual e o que serve para uma pode não servir para outra. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Preparação proativa para oferecer maior proteção e governança de dados

É difícil prever alterações específicas em contextos regulatórios, mas, no geral, conviria ir além da estrutura legal mínima para garantir o uso respeitoso dos dados pessoais e fornecer transparência e opção relacionada à tomada de decisão algorítmica.


* Considere planejar uma situação antecipadamente em que pode haver novas restrições aos dados coletados de indivíduos e haja uma necessidade de mostrar como ela foi usada para tomar decisões.
* Considere a preparação extra em que os usuários podem incluir populações vulneráveis marginalizadas, crianças, usuários em vulnerabilidade econômica ou usuários suscetíveis a serem influenciados com base na manipulação algorítmica.
* Considere a insatisfação generalizada com a maneira como foram executados os programas e algoritmos de coleta de dados que segmentam e influenciam o público-alvo e como evitar erros estratégicos comprovados.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Avaliações proativas durante o ciclo de vida do projeto

Considere a criação de métodos para membros da equipe, usuários e proprietários de negócios para relatar preocupações com relação ao uso responsável e à criação de um processo que prioriza sua resolução e impede a retaliação.

Qualquer pessoa que pensar nos efeitos colaterais do uso de qualquer tecnologia estará limitada à sua perspectiva e experiência de vida. Expanda a variedade de opiniões disponíveis levando vozes mais diversas para suas equipes, usuários ou conselhos, de forma que eles possam se manifestar e serem incentivados a fazer isso. Considere materiais de treinamento e de aprendizado para expandir mais ainda o conhecimento da equipe nesse domínio e para adicionar uma funcionalidade para discutir tópicos complexos e confidenciais.

Considere tratar tarefas relacionadas ao uso responsável, assim como outras tarefas de transversais no ciclo de vida do aplicativo, como tarefas relacionadas à experiência do usuário, segurança ou DevOps. Essas tarefas e seus respectivos requisitos não podem ser uma consideração a posteriori. O uso responsável deve ser discutido e verificado ao longo do ciclo de vido do aplicativo.
 
## <a name="questions-and-feedback"></a>Perguntas e comentários

A Microsoft está continuamente dispendendo esforços em ferramentas e documentos para ajudar você a atuar com base nessas responsabilidades. Nossa equipe convida você a [fornecer comentários à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) caso acredite que ferramentas, recursos do produto e documentos adicionais ajudariam você a implementar essas diretrizes para usar o Personalizador.

## <a name="recommended-reading"></a>Leitura recomendada

* Veja os seis princípios da Microsoft para o desenvolvimento responsável de IA publicados no livro de janeiro de 2018, [The Future Computed](https://news.microsoft.com/futurecomputed/) (O futuro computado)
* [Quem é dono do futuro?](https://www.goodreads.com/book/show/15802693-who-owns-the-future), de Jaron Lanier.
* [Armas de destruição de matemática](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction), de Cathy O'Neil
* [Ética e Ciência de dados](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/), de DJ Patil, Hilary Mason, Mike Loukides.
* [Código de Ética do ACM](https://www.acm.org/code-of-ethics)
* [Lei de Não Discriminação de Informações Genéticas – GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Princípios FATML para algoritmos responsáveis](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Próximas etapas

[Informações: ação e contexto](concepts-features.md).
