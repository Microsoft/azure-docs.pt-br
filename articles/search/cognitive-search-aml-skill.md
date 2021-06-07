---
title: Habilidade personalizada do AML no habilidades
titleSuffix: Azure Cognitive Search
description: Estenda os recursos do Azure Pesquisa Cognitiva habilidades com modelos de Azure Machine Learning.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630268"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Habilidade AML em um pipeline de enriquecimento de Pesquisa Cognitiva do Azure

> [!IMPORTANT] 
> Essa habilidade está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente, o SDK do .NET não é compatível.

A habilidade **AML** permite estender o enriquecimento de ia com um modelo personalizado de [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) (AML). Depois que um modelo AML é [treinado e implantado](../machine-learning/concept-azure-machine-learning-architecture.md#workspace), uma habilidade **AML** integra-o ao enriquecimento de ia.

Como habilidades internas, uma habilidade **AML** tem entradas e saídas. As entradas são enviadas para o serviço AML implantado como um objeto JSON, que gera uma carga JSON como uma resposta junto com um código de status de êxito. Espera-se que a resposta tenha as saídas especificadas por sua habilidade **AML** . Qualquer outra resposta é considerada um erro e nenhum aprimoramento é executado.

> [!NOTE]
> O indexador tentará novamente duas vezes para determinados códigos de status HTTP padrão retornados do serviço AML. Esses códigos de status HTTP são:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Pré-requisitos

* Um [espaço de trabalho AML](../machine-learning/concept-workspace.md)
* Um [destino de computação do AML do serviço kubernetes do Azure](../machine-learning/concept-compute-target.md) neste espaço de trabalho com um [modelo implantado](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * O [destino de computação deve ter o SSL habilitado](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). O Azure Pesquisa Cognitiva permite apenas o acesso a pontos de extremidade **https**
  * Certificados autoassinados não podem ser usados.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Custom. AmlSkill

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas. Quais parâmetros você escolhe usar depende de qual [autenticação o serviço AML exige, se houver](#WhatSkillParametersToUse)

| Nome do parâmetro | Descrição |
|--------------------|-------------|
| `uri` | (Necessário para autenticação [sem autenticação ou chave](#WhatSkillParametersToUse)) O [URI de Pontuação do serviço AML](../machine-learning/how-to-consume-web-service.md) ao qual a carga _JSON_ será enviada. Somente o esquema de URI **https** é permitido. |
| `key` | (Necessário para [autenticação de chave](#WhatSkillParametersToUse)) A [chave para o serviço AML](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Necessário para [autenticação de token](#WhatSkillParametersToUse)). A ID de recurso Azure Resource Manager do serviço AML. Ele deve estar no formato assinaturas/{GUID}/resourceGroups/{nome-do-grupo-de-recursos}/Microsoft. MachineLearningServices/Workspaces/{Workspace-Name}/Services/{service_name}. |
| `region` | (Opcional para [autenticação de token](#WhatSkillParametersToUse)). A [região](https://azure.microsoft.com/global-infrastructure/regions/) em que o serviço AML está implantado. |
| `timeout` | (Opcional) Quando especificado, indica o tempo limite para o cliente http que fez a chamada à API. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Por exemplo, `PT60S` por 60 segundos. Se não for definido, um valor padrão de 30 segundos será escolhido. O tempo limite pode ser definido como um máximo de 230 segundos e um mínimo de 1 segundo. |
| `degreeOfParallelism` | Adicional Quando especificado, indica o número de chamadas que o indexador fará em paralelo ao ponto de extremidade que você forneceu. Você pode diminuir esse valor se o ponto de extremidade estiver falhando em muito alto de uma carga de solicitação ou o gerar se o ponto de extremidade for capaz de aceitar mais solicitações e você quiser um aumento no desempenho do indexador.  Se não estiver definido, será usado um valor padrão de 5. O degreeOfParallelism pode ser definido como um máximo de 10 e um mínimo de 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Quais parâmetros de habilidade usar

Os parâmetros de habilidade de AML necessários dependem de qual autenticação seu serviço AML usa, se houver. Os serviços AML fornecem três opções de autenticação:

* [Autenticação baseada em chave](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). Uma chave estática é fornecida para autenticar solicitações de Pontuação de habilidades AML
  * Usar os parâmetros de _URI_ e _chave_
* [Autenticação baseada em token](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). O serviço AML é [implantado usando a autenticação baseada em token](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). A [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) do serviço de pesquisa cognitiva do Azure recebe a [função leitor](../machine-learning/how-to-assign-roles.md) no espaço de trabalho do serviço AML. Em seguida, a habilidade AML usa a identidade gerenciada do serviço de Pesquisa Cognitiva do Azure para se autenticar no serviço AML, sem a necessidade de chaves estáticas.
  * Use o parâmetro _ResourceId_ .
  * Se o serviço de Pesquisa Cognitiva do Azure estiver em uma região diferente do espaço de trabalho AML, use o parâmetro _Region_ para definir a região em que o serviço AML foi implantado
* Sem Autenticação. Nenhuma autenticação é necessária para usar o serviço AML
  * Usar o parâmetro _URI_

## <a name="skill-inputs"></a>Entradas de habilidades

Não há nenhuma entrada "predefinida" para essa habilidade. Você pode escolher um ou mais campos que já estarão disponíveis no momento da execução dessa habilidade como entradas e a carga _JSON_ enviada ao serviço AML terá campos diferentes.

## <a name="skill-outputs"></a>Saídas de habilidades

Não há nenhuma saída "predefinida" para essa habilidade. Dependendo da resposta que seu serviço AML retornará, adicione os campos de saída para que eles possam ser coletados da resposta _JSON_ .

## <a name="sample-definition"></a>Definição de exemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Estrutura JSON de entrada de exemplo

Essa estrutura _JSON_ representa o conteúdo que será enviado ao seu serviço AML. Os campos de nível superior da estrutura corresponderão aos "nomes" especificados na `inputs` seção da definição de habilidade. O valor desses campos será da `source` desses campos (que poderia ser de um campo no documento ou potencialmente de outra habilidade)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Estrutura JSON de saída de exemplo

A saída corresponde à resposta retornada de seu serviço AML. O serviço AML deve retornar apenas uma carga _JSON_ (verificada observando o `Content-Type` cabeçalho de resposta) e deve ser um objeto em que os campos são aprimoramentos que correspondem aos "nomes" no `output` e cujo valor é considerado o enriquecimento.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Definição de amostra de shaping embutida

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Estrutura JSON de entrada de shaping embutida

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Estrutura JSON de saída de exemplo de shaping embutida

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Casos de erro
Além de seu AML estar indisponível ou de enviar códigos de status não bem-sucedidos, os seguintes são considerados casos errados:

* Se o serviço AML retornar um código de status de êxito, mas a resposta indicar que ele não é `application/json` , a resposta será considerada inválida e nenhum aprimoramento será realizado.
* Se o serviço AML retornar um JSON inválido

Para casos em que o serviço AML não está disponível ou retorna um erro de HTTP, um erro amigável com todos os detalhes disponíveis sobre o erro HTTP será adicionado ao histórico de execução do indexador.

## <a name="see-also"></a>Veja também

+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Solução de problemas do serviço AML](../machine-learning/how-to-troubleshoot-deployment.md)