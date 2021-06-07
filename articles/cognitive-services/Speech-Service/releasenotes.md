---
title: Notas de versão – serviço de fala
titleSuffix: Azure Cognitive Services
description: Um log em execução de versões de recursos do Speech Service, melhorias, correções de bugs e problemas conhecidos.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 3b91f4206d7715f199d7b532853471ce179a7a9e
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105726831"
---
# <a name="speech-service-release-notes"></a>Notas de versão do Serviço de Fala

## <a name="speech-sdk-1160-2021-march-release"></a>SDK de fala 1.16.0:2021 – versão de março

**Observação**: o SDK do Speech no Windows depende do Microsoft Visual C++ compartilhado redistribuível para o Visual Studio 2015, 2017 e 2019. Baixe [aqui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Problemas conhecidos**

**C++/c #/Java**: `DialogServiceConnector` não é possível usar um `CustomCommandsConfig` para acessar um aplicativo de comandos personalizados e, em vez disso, encontrará um erro de conexão. Isso pode ser solucionado Adicionando manualmente a ID do aplicativo à solicitação com `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)` . O comportamento esperado do `CustomCommandsConfig` será restaurado na próxima versão.

**Resumo de destaques**
- Memória e espaço em disco menores tornando o SDK mais eficiente – desta vez o foco estava no Android.
- Suporte aprimorado para áudio compactado para conversão de texto em texto e para fala, criando comunicação de cliente/servidor mais eficiente.
- Os caracteres animados que falam com vozes de conversão de texto em fala agora podem mover seus Lips e rostos naturalmente, seguindo o que estão dizendo.
- Novos recursos e aprimoramentos para tornar o SDK de fala útil para mais casos de uso e em mais configurações.
- Várias correções de bugs para resolver problemas que você, nossos clientes valorizados, foram sinalizadas no GitHub! Obrigado! Continue os comentários!

#### <a name="new-features"></a>Novos recursos

- **C++/c #/Java/Python**: movido para a versão mais recente do GStreamer (1.18.3) para adicionar suporte para transcrever _qualquer_ formato de mídia no Windows, Linux e Android. Consulte a documentação [aqui](/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). Anteriormente, o SDK só oferecia suporte a um subconjunto de formatos com suporte do GStreamer. Isso proporciona a você a flexibilidade de usar o formato de áudio ideal para seu caso de uso.
- **C++/c #/Java/Objective-C/python**: suporte adicionado para decodificar áudio de TTS/sintetizado compactado com o SDK. Se você definir o formato de áudio de saída como PCM e GStreamer estiver disponível no seu sistema, o SDK solicitará automaticamente o áudio compactado do serviço para economizar largura de banda e decodificar o áudio no cliente. Isso pode reduzir a largura de banda necessária para seu caso de uso. Você pode definir `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` como `false` para desabilitar esse recurso. Detalhes de [C++](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.propertyid), [Java](/java/api/com.microsoft.cognitiveservices.speech.propertyid), [Objective-C](/objectivec/cognitive-services/speech/spxpropertyid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?preserve-view=true&view=azure-python).
- **JavaScript**: Node.js os usuários agora podem usar a [ `AudioConfig.fromWavFileInput` API](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromWavFileInput_File_), permitindo que os clientes enviem o caminho em disco para um arquivo WAV para o SDK que o SDK irá reconhecer. Isso resolve o [problema do GitHub #252](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252).
- **C++/c #/Java/Objective-C/python**: o `GetVoicesAsync()` método adicionado para TTS retorna todas as vozes de síntese disponíveis programaticamente. Isso permite que você liste as vozes disponíveis em seu aplicativo ou escolha programaticamente de diferentes vozes. Detalhes de [C++](/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer#methods), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer#methods), [Objective-C](/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoices)e [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python#methods).
- **C++/c #/Java/JavaScript/Objective-C/python**: adicionado `VisemeReceived` evento para a síntese de TTS/fala para retornar a animação viseme síncrona. O visemes permite que você crie assistentes de difusão de notícias mais naturais, jogos e caracteres de desenho mais interativos e vídeos mais intuitivos de ensino de linguagem. As pessoas com deficiência auditiva também podem pegar sons visualmente e "Lip-Read" qualquer conteúdo de fala. Consulte a documentação [aqui](/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme).
- **C++/c #/Java/JavaScript/Objective-C/python**: adicionado `BookmarkReached` evento para TTS. Você pode definir indicadores no SSML de entrada e obter os deslocamentos de áudio para cada indicador. Você pode usar isso em seu aplicativo para executar uma ação quando determinadas palavras são faladas por conversão de texto em fala. Consulte a documentação [aqui](/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element).
- **Java**: suporte adicionado para APIs de reconhecimento do palestrante, permitindo que você use o reconhecimento do viva-voz do Java. Detalhes [aqui](/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer).
- **C++/c #/Java/JavaScript/Objective-C/python**: foram adicionados dois novos formatos de áudio de saída com o contêiner WebM para TTS (Webm16Khz16BitMonoOpus e Webm24Khz16BitMonoOpus). Esses são formatos melhores para streaming de áudio com o codec Opus. Detalhes de [C++](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat), [Objective-C](/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python).
- **C++/c #/Java/Python**: suporte adicionado no Linux para permitir que as conexões tenham sucesso em ambientes nos quais o acesso à rede a listas de revogação de certificado foi bloqueado. Isso habilita cenários em que você opta por permitir que o computador cliente se conecte somente ao serviço de fala do Azure. Consulte a documentação [aqui](/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
- **C++/c #/Java**: suporte adicionado para recuperar o perfil de voz para o cenário de reconhecimento do palestrante para que um aplicativo possa comparar dados do alto-falante com um perfil de voz existente. Detalhes de [C++](/cpp/cognitive-services/speech/speakerrecognizer), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer)e [Java](/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer). Isso resolve o [problema do GitHub #808](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/808).
- **Objective-C/Swift**: suporte adicionado para estrutura de módulo com cabeçalho de abrangência. Isso permite importar o SDK de fala como um módulo em aplicativos de objetivo do iOS/Mac-C/Swift. Isso resolve o [problema do GitHub #452](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452).
- **Python**: suporte adicionado para [Python 3,9](/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) e descartado suporte para Python 3,5 por [fim da vida útil do Python para 3,5](https://devguide.python.org/devcycle/#end-of-life-branches).

#### <a name="improvements"></a>Aprimoramentos

- **Java**: como parte do esforço de várias versões para reduzir o uso de memória e a superfície de disco do SDK de fala, os binários do Android agora são de 3% a 5% menores.
- **C#**: melhor precisão, legibilidade e consulte as seções de nossa documentação de referência do C# [aqui](/dotnet/api/microsoft.cognitiveservices.speech) para melhorar a usabilidade do SDK em C#.
- **C++/c #/Java/Objective-C/python**: moveu o controle de microfone e de alto-falante para uma biblioteca compartilhada separada. Isso permite o uso do SDK em casos de uso que não exigem hardware de áudio, por exemplo, se você não precisar de um microfone ou palestrante para seu caso de uso no Linux, não será necessário instalar o libasound.

#### <a name="bug-fixes"></a>Correções de bug

- **JavaScript**: os cabeçalhos de arquivo WAV grandes agora são analisados corretamente (aumenta a fatia do cabeçalho para 512 bytes). Isso resolve o [problema do GitHub #962](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962).
- **JavaScript**: problema de tempo de microfone corrigido se o fluxo do MIC terminar antes de parar o reconhecimento, resolvendo um problema com o reconhecimento de fala não funcionando no Firefox.
- **JavaScript**: agora tratamos corretamente a promessa de inicialização quando o navegador força o MIC antes de ser concluído.
- **JavaScript**: substituimos a dependência de URL com a análise de URL. Isso resolve o [problema do GitHub #264](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264).
- **Android**: retornos de chamada fixos não funcionam quando `minifyEnabled` é definido como true.
- **C++/c #/Java/Objective-C/python**: `TCP_NODELAY` será definido corretamente para a e/s de soquete subjacente para TTS para reduzir a latência.
- **C++/c #/Java/Python/Objective-C/go**: corrigiu uma falha ocasional quando o reconhecedor foi destruído logo após iniciar um reconhecimento.
- **C++/c #/Java**: corrigiu uma falha ocasional na destruição do reconhecedor do palestrante.

#### <a name="samples"></a>Exemplos

- **JavaScript**: [exemplos de navegador](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) não exigem mais download de arquivo de biblioteca JavaScript separado.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>CLI de fala (também conhecida como SPX): 2021 – versão de março

**Observação**: introdução à CLI (interface de linha de comando) do serviço de fala do Azure [aqui](/azure/cognitive-services/speech-service/spx-basics). A CLI permite que você use o serviço de fala do Azure sem escrever nenhum código.

#### <a name="new-features"></a>Novos recursos

- Adicionado o `spx intent` comando para reconhecimento de intenção, substituindo `spx recognize intent` .
- O reconhecimento e a intenção agora podem usar o Azure Functions para calcular a taxa de erros do Word usando `spx recognize --wer url <URL>` .
- Recognize agora pode gerar resultados como arquivos VTT usando `spx recognize --output vtt file <FILENAME>` .
- Informações de chave confidenciais agora obscurecidas na saída de depuração/detalhada.
- Verificação de URL adicionada e mensagem de erro para o campo de conteúdo na transcrição do lote criar.

**Teste do COVID-19 resumida**:

Como o pandemia contínuo continua a exigir que nossos engenheiros trabalhem em casa, os scripts de verificação manual pandemia foram significativamente reduzidos. Testamos em menos dispositivos com menos configurações e a probabilidade de bugs específicos do ambiente adiados pode ser aumentada. Ainda podemos validar rigorosamente com um grande conjunto de automação. No caso improvável de não ter perdido algo, informe-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Mantenha-se íntegro!



## <a name="speech-sdk-1150-2021-january-release"></a>SDK de fala 1.15.0:2021 – versão de janeiro

**Observação**: o SDK do Speech no Windows depende do Microsoft Visual C++ compartilhado redistribuível para o Visual Studio 2015, 2017 e 2019. Baixe [aqui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Resumo de destaques**
- Memória e espaço em disco menores tornando o SDK mais eficiente.
- Formatos de saída de alta fidelidade disponíveis para a visualização privada de voz neural personalizada.
- O reconhecedor de intenção agora pode retornar mais do que a principal intenção, dando a você a capacidade de fazer uma avaliação separada sobre a intenção do cliente.
- Seu assistente de voz ou bot agora estão mais fáceis de configurar, e você pode deixar que ele pare de escutar imediatamente e exercer um maior controle sobre como ele responde a erros.
- Melhoria no desempenho do dispositivo por meio de tornar a compactação opcional.
- Use o SDK do Speech no Windows ARM/ARM64.
- Depuração de nível baixo aprimorada.
- O recurso de avaliação de pronúncia agora está mais amplamente disponível.
- Várias correções de bugs para resolver problemas que você, nossos clientes valorizados, foram sinalizadas no GitHub! Obrigado! Continue os comentários!

**Na**
- O SDK de fala agora é mais eficiente e leve. Iniciamos um esforço de várias versões para reduzir o uso de memória e a superfície de disco do SDK de fala. Como primeira etapa, fizemos reduções significativas de tamanho de arquivo em bibliotecas compartilhadas na maioria das plataformas. Em comparação com a versão 1,14:
  - as bibliotecas do Windows compatíveis com o UWP de 64 bits são aproximadamente 30% menores.
  - as bibliotecas do Windows de 32 bits ainda não estão vendo melhorias de tamanho.
  - As bibliotecas do Linux são de 20-25% menores.
  - Bibliotecas Android são 3-5% menores.

**Novos recursos**
- **Todos**: novos formatos de saída do 48kHz disponíveis para a visualização privada da voz neural personalizada por meio da API de síntese de fala TTS: Audio48Khz192KBitRateMonoMp3, Audio-48kHz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, Audio-48kHz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, RAW-48kHz-16 bits-mono-PCM, Riff48Khz16BitMonoPcm, riff-48kHz-16 bits-mono-PCM.
- **Tudo**: a voz personalizada também é mais fácil de usar. Adicionado suporte para configuração de voz personalizada `EndpointId` via ([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). Antes dessa alteração, os usuários de voz personalizados precisavam definir a URL do ponto de extremidade por meio do `FromEndpoint` método. Agora, os clientes podem usar o `FromSubscription` método como vozes públicas e, em seguida, fornecer a ID de implantação por meio da configuração `EndpointId` . Isso simplifica a configuração de vozes personalizadas. 
- **C++/c #/Java/Objective-C/python**: Obtenha mais do que a principal intenção de `IntentRecognizer` . Agora, ele dá suporte à configuração do resultado JSON que contém todas as intenções e não apenas à principal intenção de pontuação por meio do `LanguageUnderstandingModel FromEndpoint` método usando o `verbose=true` parâmetro Uri. Isso resolve o [problema do GitHub #880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880). Consulte a documentação atualizada [aqui](./quickstarts/intent-recognition.md#add-a-languageunderstandingmodel-and-intents).
- **C++/c #/Java**: faça com que seu assistente de voz ou bot pare de escutar de forma imcorretiva. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) agora tem um `StopListeningAsync()` método a ser acompanhado `ListenOnceAsync()` . Isso interromperá imediatamente a captura de áudio e aguardará normalmente um resultado, tornando-o perfeito para uso com o botão "parar agora" – Pressione os cenários.
- **C++/c #/Java/JavaScript**: faça com que seu assistente de voz ou bot reaja melhor aos erros de sistema subjacentes. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) agora tem um novo `TurnStatusReceived` manipulador de eventos. Esses eventos opcionais correspondem a cada [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) resolução no bot e relatarão as falhas de execução quando ocorrerem, por exemplo, como resultado de uma exceção sem tratamento, tempo limite ou queda de rede entre a fala de linha direta e o bot. `TurnStatusReceived` torna mais fácil responder às condições de falha. Por exemplo, se um bot levar muito tempo em uma consulta de banco de dados de back-end (por exemplo, pesquisar um produto), `TurnStatusReceived` permite que o cliente saiba como solicitar novamente "Desculpe, eu não consegui isso, poderia tentar novamente" ou algo semelhante.
- **C++/c #**: Use o SDK de fala em mais plataformas. O [pacote NuGet do SDK de fala](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) agora dá suporte a binários nativos do Windows ARM/ARM64 desktop (UWP já tem suporte) para tornar o SDK de fala mais útil em mais tipos de máquina.
- **Java**: [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) agora tem um `setSpeechActivityTemplate()` método que não foi excluído intencionalmente do idioma anteriormente. Isso é equivalente a definir a `Conversation_Speech_Activity_Template` propriedade e solicitará que todas as atividades futuras da estrutura de bot originadas pelo serviço de fala de linha direta mesclem o conteúdo fornecido em suas cargas JSON.
- **Java**: depuração de nível baixo aprimorada. A [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) classe agora tem um `MessageReceived` evento, semelhante a outras linguagens de programação (C++, C#). Esse evento fornece acesso de baixo nível aos dados de entrada do serviço e pode ser útil para diagnóstico e depuração.
- **JavaScript**: configuração mais fácil para assistentes de voz e bots por meio do [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) , que agora tem `fromHost()` e `fromEndpoint()` métodos de fábrica que simplificam o uso de locais de serviço personalizados versus definir propriedades manualmente. Também padronizamos a especificação opcional de `botId` para usar um bot não padrão entre as fábricas de configuração.
- **JavaScript**: melhoria no desempenho do dispositivo por meio da propriedade de controle de cadeia de caracteres adicionada para compactação do WebSocket. Por motivos de desempenho, desabilitamos a compactação WebSocket por padrão. Isso pode ser reabilitado para cenários de baixa largura de banda. Mais detalhes [aqui](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid). Isso resolve o [problema do GitHub #242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242).
- **JavaScript**: suporte adicionado para avaliação de pronúncia para habilitar a avaliação de pronúncia de fala. Consulte o guia de início rápido [aqui](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript).

**Correções de bugs**
- **Todos** (exceto JavaScript): Corrigido uma regressão na versão 1,14, em que muita memória foi alocada pelo reconhecedor.
- **C++**: Corrigido um problema de coleta de lixo com o `DialogServiceConnector` , abordando o [problema do GitHub #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C#**: Corrigido um problema com o desligamento de thread que fazia com que os objetos fosse bloqueados por aproximadamente um segundo quando descartados.
- **C++/c #/Java**: correção de uma exceção que impede um aplicativo de configurar o token de autorização de fala ou o modelo de atividade mais de uma vez em um `DialogServiceConnector` .
- **C++/c #/Java**: correção de uma falha de reconhecedor devido a uma condição de corrida na desmontagem.
- **JavaScript**: [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) não honra anteriormente o parâmetro opcional `botId` especificado em `BotFrameworkConfig` factories. Isso tornou necessário definir o parâmetro de `botId` cadeia de caracteres de consulta manualmente para usar um bot não padrão. O bug foi corrigido e os `botId` valores fornecidos para `BotFrameworkConfig` as fábricas serão respeitados e usados, incluindo o novo `fromHost()` e o `fromEndpoint()` Additions. Isso também se aplica ao `applicationId` parâmetro para `CustomCommandsConfig` .
- **JavaScript**: problema corrigido do [GitHub #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), permitindo reutilização do objeto Recognizer.
- **JavaScript**: Corrigido um problema em que o SKD estava enviando `speech.config` várias vezes em uma sessão TTS, desperdiçando largura de banda.
- **JavaScript**: tratamento de erro simplificado na autorização do microfone, permitindo que mais mensagens descritivas sejam compagadas quando o usuário não tiver permitido a entrada do microfone no navegador.
- **JavaScript**: problema corrigido do [GitHub #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) em que erros de tipo `ConversationTranslator` e `ConversationTranscriber` causou um erro de compilação para usuários do TypeScript.
- **Objective-C**: Corrigido um problema em que a compilação do GStreamer falhou para Ios no Xcode 11,4, abordando o [problema do GitHub #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python**: problema corrigido do [GitHub #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870), removendo "DeprecationWarning: o módulo IMP foi preterido em favorecer de importlib".

**Amostras**
- [Amostra de arquivo de navegador de JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) agora usa arquivos para reconhecimento de fala. Isso resolve o [problema do GitHub #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884).

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>CLI de fala (também conhecida como SPX): 2021 – versão de janeiro

**Novos recursos**
- A CLI de fala agora está disponível como um [pacote NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) e pode ser instalada por meio da CLI do .net como uma ferramenta global .NET que você pode chamar a partir da linha de comando/shell.
- O [repositório de modelos do fala personalizada DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) foi atualizado para usar a CLI de fala para seus fluxos de trabalho de fala personalizada.

**Teste de COVID-19 resumida**: como o pandemia contínuo continua a exigir que nossos engenheiros trabalhem em casa, os scripts de verificação manual pandemia foram significativamente reduzidos. Testamos em menos dispositivos com menos configurações e a probabilidade de bugs específicos do ambiente adiados pode ser aumentada. Ainda podemos validar rigorosamente com um grande conjunto de automação. No caso improvável de não ter perdido algo, informe-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Mantenha-se íntegro!

## <a name="text-to-speech-2020-december-release"></a>Conversão de texto em fala 2020 – versão de dezembro

**Novas vozes neurais no GA e na versão prévia**

Lançada 51 novas vozes para um total de 129 vozes neurais em 54 idiomas/localidades:

- **46 novas vozes em localidades de GA**: Shakir em `ar-EG` árabe (Egito), Hamed em `ar-SA` árabe (Arábia Saudita), Borislav `bg-BG` em búlgaro (Bulgária), Joana em `ca-ES` Catalão (Espanha), Antonin na `cs-CZ` tcheco (República Tcheca), Jeppe em `da-DK` dinamarquês (Dinamarca), Jonas em `de-AT` alemão (Áustria), Jan em `de-CH` alemão (Suíça), Nestoras em `el-GR` grego (Grécia), Liam em `en-CA` Inglês (Canadá), Connor em inglês `en-IE` (Irlanda), Madhur em `en-IN` híndi (Índia), Mohan em `en-IN` télugo (Índia), Prabhat em `en-IN` Inglês (Índia), Valluvar na `en-IN` tâmil (Índia), Enric in `es-ES` Catalão (Espanha), Kert em `et-EE` estoniano (Estônia), Harri em `fi-FI` finlandês (Finlândia), Selma em `fi-FI` finlandês (Finlândia), Fabrice em `fr-CH` francês (Suíça), Colm na `ga-IE` irlandês (Irlanda), avri em `he-IL` Hebraico (Israel), Srecko em `hr-HR` Croata (Croácia), Tamas em `hu-HU` húngaro (Hungria), Gadis em `id-ID` indonésio (Indonésia), Leonas em `lt-LT` Lituano (Lituânia), Nils em `lv-LV` letão (Letônia), Osman em `ms-MY` Malaio (Malásia), Joseph em `mt-MT` Maltês (Malta) , Finn em `nb-NO` norueguês, Bokmål (Noruega), Pernille em `nb-NO` norueguês, Bokmål (Noruega), Fenna em `nl-NL` holandês (Holanda), Maarten em `nl-NL` holandês (Holanda), Agnieszka em `pl-PL` polonês (Polônia), Marek em `pl-PL` polonês (Polônia), Duarte em `pt-BR` Português (Brasil), Raquel em `pt-PT` Português (Potugal), Emil em `ro-RO` romeno (Romênia), Dmitry em `ru-RU` russo (Rússia), Svetlana em `ru-RU` russo (Rússia), Lukas em `sk-SK` Eslovaco (Eslováquia), Rok em `sl-SI` esloveno (Eslovênia), Mattias em `sv-SE` sueco (Suécia), Sofie em `sv-SE` sueco (Suécia), Niwat em `th-TH` tailandês (Tailândia), Ahmet em `tr-TR` Turco (Turquia), NamMinh em `vi-VN` vietnamita (Vietnã), HsiaoChen em `zh-TW` dólar taiwanês mandarim (Taiwan), YunJhe em `zh-TW` dólar taiwanês mandarim (Taiwan), HiuMaan em `zh-HK` chinês cantonês (Hong Kong), WanLung em `zh-HK` chinês cantonês (Hong Kong).

- **5 novas vozes em localidades de visualização**: Kert em `et-EE` estoniano (Estônia), Colm na `ga-IE` irlandês (Irlanda), Nils em `lv-LV` letão (Letônia), Leonas em `lt-LT` Lituano (Lituânia), Joseph em `mt-MT` Maltês (Malta).

Com esta versão, agora damos suporte a um total de 129 vozes neurais em 54 idiomas/localidades. Além disso, mais de 70 vozes padrão estão disponíveis em 49 idiomas/localidades. Visite [suporte a idiomas](language-support.md#text-to-speech) para obter a lista completa.

**Atualizações de criação de conteúdo de áudio**
- Interface do usuário de seleção de voz aprimorada com categorias de voz e descrições de voz detalhadas. 
- Habilitado o ajuste de intonation para todas as vozes neurais em diferentes idiomas.
- Automatizar a interface do usuário do localizaiton com base no idioma do navegador.
- Controles habilitados `StyleDegree` para todas as `zh-CN` vozes neurais.
Visite a [ferramenta de criação de conteúdo de áudio](https://speech.microsoft.com/audiocontentcreation) para conferir os novos recursos. 

**Atualizações para as vozes zh-CN**
- Foram atualizadas todas as `zh-CN` vozes neurais para dar suporte ao discurso em inglês.
- Habilitou todas as `zh-CN` vozes neurais para dar suporte ao ajuste intonation. A ferramenta de criação de conteúdo de áudio ou SSML pode ser usada para se ajustar para o melhor intonation.
- Todas as `zh-CN` vozes neurais de vários estilos foram atualizadas para dar suporte ao `StyleDegree` controle. A intensidade de emoção (suave ou forte) é ajustável.
- Atualizado `zh-CN-YunyeNeural` para dar suporte a vários estilos que podem executar emoções diferentes.

## <a name="text-to-speech-2020-november-release"></a>Conversão de texto em fala 2020 – versão de novembro

**Novas localidades e vozes em visualização**
- **Cinco novas vozes e linguagens** são introduzidas no portfólio de TTS do neural. Eles são: cortesia em maltês (Malta), ona em lituano (Lituânia), Anu em estoniano (Estônia), orla na irlandês (Irlanda) e Everita em letão (Letônia).
- **Cinco novas `zh-CN` vozes com vários estilos e funções dão suporte a**: Xiaohan, Xiaomo, Xiaorui, Xiaoxuan e Yunxi.

> Essas vozes estão disponíveis em visualização pública em três regiões do Azure: Lesteus, SouthEastAsia e WestEurope.

**GA de contêiner de TTS do neural**
- Com o contêiner de TTS do neural, os desenvolvedores podem executar a síntese de fala com as vozes digitais mais naturais em seu próprio ambiente para requisitos específicos de segurança e governança de dados. Verifique [como instalar os contêineres de fala](speech-container-howto.md). 

**Novos recursos**
- **Voz personalizada**: enabed usuários para copiar um modelo de voz de uma região para outra; suspensão e retomada de ponto de extremidade com suporte. Acesse o [portal](https://speech.microsoft.com/customvoice) aqui.
- Suporte à [marca de silêncio de SSML](speech-synthesis-markup.md#add-silence) . 
- Aprimoramentos gerais de qualidade de voz TTS: precisão de pronúncia de nível de palavra aprimorada em NB – não. Redução de 53% de erro de pronúncia.

> Leia mais neste [blog técnico](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604).

## <a name="text-to-speech-2020-october-release"></a>Conversão de texto em fala 2020 – versão de outubro

**Novos recursos**
- Maria dá suporte a um novo `newscast` estilo. Veja [como usar os estilos de fala em SSML](speech-synthesis-markup.md#adjust-speaking-styles).
- **Vozes neurais atualizadas para HiFiNet vocoder, com maior fidelidade de áudio e velocidade de síntese mais rápida**. Isso beneficia os clientes cujo cenário depende de áudio de alta Fi ou interações longas, incluindo Dubbing de vídeo, livros de áudio ou materiais de educação online. [Leia mais sobre a história e ouça os exemplos de voz em nosso blog da comunidade de tecnologia](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- O ** &  [estúdio de criação de conteúdo de áudio](https://speech.microsoft.com/audiocontentcreation) de voz personalizado localizado para 17 localidades [](https://speech.microsoft.com/customvoice)**. Os usuários podem facilmente alternar a interface do usuário para um idioma local para uma experiência mais amigável.   
- **Criação de conteúdo de áudio**: controle de grau de estilo adicionado para XiaoxiaoNeural; Refinado o recurso de quebra personalizada para incluir quebras incrementais de 50 ms. 

**Melhorias gerais de qualidade de voz TTS**
- Precisão de pronúncia de nível de palavra aprimorada `pl-PL` (redução da taxa de erro: 51%) e `fi-FI` (redução da taxa de erro: 58%)
- `ja-JP`Leitura de palavra única aprimorada para o cenário de dicionário. Erro de pronúncia reduzido por 80%.
- `zh-CN-XiaoxiaoNeural`: Qualidade de voz de estilo de sentimentos/CustomerService/newscast/cheerful/irritado aprimorada.
- `zh-CN`: Pronúncia de Erhua e tom claro e Prosody de espaço refinados aprimorados, o que melhora muito o inteligibilidade. 

## <a name="speech-sdk-1140-2020-october-release"></a>SDK de fala 1.14.0:2020 – versão de outubro

**Observação**: o SDK do Speech no Windows depende do Microsoft Visual C++ compartilhado redistribuível para o Visual Studio 2015, 2017 e 2019. Baixe [aqui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Novos recursos**
- **Linux**: suporte adicionado para Debian 10 e Ubuntu 20, 4 LTS.
- **Python/Objective-C**: suporte adicionado para a `KeywordRecognizer` API. A documentação estará [aqui](./custom-keyword-basics.md).
- **C++/Java/C #**: suporte adicionado para definir qualquer `HttpHeader` chave/valor via `ServicePropertyChannel::HttpHeader` .
- **JavaScript**: suporte adicionado para a `ConversationTranscriber` API. Leia a documentação [aqui](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript). 
- **C++/c #**: Adicionado novo `AudioDataStream FromWavFileInput` método (para leitura. Arquivos WAV) [aqui (C++)](/cpp/cognitive-services/speech/audiodatastream) e [aqui (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/c #/Java/Python/Objective-C/Swift**: adicionou um `stopSpeakingAsync()` método para parar a síntese de conversão de texto em fala. Leia a documentação de referência [aqui (C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), [aqui (C#)](/dotnet/api/microsoft.cognitiveservices.speech), [aqui (Java)](/java/api/com.microsoft.cognitiveservices.speech), [aqui (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)e [aqui (Objective-C/Swift)](/objectivec/cognitive-services/speech/).
- **C#, C++, Java**: adicionou uma `FromDialogServiceConnector()` função à `Connection` classe que pode ser usada para monitorar eventos de conexão e desconexão para o `DialogServiceConnector` . Leia a documentação de referência [aqui (C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [aqui (C++)](/cpp/cognitive-services/speech/connection)e [aqui (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection).
- **C++/c #/Java/Python/Objective-C/Swift**: suporte adicionado para avaliação de pronúncia, que avalia a pronúncia de fala e fornece comentários de alto-fala sobre a precisão e fluência de áudio falado. Leia a documentação [aqui](how-to-pronunciation-assessment.md).

**Alteração significativa**
- **JavaScript**: PullAudioOutputStream. Read () tem um tipo de retorno alterado de uma promessa interna para uma promessa de JavaScript nativo.

**Correções de bugs**
- **Todos**: regressão de 1,13 corrigido em `SetServiceProperty` onde os valores com determinados caracteres especiais foram ignorados.
- **C#**: amostras de console do Windows fixas no Visual Studio 2019 falha ao localizar DLLs nativas.
- **C#**: falha fixa com gerenciamento de memória se o fluxo for usado como `KeywordRecognizer` entrada.
- **ObjectiveC/Swift**: falha fixa com gerenciamento de memória se o fluxo for usado como entrada do reconhecedor.
- **Windows**: problema fixo de coexistência com o BT HFP/A2DP no UWP.
- **JavaScript**: mapeamento fixo de IDs de sessão para melhorar o registro em log e auxiliar nas correlações internas de depuração/serviço.
- **JavaScript**: foi adicionada correção para `DialogServiceConnector` desabilitar `ListenOnce` chamadas depois que a primeira chamada é feita.
- **JavaScript**: Corrigido o problema em que a saída de resultado seria apenas "simples".
- **JavaScript**: problema de reconhecimento contínuo corrigido no Safari no MacOS.
- **JavaScript**: mitigação de carga de CPU para cenário de alta taxa de transferência de solicitação.
- **JavaScript**: permitir acesso a detalhes do resultado do registro do perfil de voz.
- **JavaScript**: foi adicionada correção para reconhecimento contínuo no `IntentRecognizer` .
- **C++/c #/Java/Python/Swift/ObjectiveC**: correção de URL incorreta para australiaeast e brazilsouth em `IntentRecognizer` .
- **C++/c #**: adicionado `VoiceProfileType` como um argumento ao criar um `VoiceProfile` objeto.
- **C++/c #/Java/Python/Swift/ObjectiveC**: potencial fixo `SPX_INVALID_ARG` ao tentar ler `AudioDataStream` de uma determinada posição.
- **Ios**: falha fixa com reconhecimento de fala no Unity

**Amostras**
- **ObjectiveC**: foi adicionada uma amostra para reconhecimento de palavra-chave [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples).
- **C#/JavaScript**: foi adicionado o guia de início rápido para a transcrição de conversa [aqui (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) e [aqui (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription).
- **C++/c #/Java/Python/Swift/ObjectiveC**: foi adicionada uma amostra para avaliação de pronúncia [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin**: guia de início rápido atualizado para o modelo do Visual Studio mais recente [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin).

**Problema Conhecido**
- O certificado DigiCert global root G2 não tem suporte por padrão no HoloLens 2 e no Android 4,4 (KitKat) e precisa ser adicionado ao sistema para tornar o SDK de fala funcional. O certificado será adicionado às imagens do sistema operacional do HoloLens 2 em um futuro próximo. Os clientes do Android 4,4 precisam adicionar o certificado atualizado ao sistema.

**Teste do COVID-19 resumida:** Devido ao trabalho remoto nas últimas semanas, não poderíamos fazer tantos testes de verificação manual como normalmente. Não fizemos nenhuma alteração que achamos que poderia ter quebrado alguma coisa e nossos testes automatizados passaram. No caso improvável de não ter perdido algo, informe-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Mantenha-se íntegro!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>CLI de fala (também conhecida como SPX): 2020 – versão de outubro
O SPX é a interface de linha de comando para usar o serviço de fala do Azure sem escrever código. Baixe a versão mais recente [aqui](./spx-basics.md). <br>

**Novos recursos**
- `spx csr dataset upload --kind audio|language|acoustic` – Crie conjuntos de dados com base em locais, não apenas de URLs.
- `spx csr evaluation create|status|list|update|delete` – Compare novos modelos com a verdade/outros modelos de linha de base.
- `spx * list` – dá suporte à experiência não paginável (não requer--X--Skip X).
- `spx * --http header A=B` – suporte a cabeçalhos personalizados (adicionados ao Office para autenticação personalizada). 
- `spx help` – texto aprimorado e cor de texto com tique-back codificado (azul).

## <a name="text-to-speech-2020-september-release"></a>Conversão de texto em fala 2020 – versão de setembro

### <a name="new-features"></a>Novos recursos

* **TTS de neural** 
    * **Estendido para dar suporte a 18 novas linguagens/localidades.** Eles são búlgaro, tcheco, alemão (Áustria), alemão (Suíça), grego, inglês (Irlanda), francês (Suíça), Hebraico, Croata, húngaro, indonésio, malaio, romeno, eslovaco, esloveno, tâmil, télugo e vietnamita. 
    * **Lançou 14 novas vozes para enriquecer a variedade de idiomas existentes.** Veja [lista de voz e idioma completo](language-support.md#neural-voices).
    * **Novos estilos de fala `en-US` e `zh-CN` vozes.** Maria, a nova voz em inglês (EUA), dá suporte a estilos de chatbot, atendimento ao cliente e assistente. 10 novos estilos de fala estão disponíveis com nossa voz de zh-CN, XiaoXiao. Além disso, a voz neural XiaoXiao dá suporte ao `StyleDegree` ajuste. Veja [como usar os estilos de fala em SSML](speech-synthesis-markup.md#adjust-speaking-styles).

* **Contêineres: contêiner de TTS de neural liberado em visualização pública com 16 vozes disponíveis em 14 idiomas.** Saiba mais sobre [como implantar contêineres de fala para TTS de neural](speech-container-howto.md)  

Leia o [anúncio completo das atualizações de TTS para Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Conversão de texto em fala 2020 – versão de agosto

### <a name="new-features"></a>Novos recursos

* **TTS de neural: novo estilo de fala para `en-US` Voz do Aria**. AriaNeural pode parecer um elenco de notícias ao ler notícias. O estilo ' newscast-formal ' parece mais sério, enquanto o estilo ' newscast-casual ' é mais relaxado e informal. Veja [como usar os estilos de fala em SSML](speech-synthesis-markup.md).

* **Voz personalizada: um novo recurso é liberado para verificar automaticamente a qualidade dos dados de treinamento**. Quando você carregar seus dados, o sistema examinará vários aspectos de seus dados de áudio e transcrição e corrigirá ou filtrará automaticamente os problemas para melhorar a qualidade do modelo de voz. Isso abrange o volume de áudio, o nível de ruído, a precisão da pronúncia da fala, o alinhamento da fala com o texto normalizado, silêncio no áudio, além do formato de áudio e de script. 

* **Criação de conteúdo de áudio: um conjunto de novos recursos para habilitar o ajuste de voz mais poderoso e recursos de gerenciamento de áudio**.

    * Pronúncia: o recurso de ajuste de pronúncia é atualizado para o conjunto mais recente de fonema. Você pode escolher o elemento fonema correto da biblioteca e refinar a pronúncia das palavras selecionadas. 

    * Download: o recurso de "download"/"exportação" de áudio é aprimorado para dar suporte à geração de áudio por parágrafo. Você pode editar o conteúdo no mesmo arquivo/SSML, enquanto gera várias saídas de áudio. A estrutura de arquivos de "download" também é refinada. Agora, você pode facilmente obter todos os arquivos de áudio em uma pasta. 

    * Status da tarefa: a experiência de exportação de vários arquivos foi aprimorada. Quando você exportar vários arquivos no passado, se um dos arquivos falhar, a tarefa inteira falhará. Mas, agora, todos os outros arquivos serão exportados com êxito. O relatório de tarefas é aprimorado com informações mais detalhadas e estruturadas. Você pode verificar os logs de todos os arquivos com falha e sentenças agora com o relatório. 

    * Documentação do SSML: vinculada ao documento SSML para ajudá-lo a verificar as regras de como usar todos os recursos de ajuste.

* **A API de lista de voz é atualizada para incluir um nome de exibição amigável e os estilos de fala com suporte para vozes neurais**.

### <a name="general-tts-voice-quality-improvements"></a>Melhorias gerais de qualidade de voz TTS

* Redução do erro de pronúncia no nível de palavra% para `ru-RU` (erros reduzidos por 56%) e `sv-SE` (erros reduzidos por 49%)

* Melhoria da leitura de palavras Polyphony em `en-US` vozes neurais por 40%. Exemplos de palavras polyphonys incluem "Read", "Live", "content", "Record", "Object" etc. 

* Melhoria da naturalidade do Tom da pergunta no `fr-FR` . MOS (Pontuação média de opinião) ganho: + 0,28

* Atualização do vocoders para as seguintes vozes, com melhorias de fidelidade e velocidade geral de desempenho em 40%.

    | Locale | Voz |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Correções de bug

* Correção de vários bugs com a ferramenta de criação de conteúdo de áudio 
    * Corrigido o problema com a atualização automática. 
    * Correção de problemas com estilos de voz em zh-CN na região do Sul Ásia Oriental.
    * Correção do problema de estabilidade, incluindo um erro de exportação com a marca ' break ' e erros de pontuação.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Novas localidades de fala em texto: 2020 – versão de agosto
A conversão de fala em texto lançou 26 novas localidades em agosto: 2 idiomas europeus `cs-CZ` e `hu-HU` 5 localidades em inglês e 19 localidades do espanhol que abrangem a maioria dos países da América do Sul. Abaixo está uma lista das novas localidades. Veja a lista completa de idiomas [aqui](./language-support.md).

| Local  | Linguagem                          |
|---------|-----------------------------------|
| `cs-CZ` | Tcheco (República Tcheca)            | 
| `en-HK` | Inglês (Hong Kong)               | 
| `en-IE` | Inglês (Irlanda)                 | 
| `en-PH` | Inglês (Filipinas)             | 
| `en-SG` | Inglês (Singapura)               | 
| `en-ZA` | Inglês (África do Sul)            | 
| `es-AR` | Espanhol (Argentina)               | 
| `es-BO` | Espanhol (Bolívia)                 | 
| `es-CL` | Espanhol (Chile)                   | 
| `es-CO` | Espanhol (Colômbia)                | 
| `es-CR` | Espanhol (Costa Rica)              | 
| `es-CU` | Espanhol (Cuba)                    | 
| `es-DO` | Espanhol (República Dominicana)      | 
| `es-EC` | Espanhol (Equador)                 | 
| `es-GT` | Espanhol (Guatemala)               | 
| `es-HN` | Espanhol (Honduras)                | 
| `es-NI` | Espanhol (Nicarágua)               | 
| `es-PA` | Espanhol (Panamá)                  | 
| `es-PE` | Espanhol (Peru)                    | 
| `es-PR` | Espanhol (Porto Rico)             | 
| `es-PY` | Espanhol (Paraguai)                | 
| `es-SV` | Espanhol (El Salvador)             | 
| `es-US` | Espanhol (EUA)                     | 
| `es-UY` | Espanhol (Uruguai)                 | 
| `es-VE` | Espanhol (Venezuela)               | 
| `hu-HU` | Húngaro (Hungria)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>SDK de fala 1.13.0:2020 – versão de julho

**Observação**: o SDK do Speech no Windows depende do Microsoft Visual C++ compartilhado redistribuível para o Visual Studio 2015, 2017 e 2019. Baixe-o e instale-o [aqui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Novos recursos**
- **C#**: suporte adicionado para transcrição de conversa assíncrona. Consulte a documentação [aqui](./how-to-async-conversation-transcription.md).  
- **JavaScript**: adicionado suporte a reconhecimento do locutor para o [navegador](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) e [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript**: suporte adicionado para detecção automática de idioma/ID de idioma. Consulte a documentação [aqui](./how-to-automatic-language-detection.md?pivots=programming-language-javascript).
- **Objective-C**: suporte adicionado para [conversa com vários dispositivos](./multi-device-conversation.md) e [transcrição de conversa](./conversation-transcription.md). 
- **Python**: suporte de áudio compactado adicionado para Python no Windows e Linux. Consulte a documentação [aqui](./how-to-use-codec-compressed-audio-input-streams.md). 

**Correções de bugs**
- **Tudo**: Corrigido um problema que fazia com que o KeywordRecognizer não avançasse os fluxos após um reconhecimento.
- **Todos**: Corrigido um problema que fazia com que o fluxo fosse obtido de um KeywordRecognitionResult para não conter a palavra-chave.
- **Tudo**: Corrigido um problema em que o SendMessageAsync não envia realmente a mensagem pela conexão depois que os usuários terminam de esperar.
- **Todos**: correção de uma falha em APIs reconhecimento do locutor quando os usuários chamam o método VoiceProfileClient:: SpeakerRecEnrollProfileAsync várias vezes e não aguardaram que as chamadas sejam concluídas.
- **Todos**: habilitar o registro em log de arquivo fixo em classes VoiceProfileClient e SpeakerRecognizer.
- **JavaScript**: Corrigido um [problema](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) com a limitação quando o navegador é minimizado.
- **JavaScript**: Corrigido um [problema](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) com um vazamento de memória em fluxos.
- **JavaScript**: cache adicionado para respostas de OCSP de NodeJS.
- **Java**: Corrigido um problema que estava fazendo com que os campos BigInteger sempre retornassem 0.
- **Ios**: Corrigido um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) com a publicação de aplicativos baseados no SDK da fala na iOS App Store.

**Amostras**
- **C++**: foi adicionado um código de exemplo para reconhecimento do locutor [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**Teste do COVID-19 resumida:** Devido ao trabalho remoto nas últimas semanas, não poderíamos fazer tantos testes de verificação manual como normalmente. Não fizemos nenhuma alteração que achamos que poderia ter quebrado alguma coisa e nossos testes automatizados passaram. No caso improvável de não ter perdido algo, informe-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Mantenha-se íntegro!

## <a name="text-to-speech-2020-july-release"></a>Conversão de texto em fala 2020-versão de julho

### <a name="new-features"></a>Novos recursos

* **TTS de neural, 15 novas vozes neurais**: as novas vozes adicionadas ao portfólio de TTS do neural são Salma em `ar-EG` árabe (Egito), Zariyah em `ar-SA` árabe (Arábia Saudita), Alba in `ca-ES` Catalão (Espanha), Christel em `da-DK` dinamarquês (Dinamarca), Neerja em `es-IN` Inglês (Índia), Noora em `fi-FI` finlandês (Finlândia), swara em `hi-IN` híndi (Índia), Colette em `nl-NL` holandês (Holanda), Zofia em `pl-PL` polonês (Polônia), Fernanda em `pt-PT` Português (Portugal), Dariya em `ru-RU` russo (Rússia), Hillevi em `sv-SE` sueco (Suécia), achara em `th-TH` tailandês (Tailândia), HiuGaai em `zh-HK` chinês (Cantonês, tradicional) e HsiaoYu em `zh-TW` chinês (dólar taiwanês mandarim). Verifique todos os [idiomas com suporte](./language-support.md#neural-voices).  

* **Voz personalizada, testes de voz simplificados com o fluxo de treinamento para simplificar a experiência do usuário**: com o novo recurso de teste, cada voz será testada automaticamente com um conjunto de teste predefinido otimizado para cada idioma para cobrir cenários de assistente de voz e geral. Esses conjuntos de testes são cuidadosamente selecionados e testados para incluir casos de uso típicos e fonemas na linguagem. Além disso, os usuários ainda podem optar por carregar seus próprios scripts de teste ao treinar um modelo.

* **Criação de conteúdo de áudio: um conjunto de novos recursos é lançado para permitir um ajuste de voz mais poderoso e recursos de gerenciamento de áudio**

    * `Pitch`, `rate` e `volume` são aprimorados para dar suporte ao ajuste com um valor predefinido, como lento, médio e rápido. Agora, é simples para os usuários escolherem um valor "constante" para a edição de áudio.

    ![Ajuste de áudio](media/release-notes/audio-tuning.png)

    * Os usuários agora podem examinar o `Audio history` seu arquivo de trabalho. Com esse recurso, os usuários podem facilmente acompanhar todo o áudio gerado relacionado a um arquivo de trabalho. Eles podem verificar a versão do histórico e comparar a qualidade durante o ajuste ao mesmo tempo. 

    ![Histórico de áudio](media/release-notes/audio-history.png)

    * Agora, o `Clear` recurso é mais flexível. Os usuários podem limpar um parâmetro de ajuste específico e manter outros parâmetros disponíveis para o conteúdo selecionado.  

    * Um vídeo do tutorial foi adicionado na [página de aterrissagem](https://speech.microsoft.com/audiocontentcreation) para ajudar os usuários a começar rapidamente com o gerenciamento de áudio e o ajuste de voz TTS. 

### <a name="general-tts-voice-quality-improvements"></a>Melhorias gerais de qualidade de voz TTS

* Vocoder de TTS aprimorados em para maior fidelidade e menor latência.

    * Atualização de Elsa em `it-IT` para um novo vocoder que obteve o ganho de + 0,464 CMOS (Pontuação de opinião média comparativa) em qualidade de voz, 40% mais rápido em síntese e 30% de redução na latência do primeiro byte. 
    * Xiaoxiao atualizado `zh-CN` para o novo vocoder com o CMOS de + 0148 para o domínio geral, + 0,348 para o estilo newscast e + 0,195 para o estilo Lyrical. 

* `de-DE`Modelos de `ja-JP` voz e atualizados para tornar a saída de TTS mais natural.
    
    * Katja atualizado `de-DE` com o método de modelagem Prosody mais recente, o ganho (Pontuação média de opinião) é + 0,13. 
    * Atualizado o Nanami em `ja-JP` com um novo modelo Prosody de ênfase de Tom, o ganho (Pontuação média de opinião) é + 0,19;  

* Precisão de pronúncia de nível de palavra aprimorada em cinco idiomas.

    | Linguagem | Redução de erros de pronúncia |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Correções de bug

* Leitura de moeda
    * Corrigido o problema com a leitura de moeda para `es-ES` e `es-MX`
     
    | Linguagem | Entrada | Leitura após aperfeiçoamento |
    |---|---|---|
    | `es-MX` | $1.58 | un peso Cincuenta y Ocho centavos |
    | `es-ES` | $1.58 | Dólar Cincuenta y Ocho centavos |

    * Suporte para moeda negativa (como "-325 &euro; ") nas seguintes localidades: `en-US` ,,,,, `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Leitura de endereço aprimorada no `pt-PT` .
* Correção `en-AU` dos problemas de pronúncia Natasha () e Libby ( `en-UK` ) na palavra "for" e "quatro".  
* Correção de bugs na ferramenta de criação de conteúdo de áudio
    * A pausa adicional e inesperada depois que o segundo parágrafo é corrigido.  
    * O recurso ' no break ' é adicionado novamente de um bug de regressão. 
    * O problema de atualização aleatória do Speech Studio é fixo.  

### <a name="samplessdk"></a>Exemplos/SDK

* JavaScript: corrige o problema de reprodução no Firefox e no Safari no macOS e no iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>SDK de fala 1.12.1:2020 – versão de junho
**CLI de fala (também conhecida como SPX)**
-   Adicionados recursos de pesquisa na ajuda da CLI:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Atualizado para trabalhar com as APIs de Fala Personalizada e lote do v 3.0 implantadas recentemente:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Novos recursos**
-   **C \# , C++**: reconhecimento do locutor Preview: esse recurso habilita a identificação do orador (que está falando?) e a verificação do palestrante (é o palestrante que alega ser?). Comece com uma [visão geral](./speaker-recognition-overview.md), leia o [artigo reconhecimento do locutor noções básicas](./get-started-speaker-recognition.md)ou os [documentos de referência de API](/rest/api/speakerrecognition/).

**Correções de bugs**
-   **C \# , C++**: a gravação fixa do microfone não estava funcionando em 1,12 no reconhecimento do viva-voz.
-   **JavaScript**: correções de conversão de texto em fala no Firefox e Safari no MacOS e Ios.
-   Correção da falha na violação de acesso do verificador de aplicativos do Windows na transcrição de conversa ao usar o fluxo de oito canais.
-   Correção da falha na violação de acesso do verificador de aplicativos do Windows na tradução de conversa de vários dispositivos.

**Amostras**
-   **C#**: [exemplo de código](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) para reconhecimento de palestrante.
-   **C++**: [exemplo de código](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) para reconhecimento de palestrante.
-   **Java**: [exemplo de código](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) para reconhecimento de intenção no Android. 

**Teste do COVID-19 resumida:** Devido ao trabalho remoto nas últimas semanas, não poderíamos fazer tantos testes de verificação manual como normalmente. Não fizemos nenhuma alteração que achamos que poderia ter quebrado alguma coisa e nossos testes automatizados passaram. No caso improvável de não ter perdido algo, informe-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Mantenha-se íntegro!


## <a name="speech-sdk-1120-2020-may-release"></a>SDK de fala 1.12.0:2020 – lançamento de maio
**CLI de fala (também conhecido como SPX)**
- O **SPX** é uma nova ferramenta de linha de comando que permite executar reconhecimento, síntese, tradução, transcrição de lote e gerenciamento de fala personalizado na linha de comando. Use-o para testar o serviço de fala ou para gerar script das tarefas de serviço de fala que você precisa executar. Baixe a ferramenta e leia a documentação [aqui](./spx-overview.md).

**Novos recursos**
- **Go**: suporte ao novo idioma do Go para [reconhecimento de fala](./get-started-speech-to-text.md?pivots=programming-language-go) e [Assistente de voz personalizado](./quickstarts/voice-assistants.md?pivots=programming-language-go). Configure seu ambiente de desenvolvimento [aqui](./quickstarts/setup-platform.md?pivots=programming-language-go). Para obter o código de exemplo, consulte a seção de exemplos abaixo. 
- **JavaScript**: suporte adicionado ao navegador para conversão de texto em fala. Consulte a documentação [aqui](./get-started-text-to-speech.md?pivots=programming-language-JavaScript).
- **C++, C#, Java**: novo `KeywordRecognizer` objeto e APIs com suporte em plataformas Windows, Android, Linux & Ios. Leia a documentação [aqui](./custom-keyword-overview.md). Para obter o código de exemplo, consulte a seção de exemplos abaixo. 
- **Java**: conversa de vários dispositivos adicionada com suporte à tradução. Consulte o documento de referência [aqui](/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Melhorias & otimizações**
- **JavaScript**: implementação do microfone do navegador otimizada melhorando a precisão do reconhecimento de fala.
- **Java**: associações Refatorada usando implementação de JNI direta sem Swig. Essa alteração reduz 10 vezes o tamanho das associações para todos os pacotes Java usados para Windows, Android, Linux e Mac e facilita o desenvolvimento adicional da implementação Java do SDK de fala.
- **Linux**: [documentação](./speech-sdk.md?tabs=linux) de suporte atualizada com as últimas observações específicas do RHEL 7.
- Lógica de conexão aprimorada para tentar se conectar várias vezes quando ocorrerem erros de serviço e de rede.
- Atualização da página de início rápido do [Portal.Azure.com](https://portal.azure.com) Speech para ajudar os desenvolvedores a executarem a próxima etapa na jornada de fala do Azure.

**Correções de bugs**
- **C#, Java**: Corrigido um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) com o carregamento de bibliotecas do SDK no ARM do Linux (de 32 bits e 64 bits).
- **C#**: descarte explícito fixo de identificadores nativos para objetos TranslationRecognizer, IntentRecognizer e Connection.
- **C#**: gerenciamento de tempo de vida de entrada de áudio corrigido para o objeto ConversationTranscriber.
- Corrigido um problema em que o `IntentRecognizer` motivo do resultado não foi definido corretamente ao reconhecer tentativas de frases simples.
- Corrigido um problema em que o `SpeechRecognitionEventArgs` deslocamento de resultado não foi definido corretamente.
- Correção de uma condição de corrida em que o SDK estava tentando enviar uma mensagem de rede antes de abrir a conexão WebSocket. O was é reproduzível `TranslationRecognizer` durante a adição de participantes.
- Correção de vazamentos de memória no mecanismo de reconhecimento de palavra-chave.

**Amostras**
- **Go**: foram adicionados guias de início rápido para [reconhecimento de fala](./get-started-speech-to-text.md?pivots=programming-language-go) e [Assistente de voz personalizado](./quickstarts/voice-assistants.md?pivots=programming-language-go). Encontre o código de exemplo [aqui](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples). 
- **JavaScript**: foram adicionados guias de início rápido para conversão de [texto em fala](./get-started-text-to-speech.md?pivots=programming-language-javascript), [tradução](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)e [reconhecimento de intenção](./quickstarts/intent-recognition.md?pivots=programming-language-javascript).
- Exemplos de reconhecimento de palavra-chave para [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) e [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Teste do COVID-19 resumida:** Devido ao trabalho remoto nas últimas semanas, não poderíamos fazer tantos testes de verificação manual como normalmente. Não fizemos nenhuma alteração que achamos que poderia ter quebrado alguma coisa e nossos testes automatizados passaram. Se tivéssemos perdido algo, informe-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Mantenha-se íntegro!

## <a name="speech-sdk-1110-2020-march-release"></a>SDK de fala 1.11.0:2020 – versão de março
**Novos recursos**
- Linux: suporte adicionado para Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 com [instruções](./how-to-configure-rhel-centos-7.md) sobre como configurar o sistema para o SDK de fala.
- Linux: suporte adicionado para .NET Core C# no Linux ARM32 e ARM64. Leia mais [aqui](./speech-sdk.md?tabs=linux). 
- C#, C++: adicionado `UtteranceId` em `ConversationTranscriptionResult` , uma ID consistente em todos os intermediários e o resultado do reconhecimento de fala final. Detalhes de [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: suporte adicionado para `Language ID` . Consulte speech_sample. py no [repositório GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: adicionado suporte ao formato de entrada de áudio compactado na plataforma Windows para todos os aplicativos de console do Win32. Detalhes [aqui](./how-to-use-codec-compressed-audio-input-streams.md). 
- JavaScript: suporte à síntese de fala (conversão de texto em fala) no NodeJS. Saiba mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: adicione novas APIs para habilitar a inspeção de todas as mensagens de envio e recebidas. Saiba mais [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Correções de bugs**
- C#, C++: Corrigido um problema; `SendMessageAsync` agora, envia uma mensagem binária como um tipo binário. Detalhes de [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: Corrigido um problema em que o uso do `Connection MessageReceived` evento pode causar falha se `Recognizer` for descartado antes do `Connection` objeto. Detalhes de [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: o tamanho do buffer de áudio do microfone diminuiu de 800ms para 100 ms para melhorar a latência.
- Android: Corrigido um [problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) com o emulador Android x86 no Android Studio.
- JavaScript: suporte adicionado para regiões na China com a `fromSubscription` API. Detalhes [aqui](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-). 
- JavaScript: Adicione mais informações de erro para falhas de conexão de NodeJS.
        
**Amostras**
- Unity: o exemplo público de reconhecimento de intenção é fixo, em que a importação de JSON LUIS estava falhando. Detalhes [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: exemplo adicionado para `Language ID` . Detalhes [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Teste do Covid19 resumida:** Devido ao trabalho remoto nas últimas semanas, não pudemos fazer o teste manual de verificação do dispositivo como normalmente. Por exemplo, não foi possível testar a entrada do microfone e a saída do orador no Linux, iOS e macOS. Não fizemos nenhuma alteração que achamos que poderia ter quebrado alguma coisa nessas plataformas, e nossos testes automatizados passaram. No caso improvável de não ter perdido algo, informe-nos no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Obrigado pelo seu suporte contínuo. Como sempre, poste perguntas ou comentários no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) ou [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Mantenha-se íntegro!

## <a name="speech-sdk-1100-2020-february-release"></a>SDK de fala 1.10.0:2020 – versão de fevereiro

**Novos recursos**

 - Pacotes python adicionados para dar suporte à nova versão 3,8 do Python.
 - Red Hat Enterprise Linux (RHEL) suporte a/CentOS 8 x64 (C++, C#, Java, Python).
   > [!NOTE] 
   > Os clientes devem configurar o OpenSSL de acordo com [estas instruções](./how-to-configure-openssl-linux.md).
 - Suporte do Linux ARM32 para Debian e Ubuntu.
 - O DialogServiceConnector agora dá suporte a um parâmetro opcional "ID do bot" em BotFrameworkConfig. Esse parâmetro permite o uso de vários bots de fala de linha direta com um único recurso de fala do Azure. Sem o parâmetro especificado, o bot padrão (conforme determinado pela página de configuração de canal da Direct line Speech) será usado.
 - DialogServiceConnector agora tem uma propriedade SpeechActivityTemplate. O conteúdo dessa cadeia de caracteres JSON será usado pela Direct line Speech para preencher previamente uma grande variedade de campos com suporte em todas as atividades que atingem um bot de fala de linha direta, incluindo atividades geradas automaticamente em resposta a eventos como reconhecimento de fala.
 - A TTS agora usa a chave de assinatura para autenticação, reduzindo a latência de primeiro byte do primeiro resultado de síntese após a criação de um sintetizador.
 - Modelos de reconhecimento de fala atualizados para 19 localidades para uma redução média da taxa de erros do Word de 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, ZH-HK, NB-NO, Fi-FL, ru-RU, pl-PL, CA-ES, zh-TW, th-ÉSIMO, pt-PT, TR-TR). Os novos modelos trazem melhorias significativas em vários domínios, incluindo o ditado, Call-Center cenários de transcrição e de indexação de vídeo.

**Correções de bugs**

 - Corrigido o bug em que o transcrevedor de conversa não aguardou corretamente em APIs JAVA 
 - Correção do emulador do Android x86 para o problema do Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Adição ausente (Get | Set) métodos de propriedade para AudioConfig
 - Corrigir um bug de TTS em que o audioDataStream não pôde ser interrompido quando a conexão falha
 - Usar um ponto de extremidade sem uma região causaria falhas de USP para o tradutor de conversa
 - A geração de ID em aplicativos universais do Windows agora usa um algoritmo GUID exclusivo apropriado; Ele, anteriormente, de forma não intencional, um padrão de implementação de fragmentado que frequentemente produziu colisões em grandes conjuntos de interações.
 
 **Amostras**
 
 - Exemplo de Unity para usar o SDK de fala com o [microfone do Unity e streaming do modo Push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Outras alterações**

 - [Documentação de configuração do OpenSSL atualizada para Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>SDK de fala 1.9.0:2020 – versão de janeiro

**Novos recursos**

- Conversa com vários dispositivos: Conecte vários dispositivos à mesma fala ou conversa baseada em texto e, opcionalmente, traduza as mensagens enviadas entre elas. Saiba mais neste [artigo](multi-device-conversation.md). 
- Suporte de reconhecimento de palavra-chave adicionado ao pacote Android. aar e adicionado suporte para tipos x86 e x64. 
- Objective-C: `SendMessage` e `SetMessageProperty` métodos adicionados ao `Connection` objeto. Consulte a documentação [aqui](/objectivec/cognitive-services/speech/spxconnection).
- A API do TTS C++ agora dá suporte `std::wstring` como entrada de texto de síntese, removendo a necessidade de converter um wstring em cadeia de caracteres antes de passá-lo para o SDK. Veja os detalhes [aqui](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: a [ID do idioma](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) e a configuração do idioma de [origem](./how-to-specify-source-language.md?pivots=programming-language-csharp) agora estão disponíveis.
- JavaScript: adicionou um recurso ao `Connection` objeto para passar mensagens personalizadas do serviço de fala como um retorno de chamada `receivedServiceMessage` .
- JavaScript: suporte adicionado para `FromHost API` para facilitar o uso com contêineres locais e nuvens soberanas. Consulte a documentação [aqui](speech-container-howto.md).
- JavaScript: agora respeitamos `NODE_TLS_REJECT_UNAUTHORIZED` graças a uma contribuição do [orgads](https://github.com/orgads). Veja os detalhes [aqui](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Alterações interruptivas**

- `OpenSSL` foi atualizado para a versão 1.1.1 b e é vinculado estaticamente à biblioteca principal do SDK de fala para Linux. Isso poderá causar uma interrupção se a caixa de entrada `OpenSSL` não tiver sido instalada `/usr/lib/ssl` no diretório no sistema. Consulte [nossa documentação](how-to-configure-openssl-linux.md) em documentos do SDK de fala para contornar o problema.
- Alteramos o tipo de dados retornado para C# `WordLevelTimingResult.Offset` de `int` para `long` para permitir o acesso ao `WordLevelTimingResults` quando os dados de fala forem maiores do que 2 minutos.
- `PushAudioInputStream` e `PullAudioInputStream` agora envie informações de cabeçalho WAV para o serviço de fala com base em `AudioStreamFormat` , opcionalmente especificado quando elas foram criadas. Os clientes agora devem usar o [formato de entrada de áudio com suporte](how-to-use-audio-input-streams.md). Qualquer outro formato terá resultados de reconhecimento de qualidade inferior ou poderá causar outros problemas. 

**Correções de bugs**

- Consulte a `OpenSSL` atualização em alterações significativas acima. Corrigimos uma falha intermitente e um problema de desempenho (contenção de bloqueio sob alta carga) no Linux e no Java. 
- Java: fez melhorias no fechamento de objetos em cenários de alta simultaneidade.
- Reestruturado nosso pacote NuGet. Removemos as três cópias de `Microsoft.CognitiveServices.Speech.core.dll` e `Microsoft.CognitiveServices.Speech.extension.kws.dll` em pastas lib, tornando o pacote NuGet menor e mais rápido para baixar e adicionamos os cabeçalhos necessários para compilar alguns aplicativos nativos do C++.
- Corrigimos amostras de início rápido [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Eles estavam saindo sem a exibição da exceção "microfone não encontrado" no Linux, macOS, Windows.
- Correção de falha do SDK com longos resultados de reconhecimento de fala em determinados caminhos de código como [Este exemplo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Corrigido o erro de implantação do SDK no ambiente de aplicativo Web do Azure para resolver [esse problema do cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Correção de um erro de TTS ao usar `<voice>` marcação múltipla ou `<audio>` marca para resolver [esse problema do cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Foi corrigido um erro de TTS 401 quando o SDK é recuperado de suspenso.
- JavaScript: Corrigido uma importação circular de dados de áudio graças a uma contribuição de [euirim](https://github.com/euirim). 
- JavaScript: suporte adicionado para definir propriedades de serviço, conforme adicionado em 1,7.
- JavaScript: Corrigido um problema em que um erro de conexão pode resultar em tentativas de reconexão de WebSocket contínuas e sem êxito.

**Amostras**

- Exemplo adicional de reconhecimento de palavra-chave para Android [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Adição de exemplo de TTS para o cenário de servidor [aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Foram adicionados inícios rápidos de conversa com vários dispositivos para C# e C++ [aqui](quickstarts/multi-device-conversation.md).

**Outras alterações**

- Tamanho otimizado da biblioteca principal do SDK no Android.
- O SDK no 1.9.0 e em diante dá suporte `int` a ambos os `string` tipos e no campo versão da assinatura de voz para o Assinante de conversa.

## <a name="speech-sdk-180-2019-november-release"></a>SDK de fala 1.8.0:2019 – versão de novembro

**Novos recursos**

- Adicionada uma `FromHost()` API para facilitar o uso com contêineres locais e nuvens soberanas.
- Detecção de Idioma de origem automática adicionada para reconhecimento de fala (em Java e C++)
- `SourceLanguageConfig`Objeto adicionado para reconhecimento de fala, usado para especificar os idiomas de origem esperados (em Java e C++)
- `KeywordRecognizer`Suporte adicionado no Windows (UWP), Android e Ios por meio dos pacotes NuGet e Unity
- API Java de conversa remota adicionada para fazer a transcrição de conversa em lotes assíncronos.

**Alterações interruptivas**

- Funcionalidades de transistores de conversa movidas no namespace `Microsoft.CognitiveServices.Speech.Transcription` .
- Partes dos métodos de conversação são movidas para a nova `Conversation` classe.
- Suporte removido para iOS de 32 bits (ARMv7 e x86)

**Correções de bugs**

- Correção de falha se `KeywordRecognizer` o local for usado sem uma chave de assinatura de serviço de fala válida

**Amostras**

- Exemplo do Xamarin para `KeywordRecognizer`
- Exemplo de Unity para `KeywordRecognizer`
- Exemplos de C++ e Java para Detecção de Idioma de origem automática.

## <a name="speech-sdk-170-2019-september-release"></a>SDK de fala 1.7.0:2019 – versão de setembro

**Novos recursos**

- Adicionado suporte beta para Xamarin em Plataforma Universal do Windows (UWP), Android e iOS
- Adicionado suporte do iOS para o Unity
- Adição `Compressed` de suporte de entrada para alaw, mulaw, FLAC no Android, Ios e Linux
- Adicionado `SendMessageAsync` `Connection` à classe para enviar uma mensagem para o serviço
- Adicionado `SetMessageProperty` à `Connection` classe para definir a propriedade de uma mensagem
- Ligações adicionadas a TTS para Java (JRE e Android), Python, Swift e Objective-C
- Suporte à reprodução de TTS adicionado para macOS, iOS e Android.
- Informações de "limite de palavras" adicionadas para TTS.

**Correções de bugs**

- Correção do problema de compilação do IL2CPP no Unity 2019 para Android
- Corrigido o problema com cabeçalhos malformados na entrada do arquivo WAV que está sendo processada incorretamente
- Corrigido o problema com UUIDs que não são exclusivos em algumas propriedades de conexão
- Correção de alguns avisos sobre especificadores de nulidade nas associações Swift (pode exigir pequenas alterações de código)
- Correção de um bug que fazia com que as conexões WebSocket fosse fechadas de acordo com a carga de rede
- Correção de um problema no Android que às vezes resulta em IDs de impressão duplicadas usadas pelo `DialogServiceConnector`
- Melhorias na estabilidade de conexões entre interativações de várias transformações e o relatório de falhas (por meio de `Canceled` eventos) quando ocorrem com `DialogServiceConnector`
- `DialogServiceConnector` a sessão inicia agora fornecerá eventos, inclusive ao chamar `ListenOnceAsync()` durante um ativo `StartKeywordRecognitionAsync()`
- Endereçado uma falha associada às `DialogServiceConnector` atividades que estão sendo recebidas

**Amostras**

- Início rápido para Xamarin
- Atualizado o início rápido do CPP com as informações de ARM64 do Linux
- Guia de início rápido do Unity atualizado com informações do iOS

## <a name="speech-sdk-160-2019-june-release"></a>SDK de fala 1.6.0:2019 – versão de junho

**Amostras**

- Exemplos de início rápido para texto para fala sobre UWP e Unity
- Exemplo de início rápido para Swift no iOS
- Amostras de Unity para & de fala Reconhecimento de intenção e tradução
- Exemplos de guia de início rápido atualizados para `DialogServiceConnector`

**Melhorias/Alterações**

- Namespace de caixa de diálogo:
  - `SpeechBotConnector` foi renomeado para `DialogServiceConnector`
  - `BotConfig` foi renomeado para `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` foi remapeado para `DialogServiceConfig::FromBotSecret()`
  - Todos os clientes de fala de linha direta existentes continuam com suporte após a renomeação
- Atualizar o adaptador REST TTS para dar suporte a proxy, conexão persistente
- Melhorar a mensagem de erro quando uma região inválida é passada
- Swift/Objective-C:
  - Melhoria no relatório de erros: os métodos que podem resultar em um erro agora estão presentes em duas versões: uma que expõe um `NSError` objeto para tratamento de erros e outra que gera uma exceção. O primeiro é exposto a Swift. Essa alteração requer adaptações para o código Swift existente.
  - Manipulação de eventos aprimorada

**Correções de bugs**

- Correção para TTS: em que o `SpeakTextAsync` futuro retornou sem aguardar até que o áudio tenha concluído a renderização
- Correção para o marshaling de cadeias de caracteres em C# para habilitar o suporte a idioma completo
- Correção do problema do aplicativo .NET Core para carregar a biblioteca principal com a estrutura de destino net461 em exemplos
- Correção de problemas ocasionais para implantar bibliotecas nativas na pasta de saída em exemplos
- Correção de fechamento de soquete da Web confiável
- Correção de uma possível falha ao abrir uma conexão sob carga pesada no Linux
- Correção de metadados ausentes no pacote de estrutura para macOS
- Correção para problemas com o `pip install --user` no Windows

## <a name="speech-sdk-151"></a>1.5.1 SDK de fala

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

**Correções de bugs**

- Corrigir o FromSubscription quando usado com a transcrição de conversa.
- Corrija o bug no reconhecimento de palavra-chave para assistentes de voz.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019-maio de lançamento

**Novos recursos**

- O reconhecimento de palavra-chave agora está disponível para Windows e Linux. Essa funcionalidade pode funcionar com qualquer tipo de microfone, mas o suporte oficial está atualmente limitado às matrizes de microfone encontradas no hardware do Azure Kinect DK ou no SDK dos dispositivos de fala.
- A funcionalidade de dica de frase está disponível por meio do SDK. Para mais informações, consulte [aqui](./get-started-speech-to-text.md).
- A funcionalidade de transcrição de conversa está disponível por meio do SDK. Consulte [aqui](./conversation-transcription.md).
- Adicione suporte para assistentes de voz usando o canal de fala de linha direta.

**Amostras**

- Foram adicionadas amostras para novos recursos ou novos serviços com suporte no SDK.

**Melhorias/Alterações**

- Adicionada várias propriedades de reconhecedor para ajustar o comportamento do serviço ou os resultados do serviço (como mascarar profanação e outros).
- Agora você pode configurar o reconhecedor por meio das propriedades de configuração padrão, mesmo que você tenha criado o reconhecedor `FromEndpoint` .
- Objective-C: `OutputFormat` a propriedade foi adicionada a `SPXSpeechConfiguration` .
- O SDK agora dá suporte a Debian 9 como uma distribuição do Linux.

**Correções de bugs**

- Corrigido um problema em que o recurso do orador foi destruido muito cedo em texto para fala.

## <a name="speech-sdk-142"></a>SDK 1.4.2 de fala

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

## <a name="speech-sdk-141"></a>1.4.1 SDK de fala

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

- Impedir que o pacote da Web carregue HTTPS-proxy-Agent.

## <a name="speech-sdk-140-2019-april-release"></a>SDK de fala 1.4.0:2019 – versão de abril

**Novos recursos**

- O SDK agora dá suporte ao serviço de conversão de texto em fala como uma versão beta. Há suporte na área de trabalho do Windows e do Linux do C++ e do C#. Para obter mais informações, consulte a [visão geral de conversão de texto em fala](text-to-speech.md#get-started).
- O SDK agora dá suporte a arquivos de áudio MP3 e Opus/OGG como arquivos de entrada de fluxo. Esse recurso está disponível apenas no Linux em C++ e em C# e, no momento, está em beta (mais detalhes [aqui](how-to-use-codec-compressed-audio-input-streams.md)).
- O SDK de fala para Java, .NET Core, C++ e Objective-C ganhou suporte para macOS. O suporte a Objective-C para macOS está atualmente em beta.
- iOS: o SDK de fala para iOS (Objective-C) agora também é publicado como um CocoaPod.
- JavaScript: suporte para microfone não padrão como um dispositivo de entrada.
- JavaScript: suporte a proxy para Node.js.

**Amostras**

- Foram adicionados exemplos de uso do SDK de fala com C++ e com o Objective-C no macOS.
- Exemplos que demonstram o uso do serviço de conversão de texto em fala foram adicionados.

**Melhorias/Alterações**

- Python: as propriedades adicionais dos resultados de reconhecimento agora são expostas por meio da `properties` propriedade.
- Para obter suporte adicional para desenvolvimento e depuração, você pode redirecionar as informações de log e diagnóstico do SDK para um arquivo de log (mais detalhes [aqui](how-to-use-logging.md)).
- JavaScript: melhorar o desempenho de processamento de áudio.

**Correções de bugs**

- Mac/iOS: um bug que levou a uma longa espera quando uma conexão com o serviço de fala não pôde ser estabelecida foi corrigida.
- Python: melhorar o tratamento de erros para argumentos em retornos de chamada do Python.
- JavaScript: o relatório de estado incorreto corrigido para a fala terminou em RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>SDK de fala 1.3.1:2019 – atualização de fevereiro

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

**Correção de bug**

- Foi corrigido um vazamento de memória ao usar a entrada do microfone. Baseada em fluxo ou entrada de arquivo não é afetada.

## <a name="speech-sdk-130-2019-february-release"></a>SDK de fala 1.3.0:2019 – versão de fevereiro

**Novos recursos**

- O SDK de fala dá suporte à seleção do microfone de entrada por meio da `AudioConfig` classe. Isso permite que você transmita dados de áudio para o serviço de fala de um microfone não padrão. Para obter mais informações, consulte a documentação que descreve a [seleção de dispositivo de entrada de áudio](how-to-select-audio-input-devices.md). Esse recurso ainda não está disponível no JavaScript.
- O Speech SDK agora dá suporte ao Unity em uma versão beta. Forneça comentários na seção de problemas no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples). Essa versão dá suporte ao Unity no Windows x86 e x64 (área de trabalho ou aplicativos da Plataforma Universal do Windows) e Android (ARM32/64, x86). Mais informações estão disponíveis em nosso [início rápido do Unity](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity).
- O arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada ao SDK do Core.

**Amostras**

O novo conteúdo a seguir está disponível no nosso [repositório de exemplo](https://aka.ms/csspeech/samples):

- Exemplos adicionais para o `AudioConfig.FromMicrophoneInput` .
- Mais exemplos do Python para conversão e reconhecimento de intenção.
- Exemplos adicionais para usar o `Connection` objeto no Ios.
- Exemplos adicionais de Java para tradução com saída de áudio.
- Novo exemplo para usar a [API REST de Transcrição de Lote](batch-transcription.md).

**Melhorias/Alterações**

- Python
  - Verificação de parâmetro e mensagens de erro aprimoradas no `SpeechConfig` .
  - Adicione suporte para o `Connection` objeto.
  - Suporte para Python de 32 bits (x86) no Windows.
  - O Speech SDK para Python está fora do beta.
- iOS
  - O SDK agora é construído com relação ao SDK versão 12.1 do iOS.
  - O SDK agora dá suporte a iOS versões 9.2 e posteriores.
  - Melhore a documentação de referência e conserte vários nomes de propriedade.
- JavaScript
  - Adicione suporte para o `Connection` objeto.
  - Adicione arquivos de definição de tipo para JavaScript agrupado
  - Suporte inicial e implementação para dicas de frase.
  - Retornar a coleção de propriedades com o serviço de JSON para reconhecimento
- DLLs do Windows agora contêm um recurso de versão.
- Se você criar um reconhecedor `FromEndpoint` , poderá adicionar parâmetros diretamente à URL do ponto de extremidade. Usando `FromEndpoint` o, você não pode configurar o reconhecedor por meio das propriedades de configuração padrão.

**Correções de bugs**

- Nome de usuário de proxy e senha de proxy vazios não foram tratados corretamente. Com esta versão, se você definir o nome de usuário de proxy e a senha de proxy como uma cadeia de caracteres vazia, eles não serão enviados ao conectarem-se ao proxy.
- As SessionIds criadas pelo SDK nem sempre eram realmente aleatórias para alguns idiomas&nbsp;/ambientes. Adicionada a inicialização aleatória do gerador para corrigir esse problema.
- Melhore o tratamento do token de autorização. Se você quiser usar um token de autorização, especifique em `SpeechConfig` e deixe a chave de assinatura vazia. Em seguida, crie o reconhecedor como de costume.
- Em alguns casos, o `Connection` objeto não foi liberado corretamente. Esse problema foi corrigido.
- O exemplo de JavaScript foi corrigido para dar suporte para saída de áudio para síntese de conversão também no Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

- Acionar o final do fluxo no turn.end, não no speech.end.
- Consertar um bug na bomba de áudio que não agendou o próximo envio em caso de falha do envio atual.
- Consertar reconhecimento contínuo com o token de autenticação.
- Correção de bug para diferentes reconhecedores/pontos de extremidade.
- Melhorias na documentação.

## <a name="speech-sdk-120-2018-december-release"></a>SDK de fala 1.2.0:2018 – versão de dezembro

**Novos recursos**

- Python
  - A versão Beta do suporte do Python (3.5 e posterior) está disponível com esta versão. Para obter mais informações, consulte aqui] (início rápido-python.md).
- JavaScript
  - O SDK de Fala para o JavaScript tem sido livre. O código-fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Agora damos suporte a node. js, mais informações podem ser encontradas [aqui](./get-started-speech-to-text.md).
  - A restrição de comprimento para sessões de áudio foi removida, a reconexão ocorrerá automaticamente sob a tampa.
- Objeto `Connection`
  - No `Recognizer` , você pode acessar um `Connection` objeto. Esse objeto permite iniciar a conexão de serviço e inscrever-se para se conectar e desconectar de eventos explicitamente.
    (Esse recurso ainda não está disponível no JavaScript e no Python.)
- Suporte para Ubuntu 18.04.
- Android
  - Suporte do ProGuard habilitado durante a geração de APK.

**Na**

- Melhorias no uso de thread interno, reduzindo o número de threads, bloqueios e exclusões mútuas.
- Relatório/informações de erros aprimorados. Em vários casos, as mensagens de erro não foram propagadas até o fim.
- As dependências de desenvolvimento atualizadas do JavaScript para usar módulos atualizados.

**Correções de bugs**

- Correção de vazamentos de memória devido a uma incompatibilidade de tipo em `RecognizeAsync` .
- Em alguns casos, as exceções foram sendo vazadas.
- Corrigindo o vazamento de memória em argumentos de evento de tradução.
- Corrigido um problema de bloqueio na reconexão longa de sessões em execução.
- Correção de um problema que poderia levar a um resultado final ausente para traduções com falha.
- C#: se uma `async` operação não foi aguardada no thread principal, era possível que o reconhecedor pudesse ser descartado antes da conclusão da tarefa assíncrona.
- Java: Corrigido um problema que resulta em uma falha da VM Java.
- Objective-C: mapeamento de enumeração fixo; RecognizedIntent foi retornado em vez de `RecognizingIntent` .
- JavaScript: defina o formato de saída padrão como "simples" em `SpeechConfig` .
- JavaScript: removendo a inconsistência entre as propriedades no objeto de configuração em JavaScript e em outras linguagens.

**Amostras**

- Atualização e correção de vários exemplos (por exemplo, vozes de saída para tradução, etc.).
- Adicionados exemplos do Node. js no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>SDK de Fala 1.1.0

**Novos recursos**

- Suporte para Android x86/x64.
- Suporte a proxy: no `SpeechConfig` objeto, agora você pode chamar uma função para definir as informações de proxy (nome do host, porta, nome de usuário e senha). Este recurso ainda não está disponível no iOS.
- Melhor código de erro e mensagens. Se um reconhecimento retornou um erro, isso já definiu `Reason` (no evento cancelado) ou `CancellationDetails` (no resultado do reconhecimento) para `Error`. O evento cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails`. Se o servidor retornou informações de erro adicionais com o erro relatado, agora ele estará disponível nos novos membros.

**Na**

- Adicionada verificação adicional na configuração do reconhecedor e adicionada outra mensagem de erro.
- Manipulação aprimorada de silêncio de longa duração no meio de um arquivo de áudio.
- Pacote NuGet: para projetos do .NET Framework, ele impede a construção com a configuração AnyCPU.

**Correções de bugs**

- Corrigido várias exceções encontradas em reconhecedores. Além disso, as exceções são capturadas e convertidas em `Canceled` evento.
- Corrigir um vazamento de memória no gerenciamento de propriedades.
- Corrigido o erro no qual um arquivo de entrada de áudio poderia travar o reconhecedor.
- Corrigido um bug no qual os eventos podiam ser recebidos após um evento de parada da sessão.
- Corrigidas algumas condições de corrida no threading.
- Corrigido um problema de compatibilidade do iOS que poderia resultar em uma falha.
- Melhorias de estabilidade para suporte de microfone Android.
- Corrigido um erro em que um reconhecedor em JavaScript ignoraria o idioma de reconhecimento.
- Correção de um bug que impede a definição de `EndpointId` (em alguns casos) em JavaScript.
- A ordem dos parâmetros foi alterada em addpropósito em JavaScript e adicionou uma `AddIntent` assinatura JavaScript ausente.

**Amostras**

- Foram adicionados exemplos de C++ e C# para uso de fluxo de pull e Push no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>SDK de Fala 1.0.1

Melhorias na confiabilidade e correções de bugs:

- Corrigido erro fatal potencial devido à condição de corrida no reconhecedor de descarte
- Correção de erro fatal potencial quando ocorrem Propriedades não definidas.
- Adicionado erro adicional e verificação de parâmetros.
- Objective-C: corrigido possível erro fatal causado por substituição de nome em NSString.
- Objective-C: visibilidade ajustada da API
- JavaScript: corrigido em relação a eventos e cargas.
- Melhorias na documentação.

Em nosso [repositório de exemplos](https://aka.ms/csspeech/samples), um novo exemplo para JavaScript foi adicionado.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK de Fala dos Serviços Cognitivos 1.0.0: versão de setembro de 2018

**Novos recursos**

- Suporte para Objective-C no iOS. Confira nosso [Início Rápido do Objective-C para iOS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone).
- Suporte para JavaScript no navegador. Confira nosso [Início Rápido do JavaScript](./get-started-speech-to-text.md).

**Alterações interruptivas**

- Com esta versão, várias alterações significativas são introduzidas.
  Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de Fala dos Serviços Cognitivos 0.6.0: versão de agosto de 2018

**Novos recursos**

- Os aplicativos UWP criados com o SDK de Fala agora podem ser aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
  Confira o [Início Rápido do UWP](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp).
- Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
- Experimental: dê suporte Java 8 no Windows (64 bits) e no Linux (Ubuntu 16.04 x64).
  Confira o guia de [início rápido do Java Runtime Environment](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre).

**Alteração funcional**

- Expor informações de detalhe de erro adicionais sobre erros de conexão.

**Alterações interruptivas**

- No Java (Android), a função `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` não requer mais um parâmetro de caminho. Agora, o caminho é detectado automaticamente em todas as plataformas com suporte.
- O get-accessor da propriedade `EndpointUrl` em Java e C# foi removido.

**Correções de bugs**

- Em Java, o resultado da síntese de áudio no reconhecedor de tradução agora está implementado.
- Foi corrigido um bug que podia causar threads inativos e um grande número de soquetes abertos e não usados.
- Foi corrigido um problema em que o reconhecimento de execução longa podia terminar no meio da transmissão.
- Corrigida uma condição de corrida no desligamento do reconhecedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de Fala dos Serviços Cognitivos 0.5.0: versão de julho de 2018

**Novos recursos**

- Suporte a plataforma Android (API 23: Android 6.0 Marshmallow ou superior). Confira o [Início Rápido para Android](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android).
- Suporte para .NET Standard 2.0 no Windows. Confira o [Início Rápido para .NET Core](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimental: Suporte a UWP no Windows (versão 1709 ou posterior).
  - Confira o [Início Rápido do UWP](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp).
  - Observação: os aplicativos UWP compilados com o SDK de Fala ainda não são aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
- Suporte ao reconhecimento de execução longa com reconexão automática.

**Alterações funcionais**

- O `StartContinuousRecognitionAsync()` dá suporte ao reconhecimento de execução longa.
- O resultado do reconhecimento contém mais campos. Eles são deslocados do início do áudio e da duração (ambos em tiques) do texto reconhecido e dos valores adicionais que representam o status de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- Suporte para AuthorizationToken para criar instâncias de fábrica.

**Alterações interruptivas**

- Eventos de reconhecimento: `NoMatch` o tipo de evento foi mesclado no `Error` evento.
- SpeechOutputFormat em C# foi renomeado para `OutputFormat` para se manter alinhado com o C++.
- O tipo de retorno de alguns métodos da interface `AudioInputStream` foi um pouco alterado:
  - Em Java, o método `read` agora retorna `long` em vez de `int`.
  - Em C#, o método `Read` agora retorna `uint` em vez de `int`.
  - Em C++, os métodos `Read` e `GetFormat` agora retornam `size_t` em vez de `int`.
- C++: as instâncias de fluxos de entrada de áudio agora podem ser passadas apenas como um `shared_ptr`.

**Correções de bugs**

- Foram corrigidos os valores retornados incorretos no resultado quando `RecognizeAsync()` atinge o tempo limite.
- A dependência das bibliotecas do Media Foundation no Windows foi removida. O SDK agora usa as APIs Core Audio.
- Correção da documentação: uma página de [regiões](regions.md) foi adicionada para descrever as regiões com suporte.

**Problema conhecido**

- O SDK de Fala para Android não relata os resultados da síntese de fala para tradução. Esse problema será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de Fala de Serviços Cognitivos 0.4.0: versão de junho de 2018

**Alterações funcionais**

- AudioInputStream

  Agora, um reconhecedor pode consumir um fluxo como a fonte de áudio. Para obter mais detalhes, confira o [guia de instruções](how-to-use-audio-input-streams.md) relacionado.

- Formato de saída detalhado

  Ao criar um `SpeechRecognizer`, você pode solicitar o formato de saída `Detailed` ou `Simple`. O `DetailedSpeechRecognitionResult` contém uma pontuação de confiança, texto reconhecido, forma léxica bruta, forma normalizada e forma normalizada com obscenidades mascaradas.

**Alteração significativa**

- Alterado para `SpeechRecognitionResult.Text` de `SpeechRecognitionResult.RecognizedText` em C#.

**Correções de bugs**

- Foi corrigido um possível problema de retorno de chamada na camada USP durante o desligamento.
- Se um reconhecedor consumir um arquivo de entrada de áudio, ele manteve o identificador de arquivo por mais tempo do que o necessário.
- Foram removidos vários deadlocks entre a bomba de mensagens e o reconhecedor.
- Dispare um resultado `NoMatch` quando o tempo de resposta do serviço esgotar.
- As bibliotecas do Media Foundation no Windows são carregadas com atraso. Essa biblioteca é necessária apenas para entrada do microfone.
- A velocidade de carregamento de dados de áudio é limitada a duas vezes a velocidade do áudio original.
- No Windows, agora os assemblies .NET em C# têm nomes fortes.
- Correção de documentação: `Region` são as informações necessárias para criar um reconhecedor.

Mais exemplos foram adicionados e são atualizados constantemente. Para obter o último conjunto de exemplos, confira o [Repositório GitHub de exemplos do SDK de Fala](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de Fala de Serviços Cognitivos 0.2.12733: versão de maio de 2018

Esta versão é a primeira versão prévia pública do SDK de Fala dos Serviços Cognitivos.
