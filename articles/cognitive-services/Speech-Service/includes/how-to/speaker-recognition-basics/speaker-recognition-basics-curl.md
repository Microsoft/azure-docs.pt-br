---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015453"
---
Neste início rápido, você aprenderá os padrões de design básicos para Reconhecimento do Locutor usando o SDK de Fala, incluindo:

* Verificação dependente do texto e independente do texto
* Identificação do locutor para identificar uma amostra de voz em meio a um grupo de vozes
* Exclusão de perfis de voz

Para obter uma visão de alto nível sobre os conceitos do Reconhecimento de Fala, confira o artigo de [visão geral](../../../speaker-recognition-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Atualmente, o Reconhecimento do Locutor é compatível *somente* com os recursos de Fala do Azure criados na região `westus`.

## <a name="text-dependent-verification"></a>Verificação dependente do texto

A Verificação do Locutor é o ato de confirmar que um locutor corresponde a uma voz conhecida ou **registrada**. A primeira etapa é **registrar** um perfil de voz, para que o serviço tenha algo com que comparar as futuras amostras de voz. Neste exemplo, você registra o perfil usando uma estratégia **dependente do texto**, que requer uma frase secreta específica a ser usada para o registro e a verificação. Confira os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases secretas compatíveis.

Comece [criando um perfil de voz](/rest/api/speakerrecognition/verification/textdependent/createprofile). Você precisará inserir a região e a chave de assinatura do serviço de Fala para cada um dos comandos curl deste artigo.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Observe que há três tipos de perfil de voz:

- Verificação dependente do texto
- Verificação independente do texto
- Identificação independente do texto

Nesse caso, você criará um perfil de voz de verificação dependente de texto. Você deve receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Em seguida, [registre o perfil de voz](/rest/api/speakerrecognition/verification/textdependent/createenrollment). Para o valor do parâmetro `--data-binary`, especifique um arquivo de áudio no computador que contém uma das senhas com suporte, como "minha voz é meu passaporte, verifique me". Você pode gravar esse arquivo de áudio com um aplicativo, como [Gravador de Voz do Windows](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab) ou pode gerá-lo usando a [conversão de texto em fala](../../../index-text-to-speech.yml).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Você deve receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Essa resposta informa que você precisa registrar mais dois amostras de áudio.

Depois de ter registrado um total de três amostras de áudio, você deverá receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Agora você está pronto para [verificar um exemplo de áudio em relação ao perfil de voz](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Este exemplo de áudio deve conter a mesma frase secreta que os exemplos que você usou para registrar o perfil de voz.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Você deve receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

O `Accept` significa que a frase secreta coincidiu e que a verificação foi bem-sucedida. A resposta também contém uma pontuação de similaridade que varia de 0,0 a 1,0.

Para concluir, [exclua o perfil de voz](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Não há resposta.

## <a name="text-independent-verification"></a>Verificação independente do texto

Em contraste com a verificação **dependente do texto**, a verificação **independente do texto**:

* Não exige a fala de uma frase secreta específica, mas pode-se falar qualquer coisa
* Não exige três amostras de áudio, mas, *sim*, 20 segundos de áudio total

Comece [criando um perfil de verificação independente de texto](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Você deve receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Em seguida, [registre o perfil de voz](/rest/api/speakerrecognition/verification/textindependent/createenrollment). Novamente, em vez de enviar três amostras de áudio, você precisa enviar amostras de áudio que contenham um total de 20 segundos de áudio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Depois de ter enviado amostras de áudio suficientes, você deverá receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Agora você está pronto para [verificar um exemplo de áudio em relação ao perfil de voz](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Novamente, esta amostra de áudio não precisa conter uma frase secreta. Ela pode conter qualquer fala, contanto que contenha um total de pelo menos quatro segundos de áudio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Você deve receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

O `Accept` significa que a verificação foi bem-sucedida. A resposta também contém uma pontuação de similaridade que varia de 0,0 a 1,0.

Para concluir, [exclua o perfil de voz](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Não há resposta.

## <a name="speaker-identification"></a>Identificação do locutor

A Identificação do Locutor é usada para determinar **quem** está falando dentre um determinado grupo de vozes registradas. O processo é semelhante à **verificação independente do texto**, sendo que a principal diferença é a capacidade de verificação em relação a vários perfis de voz de uma só vez, em vez de verificar apenas um perfil de cada vez.

Comece [criando um perfil de identificação independente de texto](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Você deve receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Em seguida, [registre o perfil de voz](/rest/api/speakerrecognition/identification/textindependent/createenrollment). Novamente, você precisa enviar amostras de áudio que contenham um total de 20 segundos de áudio. Essas amostras não precisam conter uma frase secreta.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Depois de ter enviado amostras de áudio suficientes, você deverá receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Agora você está pronto para [identificar uma amostra de áudio usando o perfil de voz](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). O comando Identify aceita uma lista delimitada por vírgula de possíveis IDs de perfil de voz. Neste caso, você só precisará passar a ID do perfil de voz criado anteriormente. No entanto, se desejar, você poderá passar várias IDs de perfil de voz, em que cada perfil de voz é registrado com amostras de áudio de uma voz diferente.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Você deve receber a resposta a seguir.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

A resposta contém a ID do perfil de voz que mais corresponde à amostra de áudio enviada. Ela também contém uma lista de perfis de voz qualificados, classificados em ordem de similaridade.

Para concluir, [exclua o perfil de voz](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Não há resposta.