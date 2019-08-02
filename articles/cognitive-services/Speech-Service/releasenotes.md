---
title: Notas de versão – serviço de fala
titleSuffix: Azure Cognitive Services
description: Veja um log em execução de versões de recursos, melhorias, correções de bugs e problemas conhecidos do serviço de fala.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 12eecc4998153cbeedeb907ecad33c56141a50e6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559117"
---
# <a name="release-notes"></a>Notas de versão

## <a name="speech-sdk-160-2019-june-release"></a>1\.6.0 do SDK de fala: 2019 – versão de junho

**Amostras**
*   Exemplos de início rápido para texto para fala sobre UWP e Unity
*   Exemplo de início rápido para Swift no iOS
*   Amostras de Unity para & de fala Reconhecimento de intenção e tradução
*   Exemplos de guia de início rápido atualizados para DialogServiceConnector

**Melhorias/Alterações**
* Namespace de caixa de diálogo:
    * SpeechBotConnector foi renomeado para DialogServiceConnector
    * BotConfig foi renomeado para DialogServiceConfig
    * BotConfig:: FromChannelSecret () foi remapeado para DialogServiceConfig:: FromBotSecret ()
    * Todos os clientes de fala de linha direta existentes continuam com suporte após a renomeação
* Atualizar o adaptador REST TTS para dar suporte a proxy, conexão persistente
* Melhorar a mensagem de erro quando uma região inválida é passada
* Swift/Objective-C:
    * Relatório de erros aprimorado: Os métodos que podem resultar em um erro agora estão presentes em duas versões: Um que expõe um `NSError` objeto para tratamento de erros e outro que gera uma exceção. O primeiro é exposto a Swift. Essa alteração requer adaptações para o código Swift existente.
    * Manipulação de eventos aprimorada

**Correções de bug**
*   Correção para TTS: em que SpeakTextAsync futuro retornou sem esperar até que o áudio tenha concluído a renderização
*   Correção para o marshaling de C# cadeias de caracteres no para habilitar o suporte a idiomas completos
*   Correção do problema do aplicativo .NET Core para carregar a biblioteca principal com a estrutura de destino net461 em exemplos
*   Correção de problemas ocasionais para implantar bibliotecas nativas na pasta de saída em exemplos
*   Correção de fechamento de soquete da Web confiável
*   Correção de uma possível falha ao abrir uma conexão sob carga muito pesada no Linux
*   Correção de metadados ausentes no pacote de estrutura para macOS
*   Correção para problemas com `pip install --user` o no Windows


## <a name="speech-sdk-151"></a>1\.5.1 SDK de fala

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

**Correções de bug**

* Corrigir o FromSubscription quando usado com a transcrição de conversa.
* Corrija o bug na palavra-chave que está separando o assistente virtual de voz primeiro.


## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: 2019-maio de lançamento

**Novos recursos**

* A funcionalidade ativar palavra (palavra-chave KWS) agora está disponível para Windows e Linux. A funcionalidade do KWS pode funcionar com qualquer tipo de microfone, o suporte oficial do KWS, no entanto, está limitado atualmente às matrizes de microfone encontradas no hardware do Azure Kinect DK ou no SDK dos dispositivos de fala.
* A funcionalidade de dica de frase está disponível por meio do SDK. Para saber mais, clique [aqui](how-to-phrase-lists.md).
* A funcionalidade de transcrição de conversa está disponível por meio do SDK. Consulte [aqui](conversation-transcription-service.md).
* Adicione suporte para assistentes virtuais primeiro de voz usando o canal de fala de linha direta.

**Amostras**

* Foram adicionadas amostras para novos recursos ou novos serviços com suporte no SDK.

**Melhorias/Alterações**

* Adicionada várias propriedades de reconhecedor para ajustar o comportamento do serviço ou os resultados do serviço (como mascarar profanação e outros).
* Agora você pode configurar o reconhecedor por meio das propriedades de configuração padrão, mesmo que você tenha `FromEndpoint`criado o reconhecedor.
* Objective-C `OutputFormat` : Property foi adicionado a SPXSpeechConfiguration.
* O SDK agora dá suporte a Debian 9 como uma distribuição do Linux.

**Correções de bug**

* Corrigido um problema em que o recurso do orador foi destruido muito cedo em texto para fala.
## <a name="speech-sdk-142"></a>SDK 1.4.2 de fala

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

