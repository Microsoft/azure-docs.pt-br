---
title: Cotas e limites de serviços de fala
titleSuffix: Azure Cognitive Services
description: Referência rápida, descrição detalhada e práticas recomendadas sobre cotas e limites de serviços de fala cognitiva do Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: alexeyo
ms.openlocfilehash: 7fe2942250e165606175392d7749becda0cff23b
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645111"
---
# <a name="speech-services-quotas-and-limits"></a>Cotas e limites de serviços de fala

Este artigo contém uma referência rápida e a **Descrição detalhada** das cotas e dos limites dos serviços de fala cognitiva do Azure para todos os [tipos de preço](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Ele também contém algumas práticas recomendadas para evitar a limitação de solicitações. 

## <a name="quotas-and-limits-quick-reference"></a>Referência rápida de cotas e limites
Ir para [cotas e limites de conversão de texto em fala](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Cotas e limites de conversão de fala em texto por recurso de fala
Nas tabelas abaixo, os parâmetros sem a linha "ajustável" **não** são ajustáveis para todos os tipos de preço.

#### <a name="online-transcription"></a>Transcrição online
Para o uso com o [SDK de fala](speech-sdk.md) e/ou a [API REST de fala em texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Quota | Gratuito (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Limite de solicitação simultânea-modelo base** | 1 | 100 (valor padrão) |
| Ajustá | Não<sup>2</sup> | Sim<sup>2</sup> |
| **Limite de solicitação simultânea-modelo personalizado** | 1 | 20 (valor padrão) |
| Ajustá | Não<sup>2</sup> | Sim<sup>2</sup> |

#### <a name="batch-transcription"></a>Transcrição em lote
| Quota | Gratuito (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| Limite da [API REST de conversão de fala em texto v 2.0 e v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | A transcrição do lote não está disponível para F0 | 300 solicitações por minuto |
| Tamanho máximo do arquivo de entrada de áudio | N/D | 1 GB |
| Tamanho máximo de blob de entrada (pode conter mais de um arquivo, por exemplo, em um arquivo zip; certifique-se de anotar o limite de tamanho de arquivo acima) | N/D | 2,5 GB |
| Tamanho máximo do contêiner de BLOB | N/D | 5 GB |
| Número máximo de BLOBs por contêiner | N/D | 10000 |
| Número máximo de arquivos por solicitação de transcrição (ao usar várias URLs de conteúdo como entrada) | N/D | 1000  |
| Número máximo de trabalhos em execução simultaneamente | N/D | 2000  |

#### <a name="model-customization"></a>Personalização do modelo
| Quota | Gratuito (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| Limite da API REST | 300 solicitações por minuto | 300 solicitações por minuto |
| Número máximo de conjuntos de valores de fala | 2 | 500 |
| Tamanho máximo do arquivo do conjunto de dados acústico | 2 GB | 2 GB |
| Tamanho máximo do arquivo do conjunto de dados de linguagem para importação | 200 MB | 1.5 GB |
| Tamanho máximo do arquivo do conjunto de dados de pronúncia para importação | 1 KB | 1 MB |
| Tamanho de texto máximo ao usar o `text` parâmetro em criar solicitação de API de [modelo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) | 200 KB | 500 KB |

<sup>1</sup> para tipo de preço **gratuito (F0)** , consulte também concessões mensais na [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>2</sup> consulte [explicações adicionais](#detailed-description-quota-adjustment-and-best-practices), [práticas recomendadas](#general-best-practices-to-mitigate-throttling-during-autoscaling)e [instruções de ajuste](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Cotas de conversão de texto em fala e limites por recurso de fala
Na tabela abaixo, os parâmetros sem a linha "ajustável" **não** são ajustáveis para todos os tipos de preço.

| Quota | Gratuito (F0)<sup>3</sup> | Standard (S0) |
|--|--|--|
| **Número máximo de transações por segundo (TPS) para vozes padrão e neural** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **Limite de solicitações simultâneas para voz personalizada** |  |  |
| Valor padrão | 10 | 10 |
| Ajustá | Nº<sup>5</sup> | Sim<sup>5</sup> |
| **Cotas específicas de HTTP** |  |
| Tamanho máximo de áudio produzido por solicitação | 10 min | 10 min |
| Número máximo de marcas distintas `<voice>` em SSML | 50 | 50 |
| **Cotas específicas do WebSocket** |  |  |
|Tamanho máximo de áudio produzido por rodada | 10 min | 10 min |
|Tamanho máximo da mensagem SSML por rodada |64 KB |64 KB |
| **Limite da API REST** | 20 solicitações por minuto | 300 solicitações por minuto |


<sup>3</sup> para tipo de preço **gratuito (F0)** , consulte também concessões mensais na [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>4</sup> consulte [explicações](#detailed-description-quota-adjustment-and-best-practices) e [melhores práticas](#general-best-practices-to-mitigate-throttling-during-autoscaling)adicionais.<br/>
<sup>5</sup> consulte [explicações adicionais](#detailed-description-quota-adjustment-and-best-practices), [práticas recomendadas](#general-best-practices-to-mitigate-throttling-during-autoscaling)e [instruções de ajuste](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Descrição detalhada, ajuste de cota e práticas recomendadas
Antes de solicitar um aumento de cota (quando aplicável), verifique se ele é necessário. O serviço de fala está usando tecnologias de dimensionamento automático para trazer os recursos computacionais necessários no modo "sob demanda" e ao mesmo tempo para manter os custos baixos do cliente sem manter uma quantidade excessiva de capacidade de hardware. Sempre que seu aplicativo recebe um código de resposta 429 ("muitas solicitações") enquanto sua carga de trabalho está dentro dos limites definidos (consulte [cotas e limites referência rápida](#quotas-and-limits-quick-reference)), a explicação mais provável é que o serviço está aumentando até sua demanda e ainda não alcançou a escala necessária, portanto, não tem imediatamente recursos suficientes para atender à solicitação. Esse estado é geralmente transitório e não deve durar o tempo.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Práticas recomendadas gerais para mitigar a limitação durante o dimensionamento automático
Para minimizar problemas relacionados à limitação (código de resposta 429), é recomendável usar as seguintes técnicas:
- Implemente a lógica de repetição no seu aplicativo
- Evite alterações nítidas na carga de trabalho. Aumentar a carga de trabalho gradualmente <br/>
*Exemplo:* Seu aplicativo está usando conversão de texto em fala e sua carga de trabalho atual é 5 TPS (transações por segundo). A próxima segunda você aumenta a carga para 20 TPS (que é quatro vezes mais). O serviço inicia imediatamente o dimensionamento para atender à nova carga, mas é provável que não seja possível fazê-lo dentro de um segundo; portanto, algumas das solicitações receberão o código de resposta 429.   
- Testar padrões de aumento de carga diferentes
  - Consulte o [exemplo de conversão de fala em texto](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Crie recursos de fala adicionais na mesma região ou em regiões diferentes e distribua a carga de trabalho entre eles usando a técnica "Round Robin". Isso é especialmente importante para o parâmetro **TPS (transações por segundo) de conversão de texto em fala** , que é definido como 200 por recurso de fala e não pode ser ajustado  

As seções a seguir descrevem casos específicos de ajuste de cotas.<br/>
Ir para [conversão de texto em fala. Aumentando o limite de solicitações simultâneas de transcrição para voz personalizada](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Conversão de fala em texto: aumentando o limite de solicitações simultâneas de transcrição online
Por padrão, o número de solicitações simultâneas é limitado a 20 por recurso de fala (modelo base) ou por ponto de extremidade personalizado (modelo personalizado). Para o tipo de preço Standard, esse valor pode ser aumentado. Antes de enviar a solicitação, verifique se você está familiarizado com o material nesta [seção](#detailed-description-quota-adjustment-and-best-practices) e conheça essas [práticas recomendadas](#general-best-practices-to-mitigate-throttling-during-autoscaling).

O aumento do limite de solicitações simultâneas **não** afeta diretamente os custos. Os serviços de fala usam o modelo "pagar apenas pelo que usar". O limite define o quão alto o serviço pode ser dimensionado antes de começar a limitar suas solicitações.

Os limites de solicitações simultâneas para modelos **básicos** e **personalizados** precisam ser ajustados **separadamente**.

O valor existente do parâmetro de limite de solicitação simultânea **não** é visível por meio de portal do Azure, Command-Line ferramentas ou solicitações de API. Para verificar o valor existente, crie uma solicitação de suporte do Azure.

>[!NOTE]
>Os [contêineres de fala](speech-container-howto.md) não exigem aumentos do limite de solicitações simultâneas, pois os contêineres são restritos apenas pelas CPUs do hardware em que estão hospedados. No entanto, os contêineres de fala têm suas próprias limitações de capacidade que devem ser levadas em conta. Veja a pergunta *"você pode ajudar com planejamento de capacidade e estimativa de custo de contêineres de fala em texto local?"* nas [perguntas frequentes sobre os contêineres de fala](./speech-container-howto.md).

#### <a name="have-the-required-information-ready"></a>Ter as informações necessárias prontas:
- Para o **modelo base**:
  - ID do recurso de fala
  - Region
- Para o **modelo personalizado**: 
  - Region
  - ID do ponto de extremidade personalizado

- **Como obter informações (modelo base)**:  
  - Ir para [portal do Azure](https://portal.azure.com/)
  - Selecione o recurso de fala para o qual você deseja aumentar o limite de solicitações de simultaneidade
  - Selecionar *Propriedades* (grupo de *Gerenciamento de recursos* ) 
  - Copie e salve os valores dos seguintes campos:
    - **ID de Recurso**
    - **Local** (sua região de ponto de extremidade)

- **Como obter informações (modelo personalizado)**:
  - Ir para o portal do [Speech Studio](https://speech.microsoft.com/)
  - Entrar, se necessário
  - Ir para Fala Personalizada
  - Selecione seu projeto
  - Ir para *implantação*
  - Selecionar o ponto de extremidade necessário
  - Copie e salve os valores dos seguintes campos:
    - **Região de serviço** (sua região de ponto de extremidade)
    - **ID do ponto de extremidade**
  
#### <a name="create-and-submit-support-request"></a>Criar e enviar solicitação de suporte
Inicie o aumento do limite de solicitações simultâneas para seu recurso ou, se necessário, verifique o limite de hoje enviando a solicitação de suporte:

- Verifique se você tem as [informações necessárias](#have-the-required-information-ready)
- Ir para [portal do Azure](https://portal.azure.com/)
- Selecione o recurso de fala para o qual você deseja aumentar (ou para verificar) o limite de solicitação de simultaneidade
- Selecionar *nova solicitação de suporte* (*suporte +* grupo de solução de problemas) 
- Uma nova janela será exibida com as informações preenchidas automaticamente sobre sua assinatura do Azure e recursos do Azure
- Insira um *Resumo* (como "aumentar limite de solicitação de simultaneidade STT")
- Em *tipo de problema* , selecione "problemas de cota ou assinatura"
- Em *subtipo de problema* exibido, selecione:
  - "Aumento de cotas ou de solicitações simultâneas"-para uma solicitação de aumento
  - "Cota ou validação de uso" para verificar o limite existente
- Clique em *Avançar: soluções*
- Prosseguir com a criação da solicitação
- Na guia *detalhes* , insira no campo *Descrição* :
  - uma observação: a solicitação é sobre a cota de **fala para texto**
  - Modelo **básico** ou **personalizado**
  - Informações de recursos do Azure [coletadas antes](#have-the-required-information-ready) 
  - Conclua a inserção das informações necessárias e clique no botão *criar* na guia *revisar + criar*
  - Observe o número da solicitação de suporte em notificações de portal do Azure. Você será contatado em breve para processamento adicional

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Conversão de fala em texto: exemplo de uma prática recomendada de padrão de carga de trabalho
Este exemplo apresenta a abordagem que recomendamos a seguir para atenuar a limitação de solicitação possível devido ao [dimensionamento automático estar em andamento](#detailed-description-quota-adjustment-and-best-practices). Não é uma "receita exata", mas meramente um modelo que convidamos seguir e ajustamos conforme necessário.

Vamos supor que um recurso de fala tenha o limite de solicitações simultâneas definido como 300. Inicie a carga de trabalho a partir de 20 conexões simultâneas e aumente a carga em 20 conexões simultâneas a cada 1,5 a 2 minutos. Controle as respostas de serviço e implemente a lógica que retorna (reduz a carga) se você obtiver muitos códigos de resposta 429. Em seguida, tente novamente no padrão de 1-2-4-4 minutos. (Isso tentará novamente o aumento de carga em 1 minuto, se ainda não funcionar, em 2 min e assim por diante)

Em geral, é altamente recomendável testar a carga de trabalho e os padrões de carga de trabalho antes de passar para a produção.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Conversão de texto em fala: aumento do limite de solicitações simultâneas de transcrição para voz personalizada
Por padrão, o número de solicitações simultâneas para um ponto de extremidade de voz personalizado é limitado a 10. Para o tipo de preço Standard, esse valor pode ser aumentado. Antes de enviar a solicitação, verifique se você está familiarizado com o material nesta [seção](#detailed-description-quota-adjustment-and-best-practices) e conheça essas [práticas recomendadas](#general-best-practices-to-mitigate-throttling-during-autoscaling).

O aumento do limite de solicitações simultâneas **não** afeta diretamente os custos. Os serviços de fala usam o modelo "pagar apenas pelo que usar". O limite define o quão alto o serviço pode ser dimensionado antes de começar a limitar suas solicitações.

O valor existente do parâmetro de limite de solicitação simultânea **não** é visível por meio de portal do Azure, Command-Line ferramentas ou solicitações de API. Para verificar o valor existente, crie uma solicitação de suporte do Azure.

>[!NOTE]
>Os [contêineres de fala](speech-container-howto.md) não exigem aumentos do limite de solicitações simultâneas, pois os contêineres são restritos apenas pelas CPUs do hardware em que estão hospedados.

#### <a name="prepare-the-required-information"></a>Prepare as informações necessárias:
Para criar uma solicitação de aumento, você precisará fornecer sua região de implantação e a ID do ponto de extremidade personalizado. Para obtê-lo, execute as seguintes ações: 

- Ir para o portal do [Speech Studio](https://speech.microsoft.com/)
- Entrar, se necessário
- Ir para a *voz personalizada*
- Selecione seu projeto
- Ir para *implantação*
- Selecionar o ponto de extremidade necessário
- Copie e salve os valores dos seguintes campos:
    - **Região de serviço** (sua região de ponto de extremidade)
    - **ID do ponto de extremidade**
  
#### <a name="create-and-submit-support-request"></a>Criar e enviar solicitação de suporte
Inicie o aumento do limite de solicitações simultâneas para seu recurso ou, se necessário, verifique o limite de hoje enviando a solicitação de suporte:

- Verifique se você tem as [informações necessárias](#prepare-the-required-information)
- Ir para [portal do Azure](https://portal.azure.com/)
- Selecione o recurso de fala para o qual você deseja aumentar (ou para verificar) o limite de solicitação de simultaneidade
- Selecionar *nova solicitação de suporte* (*suporte +* grupo de solução de problemas) 
- Uma nova janela será exibida com as informações preenchidas automaticamente sobre sua assinatura do Azure e recursos do Azure
- Insira um *Resumo* (como "aumentar o limite de solicitação de simultaneidade do ponto de extremidade personalizado TTS")
- Em *tipo de problema* , selecione "problemas de cota ou assinatura"
- Em *subtipo de problema* exibido, selecione:
  - "Aumento de cotas ou de solicitações simultâneas"-para uma solicitação de aumento
  - "Cota ou validação de uso" para verificar o limite existente
- Clique em *Avançar: soluções*
- Prosseguir com a criação da solicitação
- Na guia *detalhes* , insira no campo *Descrição* :
  - uma observação: a solicitação é sobre a cota de **conversão de texto em fala**
  - Informações de recursos do Azure [coletadas antes](#prepare-the-required-information) 
  - Conclua a inserção das informações necessárias e clique no botão *criar* na guia *revisar + criar*
  - Observe o número da solicitação de suporte em notificações de portal do Azure. Você será contatado em breve para processamento adicional