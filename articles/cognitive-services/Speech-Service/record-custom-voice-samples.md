---
title: Registrar amostras de voz personalizadas-serviço de fala
titleSuffix: Azure Cognitive Services
description: Construa uma voz personalizada com qualidade de produção, preparando um script robusto, contratando bons talentos de voz e gravando profissionalmente.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 47e1f8bf178723a5d4be323f24724214a67499e2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559192"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Gravar amostras de voz para criar uma voz personalizada

Criar uma voz personalizada de produção de alta qualidade a partir do zero não é uma tarefa casual. O componente central de uma voz personalizada é uma grande coleção de amostras de áudio da fala humana. É vital que essas gravações de áudio sejam de alta qualidade. Escolha um ator de voz que tenha experiência em fazer esses tipos de gravações e registre-as por um engenheiro de gravação capacitado, usando equipamento profissional.

Antes de poder fazer essas gravações, é necessário ter um script: as palavras que serão faladas pelo ator de voz para criar as amostras de áudio. Para obter melhores resultados, o script deve ter boa cobertura fonética e variedade suficiente para treinar o modelo de voz personalizado.

Muitos detalhes pequenos, mas importantes, envolvem a criação de uma gravação de voz profissional. Este guia é um roteiro para um processo que irá ajudá-lo a obter resultados bons e consistentes.

> [!TIP]
> Para obter resultados da mais alta qualidade, considere envolver a Microsoft para ajudar a desenvolver a voz personalizada. A Microsoft tem uma vasta experiência na produção de vozes de alta qualidade para seus próprios produtos, incluindo Cortana e Office.

## <a name="voice-recording-roles"></a>Funções de gravação de voz

Há quatro funções básicas em um projeto de gravação de voz personalizada:

Role|Finalidade
-|-
Ator de voz        |A voz dessa pessoa formará a base da voz personalizada.
Engenheiro de gravação  |Supervisiona os aspectos técnicos da gravação e opera o equipamento de gravação.
Diretor            |Prepara o script e orienta o desempenho do ator de voz.
Editor              |Finaliza os arquivos de áudio e prepara o upload para o portal de Voz Personalizada.

Uma pessoa pode preencher mais de uma função. Este guia assume que você ocupará o cargo de diretor e contratará tanto o locutor quanto um engenheiro de gravação. Caso você mesmo queira fazer as gravações, este artigo inclui algumas informações sobre a função do engenheiro de gravação. A função de editor não é necessária até após a sessão, portanto, podem ser executadas pelo diretor ou pelo engenheiro de gravação.

## <a name="choose-your-voice-talent"></a>Escolha seu locutor

Atores com experiência em dublagem ou trabalho de personagem de voz são bons talentos de voz personalizados. Também é possível encontrar talentos adequados entre anunciantes e leitores de notícias.

Escolha o ator de voz cuja voz natural você aprecia. É possível criar vozes exclusivas de "personagem", mas é muito mais difícil para a maioria dos talentos realizá-las consistentemente, e o esforço pode causar tensão na voz.

> [!TIP]
> De modo geral, evite usar vozes reconhecíveis para criar uma voz personalizada — a não ser, evidentemente, que o objetivo seja produzir uma voz de celebridade. Vozes menos conhecidas, geralmente distraem menos a atenção dos usuários.

O fator mais importante para a escolha de talentos de voz é a consistência. As gravações devem soar como se fossem feitas no mesmo dia e na mesma sala. É possível aproximar-se desse ideal por meio de melhores práticas de gravação e engenharia.

O ator de voz é a outra metade da equação. Eles devem ser capazes de falar com taxa consistente, nível de volume, densidade e Tom. A dicção clara é uma obrigação. O talento também precisa ser capaz de controlar estritamente a variação de pitch, o efeito emocional e a mannerisms de fala.

Gravar amostras de voz personalizada pode ser mais exaustivo que outros tipos de trabalho com voz. A maioria dos talentos de voz pode gravar por duas ou três horas por dia. Se possível, limite as sessões a três ou quatro por semana, com um dia de folga entre as seções.

