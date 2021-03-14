---
title: O que há de novo no serviço de Detecção Facial?
titleSuffix: Azure Cognitive Services
description: As notas de versão do serviço de face incluem um histórico das alterações de versão para várias versões.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: pafarley
ms.openlocfilehash: 9293d47d8aade11f4cc3ee55bb32574f125dfafc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428279"
---
# <a name="whats-new-in-face-service"></a>O que há de novo no serviço de Detecção Facial?

O serviço de face do Azure é atualizado em uma base contínua. Use este artigo para se manter atualizado com os aprimoramentos de recursos, correções e atualizações de documentação.

## <a name="february-2021"></a>Fevereiro de 2021

* Novo modelo de detecção de API de Detecção Facial: o novo modelo de detecção 03 é o modelo de detecção mais preciso disponível no momento. Se você for um cliente novo, é recomendável usar esse modelo. A detecção 03 melhora a RECALL e a precisão em rostos menores encontrados nas imagens (64 x 64 pixels). Os aprimoramentos adicionais incluem uma redução geral em falsos positivos e uma detecção aprimorada em orientações de face giradas. A combinação da detecção 03 com o novo reconhecimento 04 também proporcionará maior precisão de reconhecimento. Consulte [especificar um modelo de detecção facial](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model) para obter mais detalhes.
* Atributo de máscara facial: o atributo de máscara facial está disponível com o modelo de detecção mais recente 03, juntamente com o atributo adicional `"noseAndMouthCovered"` que detecta se a máscara de face está gasta conforme o esperado, cobrindo o nariz e a boca. Para usar o recurso de detecção de máscara mais recente, os usuários precisam especificar o modelo de detecção na solicitação de API: atribuir a versão do modelo com o parâmetro _detectionModel_ para `detection_03` . Consulte [especificar um modelo de detecção facial](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model) para obter mais detalhes.
* Novo modelo de reconhecimento de API de Detecção Facial: o novo modelo de reconhecimento 04 é o modelo de reconhecimento mais preciso disponível no momento. Se você for um novo cliente, recomendamos usar esse modelo para verificação e identificação. Ele melhora a precisão do reconhecimento 03, incluindo o reconhecimento aprimorado para usuários registrados com cobertura de face (máscaras de Surgical, máscaras N95, máscaras de tecido). Agora, os clientes podem criar experiências de usuário seguras e diretas que detectam se um usuário registrado está aproveitando uma capa com o modelo de detecção mais recente 03 e reconhece quem está com o modelo mais recente de reconhecimento 04. Consulte [especificar um modelo de reconhecimento facial](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-recognition-model) para obter mais detalhes.


