---
title: Ciclo de vida do modelo e do ponto de extremidade do Serviço de Fala Personalizada – Fala
titleSuffix: Azure Cognitive Services
description: A Fala Personalizada fornece modelos de base para adaptação e permite criar modelos personalizados com base em seus dados. Este artigo descreve as linhas do tempo para modelos e pontos de extremidade que usam esses modelos.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385018"
---
# <a name="model-and-endpoint-lifecycle"></a>Ciclo de vida de modelo e de ponto de extremidade

Nossa fala padrão (não personalizada) é criada com base nos modelos de IA que chamamos de modelos de base. Na maioria dos casos, treinamos um modelo de base diferente para cada idioma falado para o qual damos suporte.  Atualizamos o Serviço de Fala com novos modelos de base a cada poucos meses para aprimorar a precisão e a qualidade.  
Com a Fala Personalizada, os modelos personalizados são criados adaptando um modelo de base escolhido com os dados de seu cenário de cliente específico. Depois de criar um modelo personalizado, esse modelo não será atualizado ou alterado, mesmo que o modelo de base correspondente do qual ele foi adaptado seja atualizado no Serviço de Fala padrão.  
Essa política permite que você continue usando um modelo personalizado específico por um longo período depois de ter um modelo personalizado que atenda às suas necessidades.  Mas é recomendável recriar periodicamente seu modelo personalizado para que você possa se adaptar do modelo de base mais recente para aproveitar a precisão e a qualidade aprimoradas.

Outros termos principais relacionados ao ciclo de vida do modelo incluem:

* **Adaptação**: pegar um modelo de base e personalizá-lo para seu domínio/cenário usando dados de texto e/ou dados de áudio.
* **Decodificação**: usar um modelo e executar o reconhecimento de fala (decodificar o áudio em texto).
* **Ponto de extremidade**: uma implantação específica do usuário de um modelo de base ou de um modelo personalizado que é acessível *somente* para um determinado usuário.

### <a name="expiration-timeline"></a>Linha do tempo de término

Conforme novos modelos e novas funcionalidades se tornam disponíveis e os modelos mais antigos e menos precisos são desativados, confira as seguintes linhas do tempo para o término de modelo e ponto de extremidade:

**Modelos de base** 

* Adaptação: disponível por um ano. Depois que o modelo for importado, ele estará disponível por um ano para criar modelos personalizados. Após um ano, outros modelos personalizados devem ser criados usando uma versão mais recente do modelo de base.  
* Decodificação: disponível por dois anos após a importação. Portanto, você pode criar um ponto de extremidade e usar a transcrição do lote por dois anos com esse modelo. 
* Pontos de extremidade: disponíveis na mesma linha do tempo que a decodificação.

**Modelos personalizados**

* Decodificação: disponível por dois anos após o modelo ser criado. Portanto, você pode usar o modelo personalizado por dois anos (lote/tempo real/teste) após sua criação. Depois de dois anos, *você deve treinar novamente seu modelo* porque o modelo de base normalmente terá sido preterido para adaptação.  
* Pontos de extremidade: disponíveis na mesma linha do tempo que a decodificação.

Quando um modelo de base ou modelo personalizado expira, ele sempre retorna para a *versão mais recente do modelo de base*. Portanto, sua implementação nunca será interrompida, mas poderá se tornar menos precisa para *seus dados específicos* se os modelos personalizados atingirem o término. Você pode ver o término de um modelo nos seguintes locais na área de Fala Personalizada do Speech Studio:

* Resumo do treinamento de modelo
* Detalhe do treinamento de modelo
* Resumo de implantação
* Detalhe da implantação

Aqui está um exemplo do resumo de treinamento do modelo:

![Resumo de treinamento do modelo](media/custom-speech/custom-speech-model-training-with-expiry.png) E da página de detalhes de treinamento do modelo:

![Detalhe do treinamento de modelo](media/custom-speech/custom-speech-model-details-with-expiry.png)

Você também pode verificar as datas de término por meio das APIs de fala personalizadas [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) e [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) na propriedade `deprecationDates` na resposta JSON.

Aqui está um exemplo dos dados de término da chamada à API GetModel. O **DEPRECATIONDATES** mostra quando o modelo expira: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Observe que você pode atualizar o modelo em um ponto de extremidade de fala personalizado sem tempo de inatividade alterando o modelo usado pelo ponto de extremidade na seção de implantação do Speech Studio ou por meio da API de Fala personalizada.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>O que acontece quando os modelos expiram e como atualizá-los
O que acontece quando um modelo expira e como atualizar o modelo depende de como ele está sendo usado.
### <a name="batch-transcription"></a>Transcrição de lote
Se um modelo usado com a [transcrição do lote](batch-transcription.md) expirar, as solicitações de transcrição falharão com um erro 4xx. Para evitar isso, atualize o parâmetro `model` no JSON enviado no corpo da solicitação de **Criar Transcrição** para apontar para um modelo de base mais recente ou para um modelo personalizado mais recente. Você também pode remover a entrada `model` do JSON para sempre usar o modelo de base mais recente.
### <a name="custom-speech-endpoint"></a>Ponto de extremidade de fala personalizada
Se um modelo usado por um [ponto de extremidade de fala personalizado](how-to-custom-speech-train-model.md) expirar, o serviço retornará automaticamente para usar o modelo de base mais recente para o idioma que você está usando. Você pode selecionar **Implantação** no menu **Fala Personalizada** na parte superior da página e clicar no nome do ponto de extremidade para ver seus detalhes. Na parte superior da página de detalhes, você verá um botão **Atualizar Modelo** que permite atualizar diretamente o modelo usado por esse ponto de extremidade sem tempo de inatividade. Você também pode fazer essa alteração programaticamente usando a API REST de [**Atualizar Modelo**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel).

## <a name="next-steps"></a>Próximas etapas

* [Treinar e implantar um modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
* [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)