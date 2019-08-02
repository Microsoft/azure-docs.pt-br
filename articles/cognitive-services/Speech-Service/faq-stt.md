---
title: Perguntas frequentes sobre o serviço de Conversão de Fala em Texto no Azure
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas mais populares sobre o serviço de Fala em Texto.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: a437927f6ad43f4c3a90433faa5b9fa6f964aa5c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839244"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Perguntas frequentes sobre Conversão de Fala em Texto

Se você não encontrar respostas para suas perguntas nas perguntas frequentes, verifique[outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de linha de base e um modelo personalizado de conversão de fala em texto?**

**R**: Um modelo de linha de base foi treinado usando dados de propriedade da Microsoft e já está implantado na nuvem.  Você pode usar um modelo personalizado a fim de adaptar um modelo para se adequar melhor a um ambiente específico que tenha determinado ruído ambiente ou linguagem. Chãos de fábrica, carros, ruas barulhentas exigiriam um modelo acústico adaptado. Tópicos como biologia, física, radiologia, nomes de produtos e acrônimos personalizados exigiriam um modelo de linguagem adaptado.

**P: Por onde começar se eu quiser usar um modelo de linha de base?**

**R**: Primeiro, obtenha uma [chave de assinatura](get-started.md). Se você quiser fazer chamadas REST para os modelos de linha de base pré-empregados, confira as [APIs REST](rest-apis.md). Se você quiser usar WebSockets, [baixe o SDK](speech-sdk.md).

**P: Preciso sempre criar um modelo de fala personalizada?**

**R**: Nº Se o aplicativo usa linguagem genérica diária, você não precisa personalizar um modelo. Se o aplicativo é usado em um ambiente em que há pouco ou nenhum ruído de fundo, você não precisa personalizar um modelo.

Você pode implantar modelos personalizados e de linha de base no portal e, em seguida, executar testes de precisão neles. Você pode usar esse recurso para medir a precisão de um modelo de linha de base em comparação com um modelo personalizado.

**P: Como saberei quando o processamento de meu conjunto de dados ou modelo estará completo?**

**R**: Atualmente, o status do modelo ou do conjunto de dados na tabela é a única maneira de saber. Quando o processamento for concluído, o status será **Bem-sucedido**.

**P: Posso criar mais de um modelo?**

**R**: Não há limite para o número de modelos que você pode ter em sua coleção.

**P: Percebi que cometi um erro. Como faço para cancelar a importação de dados ou modelo de criação em andamento?**

**R**: Atualmente, não é possível reverter um processo de adaptação acústica ou de linguagem. Você pode excluir modelos e dados importados quando estão em um estado terminal.

**P: Qual é a diferença entre o modelo de Pesquisa e Ditado e o modelo de Conversação?**

**R**: Você pode escolher mais de um modelo de linha de base no serviço de Fala. O modelo de Conversa é útil para reconhecimento de fala em um estilo conversacional. Esse modelo é ideal para transcrever chamadas telefônicas. O modelo de Pesquisa e Ditado é ideal para aplicativos ativados por voz. O modelo Universal é um novo modelo que visa abordar ambos os cenários. Atualmente, o modelo Universal está no nível de qualidade ou acima do modelo de Conversação na maioria das localidades.

**P: Posso atualizar meu modelo existente (empilhamento de modelo)?**

**R**: Não é possível atualizar um modelo existente. Como solução, combine o conjunto de dados antigo ao novo conjunto de dados e readapte-os.

O conjunto de dados antigo e o novo devem ser combinados em um único arquivo .zip (para dados acústicos) ou em um arquivo .txt (para dados de linguagem). Quando a adaptação for concluída, o novo modelo atualizado precisará ser reimplantado para a obtenção de um novo ponto de extremidade

**P: Quando uma nova versão de uma linha de base está disponível, minha implantação é atualizada automaticamente?**

**R**: As implantações NÃO serão atualizadas automaticamente.

Se você tiver adaptado e implantado um modelo com a linha de base V1.0, essa implantação permanecerá como está. Os clientes podem encerrar o modelo implantado, adaptar novamente usando a versão mais recente da linha de base e implante novamente.

**P: E se eu precisar de maior simultaneidade para o modelo implantado do que o oferecido no portal?**

**R**: Você pode aumentar o modelo em incrementos de 20 solicitações simultâneas.

Entre em contato com [suporte de fala](mailto:speechsupport@microsoft.com?subject=Request%20for%20higher%20concurrency%20for%20Speech-to-text) se você precisar de uma escala maior.

**P: Posso baixar meu modelo e executá-lo localmente?**

**R**: Os modelos não podem ser baixados e executados localmente.

**P: Minhas solicitações são registradas em log?**

**R**: Ao criar uma implantação, você tem uma opção para desativar o rastreamento. Nesse ponto, o áudio ou as transcrições não serão registrados. Caso contrário, as solicitações normalmente são registradas no Azure no armazenamento seguro.

**P: Minhas solicitações são limitadas?**

**R**: A API REST limita as solicitações a 25 a cada 5 segundos. Detalhes podem ser encontrados em nossas páginas de [Conversão de fala em texto](speech-to-text.md).

**P: Como sou cobrado por dois canais de áudio?**

**R**: Se você enviar cada canal separadamente (cada canal em seu próprio arquivo), você será cobrado pela duração de cada arquivo a. Se você enviar um único arquivo com cada canal multiplexada juntos, em seguida, você será cobrado para a duração de um único arquivo.

> [!IMPORTANT]
> Se você tiver mais problemas de privacidade que o impeçam de usar o serviço de voz personalizada, entre em contato com um dos canais de suporte.

## <a name="importing-data"></a>Importação de dados

**P: Qual é o limite de tamanho de um conjunto de dados e por que esse é o limite?**

**R**: O limite atual de um conjunto de dados é de 2 GB. O limite é devido à restrição quanto ao tamanho de um arquivo para upload HTTP. 

**P: Posso compactar meus arquivos de texto para carregar um arquivo de texto maior?** 

**R**: Nº No momento são permitidos apenas os arquivos de texto não compactados.

**P: O relatório de dados indica que houve enunciados com falha. Qual é o problema?**

**R**: A falha ao carregar 100% dos enunciados em um arquivo não é um problema. Se a maioria das declarações em um conjunto de dados acústicos ou de linguagem (por exemplo, mais de 95 por cento) é importada com êxito, o conjunto de dados pode ser usado. No entanto, é recomendável que você tente entender por que as expressões falharam e corrigir os problemas. Os problemas mais comuns, como a formatação de erros, são difíceis de resolver. 

## <a name="creating-an-acoustic-model"></a>Criar um modelo acústico

**P: Quantos dados acústicos são necessários?**

**R**: Recomendamos iniciar com um intervalo de 30 minutos a uma hora de dados acústicos.

**P: Quais dados devem ser coletados?**

**R**: Colete dados que sejam os mais próximos possíveis do cenário do aplicativo e do caso de uso. A coleta de dados deve corresponder ao aplicativo de destino e aos usuários em termos de dispositivo ou dispositivos, ambientes e tipos de alto-falantes. Em geral, você deve coletar dados de uma variedade de falantes o mais ampla possível. 

**P: Como devo coletar dados acústicos?**

**R**: Você pode criar um aplicativo autônomo de coleta de dados ou usar um software de gravação de áudio pronto para uso. Você também pode criar uma versão do seu aplicativo que registre os dados de áudio e usá-los. 

**P: Preciso transcrever os dados de adaptação?**

**R**:  Sim! Você pode transcrever você mesmo ou usar um serviço profissional de transcrição. Alguns usuários preferem transcritores profissionais, e outros usam crowdsourcing ou eles mesmos fazem as transcrições.

## <a name="accuracy-testing"></a>Teste de precisão

**P: Posso realizar o teste offline do meu modelo acústico personalizado usando um modelo de linguagem personalizado?**

**R**: Sim, basta selecionar o modelo de linguagem personalizado no menu suspenso ao configurar o teste offline.

**P: Posso realizar o teste offline do meu modelo de linguagem personalizado usando um modelo acústico personalizado?**

**R**: Sim, basta selecionar o modelo acústico personalizado no menu suspenso ao configurar o teste offline.

**P: O que é o WER (Relatório de Erros do Windows) e como ele é calculado?**

**R**: O WER é a métrica de avaliação para o reconhecimento de fala. O WER é contado como o número total de erros, o que inclui inserções, exclusões e substituições, dividido pelo número total de palavras na transcrição de referência. Confira mais informações em [Relatório de Erros do Windows](https://en.wikipedia.org/wiki/Word_error_rate).

**P: Como fazer para determinar se os resultados de um teste de precisão são adequados?**

**R**: Os resultados mostram uma comparação entre o modelo de linha de base e o modelo personalizado. Você deve tentar atingir o modelo de referência para que a personalização seja útil.

**P: Como fazer para determinar o WER de um modelo base para ver se houve uma melhoria?** 

**R**: Os resultados do teste offline mostram a precisão da linha de base do modelo personalizado e a melhoria em relação à linha de base.

## <a name="creating-a-language-model"></a>Criar um modelo de linguagem

**P: Qual é a quantidade de dados de texto que preciso carregar?**

**R**: Isso depende da diferença entre as frases e o vocabulário usados no aplicativo e os modelos de linguagem iniciais. Para todas as palavras novas, é útil fornecer o maior número possível de exemplos do uso dessas palavras. Para frases comuns que são usadas em seu aplicativo, incluir frases nos dados da linguagem também é útil porque instrui o sistema a escutar esses termos também. É comum ter pelo menos 100 e, normalmente, várias centenas ou mais de declarações no conjunto de dados de linguagem. Além disso, se alguns tipos de consultas são mais comuns do que outras, você pode inserir várias cópias das consultas comuns no conjunto de dados.

**P: Posso carregar apenas uma lista de palavras?**

**R**: O upload de uma lista de palavras adicionará as palavras ao vocabulário, mas não ensinará ao sistema como as palavras normalmente são usadas. Fornecendo expressões completas ou parciais (orações ou frases de itens que os usuários provavelmente dirão), o modelo de linguagem pode aprender as novas palavras e como elas são usadas. O modelo de linguagem personalizado é bom não apenas para incluir novas palavras no sistema, mas também para ajustar a probabilidade de palavras conhecidas para sua aplicação. Fornecer utterances completas ajuda o sistema Saiba mais. 

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