## <a name="speech-sdk-141"></a>1\.4.1 SDK de fala

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

* Impedir que o pacote da Web carregue HTTPS-proxy-Agent.

## <a name="speech-sdk-140-2019-april-release"></a>1\.4.0 do SDK de fala: 2019 – versão de abril

**Novos recursos** 

* O SDK agora dá suporte ao serviço de conversão de texto em fala como uma versão beta. Ele tem suporte na área de trabalho do Windows C++ e C#do Linux de e. Para obter mais informações, consulte a [visão geral de conversão de texto em fala](text-to-speech.md#get-started-with-text-to-speech).
* O SDK agora dá suporte a arquivos de áudio MP3 e Opus/OGG como arquivos de entrada de fluxo. Esse recurso está disponível apenas no Linux a C++ partir C# do e no, no momento, está em beta (mais detalhes [aqui](how-to-use-codec-compressed-audio-input-streams.md)).
* O SDK de fala para Java, .NET Core C++ e Objective-C ganhou suporte para MacOS. O suporte a Objective-C para macOS está atualmente em beta.
* iOS: O SDK de fala para iOS (Objective-C) agora também é publicado como um CocoaPod.
* JavaScript: Suporte para microfone não padrão como um dispositivo de entrada.
* JavaScript: Suporte de proxy para node. js.

**Amostras**

* Foram adicionados exemplos de uso do SDK C++ de fala com e com o Objective-C no MacOS.
* Exemplos que demonstram o uso do serviço de conversão de texto em fala foram adicionados.

**Melhorias/Alterações**

* Python: As propriedades adicionais dos resultados de reconhecimento agora são expostas por meio da `properties` propriedade.
* Para obter suporte adicional para desenvolvimento e depuração, você pode redirecionar as informações de log e diagnóstico do SDK para um arquivo de log (mais detalhes [aqui](how-to-use-logging.md)).
* JavaScript: Melhorar o desempenho de processamento de áudio.

**Correções de bug**

* Mac/iOS: Um bug que levou a uma longa espera quando uma conexão com o serviço de fala não pôde ser estabelecida foi corrigida.
* Python: melhorar o tratamento de erros para argumentos em retornos de chamada do Python.
* JavaScript: Corrigido o relatório de estado incorreto para fala encerrado em RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>1\.3.1 do SDK de fala: 2019 – atualização de fevereiro

Essa é uma liberação de correção de bug e afeta apenas o SDK nativo/gerenciado. Ele não está afetando a versão JavaScript do SDK.

**Correção de bug**

* Foi corrigido um vazamento de memória ao usar a entrada do microfone. Baseada em fluxo ou entrada de arquivo não é afetada.

## <a name="speech-sdk-130-2019-february-release"></a>SDK de Fala 1.3.0: Versão de fevereiro de 2019

**Novos recursos**

* O Speech SDK dá suporte à seleção de entrada microfone por meio da classe AudioConfig. Isso permite que você transmita dados de áudio para os serviços de fala de um microfone não padrão. Para obter mais informações, consulte a documentação que descreve a [seleção de dispositivo de entrada de áudio](how-to-select-audio-input-devices.md). Esse recurso ainda não está disponível no JavaScript.
* O Speech SDK agora dá suporte ao Unity em uma versão beta. Forneça comentários na seção de problemas no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples). Essa versão dá suporte ao Unity no Windows x86 e x64 (área de trabalho ou aplicativos da Plataforma Universal do Windows) e Android (ARM32/64, x86). Mais informações estão disponíveis em nosso [início rápido do Unity](quickstart-csharp-unity.md).
* O arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada ao SDK do Core.


**Amostras**

