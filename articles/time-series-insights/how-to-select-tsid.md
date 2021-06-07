---
title: Práticas recomendadas para escolher uma ID de série temporal-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas ao escolher uma ID de série temporal no Azure Time Series Insights Gen2.
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 8aefa6efa5f10dae8a28e7126b91b7b5f6ac77a6
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950732"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Práticas recomendadas para escolher uma ID do Time Series

Este artigo resume a importância da ID da série temporal para seu ambiente de Azure Time Series Insights Gen2 e as práticas recomendadas para escolher uma.

## <a name="choose-a-time-series-id"></a>Escolha uma ID do Time Series

A seleção de uma ID de série temporal apropriada é crítica. Escolher uma ID do Time Series é como escolher uma chave de partição para um banco de dados. É necessário quando você cria um ambiente de Azure Time Series Insights Gen2.

Assista ao tutorial de provisionamento de ambiente para obter uma explicação detalhada da ID da série temporal. Você exibirá dois exemplos diferentes de conteúdo de telemetria JSON e a seleção de ID de série temporal correta para cada um.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> As IDs de série temporal são:
>
> * Uma propriedade que *diferencia maiúsculas de minúsculas* : letras e maiúsculas de caracteres são usadas em pesquisas, comparações, atualizações e ao particionar.
> * Uma propriedade *imutável* : uma vez criada, ela não pode ser alterada.

> [!TIP]
> Se a origem do evento for um hub IoT, a ID da série temporal provavelmente será ***iothub-Connection-Device-ID***.

As principais práticas recomendadas a serem seguidas incluem:

* Escolha uma chave de partição com muitos valores distintos (por exemplo, centenas ou milhares). Em muitos casos, essa pode ser a ID do dispositivo, ID do sensor ou ID de marca em seu JSON.
* A ID do Time Series deve ser exclusiva no nível do nó folha do seu [Modelo do Time Series](./concepts-model-overview.md).
* O limite de caracteres para a cadeia de caracteres do nome da propriedade da ID da série temporal é 128. Para o valor da propriedade da ID da série temporal, o limite de caracteres é 1.024.
* Se um valor de propriedade exclusivo para a ID da série temporal estiver ausente, ele será tratado como um valor nulo e seguirá a mesma regra da restrição de exclusividade.
* Se a ID da série temporal estiver aninhada em um objeto JSON complexo, certifique-se de seguir as [regras de mesclagem](./concepts-json-flattening-escaping-rules.md) de entrada ao fornecer o nome da propriedade. Confira o exemplo [B](concepts-json-flattening-escaping-rules.md#example-b).
* Você também pode selecionar até *três* Propriedades de chave como sua ID de série temporal. Sua combinação será uma chave composta que representa a ID da série temporal.  
  > [!NOTE]
  > Suas propriedades de três chaves devem ser cadeias de caracteres.
  > Você precisaria consultar essa chave composta em vez de uma propriedade de cada vez.

## <a name="select-more-than-one-key-property"></a>Selecionar mais de uma propriedade de chave

Os cenários a seguir descrevem a seleção de mais de uma propriedade de chave como sua ID de série temporal.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exemplo 1: ID de série temporal com uma chave exclusiva

* Você tem frotas de ativos herdados. Cada um tem uma chave exclusiva.
* Uma frota é identificada exclusivamente pela propriedade **DeviceID**. Para outra frota, a propriedade Unique é **ObjectID**. Nenhuma frota contém a propriedade exclusiva da outra frota. Neste exemplo, você selecionaria duas chaves, **DeviceID** e **ObjectID**, como chaves exclusivas.
* Aceitamos valores nulos e a falta de uma presença de propriedade no conteúdo do evento conta como um valor nulo. Essa também é a maneira apropriada de lidar com o envio de dados para duas origens de evento, em que os dados em cada origem de evento têm uma ID de série temporal exclusiva.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exemplo 2: ID de série temporal com uma chave composta

* Você precisa que várias propriedades sejam exclusivas dentro do mesma frota de ativos.
* Você é um fabricante de prédios inteligentes e implanta sensores em todas as salas. Em cada sala, você normalmente tem os mesmos valores para **sensorid**. Os exemplos são **sensor1**, **sensor2** e **sensor3**.
* Seu edifício tem sobreposição de piso e números de sala entre sites na propriedade **flrRm**. Esses números têm valores como **1a**, **2B** e **3a**.
* Você tem uma propriedade, **local**, que contém valores como **Redmond**, **Barcelona** e **Tokyo**. Para criar a exclusividade, você designa as três propriedades a seguir como suas chaves de ID de série temporal: **sensorid**, **flrRm** e **local**.

Exemplo de evento bruto:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Na portal do Azure, você pode inserir a chave composta da seguinte maneira:

[![Configure a ID da Série Temporal para o ambiente.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > Em portal do Azure, não insira nomes separados por vírgulas de propriedades em um caixa emails, caso contrário, ele será tratado como um único nome de propriedade que contém vírgulas.
  > Insira cada nome de propriedade em seu próprio caixa emails.

## <a name="next-steps"></a>Próximas etapas

* Leia as [regras de saída e mesclagem JSON](./concepts-json-flattening-escaping-rules.md) para entender como os eventos serão armazenados.

* Planeje seu [ambiente de Azure Time Series insights Gen2](./how-to-plan-your-environment.md).