As gravações feitas para um modelo de voz devem ser emocionalmente neutras. Ou seja, um enunciado triste não deve ser lido de maneira triste. O humor poderá ser adicionado à fala sintetizada posteriormente por meio de controles de prosódia. Trabalhe com o ator de voz para desenvolver uma "persona" que defina o tom emocional e sonoro geral da voz personalizada. No processo, você identificará o que será "neutro" para essa persona.

Uma persona pode ter, por exemplo, uma personalidade naturalmente otimista. Portanto, a voz "dela" poderá conter uma nota de otimismo mesmo quando ela falar de modo neutro. No entanto, esse traço de personalidade deve ser sutil e consistente. Ouça as leituras de vozes existentes para ter uma ideia do que você está procurando.

> [!TIP]
> Normalmente, você vai querer ter as gravações de voz que faz. O ator de voz deve ser favorável a um contrato de prestação de serviço para o projeto.

## <a name="create-a-script"></a>Criar um script

O ponto de partida de qualquer sessão de gravação de voz personalizada é o script, que contém os enunciados a serem falados pelo ator de voz. (O termo "enunciados" engloba sentenças completas e frases curtas.)

Os enunciados no script podem vir de qualquer lugar: ficção, não ficção, transcrições de discursos, relatórios de notícias e qualquer outro material disponível em formato impresso. Para certificar-se de que a voz produz bons resultados com tipos de palavras específicos (como terminologia médica ou jargões de programação), é possível incluir frases de trabalhos acadêmicos ou documentos técnicos. Para uma breve discussão sobre possíveis problemas legais, veja a seção ["Questões legais"](#legalities). Você também pode gravar seu próprio texto.

Os enunciados não precisam ser originados da mesma fonte ou do mesmo tipo de fonte. Nem precisam ser relacionados entre si. No entanto, se você usar frases configuradas (por exemplo, "Você fez logon com êxito") no aplicativo de fala, inclua-as no script. Isso dará à voz personalizada uma possibilidade melhor de pronunciar bem essas frases. E, caso decida usar uma gravação no lugar da fala sintetizada, você já a terá na mesma voz.

Embora a consistência seja a chave na escolha do ator de voz, a variedade é a marca registrada de um bom script. O script deve incluir muitas palavras e frases diferentes com uma variedade de comprimentos de frases, estruturas e estados de humor. Cada som no idioma deve ser representado várias vezes e em vários contextos (chamado *cobertura fonética).*

Além disso, o texto deve incorporar todas as maneiras pelas quais um determinado som pode ser representado por escrito e colocar cada som em lugares diferentes nas frases. Tanto as sentenças declarativas quanto as perguntas devem ser incluídas e lidas com entonação apropriada.

É difícil gravar um script que forneça *apenas dados suficientes* para permitir que o portal de Fala Personalizada crie uma boa voz. Na prática, a maneira mais simples de construir um script que alcance uma cobertura fonética robusta é incluir um grande número de amostras. As vozes padrão que a Microsoft fornece foram criadas com base em dezenas de milhares de enunciados. Você deve estar preparado para gravar de alguns a vários milhares de enunciados, no mínimo, para criar uma voz personalizada de qualidade de produção.

Verifique o script cuidadosamente para observar se há erros. Se possível, solicite a uma outra pessoa que também verifique o script. Ao repassar o script com o ator de voz, provavelmente você localizará alguns erros.

### <a name="script-format"></a>Formato do script

Você pode gravar o script no Microsoft Word. O script é para uso durante a sessão de gravação, então, você pode configurá-lo da melhor maneira que deseja trabalhar. Crie o arquivo de texto exigido pelo portal de Voz Personalizada separadamente.

Um formato de script básico contém três colunas:

* O número do enunciado, começando em 1. A numeração torna fácil para todos no estúdio referirem-se a um enunciado específico ("vamos tentar o número 356 novamente"). É possível usar o recurso de numeração de parágrafo do Word para numerar as linhas da tabela automaticamente.
* Uma coluna em branco na qual você escreverá o número da tomada ou o código de tempo de cada enunciado para ajudar a localizá-lo na gravação finalizada.
* O texto do próprio enunciado.

![Script de exemplo](media/custom-voice/script.png)

> [!NOTE]
> A maioria dos estúdios grava em pequenos segmentos conhecidos como *tomadas*. Cada tomada normalmente contém de 10 a 24 enunciados. Apenas anotar o número da tomada é o suficiente para localizar um enunciado posteriormente. Se estiver gravando em um estúdio que prefere fazer gravações mais longas, convém anotar o código de tempo. O estúdio terá uma exibição de tempo proeminente.

Deixe espaço suficiente após cada linha para escrever notas. Certifique-se de que nenhum enunciado está dividido entre páginas. Numere as páginas e imprima o script em um lado do papel.

Imprima três cópias do script: uma para o ator de voz, uma para o engenheiro e outra para o diretor (você). Use um clipe de papel em vez de grampos: um ator de voz experiente separará as páginas para evitar fazer barulho quando as páginas forem viradas.

### <a name="legalities"></a>Legalidades

Sob a lei de direitos autorais, a leitura de um texto com direitos autorais pelo ator pode ser uma performance para a qual o autor do trabalho deve ser compensado. Essa performance não será reconhecida no produto final, a voz personalizada. Mesmo assim, a legalidade do uso de um trabalho com direitos autorais para este fim não está bem estabelecida. A Microsoft não pode fornecer assessoria jurídica sobre esse problema, portanto, consulte seu próprio advogado.

Felizmente, é possível evitar completamente esses problemas. Há muitas fontes de texto que podem ser utilizadas sem permissão ou licença.

|Fonte de texto|Descrição|
|-|-|
|[Corpus CMU Arctic](http://festvox.org/cmu_arctic/)|Cerca de 1100 frases selecionadas de obras que não possuem direitos autorais, especificamente para uso em projetos de sintetização de fala. Um excelente ponto de partida.|
|Obras que não estão mais<br>sob direitos autorais|Normalmente, obras publicadas antes de 1923. Para inglês, o [Project Gutenberg](https://www.gutenberg.org/) oferece dezenas de milhares de obras desse tipo. Talvez seja conveniente concentra-se em textos mais recentes, pois a linguagem estará mais próxima do inglês moderno.|
|Obras&nbsp;governamentais|Os trabalhos criados pelo governo Estados Unidos não são protegidos por direitos autorais no Estados Unidos, embora o governo possa reivindicar direitos autorais em outros países/regiões.|
|Domínio público|Obras para as quais os direitos autorais foram explicitamente renunciados ou foram dedicadas ao domínio público. Pode não ser possível renunciar totalmente aos direitos autorais em algumas jurisdições.|
|Obras com licença permissiva|Obras distribuídas sob licença como Creative Commons ou GFDL (Licença GNU de Documentação Livre). Wikipedia usa GFDL. Algumas licenças, no entanto, podem impor restrições à performance do conteúdo licenciado que podem afetar a criação de um modelo de voz personalizada, portanto, leia a licença atentamente.|

## <a name="recording-your-script"></a>Gravar o script

Grave o script em um estúdio de gravação profissional especializado em trabalho de voz. Esses estúdios terão uma cabine de gravação, o equipamento certo e as pessoas capacitadas para operá-lo. Vale a pena não economizar na gravação.

Discuta seu projeto com o engenheiro de gravação do estúdio e ouça seus conselhos. A gravação deve ter pouca ou nenhuma compressão de gama dinâmica (máximo de 4: 1). É essencial que o áudio tenha um volume consistente e uma alta relação sinal-ruído, além de estar livre de sons indesejados.

### <a name="do-it-yourself"></a>Faça você mesmo

Se você mesmo quer fazer a gravação, em vez de entrar em um estúdio de gravação, aqui está um breve manual. Graças à ascensão da gravação caseira e podcast, é mais fácil do que nunca encontrar bons conselhos e recursos de gravação online.

A "cabine de gravação" deve ser uma pequena sala sem nenhum eco perceptível ou "som do ambiente." A sala deve ser o mais silenciosa e à prova de som possível. Cortinas nas paredes podem ser usadas para reduzir o eco e neutralizar ou "isolar" o tom da sala.

Use um microfone condensador de estúdio de alta qualidade ("mic" para abreviar) destinado a gravação de voz. Microfones Sennheiser, AKG e mesmo Zoom mais recentes podem produzir bons resultados. Você pode comprar um microfone ou alugar um de uma locadora de equipamentos audiovisuais local. Procure um com uma interface USB. Esse tipo de microfone combina convenientemente o elemento do microfone, pré-amplificador e conversor analógico-digital em um único pacote, simplificando a conexão.

Você também pode usar um microfone analógico. Muitas locadoras oferecem microfones "vintage" reconhecidos pela sua singularidade vocal. Observe que a engrenagem analógica profissional usa conectores XLR balanceados, em vez do plugue de 1/4" usado em equipamentos de consumo. Se for analógico, também precisará de um pré-amplificador e uma interface de áudio do computador com esses conectores.

Instale o microfone em um suporte ou pedestal e instale um filtro pop na frente do microfone para eliminar o ruído de consoantes "explosivas" como "p" e "b." Alguns microfones vêm com uma montagem de suspensão que isola-os das vibrações no suporte, o que é útil.

O ator de voz deve ficar a uma distância consistente do microfone. Use fita adesiva no piso para marcar onde devem ficar posicionados. Se o ator de voz preferir sentar, tenha cuidado especial para monitorar a distância do microfone e evitar o ruído da cadeira.

Use um suporte para manter o script. Evite inclinar o suporte para que o som não seja refletido em direção ao microfone.

A pessoa que opera o equipamento de gravação — o engenheiro — deve estar em uma sala separada do ator de voz, com alguma forma para comunicar-se com o ator na cabine de gravação (um *circuito de talkback).*

A gravação deve conter o mínimo de ruído possível, com uma meta de relação sinal/ruído de 80 db ou melhor.

Ouça atentamente uma gravação de silêncio na "cabine", descubra onde origina-se qualquer ruído e elimine a causa. As fontes comuns de ruído são saídas de ar, reatores de luz fluorescente, tráfego nas estradas próximas e ventiladores de equipamentos (até mesmo os notebooks podem ter ventiladores). Microfones e cabos podem captar o ruído elétrico da fiação CA próxima, geralmente uma vibração ou zumbido. Uma agitação também pode ser causada por uma *loop de aterramento*, que é causado pela conexão de equipamento a mais de um circuito elétrico.

> [!TIP]
> Em alguns casos, você poderá usar um equalizador ou um plug-in de software de redução de ruído para ajudar a remover o ruído das gravações, embora seja sempre melhor interrompê-lo na origem.

Defina os níveis, de modo que a maior parte do intervalo dinâmico disponível de gravação digital seja usada sem distorção. Isso significa definir o áudio para um nível alto, mas não tão alto que fique distorcido. Um exemplo da forma de onda de uma boa gravação é mostrado na imagem a seguir:

![Forma de onda de uma boa gravação](media/custom-voice/good-recording.png)

Aqui, a maior parte da gama (altura) é usada, mas os picos mais altos do sinal não atingem a parte superior ou inferior da janela. Você também pode ver que o silêncio na gravação aproxima-se de uma linha horizontal fina, indicando um baixo nível de ruído. Essa gravação possui gama dinâmica aceitável e relação sinal-ruído.

Grave diretamente no computador usando uma interface de áudio de alta qualidade ou uma porta USB, dependendo do microfone que estiver usando. Para analógico, mantenha a cadeia de áudio simples: mic, pré-amplificador, interface de áudio, computador. Você pode licenciar tanto o [Avid Pro Tools](https://www.avid.com/en/pro-tools) quanto o [Adobe Audition](https://www.adobe.com/products/audition.html) mensalmente a um custo razoável. Se o orçamento estiver extremamente limitado, experimente o [Audacity](https://www.audacityteam.org/) gratuito.

Grave em 44,1 kHz monofônico de 16 bits (qualidade de CD) ou melhor. A última geração é de 48 kHz/24 bits se o equipamento tiver suporte para isso. Você reduzirá o tamanho do áudio para 16 kHz/16 bits antes de enviá-lo para o portal de Voz Personalizada. Ainda assim, vale a pena ter uma gravação original de alta qualidade se for necessário ter edições.

O ideal é ter pessoas diferentes nas funções de diretor, engenheiro e ator de voz. Não tente fazer tudo sozinho. Em uma situação de emergência, uma pessoa pode ser tanto o diretor quanto o engenheiro.

### <a name="before-the-session"></a>Antes da sessão

Para evitar desperdiçar tempo de estúdio, repasse o script com o ator de voz antes da sessão de gravação. Embora o talento de voz se familiarize com o texto, eles podem esclarecer a pronúncia de quaisquer palavras desconhecidas.

> [!NOTE]
> A maioria dos estúdios de gravação oferece exibição eletrônica de scripts na cabine de gravação. Nesse caso, digite diretamente no documento do script as observações a serem repassadas rapidamente. Ainda será necessário ter uma cópia em papel para fazer anotações durante a sessão. A maioria dos engenheiros também vai querer uma cópia impressa. Além disso, é necessário ter uma terceira cópia impressa como backup para o ator de voz, caso o computador esteja inoperante.

O ator de voz pode perguntar qual palavra você quer enfatizar em um enunciado (a "palavra operativa"). Informe que você deseja uma leitura natural sem nenhuma ênfase específica. A ênfase poderá ser adicionada quando a fala for sintetizada e não deve fazer parte da gravação original.

Direcione o ator de voz para pronunciar palavras distintamente. Cada palavra do script deve ser pronunciada como escrita. Os sons não devem ser omitidos nem desarticulados, como é comum em fala casual, *a menos que tenham sido escritos dessa maneira no script*.

|Texto escrito|Pronúncia casual indesejada|
|-|-|
|nunca vou desistir de você|nunca vo desistir de você|
|existem quatro luzes|existe quatro luzes|
|como está o tempo hoje|como tá o tempo hoje|
|diga oi para o meu amiguinho|diga oi pro meu amiguinho|

O locutor *não* deve adicionar pausas claras entre as palavras. A frase ainda deve fluir naturalmente, mesmo quando soar um pouco formal. Talvez seja necessário treinar essa sutil distinção para acertá-la.

### <a name="the-recording-session"></a>A sessão de gravação

Crie uma gravação de referência ou um *arquivo de correspondência,* de um enunciado típica no início da sessão. Peça ao ator que repita essa linha mais ou menos a cada página. A cada vez, compare a nova gravação com a referência. Essa prática ajuda o ator a permanecer consistente em volume, tempo, densidade e entonação. Enquanto isso, o engenheiro pode usar o arquivo correspondente como referência para níveis e consistência geral do som.

O arquivo correspondente será especialmente importante ao retomar a gravação após um intervalo ou em outro dia. Você reproduzirá algumas vezes para o ator e pedirá que ele repita a cada vez até que esteja correspondendo adequadamente.

Treine o ator para respirar fundo e pausar por um momento antes de cada enunciado. Grave alguns segundos de silêncio entre os enunciados. As palavras devem ser pronunciadas da mesma maneira sempre que aparecem, considerando o contexto. Por exemplo, "analise" como um verbo é pronunciado diferente de "análise" como um substantivo.

Grave uns bons cinco segundos de silêncio antes da primeira gravação para capturar o "som do ambiente." Essa prática ajuda o portal de Voz Personalizada a compensar qualquer ruído restante nas gravações.

> [!TIP]
> Tudo o que você realmente precisa capturar é o locutor para que possa fazer uma gravação monofônica (apenas um canal) apenas das falas. No entanto, se você gravar em estéreo, poderá usar o segundo canal para gravar a conversa na sala de controle e capturar a discussão de determinadas falas ou tomadas. Remova essa faixa da versão enviada para o portal de Voz Personalizada.

Ouça atentamente, usando fones de ouvido, a performance do ator de voz. Você está procurando uma boa dicção natural, pronúncia correta e ausência de sons indesejados. Não hesite em pedir ao ator de voz para regravar um enunciado que não atenda a esses padrões.

> [!TIP]
> Se você estiver usando um alto volume de enunciados, um único enunciado poderá não ter um efeito perceptível na voz personalizada resultante. Pode ser mais fácil simplesmente anotar quaisquer enunciados com problemas, excluí-los do conjunto de dados e verificar como a voz personalizada é exibida. Você sempre poderá retornar ao estúdio posteriormente e gravar as amostras com falha.

Observe o número da tomada ou código de tempo no script para cada enunciado. Peça para o engenheiro marcar cada um dos enunciados nos metadados da gravação ou na lista de deixas.

Faça interrupções regulares e forneça uma bebida para ajudar seus talentos de voz a manter sua voz em bom formato.

### <a name="after-the-session"></a>Após a sessão

Estúdios de gravação modernos são executados em computadores. No final da sessão, você receberá um ou mais arquivos de áudio, não uma fita. Esses arquivos provavelmente serão em formato WAV ou AIFF em qualidade de CD (44,1 kHz/16 bits) ou superior. 48 kHz/24 bits é comum e recomendável. Taxas de amostragem mais altas, como 96 kHz, geralmente não são necessárias.

O portal de Voz Personalizada exige que cada enunciado fornecido esteja em arquivo próprio. Cada arquivo de áudio fornecido pelo Estúdio contém vários enunciados. Portanto, a principal tarefa de pós-produção é dividir as gravações e prepará-las para envio. O engenheiro de gravação pode ter colocado marcadores no arquivo (ou fornecido uma folha de deixas separada) para indicar o ponto em que cada enunciado começa.

Use as anotações para localizar as tomadas corretas que você quer e então use uma ferramenta de edição de som como [Avid Pro Tools](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html) ou o [Audacity](https://www.audacityteam.org/) gratuito para copiar cada enunciado em um novo arquivo.

Deixe apenas 0,2 segundo de silêncio no início e no final de cada clipe, exceto o primeiro. Esse arquivo deve iniciar com cinco segundos completos de silêncio. Não use um editor de áudio para "zerar" partes silenciosas do arquivo. A inclusão do "tom da sala" ajudará os algoritmos da voz personalizada a compensar qualquer ruído de fundo residual.

Ouça cada arquivo atentamente. Nessa fase, é possível editar pequenos sons indesejados que não foram percebidos durante a gravação, como um leve som labial antes de uma fala, mas tenha cuidado para não remover nenhuma fala real. Se você não conseguir consertar um arquivo, remova-o do conjunto de dados e anote que você fez isso.

Converta cada arquivo em 16 bits e uma taxa de amostragem de 16 kHz antes de salvar e, se registrou a conversa no estúdio, remova o segundo canal. Salve cada arquivo no formato WAV, nomeando os arquivos com o número do enunciado no script.

Por fim, crie a *transcrição* que associa cada arquivo WAV a uma versão em texto do enunciado correspondente. [Criar fontes de voz personalizada](how-to-customize-voice-font.md) inclui detalhes do formato necessário. É possível copiar o texto diretamente do script. Em seguida, crie um arquivo zip dos arquivos WAV e a transcrição do texto.

Arquive as gravações originais em um local seguro, caso precise delas posteriormente. Guarde também o script e as anotações.

## <a name="next-steps"></a>Próximas etapas

Você está pronto para fazer upload as gravações e criar a voz personalizada.

> [!div class="nextstepaction"]
> [Criar fontes de voz personalizadas](how-to-customize-voice-font.md)
