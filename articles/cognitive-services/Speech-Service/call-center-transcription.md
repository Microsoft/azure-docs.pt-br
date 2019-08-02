---
title: Transcrição do Call Center – serviço de fala
titleSuffix: Azure Cognitive Services
description: Um cenário comum para a conversão de fala em texto é a transcrição de grandes volumes de dados de telefonia que podem ser recebidos de vários sistemas, como o IVR (resposta interativa de voz). O áudio pode ser estéreo ou mono e bruto com pouco ou nenhum pós-processamento feito no sinal. Usando os Serviços de Fala e o Modelo de fala unificado, uma empresa pode obter transcrições de alta qualidade, com muitos sistemas de captura de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b7c7bfffb5ddf947dc9bd25e6828e2816a7325cd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559739"
---
# <a name="speech-services-for-telephony-data"></a>Serviços de Fala para dados de telefonia

Normalmente, os dados de telefonia gerados por meio de telefones fixos, celulares e rádios são de baixa qualidade e de banda estreita no intervalo de 8 KHz, o que cria desafios durante a conversão de fala em texto. Os modelos de reconhecimento de fala mais recentes dos Serviços de Fala do Azure se destacam na transcrição desses dados de telefonia, mesmo nos casos em que os dados são difíceis de serem entendidos por humanos. Esses modelos são treinados com grandes volumes de dados de telefonia e têm a melhor precisão de reconhecimento do mercado, mesmo em ambientes com ruídos.

Um cenário comum para a conversão de fala em texto é a transcrição de grandes volumes de dados de telefonia que podem ser recebidos de vários sistemas, como o IVR (resposta interativa de voz). O áudio que desses sistemas fornecem pode ser estéreo ou mono e bruto com pouco ou nenhum pós-processamento feito no sinal. Usando os Serviços de Fala e o Modelo de fala unificado, uma empresa pode obter transcrições de alta qualidade, sejam quais forem os sistemas usados para a captura de áudio.

Os dados de telefonia podem ser usados para compreender melhor as necessidades de seus clientes, identificar novas oportunidades de marketing ou avaliar o desempenho dos agentes de call center. Depois que os dados são transcritos, uma empresa pode usar a saída para telemetria aprimorada, identificar as frases-chave ou analisar o sentimento do cliente.

As tecnologias descritas nesta página são usadas pela Microsoft internamente para vários serviços de processamento de chamadas de suporte, em tempo real e no modo de lote.

Vamos analisar alguns dos recursos de tecnologia e relacionados que são oferecidos pelos Serviços de Fala do Azure.

> [!IMPORTANT]
> O modelo Unificado dos Serviços de Fala é treinado usando dados diversificados e oferece uma solução de modelo único para vários cenários, que vão de Ditado à análise de Telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia do Azure para call centers

Além disso, o aspecto funcional dos Serviços de Fala e sua finalidade principal – quando aplicada ao call center – é melhorar a experiência do cliente. Há três domínios bem definidos a este respeito:

* Análise pós-chamada, ou seja, processamento em lotes de gravações de chamada
* Processamento de análise em tempo real do sinal de áudio para extração de vários insights durante a chamada (com o sentimento sendo um caso de uso proeminente) e
* Assistentes Virtuais (bots) conduzindo o diálogo entre o cliente e o bot em uma tentativa de resolver o problema do cliente sem a participação de nenhum agente ou sendo o aplicativo de protocolos de IA para auxiliar o agente.

