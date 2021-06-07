---
title: Perguntas frequentes sobre Conversão de Fala em Texto
titleSuffix: Azure Cognitive Services
description: Obtenha repostas para perguntas frequentes sobre o serviço de Conversão de Fala em Texto.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: panosper
ms.openlocfilehash: f71fd01d45604dff843ad6eba62561937366a125
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382327"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Perguntas frequentes sobre Conversão de Fala em Texto

Se você não encontrar respostas para suas perguntas nas perguntas frequentes, verifique[outras opções de suporte](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de linha de base e um modelo personalizado de fala em texto?**

**R**: Um modelo de linha de base foi treinado usando dados de propriedade da Microsoft e já está implantado na nuvem. Você pode usar um modelo personalizado a fim de adaptar um modelo para se adequar melhor a um ambiente específico que tenha determinado ruído ambiente ou linguagem. Chãos de fábrica, carros, ruas barulhentas exigiriam um modelo acústico adaptado. Tópicos como biologia, física, radiologia, nomes de produtos e acrônimos personalizados exigiriam um modelo de linguagem adaptado. Se você treinar um modelo personalizado, deverá começar com o texto relacionado para aprimorar o reconhecimento de termos e frases especiais.

**P: Por onde começar se eu quiser usar um modelo de linha de base?**

**R**: Primeiro, obtenha uma [chave de assinatura](overview.md#try-the-speech-service-for-free). Se você quiser fazer chamadas REST para os modelos de linha de base pré-empregados, confira as [APIs REST](./overview.md#reference-docs). Se você quiser usar WebSockets, [baixe o SDK](speech-sdk.md).

**P: Preciso sempre criar um modelo de fala personalizado?**

**R**: Não. Se o aplicativo usa linguagem genérica diária, você não precisa personalizar um modelo. Se o aplicativo é usado em um ambiente em que há pouco ou nenhum ruído de fundo, você não precisa personalizar um modelo.

Você pode implantar modelos personalizados e de linha de base no portal e, em seguida, executar testes de precisão neles. Você pode usar esse recurso para medir a precisão de um modelo de linha de base em comparação com um modelo personalizado.

**P: Como saberei quando o processamento para meu conjunto de dados ou modelo estará completo?**

**R:** Atualmente, o status do modelo ou conjunto de dados na tabela é a única maneira de saber. Quando o processamento for concluído, o status será **Bem-sucedido**.

**P: Eu posso criar mais de um modelo?**

**R**: Não há limite para o número de modelos que você pode ter em seu conjunto.

**P: Percebi que cometi um erro. Como cancelo minha importação de dados ou criação de modelo em andamento?**

**R**: Atualmente você não pode reverter um processo de adaptação acústica ou de linguagem. Você pode excluir modelos e dados importados quando estão em um estado terminal.

**P: Obtenho vários resultados para cada frase com o formato de saída detalhado. Qual deles devo usar?**

**R**: Sempre use o primeiro resultado, mesmo que outro resultado ("N-melhor") possa ter um valor de confiança maior. O Serviço de Fala considera o primeiro resultado como o melhor. Ele também poderá ser uma cadeia de caracteres vazia se nenhuma fala for reconhecida.

Os outros resultados provavelmente são piores e podem não ter a capitalização e pontuação total aplicadas. Esses resultados são mais úteis em cenários especiais, como oferecer aos usuários a opção de escolher correções de uma lista ou manipular comandos reconhecidos incorretamente.

**P: Por que existem modelos de base diferentes?**

**R**: Você pode escolher entre mais de um modelo de base no Serviço de Fala. Cada nome de modelo contém a data em que foi adicionado. Ao começar a treinar um modelo personalizado, use o modelo mais recente para obter a melhor precisão. Modelos de base mais antigos ainda estão disponíveis por algum tempo quando um novo modelo é disponibilizado. Você pode continuar usando o modelo com o qual trabalhou até que ele seja desativado (confira [Ciclo de vida de modelo e ponto de extremidade](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). Ainda é recomendável mudar para o modelo de base mais recente para obter uma precisão maior.

**P: Posso atualizar meu modelo existente (modelo de empilhamento)?**

**R**: Não é possível atualizar um modelo existente. Como solução, combine o conjunto de dados antigo ao novo conjunto de dados e readapte-os.

O conjunto de dados antigo e o novo devem ser combinados em um único arquivo .zip (para dados acústicos) ou em um arquivo .txt (para dados de linguagem). Quando a adaptação for concluída, o novo modelo atualizado precisará ser reimplantado para a obtenção de um novo ponto de extremidade

**P: Quando uma nova versão de um modelo de base está disponível, minha implantação é atualizada automaticamente?**

**R**: As implantações NÃO serão atualizadas automaticamente.

Se você tiver adaptado e implantado um modelo, essa implantação permanecerá como está. Você pode encerrar o modelo implantado, readaptar usando a versão mais recente do modelo base e reimplantar para obter maior precisão.

Os modelos de base e os modelos personalizados serão desativados após algum tempo (confira [Ciclo de vida de modelo e ponto de extremidade](./how-to-custom-speech-model-and-endpoint-lifecycle.md)).

**Pergunta: Posso baixar meu modelo e executá-lo localmente?**

**R**: Você pode executar um modelo personalizado localmente em um [contêiner do Docker](speech-container-howto.md?tabs=cstt).

**P: Posso copiar ou mover meus conjuntos de valores, modelos e implantações para outra região ou assinatura?**

**R**: Você pode usar a [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) para copiar um modelo personalizado para outra região ou assinatura. Não é possível copiar conjuntos de valores ou implantações. Você pode importar um conjunto de dados novamente em outra assinatura e criar pontos de extremidade usando as cópias do modelo.

**P: Minhas solicitações são registradas?**

**R**: Por padrão, as solicitações não são registradas em log (nem áudio, nem transcrição). Se necessário, você pode selecionar a opção *Registrar o conteúdo desse ponto de extremidade* ao [criar um ponto de extremidade personalizado](how-to-custom-speech-train-model.md#deploy-a-custom-model). Você também pode habilitar o registro em log de áudio no [SDK de Fala](how-to-use-logging.md) com base em cada solicitação sem criar um ponto de extremidade personalizado. Em ambos os casos, os resultados de áudio e de reconhecimento de solicitações serão armazenados no armazenamento seguro. Para assinaturas que usam o armazenamento de propriedade da Microsoft, elas estarão disponíveis por 30 dias.

Você pode exportar os arquivos registrados na página de implantação no Speech Studio se usar um ponto de extremidade personalizado com a opção *Registrar o conteúdo desse ponto de extremidade* habilitada. Se o registro em log de áudio for habilitado por meio do SDK, chame a [API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) para acessar os arquivos.

**Pergunta: são minhas solicitações limitadas?**

**A**: Confira [Cotas e limites de Serviços de Fala](speech-services-quotas-and-limits.md).

**P: Como sou cobrado pelo áudio de dois canais?**

**R**: Se enviar cada canal separadamente (cada canal em no próprio arquivo), você será cobrado pela duração de cada arquivo. Se enviar um arquivo com cada canal multiplexado, você será cobrado pela duração do único arquivo. Para obter detalhes sobre preços, veja a [página de preços dos Serviços Cognitivos do Azure](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Se você tiver mais problemas de privacidade que o impeçam de usar o serviço de voz personalizada, entre em contato com um dos canais de suporte.

## <a name="increasing-concurrency"></a>Aumentando a simultaneidade
Confira [Cotas e limites de Serviços de Fala](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Importando dados

**P: Qual é o limite de tamanho de um conjunto de dados e por que é o limite?**

**A**: O limite é devido à restrição quanto ao tamanho de um arquivo para upload HTTP. Confira [Cotas e limites de Serviços de Fala](speech-services-quotas-and-limits.md) para obter o limite real. Você pode dividir seus dados em vários conjuntos de dados e selecionar todos eles para treinar o modelo.

**P: Posso compactar os arquivos de texto para poder carregar um arquivo de texto maior?**

**R**: Não. No momento são permitidos apenas os arquivos de texto não compactados.

**P: O relatório de dados diz que houve falha de enunciados. Qual é o problema?**

**R**: A falha ao carregar 100% das declarações em um arquivo não é problema. Se a maioria das declarações em um conjunto de dados acústicos ou de linguagem (por exemplo, mais de 95 por cento) é importada com êxito, o conjunto de dados pode ser usado. No entanto, é recomendável que você tente entender por que as expressões falharam e corrigir os problemas. Os problemas mais comuns, como a formatação de erros, são difíceis de resolver.

## <a name="creating-an-acoustic-model"></a>Criar um modelo acústico

**P: Quantos dados acústicos são necessários?**

**R**: É recomendável iniciar com 30 minutos a uma hora de dados acústicos.

**P: Quais dados devo coletar?**

**R**: Colete dados que sejam o mais próximos possível do cenário do aplicativo e do caso de uso. A coleta de dados deve corresponder ao aplicativo de destino e aos usuários em termos de dispositivo ou dispositivos, ambientes e tipos de alto-falantes. Em geral, você deve coletar dados de uma variedade de falantes o mais ampla possível.

**P: Como devo coletar dados acústicos?**

**R**: Você pode criar um aplicativo de coleta de dados independente ou usar um software de gravação de áudio pronto para uso. Você também pode criar uma versão do seu aplicativo que registre os dados de áudio e usá-los.

**P: Preciso transcrever dados de adaptação?**

**R**: Sim. Você pode transcrever você mesmo ou usar um serviço profissional de transcrição. Alguns usuários preferem transcritores profissionais, e outros usam crowdsourcing ou eles mesmos fazem as transcrições.

**P: Quanto tempo levará para treinar um modelo personalizado com os dados de áudio?**

**R**: Treinar um modelo com os dados de áudio pode ser um processo longo. Dependendo da quantidade de dados, pode levar vários dias para criar um modelo personalizado. Se ele não puder ser concluído em uma semana, o serviço poderá anular a operação de treinamento e relatar o modelo como com falha.

Use uma das [regiões](custom-speech-overview.md#set-up-your-azure-account) em que o hardware dedicado está disponível para treinamento. O Serviço de Fala usará até 20 horas de áudio para treinamento nessas regiões. Em outras regiões, ele usará apenas até 8 horas.

Em geral, o serviço processa aproximadamente 10 horas de dados de áudio por dia em regiões com hardware dedicado. Ele pode processar apenas cerca de 1 hora de dados de áudio por dia em outras regiões. Você pode copiar o modelo totalmente treinado para outra região usando a [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription). O treinamento com apenas texto é muito mais rápido e geralmente termina em minutos.

Alguns modelos de base não podem ser personalizados com os dados de áudio. Para eles, o serviço usará apenas o texto da transcrição para treinamento e ignorará os dados de áudio. O treinamento será concluído com muito mais rapidez e os resultados serão os mesmos que o treinamento com apenas texto. Confira [Suporte a idiomas](language-support.md#speech-to-text) para obter uma lista de modelos de base que dão suporte ao treinamento com os dados de áudio.

## <a name="accuracy-testing"></a>Teste de precisão

**P: O que é o WER (Relatório de Erros do Windows) e como é calculado?**

**R**: WER é a métrica de avaliação para o reconhecimento de fala. O WER é contado como o número total de erros, o que inclui inserções, exclusões e substituições, dividido pelo número total de palavras na transcrição de referência. Para obter mais informações, confira [Avaliar a precisão da Fala Personalizada](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy).

**P: Como faço para determinar se os resultados de um teste de precisão são adequados?**

**R**: Os resultados mostram uma comparação entre o modelo de linha de base e o modelo personalizado. Você deve tentar atingir o modelo de referência para que a personalização seja útil.

**P: Como faço para determinar o WER de um modelo de base para que eu possa ver se houve melhoria?**

**R**: Os resultados do teste offline mostram a exatidão da precisão da linha de base do modelo personalizado e a melhoria em relação à linha de base.

## <a name="creating-a-language-model"></a>Criar um modelo de linguagem

**P: Qual quantidade de dados de texto é necessário carregar?**

**R**: Depende de como o vocabulário e as frases usados em seu aplicativo são diferentes dos modelos de linguagem iniciais. Para todas as palavras novas, é útil fornecer o maior número possível de exemplos do uso dessas palavras. Para frases comuns que são usadas em seu aplicativo, incluir frases nos dados da linguagem também é útil porque instrui o sistema a escutar esses termos também. É comum ter pelo menos 100 e, normalmente, várias centenas ou mais de declarações no conjunto de dados de linguagem. Além disso, se alguns tipos de consultas são mais comuns do que outras, você pode inserir várias cópias das consultas comuns no conjunto de dados.

**P: Posso carregar apenas uma lista de palavras?**

**R**: Carregar uma lista de palavras adicionará as palavras ao vocabulário, mas isso não ensinará ao sistema como as palavras normalmente são usadas. Fornecendo expressões completas ou parciais (orações ou frases de itens que os usuários provavelmente dirão), o modelo de linguagem pode aprender as novas palavras e como elas são usadas. O modelo de linguagem personalizado é bom não apenas para incluir novas palavras no sistema, mas também para ajustar a probabilidade de palavras conhecidas para sua aplicação. Fornecer utterances completas ajuda o sistema Saiba mais.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Modelo de locatário (Fala Personalizada com os dados do Microsoft 365)

**P: Quais informações estão incluídas no modelo de locatário e como elas são criadas?**

**R:** Um modelo de locatário é criado usando emails de [grupo público](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e documentos que podem ser vistos por qualquer pessoa em sua organização.

**P: Quais experiências de fala foram aprimoradas pelo modelo de locatário?**

**R:** Quando o modelo de locatário é habilitado, criado e publicado, ele é usado para aprimorar o reconhecimento de qualquer aplicativo empresarial criado usando o Serviço de Fala, isso também passa um token de usuário do Azure AD que indica a associação à empresa.

As experiências de fala internas do Microsoft 365, como Ditado e Legendas do PowerPoint, não são alteradas quando você cria um modelo de locatário para seus aplicativos do Serviço de Fala.

## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)