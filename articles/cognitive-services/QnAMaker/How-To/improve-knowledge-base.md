---
title: Melhorar a base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: O aprendizado ativo permite melhorar a qualidade da sua base de dados de Conhecimento, sugerindo perguntas alternativas, com base em envios de usuário, para o par de pergunta e resposta. Você examina essas sugestões, adicionando-as às perguntas existentes ou rejeitando-as. Sua base de dados de conhecimento não é alterada automaticamente. Você deve aceitar as sugestões para qualquer alteração tenha efeito. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: f59f281b1bf7fa2851ab7759a0167b5d39ef44c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372641"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Use o aprendizado ativo para melhorar a sua base de dados de Conhecimento

O aprendizado ativo permite melhorar a qualidade da sua base de dados de Conhecimento, sugerindo perguntas alternativas, com base em envios de usuário, para o par de pergunta e resposta. Você examina essas sugestões, adicionando-as às perguntas existentes ou rejeitando-as. 

Sua base de dados de conhecimento não é alterada automaticamente. Você precisa aceitar as sugestões para que qualquer alteração entre em vigor. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.

## <a name="what-is-active-learning"></a>O que é aprendizado ativo?

O QnA Maker aprende novas variações de pergunta com comentários implícitos e explícitos.
 
* Comentários implícitos – o classificador compreende quando uma pergunta do usuário tem várias respostas com pontuações muito parecidas e considera isso como comentários. 
* Comentários explícitos – quando várias respostas com pouca variação nas pontuações são retornadas da base de conhecimento, o aplicativo cliente solicita ao usuário qual pergunta é a pergunta correta. Os comentários explícitos do usuário são enviados para o QnA Maker com a API de treinamento. 

Qualquer um dos métodos fornece ao classificador consultas semelhantes são clusterizadas.

## <a name="how-active-learning-works"></a>Como o aprendizado ativo funciona

O aprendizado ativo é disparado com base nas pontuações das primeiras melhores respostas retornadas pelos QnA Maker para qualquer consulta. Se as diferenças de pontuação residem dentro de um pequeno intervalo, a consulta é considerada uma possível _sugestão_ para cada uma das possíveis respostas. 

Todas as sugestões são clusterizadas por similaridade e as principais sugestões de perguntas alternativas são exibidas com base na frequência das consultas específicas por usuários finais. O aprendizado ativo fornece as melhores sugestões possíveis em casos em que os pontos de extremidade estão obtendo uma quantidade e variedade razoáveis de consultas de uso.

Quando as consultas de 5 ou mais semelhantes estão agrupadas, a cada 30 minutos, QnA Maker sugere as perguntas baseadas no usuário para o designer de dados de conhecimento para aceitar ou rejeitar.

Depois que as perguntas são sugeridas no portal do QnA Maker, você precisa examinar e aceitar ou rejeitar essas sugestões. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Atualizar sua versão para usar o aprendizado ativo

O aprendizado ativo é compatível com a versão de tempo de execução 4.4.0 e superior. Se sua base de dados de conhecimento foi criada em uma versão anterior, [atualize seu tempo de execução](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) para usar esse recurso. 

## <a name="best-practices"></a>Práticas recomendadas