## <a name="january-2021"></a>Janeiro de 2021
* Reduzir a latência ao usar o API de Detecção Facial: a equipe de face publicou um novo artigo detalhando possíveis causas de latência ao usar o serviço e as estratégias de mitigação possíveis. Consulte [mitigar latência ao usar o serviço de face](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>Dezembro de 2020
* Configuração do cliente para o armazenamento de ID facial: embora o serviço de face não armazene imagens do cliente, os recursos de face extraídos serão armazenados no servidor. A ID de face é um identificador do recurso facial e será usado em [face-Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [facial-Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)e [face-Find semelhantes](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Os recursos de face armazenados expirarão e serão excluídos 24 horas após a chamada de detecção original. Agora, os clientes podem determinar o período de tempo em que essas IDs de face são armazenadas em cache. O valor máximo ainda é de até 24 horas, mas um valor mínimo de 60 segundos agora pode ser definido. Os novos intervalos de tempo para as IDs de face que estão sendo armazenadas em cache são qualquer valor entre 60 segundos e 24 horas. Mais detalhes podem ser encontrados na referência da API de [detecção de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (o parâmetro *faceIdTimeToLive* ).

## <a name="november-2020"></a>Novembro de 2020
* Publicou um exemplo de aplicativo de registro de face para demonstrar as práticas recomendadas para estabelecer um consentimento significativo e criar sistemas de reconhecimento de face de alta precisão por meio de registros de alta qualidade. O exemplo de código-fonte aberto pode ser encontrado no guia [criar um aplicativo de registro](build-enrollment-app.md) e no [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), pronto para os desenvolvedores implantarem ou personalizarem. 

## <a name="august-2020"></a>Agosto de 2020
* Criptografia de dados gerenciada pelo cliente em repouso: o serviço de face criptografa automaticamente seus dados ao mantê-los para a nuvem. A criptografia do serviço de face protege seus dados para ajudá-lo a atender aos compromissos de segurança e conformidade da organização. Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Também há uma nova opção para gerenciar sua assinatura com suas próprias chaves chamadas CMK (chaves gerenciadas pelo cliente). Mais detalhes podem ser encontrados em [chaves gerenciadas pelo cliente](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>Abril de 2020
* Novo modelo de reconhecimento de API de Detecção Facial: o novo modelo de reconhecimento 03 é o modelo mais preciso disponível no momento. Se você for um novo cliente, recomendamos o uso desse modelo. O reconhecimento 03 fornecerá precisão aprimorada para comparações de semelhanças e comparações de correspondência de pessoas. Mais detalhes podem ser encontrados em [especificar um modelo de reconhecimento facial](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Junho de 2019

* Foi adicionado um novo modelo de detecção facial com precisão aprimorada em faces pequenas, rápidas, obstruído e borradas. Use-a por meio [de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facelist-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)adicionar face, [LargeFaceList-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [pessoa-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) adicionar face e [LargePersonGroup pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) especificando o novo nome do modelo de detecção facial `detection_02` no `detectionModel` parâmetro. Mais detalhes sobre [como especificar um modelo de detecção](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>Abril de 2019

* Maior precisão geral dos `age` atributos e `headPose` . O `headPose` atributo também é atualizado com o `pitch` valor habilitado agora. Use esses atributos especificando-os no `returnFaceAttributes` parâmetro do parâmetro de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Melhoria na velocidade de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Facelist-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-adicionar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [pessoa de](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) pessoa física-adicionar face e [LargePersonGroup pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2019"></a>Março de 2019

* Adicionou um novo modelo de reconhecimento facial com precisão aprimorada. Use-a por meio [de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facelist-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [Person-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) Create e [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) especificando o novo nome do modelo de reconhecimento facial `recognition_02` no `recognitionModel` parâmetro. Mais detalhes sobre [como especificar um modelo de reconhecimento](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Janeiro de 2019

* Adicionado recurso de instantâneo para dar suporte à migração de dados entre assinaturas: [instantâneo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Mais detalhes [sobre como migrar os dados de face para uma assinatura facial diferente](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Outubro de 2018

* Descrição refinada para `status`, `createdDateTime`, `lastActionDateTime` e `lastSuccessfulTrainingDateTime` em [PersonGroup – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) e [LargeFaceList – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Maio de 2018

* Atributo `gender` aprimorado significativamente e também atributos `age`, `glasses`, `facialHair`, `hair`, `makeup` aprimorados. Use-os por meio do parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`. 

* Limite de tamanho do arquivo de imagem de entrada maior, passando 4 MB para 6 MB em [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [Pessoa de LargePersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Março de 2018

* Adicionado o Contêiner de Escala de Milhões: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mais detalhes sobre [Como usar o recurso de larga escala](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Parâmetro [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` aumentado de [1, 5] para [1, 100] e o padrão para 10.

## <a name="may-2017"></a>Maio de 2017

* Adicionados os atributos `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` e `noise` no parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.

* Suporte para dez mil pessoas em um PersonGroup e [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Paginação compatível em [PersonGroup pessoa – Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) com parâmetros opcionais: `start` e `top`.

* Simultaneidade compatível na adição/exclusão de faces com relação a diferentes FaceLists e diferentes pessoas em PersonGroup.

## <a name="march-2017"></a>Março de 2017
* Adicionado o atributo `emotion` no parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.

* Corrigida face que não podia ser detectada com o retângulo retornado de [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como `targetFace` em [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Corrigido tamanho de face detectável garantir que fique estritamente entre 36 x 36 a 4.096 x 4.096 pixels.

## <a name="november-2016"></a>Novembro de 2016
* Adicionada assinatura Armazenamento Facial Padrão para armazenar faces persistentes adicionais ao usar [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) para correspondência de similaridade ou identificação. As imagens armazenadas são cobradas a US$ 0,5 por mil faces, e essa taxa é dividida proporcionalmente por dia. As assinaturas da camada gratuita continuam limitadas a mil pessoas no total.

## <a name="october-2016"></a>Outubro de 2016
* Alterada a mensagem de erro de mais de uma face em targetFace, passando de 'Existem mais de uma face na imagem' para 'Existe mais de uma face na imagem' em [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="july-2016"></a>Julho de 2016
* Face compatível para autenticação de objeto em [Face – Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Adicionado o parâmetro `mode` opcional possibilitando a seleção de dois modos de trabalho: `matchPerson` e `matchFace`, em [Face – Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e o padrão é `matchPerson`.

* Adicionado o parâmetro `confidenceThreshold` opcional para o usuário definir o limite de se uma face pertence a um objeto de Pessoa em [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Adicionados os parâmetros `start` e `top` opcionais em [PersonGroup – Listar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) para permitir que o usuário especifique o ponto inicial e o número total de PersonGroups para a lista.

## <a name="v10-changes-from-v0"></a>Alterações à V1.0 da V0

* Atualizado o ponto de extremidade da raiz do serviço de ```https://westus.api.cognitive.microsoft.com/face/v0/``` para ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Alterações aplicadas a: [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face – Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Face – Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Atualizado o tamanho mínimo face detectável para 36 x 36 pixels. Faces menores que 36 x 36 pixels não serão detectadas.

* Preteridos os dados de PersonGroup e Pessoa de Detecção Facial V0. Esses dados não podem ser acessados com o serviço Detecção Facial V1.0.

* Preterido o ponto de extremidade V0 da API de Detecção Facial em 30 de junho de 2016.