O novo conteúdo a seguir está disponível no nosso [repositório de exemplo](https://aka.ms/csspeech/samples):

* Exemplos adicionais para AudioConfig.FromMicrophoneInput.
* Mais exemplos do Python para conversão e reconhecimento de intenção.
* Exemplos adicionais para usar o objeto de Conexão no iOS.
* Exemplos adicionais de Java para tradução com saída de áudio.
* Novo exemplo para usar a [API REST de Transcrição de Lote](batch-transcription.md).

**Melhorias/Alterações**

* Python
  * Mensagens de erro e verificação de parâmetro aprimoradas em SpeechConfig.
  * Adicione suporte para o objeto de Conexão.
  * Suporte para Python de 32 bits (x86) no Windows.
  * O Speech SDK para Python está fora do beta.
* iOS
  * O SDK agora é construído com relação ao SDK versão 12.1 do iOS.
  * O SDK agora dá suporte a iOS versões 9.2 e posteriores.
  * Melhore a documentação de referência e conserte vários nomes de propriedade.
* JavaScript
  * Adicione suporte para o objeto de Conexão.
  * Adicione arquivos de definição de tipo para JavaScript agrupado
  * Suporte inicial e implementação para dicas de frase.
  * Retornar a coleção de propriedades com o serviço de JSON para reconhecimento
* DLLs do Windows agora contêm um recurso de versão.
* Se você criar um reconhecedor `FromEndpoint` , poderá adicionar parâmetros diretamente à URL do ponto de extremidade. Usando `FromEndpoint` o, você não pode configurar o reconhecedor por meio das propriedades de configuração padrão.

**Correções de bug**

* Nome de usuário de proxy e senha de proxy vazios não foram tratados corretamente. Com esta versão, se você definir o nome de usuário de proxy e a senha de proxy como uma cadeia de caracteres vazia, eles não serão enviados ao conectarem-se ao proxy.
* As SessionIds criadas pelo SDK nem sempre eram realmente aleatórias para alguns idiomas&nbsp;/ambientes. Adicionada a inicialização aleatória do gerador para corrigir esse problema.
* Melhore o tratamento do token de autorização. Se você quiser usar um token de autorização, especifique em SpeechConfig e deixe a chave de assinatura vazia. Em seguida, crie o reconhecedor como de costume.
* Em alguns casos, o objeto Conexão não foi lançado corretamente. Esse problema foi corrigido.
* O exemplo de JavaScript foi corrigido para dar suporte para saída de áudio para síntese de conversão também no Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

* Acionar o final do fluxo no turn.end, não no speech.end.
* Consertar um bug na bomba de áudio que não agendou o próximo envio em caso de falha do envio atual.
* Consertar reconhecimento contínuo com o token de autenticação.
* Correção de bug para diferentes reconhecedores/pontos de extremidade.
* Melhorias na documentação.

## <a name="speech-sdk-120-2018-december-release"></a>SDK dE Fala 1.2.0: Versão de dezembro de 2018

**Novos recursos**

* Python
  * A versão Beta do suporte do Python (3.5 e posterior) está disponível com esta versão. Para obter mais informações, consulte aqui] (início rápido-python.md).
* JavaScript
  * O SDK de Fala para o JavaScript tem sido livre. O código-fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Agora damos suporte a node. js, mais informações podem ser encontradas [aqui](quickstart-js-node.md).
  * A restrição de comprimento para sessões de áudio foi removida, a reconexão ocorrerá automaticamente sob a tampa.
* Objeto de Conexão
  * No reconhecedor, você pode acessar um objeto de conexão. Esse objeto permite iniciar a conexão de serviço e inscrever-se para se conectar e desconectar de eventos explicitamente.
    (Esse recurso ainda não está disponível no JavaScript e no Python.)
* Suporte para Ubuntu 18.04.
* Android
  * Suporte do ProGuard habilitado durante a geração de APK.

**Melhorias**

* Melhorias no uso de thread interno, reduzindo o número de threads, bloqueios e exclusões mútuas.
* Relatório/informações de erros aprimorados. Em vários casos, as mensagens de erro não foram propagadas até o fim.
* As dependências de desenvolvimento atualizadas do JavaScript para usar módulos atualizados.

**Correções de bug**

* Vazamentos de memória fixa devido a um tipo de incompatibilidade no RecognizeAsync.
* Em alguns casos, as exceções foram sendo vazadas.
* Corrigindo o vazamento de memória em argumentos de evento de tradução.
* Corrigido um problema de bloqueio na reconexão longa de sessões em execução.
* Correção de um problema que poderia levar a um resultado final ausente para traduções com falha.
* C#: Se uma operação assíncrona não foi colocada em espera no thread principal, era possível que o reconhecedor pudesse ser descartado antes que a tarefa assíncrona fosse concluída.
* Java: Corrigido um problema que resultou em uma falha da máquina virtual Java.
* Objective-C: Mapeamento enum fixo; RecognizedIntent foi retornado em vez de RecognizingIntent.
* JavaScript: Formato de saída padrão definido em 'simples' no SpeechConfig.
* JavaScript: Removendo a inconsistência entre as propriedades no objeto de configuração em JavaScript e outras linguagens.