Para as práticas recomendadas ao usar o aprendizado ativo, veja [Práticas recomendadas](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Proximidade da pontuação entre as perguntas da base de dados de conhecimento

Quando a pontuação de uma pergunta tem um alto grau de confiabilidade, tal como 80%, o intervalo de pontuações que são consideradas para aprendizado ativo é amplo, dentro de aproximadamente 10%. Conforme a pontuação de confiança diminui, por exemplo, para 40%, o intervalo de pontuações diminui também, para dentro de aproximadamente 4%. 

O algoritmo para determinar a proximidade não é um cálculo simples. Os intervalos nos exemplos anteriores não devem ser corrigidos, mas devem ser usados como um guia para entender o impacto apenas do algoritmo.

## <a name="turn-on-active-learning"></a>Ativar o aprendizado ativo

O aprendizado ativo está desativado por padrão. Ative-o para ver sugestões de perguntas. 

1. Selecione **publicar** para publicar a base de dados de Conhecimento. Consultas de aprendizado ativo são coletadas do ponto de extremidade de previsão de API GenerateAnswer apenas. As consultas para o painel de teste no portal do Qna Maker não afetam o aprendizado ativo.

1. Para desativar o aprendizado ativo, clique em seu **Nome** e vá para [**Configurações de Serviço**](https://www.qnamaker.ai/UserSettings) no portal do QnA Maker no canto superior direito.  

    ![Ative o aprendizado ativo sugerido alternativas de pergunta da página de configurações de serviço. Selecione seu nome de usuário no menu superior direito e selecione configurações de serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Localize o serviço QnA Maker e, em seguida, ative o **aprendizado ativo**. 

    [![Na página de configurações de serviço, ativar/desativar o recurso de aprendizado ativo. Se você não conseguir ativar/desativar o recurso, você precisa atualizar seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Uma vez que o **aprendizado ativo** for habilitado, o conhecimento sugere novas perguntas em intervalos regulares, com base em perguntas enviadas por usuários. Você pode desabilitar o **aprendizado ativo** desativando a configuração novamente.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Adicionar a sugestão de aprendizado ativo à base de dados de conhecimento

1. Para ver as sugestões de perguntas, na página **Editar** da base de dados de conhecimento, selecione **Mostrar sugestões**. 

    [![Na seção de edição do portal, selecione Mostrar sugestões para ver novas alternativas de pergunta do aprendizado ativo.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrar a base de dados de conhecimento com pares de pergunta e resposta para mostrar apenas sugestões, selecionando **filtrar por sugestões**.

    [![Use o filtro por alternância de sugestões para exibir apenas as alternativas do aprendizado ativo pergunta sugeridas.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Cada seção de pergunta com sugestões mostra as novas perguntas com uma marca de seleção, `✔`, para aceitar a pergunta, ou então um `x` para rejeitar as sugestões. Selecione a marca de seleção para adicionar a pergunta. 

    [![Selecione ou rejeitar alternativas de pergunta sugerida do aprendizado ativo, selecionando a marca de seleção verde ou marca de exclusão vermelho.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Você pode adicionar ou excluir _todas as sugestões_ selecionando **Adicionar todas** ou **Rejeitar todas**.

1. Selecione **Salvar e Treinar** para salvar as alterações à base de dados de conhecimento.

1. Selecione **publicar** para permitir que as alterações estejam disponíveis da API GenerateAnswer.

    Quando as consultas de 5 ou mais semelhantes estão agrupadas, a cada 30 minutos, QnA Maker sugere as perguntas baseadas no usuário para o designer de dados de conhecimento para aceitar ou rejeitar.

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Determinar a melhor escolha quando várias perguntas têm pontuações semelhantes

Quando a pontuação de uma pergunta for muito próxima à de outras perguntas, o desenvolvedor do aplicativo cliente poderá escolher solicitar esclarecimentos.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Usar a propriedade superior na solicitação GenerateAnswer

Ao enviar uma pergunta para o QnA Maker a fim de obter uma resposta, parte do corpo JSON permite retornar mais de uma das primeiras respostas:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Quando o aplicativo cliente (por exemplo, um chat bot) recebe a resposta, as três primeiras perguntas são retornadas:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Acompanhamento do aplicativo cliente quando perguntas têm pontuações semelhantes

O aplicativo cliente exibe todas as perguntas com uma opção para o usuário selecionar a pergunta que melhor representa suas intenções. 

Depois que o usuário seleciona uma das perguntas existentes, o aplicativo cliente envia a escolha do usuário como comentários usando a API de treinamento do QnA Maker. Esses comentários conclui a ativa loop de comentários de aprendizado. 

Use o [exemplo de Bot do Azure](https://aka.ms/activelearningsamplebot) para ver o aprendizado ativo em um cenário de ponta a ponta.

## <a name="train-api"></a>API de treinamento

Comentários de aprendizado ativo é enviado para o QnA Maker com a solicitação de POSTAGEM da API de treinamento. A assinatura de API é:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Propriedade de solicitação HTTP|NOME|Type|Finalidade|
|--|--|--|--|
|Parâmetro de rota de URL|ID da base de dados de Conhecimento|string|o GUID da base de dados de conhecimento.|
|Host subdomain|Nome do recurso QnAMaker|string|O nome do host para o QnA Maker em sua assinatura do Azure. Isso está disponível na página de configurações depois de publicar a base de Conhecimento. |
|Cabeçalho|Tipo de conteúdo|string|o tipo de mídia do corpo enviado para a API. Valor padrão é: `application/json`|
|Cabeçalho|Autorização|string|sua chave de ponto de extremidade (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo do POST|Objeto JSON|JSON|Os comentários de treinamento|

O corpo JSON tem várias configurações:

|Propriedade de corpo JSON|Type|Finalidade|
|--|--|--|--|
|`feedbackRecords`|matriz|Lista de comentários.|
|`userId`|string|A ID de usuário da pessoa que está aceitando as sugestões de perguntas. O formato de ID de usuário cabe a você. Por exemplo, um endereço de email pode ser uma ID de usuário válido em sua arquitetura. Opcional.|
|`userQuestion`|string|Texto exato da pergunta. Obrigatório.|
|`qnaID`|número|ID da pergunta, encontrada na [GenerateAnswer resposta](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Um exemplo de corpo JSON é semelhante a:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Uma resposta bem-sucedida retorna um status de 204 e nenhum corpo de resposta JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aprendizado ativo é salvo na base de Conhecimento exportado

Quando seu aplicativo tenha habilitado de aprendizado ativo, e você exporta o aplicativo, o `SuggestedQuestions` coluna no arquivo tsv retém os dados de aprendizado ativo. 

O `SuggestedQuestions` coluna é um objeto JSON de informações do implícita (`autosuggested`) e explícitas (`usersuggested`) comentários. Um exemplo desse objeto JSON para uma única pergunta enviado pelo usuário de `help` é:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Quando você reimportar esse aplicativo, o aprendizado ativo continua a coletar informações e recomendável sugestões para sua base de dados de Conhecimento. 

## <a name="next-steps"></a>Próximas etapas
 
> [!div class="nextstepaction"]
> [Usar metadados com a API de GenerateAnswer](metadata-generateanswer-usage.md)