Um diagrama da arquitetura típica da implementação de um cenário de lote é descrito na imagem abaixo ![Arquitetura de transcrição de call center](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Componentes da tecnologia de análise de fala

Independentemente de o domínio ser pós-chamada ou em tempo real, o Azure oferece um conjunto aperfeiçoado e emergente de tecnologias para melhorar a experiência do cliente.

### <a name="speech-to-text-stt"></a>STT (conversão de fala em texto)

A [conversão de fala em texto](speech-to-text.md) é o recurso mais procurado em qualquer solução de call center. Como muitos dos processos de análise downstream dependem do texto transcrito, a WER (taxa de erros de palavras) é de extrema importância. Um dos principais desafios na transcrição de call center é o ruído predominante no call center (por exemplo: outros agentes falando em segundo plano), a grande variedade de localidades de idioma e dialetos, bem como a baixa qualidade do sinal de telefone real. A WER está altamente correlacionada à qualidade de treinamento dos modelos acústicos e de linguagem para determinada localidade e, assim, é importante ter a capacidade de personalizar o modelo para a sua localidade. Nossos modelos Unificados mais recentes da versão 4.x são a solução para a precisão e a latência de transcrição. Treinados com dezenas de milhares de horas de dados acústicos e bilhões de informações lexicais, os modelos Unificados são os modelos mais precisos do mercado para a transcrição de dados de call center.

### <a name="sentiment"></a>Sentimento
Avaliar se o cliente teve uma boa experiência é uma das áreas mais importantes da Análise de fala quando aplicado no espaço do call center. Nossa [API de Transcrição em Lote](batch-transcription.md) oferece a análise de sentimento por enunciado. Você pode agregar o conjunto de valores obtidos como parte de uma transcrição de chamada para determinar o sentimento da chamada para os agentes e o cliente.

### <a name="silence-non-talk"></a>Silêncio (sem conversa)
Não é incomum que 35% de uma chamada de suporte consista no que chamamos de tempo sem conversa. Alguns cenários em que não há conversa são: agentes pesquisando o histórico de casos com um cliente, agentes usando ferramentas que permitem acessar a área de trabalho do cliente e executar funções, clientes esperando uma transferência e assim por diante. É extremamente importante poder avaliar quando o silêncio está ocorrendo em uma chamada, pois há diversos fatores importantes de sensibilidade do cliente que ocorrem nesses tipos de cenários e quando eles ocorrem na chamada.

### <a name="translation"></a>Conversão
Algumas empresas estão experimentando fornecer transcrições traduzidas de chamadas de suporte de idiomas estrangeiros, de modo que os gerentes de entrega possam entender a experiência mundial de seus clientes. Nossas funcionalidades de [tradução](translation.md) são incomparáveis. Podemos converter áudio em áudio ou áudio em texto em uma grande variedade de localidades.

### <a name="text-to-speech"></a>Conversão de Texto em Fala
A [conversão de texto em fala](text-to-speech.md) é outra área importante na implementação de bots que interagem com os clientes. O caminho típico é o que o cliente fala, sua voz é transcrita em texto, o texto é analisado quanto às intenções, uma resposta é sintetizada com base na intenção reconhecida e, em seguida, um ativo é exposto ao cliente ou uma resposta de voz sintetizada é gerada. Evidentemente, tudo isso deve ocorrer com rapidez – portanto, a latência é um componente importante no sucesso desses sistemas.

Nossa latência de ponta a ponta é muito baixa, considerando as diversas tecnologias envolvidas, como [Conversão de Fala em Texto](speech-to-text.md), [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/) e [Conversão de Texto em Fala](text-to-speech.md).

Nossas novas vozes também são praticamente idênticas às vozes humanas. É possível usar vozes para dar ao seu bot uma personalidade única.

### <a name="search"></a>Pesquisar
Outro ponto importante da análise é identificar as interações em que uma experiência ou um evento específico tenha ocorrido. Isso normalmente é feito com uma das duas abordagens: uma pesquisa ad-hoc, em que o usuário apenas digita uma frase e o sistema responde, ou uma consulta mais estruturada, em que um analista pode criar um conjunto de instruções lógicas que identifiquem um cenário em uma chamada e, em seguida, cada chamada possa ser indexada em relação a esses conjunto de consultas. Um bom exemplo de pesquisa é a instrução de conformidade ubíqua “esta chamada será gravada para fins de qualidade... ”, pois muitas empresas desejam garantir que seus agentes forneçam esse aviso de isenção de responsabilidade aos clientes antes que a chamada seja, de fato, gravada. A maioria dos sistemas de análise tem a capacidade de analisar a tendência dos comportamentos encontrados pelos algoritmos de consulta/pesquisa, uma vez que esse relatório de tendências é, em última análise, uma das funções mais importantes de um sistema de análise. Por meio do [diretório de Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/directory/search/), sua solução de ponta a ponta pode ser consideravelmente aprimorada com funcionalidades de indexação e pesquisa.

### <a name="key-phrase-extraction"></a>Extração de Frases-chave
Essa área é uma das aplicações de análise que mais gera desafios e que vem se beneficiando da aplicação de IA e ML. O cenário principal aqui é inferir a intenção do cliente. Por que o cliente está ligando? Qual é o problema do cliente? Por que o cliente teve uma experiência negativa? Nosso [serviço de Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornece um conjunto de análise pronto para uso para atualização rápida de sua solução de ponta a ponta, visando extrair essas palavras-chave ou frases importantes.

Agora vamos dar uma olhada no processamento em lotes e nos pipelines em tempo real do reconhecimento de fala um pouco mais detalhadamente.

## <a name="batch-transcription-of-call-center-data"></a>Transcrição em lote de dados do call center

Para a transcrição em massa de áudio, desenvolvemos a [API de Transcrição em Lote](batch-transcription.md). A API de Transcrição em Lote foi desenvolvida para transcrever grandes quantidades de dados de áudio de forma assíncrona. Com relação à transcrição de dados do call center, nossa solução se baseia nestes pilares:

* **Precisão**: com os modelos Unificados da quarta geração, oferecemos qualidade de transcrição insuperável.
* **Latência**: entendemos que, ao realizar transcrições em massa, há uma necessidade urgente das transcrições. Os trabalhos de transcrição iniciados por meio da [API de Transcrição em Lote](batch-transcription.md) serão colocados na fila imediatamente e, depois que o trabalho for executado, a transcrição será realizada mais rapidamente do que a transcrição em tempo real.
* **Segurança**: Entendemos que as chamadas podem conter dados confidenciais. Tenha certeza de que a segurança é uma das nossas maiores prioridades. Nosso serviço obteve as certificações ISO, SOC, HIPAA e PCI.

Os call centers geram grandes volumes de dados de áudio diariamente. Caso sua empresa armazene dados de telefonia em um local central, como o Armazenamento do Azure, você poderá usar a [API de Transcrição em Lote](batch-transcription.md) para solicitar e receber transcrições de forma assíncrona.

Uma solução típica usa estes serviços:

* Os Serviços de Fala do Azure são usados para a transcrição de fala em texto. Uma assinatura Standard (SO) dos Serviços de Fala é necessária para uso da API de Transcrição em Lote. Assinaturas gratuitas (F0) não funcionarão.
* O [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é usado para armazenar dados de telefonia e as transcrições retornadas pela API de Transcrição em Lote. Essa conta de armazenamento deve usar notificações, especificamente para quando novos arquivos são adicionados. Essas notificações são usadas para disparar o processo de transcrição.
* O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) é usado para criar o URI de SAS (Assinatura de Acesso Compartilhado) para cada gravação e disparar a solicitação HTTP POST a fim de iniciar uma transcrição. Além disso, o Azure Functions é usado para criar solicitações a fim de recuperar e excluir transcrições usando a API de Transcrição em Lote.
* Os [WebHooks](webhooks.md) são usados para receber notificações quando as transcrições são concluídas.

Internamente, estamos usando as tecnologias acima para dar suporte às chamadas de cliente da Microsoft no modo de lote.
![Arquitetura de lote](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Transcrição em tempo real para dados do call center

Para algumas empresas, a transcrição das conversas em tempo real é obrigatória. A transcrição em tempo real pode ser usada para identificar palavras-chave e disparar pesquisas de conteúdo e de recursos relevantes para a conversa, monitorar o sentimento, melhorar a acessibilidade ou fornecer traduções para os clientes e agentes que não são falantes nativos.

Para cenários que exigem a transcrição em tempo real, recomendamos o uso do [SDK de Fala](speech-sdk.md). Atualmente, a conversão de fala em texto está disponível em [mais de 20 idiomas](language-support.md), e o SDK está disponível em C++, C#, Java, Python, Node.js, Objective-C e JavaScript. Amostras estão disponíveis em cada idioma no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Para obter as notícias e as atualizações mais recentes, confira [Notas sobre a versão](releasenotes.md).

Internamente, estamos usando as tecnologias acima para analisar em tempo real as chamadas de cliente da Microsoft.

![Arquitetura de lote](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Explicação sobre IVRs

Os Serviços de Fala podem ser integrados com facilidade a qualquer solução usando o [SDK de Fala](speech-sdk.md) ou a [API REST](rest-apis.md). No entanto, a transcrição de call center pode exigir outras tecnologias. Normalmente, é necessária uma conexão entre um sistema IVR e o Azure. Embora não ofereçamos esses componentes, queremos descrever o que envolve uma conexão com um IVR.

Vários produtos de serviço de telefonia ou de IVR (como o Genesys ou o AudioCodes) oferecem funcionalidades de integração que podem ser aproveitadas para habilitar a passagem de áudio de entrada e saída em um serviço do Azure. Basicamente, um serviço personalizado do Azure pode fornecer uma interface específica para definir sessões de chamada telefônica (como Início da Chamada ou Encerramento da Chamada) e expor uma API de WebSocket para receber o áudio do fluxo de entrada que é usado com os Serviços de Fala. As respostas de saída, como transcrição de conversas ou conexões com o Bot Framework, podem ser sintetizadas com o serviço de conversão de texto em fala da Microsoft e retornadas ao IVR para reprodução.

Outro cenário é a integração de SIP Direto. Um serviço do Azure se conecta a um Servidor SIP, obtendo, portanto, um fluxo de entrada e um fluxo de saída, que é usado para as fases de conversão de fala em texto e de texto em fala. Para se conectar a um Servidor SIP, há ofertas de software comercial, como o SDK do Ozeki ou [a API de chamadas e reuniões do Teams](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (atualmente em beta), que foram projetadas para dar suporte a esse tipo de cenário para chamadas de áudio.

## <a name="customize-existing-experiences"></a>Personalizar as experiências existentes

O Serviço de Fala do Azure funciona bem com modelos internos, no entanto, talvez você queira personalizar e ajustar ainda mais a experiência para seu ambiente ou produto. As opções de personalização vão do ajuste do modelo acústico a fontes de voz exclusivas para sua marca. Depois de criar um modelo personalizado, você poderá usá-lo com um dos Serviços de Fala do Azure em tempo real ou no modo de lote.

| Serviço de Fala | Modelo | Descrição |
|----------------|-------|-------------|
| Conversão de fala em texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Crie um modelo acústico personalizado para aplicativos, ferramentas ou dispositivos usados em ambientes particulares, como um carro ou um chão de fábrica, cada um deles com condições específicas de gravação. Exemplos incluem fala com sotaque, ruídos de fundo específicos ou uso de um microfone específico para gravação. |
| | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e da gramática específicos de um setor, por exemplo, terminologia médica ou jargão de TI. |
| | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Usando um modelo de pronúncia personalizado, você pode definir a forma fonética e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa para começar é de um arquivo de pronúncia (um arquivo .txt simples). |
| Conversão de texto em fala | [Fonte de voz](how-to-customize-voice-font.md) | Fontes de voz personalizadas permitem que você crie uma voz reconhecível exclusiva para sua marca. É necessária apenas uma pequena quantidade de dados para começar a usar. Quanto mais dados que você fornecer, mais natural e humana sua fonte de voz soará. |

## <a name="sample-code"></a>Código de exemplo

Há um código de exemplo disponível no GitHub para cada um dos Serviços de Fala do Azure. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados. Use estes links para exibir exemplos de SDK e REST:

* [Exemplos de conversão de fala em texto e tradução de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