**Amostras**

* Atualização e correção de vários exemplos (por exemplo, vozes de saída para tradução, etc.).
* Adicionados exemplos do Node. js no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>SDK de Fala 1.1.0

**Novos recursos**

* Suporte para Android x86/x64.
* Suporte do proxy: No objeto SpeechConfig, agora você pode chamar uma função para definir as informações de proxy (nome do host, porta, nome de usuário e senha). Este recurso ainda não está disponível no iOS.
* Melhor código de erro e mensagens. Se um reconhecimento retornou um erro, isso já definiu `Reason` (no evento cancelado) ou `CancellationDetails` (no resultado do reconhecimento) para `Error`. O evento cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails`. Se o servidor retornou informações de erro adicionais com o erro relatado, agora ele estará disponível nos novos membros.

**Melhorias**

* Adicionada verificação adicional na configuração do reconhecedor e adicionada outra mensagem de erro.
* Manipulação aprimorada de silêncio de longa duração no meio de um arquivo de áudio.
* Pacote NuGet: para projetos do .NET Framework, ele impede a construção com a configuração AnyCPU.

**Correções de bug**

* Corrigido várias exceções encontradas em reconhecedores. Além disso, as exceções são capturadas e convertidas em evento cancelado.
* Corrigir um vazamento de memória no gerenciamento de propriedades.
* Corrigido o erro no qual um arquivo de entrada de áudio poderia travar o reconhecedor.
* Corrigido um bug no qual os eventos podiam ser recebidos após um evento de parada da sessão.
* Corrigidas algumas condições de corrida no threading.
* Corrigido um problema de compatibilidade do iOS que poderia resultar em uma falha.
* Melhorias de estabilidade para suporte de microfone Android.
* Corrigido um erro em que um reconhecedor em JavaScript ignoraria o idioma de reconhecimento.
* Corrigido um bug que impedia a configuração do EndpointId (em alguns casos) em JavaScript.
* Alterou a ordem dos parâmetros no AddIntent em JavaScript e adicionou a assinatura JavaScript ausente do AddIntent.

**Amostras**

* Adicionado o C++ e C# samplea para uso de fluxo de pull e push na [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>SDK de Fala 1.0.1

Melhorias na confiabilidade e correções de bugs:

* Corrigido erro fatal potencial devido à condição de corrida no reconhecedor de descarte
* Corrigido erro fatal potencial no caso de propriedades não definidas.
* Adicionado erro adicional e verificação de parâmetros.
* Objective-C: corrigido possível erro fatal causado por substituição de nome em NSString.
* Objective-C: visibilidade ajustada da API
* JavaScript: corrigido em relação a eventos e conteúdos.
* Melhorias na documentação.

Em nosso [repositório de exemplos](https://aka.ms/csspeech/samples), um novo exemplo para JavaScript foi adicionado.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK de Fala dos Serviços Cognitivos 1.0.0: versão de setembro de 2018

**Novos recursos**

* Suporte para Objective-C no iOS. Confira nosso [Início Rápido do Objective-C para iOS](quickstart-objectivec-ios.md).
* Suporte para JavaScript no navegador. Confira nosso [Início Rápido do JavaScript](quickstart-js-browser.md).

**Alterações da falha**

* Com esta versão, várias alterações significativas são introduzidas.
  Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de Fala dos Serviços Cognitivos 0.6.0: versão de agosto de 2018

**Novos recursos**

* Os aplicativos UWP criados com o SDK de Fala agora podem ser aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
  Confira o [Início Rápido do UWP](quickstart-csharp-uwp.md).
* Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
* Experimental: dê suporte Java 8 no Windows (64 bits) e no Linux (Ubuntu 16.04 x64).
  Confira o [Início Rápido do Java Runtime Environment](quickstart-java-jre.md).

**Alteração funcional**

* Expor informações de detalhe de erro adicionais sobre erros de conexão.

**Alterações da falha**

* No Java (Android), a função `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` não requer mais um parâmetro de caminho. Agora, o caminho é detectado automaticamente em todas as plataformas com suporte.
* O get-accessor da propriedade `EndpointUrl` em Java e C# foi removido.

**Correções de bug**

* Em Java, o resultado da síntese de áudio no reconhecedor de tradução agora está implementado.
* Foi corrigido um bug que podia causar threads inativos e um grande número de soquetes abertos e não usados.
* Foi corrigido um problema em que o reconhecimento de execução longa podia terminar no meio da transmissão.
* Corrigida uma condição de corrida no desligamento do reconhecedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de Fala dos Serviços Cognitivos 0.5.0: versão de julho de 2018

**Novos recursos**

* Suporte à plataforma Android (API 23: Android 6.0 Marshmallow ou superior). Confira o [Início Rápido para Android](quickstart-java-android.md).
* Suporte para .NET Standard 2.0 no Windows. Confira o [Início Rápido para .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimental: suporte à UWP no Windows (versão 1709 ou posterior).
  * Confira o [Início Rápido do UWP](quickstart-csharp-uwp.md).
  * Observação: os aplicativos UWP compilados com o SDK de Fala ainda não são aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
* Suporte ao reconhecimento de execução longa com reconexão automática.

**Alterações funcionais**

* O `StartContinuousRecognitionAsync()` dá suporte ao reconhecimento de execução longa.
* O resultado do reconhecimento contém mais campos. Eles são deslocados do início do áudio e da duração (ambos em tiques) do texto reconhecido e dos valores adicionais que representam o status de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
* Suporte para AuthorizationToken para criar instâncias de fábrica.

**Alterações da falha**

* Eventos de reconhecimento: o tipo de evento NoMatch foi mesclado ao evento Erro.
* SpeechOutputFormat em C# foi renomeado para OutputFormat para permanecer alinhado com o C++.
* O tipo de retorno de alguns métodos da interface `AudioInputStream` foi um pouco alterado:
   * Em Java, o método `read` agora retorna `long` em vez de `int`.
   * Em C#, o método `Read` agora retorna `uint` em vez de `int`.
   * Em C++, os métodos `Read` e `GetFormat` agora retornam `size_t` em vez de `int`.
* C++: as instâncias de fluxos de entrada de áudio agora podem ser passadas apenas como um `shared_ptr`.

**Correções de bug**

* Foram corrigidos os valores retornados incorretos no resultado quando `RecognizeAsync()` atinge o tempo limite.
* A dependência das bibliotecas do Media Foundation no Windows foi removida. O SDK agora usa as APIs Core Audio.
* Correção da documentação: uma página de [regiões](regions.md) foi adicionada para descrever as regiões com suporte.

**Problema conhecido**

* O SDK de Fala para Android não relata os resultados da síntese de fala para tradução. Esse problema será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de Fala dos Serviços Cognitivos 0.4.0: versão de junho de 2018

**Alterações funcionais**

- AudioInputStream

  Agora, um reconhecedor pode consumir um fluxo como a fonte de áudio. Para obter mais detalhes, confira o [guia de instruções](how-to-use-audio-input-streams.md) relacionado.

- Formato de saída detalhado

  Ao criar um `SpeechRecognizer`, você pode solicitar o formato de saída `Detailed` ou `Simple`. O `DetailedSpeechRecognitionResult` contém uma pontuação de confiança, texto reconhecido, forma léxica bruta, forma normalizada e forma normalizada com obscenidades mascaradas.

**Alterações da falha**

- Alterado para `SpeechRecognitionResult.Text` de `SpeechRecognitionResult.RecognizedText` em C#.

**Correções de bug**

- Foi corrigido um possível problema de retorno de chamada na camada USP durante o desligamento.

- Se um reconhecedor consumir um arquivo de entrada de áudio, ele manteve o identificador de arquivo por mais tempo do que o necessário.

- Foram removidos vários deadlocks entre a bomba de mensagens e o reconhecedor.

- Dispare um resultado `NoMatch` quando o tempo de resposta do serviço esgotar.

- As bibliotecas do Media Foundation no Windows são carregadas com atraso. Essa biblioteca é necessária apenas para entrada do microfone.

- A velocidade de carregamento de dados de áudio é limitada a duas vezes a velocidade do áudio original.

- No Windows, agora os assemblies .NET em C# têm nomes fortes.

- Correção de documentação: `Region` são as informações necessárias para criar um reconhecedor.

Mais exemplos foram adicionados e são atualizados constantemente. Para obter o último conjunto de exemplos, confira o [Repositório GitHub de exemplos do SDK de Fala](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de Fala dos Serviços Cognitivos 0.2.12733: versão de maio de 2018

Esta versão é a primeira versão prévia pública do SDK de Fala dos Serviços Cognitivos.
