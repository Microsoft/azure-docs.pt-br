---
title: O que há de novo no serviço de Detecção Facial?
titleSuffix: Azure Cognitive Services
description: As notas sobre a versão para o serviço de Detecção Facial incluem um histórico das alterações para as várias versões.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
ms.custom: contperf-fy21q3
ms.openlocfilehash: c580828d29e92ecef7ecc73b8f3e5843c3ecd23d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078870"
---
# <a name="whats-new-in-face-service"></a>O que há de novo no serviço de Detecção Facial?

O serviço de Detecção Facial do Azure é atualizado continuamente. Use este artigo para se atualizar sobre os aprimoramentos de recursos, correções e atualizações de documentação.

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="new-face-api-detection-model"></a>Novo modelo de detecção de API de Detecção Facial
* O novo modelo de detecção 03 é o modelo de detecção mais preciso disponível no momento. Se você for um cliente novo, é recomendável usar esse modelo. A detecção 03 melhora o recall e a precisão em rostos menores encontrados nas imagens (64 x 64 pixels). Os aprimoramentos adicionais incluem uma redução geral em falsos positivos e uma detecção aprimorada em orientações de face giradas. A combinação da detecção 03 com o novo modelo de reconhecimento 04 também proporcionará precisão de reconhecimento aprimorada. Consulte [Especificar um modelo de detecção facial](./face-api-how-to-topics/specify-detection-model.md) para mais detalhes.
### <a name="new-detectable-face-attributes"></a>Novos atributos de Detecção Facial detectáveis
* O atributo `faceMask` está disponível com o modelo de detecção 03 mais recente, juntamente com o atributo adicional `"noseAndMouthCovered"` que detecta se a máscara facial está sendo usada conforme o esperado, cobrindo o nariz e a boca. Para usar a funcionalidade de detecção de máscara mais recente, os usuários precisam especificar o modelo de detecção na solicitação de API: atribuir a versão do modelo com o parâmetro _detectionModel_ para `detection_03`. Consulte [Especificar um modelo de detecção facial](./face-api-how-to-topics/specify-detection-model.md) para mais detalhes.
### <a name="new-face-api-recognition-model"></a>Novo modelo de reconhecimento de API de Detecção Facial
* O novo modelo de reconhecimento 04 é o modelo de reconhecimento mais preciso disponível no momento. Se você é um novo cliente, recomendamos usar esse modelo para verificação e identificação. Ele melhora a precisão do reconhecimento 03, incluindo o reconhecimento aprimorado para usuários registrados com cobertura facial (máscaras cirúrgicas, máscaras N95, máscaras de tecido). Agora, os clientes podem criar experiências de usuário seguras e diretas que detectam se um usuário registrado está usando uma cobertura facial com o modelo de detecção 03 mais recente e reconhecem quem está com o modelo de reconhecimento 04 mais recente. Consulte [Especificar um modelo de reconhecimento facial](./face-api-how-to-topics/specify-recognition-model.md) para mais detalhes.


## <a name="january-2021"></a>Janeiro de 2021
### <a name="mitigate-latency"></a>Mitigar a latência
* A equipe de Detecção Facial publicou um novo artigo detalhando possíveis causas de latência ao usar o serviço e as estratégias de mitigação possíveis. Consulte [Mitigar a latência ao usar o serviço de Detecção Facial](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>Dezembro de 2020
### <a name="customer-configuration-for-face-id-storage"></a>Configuração do cliente para o armazenamento de Face ID
* Embora o serviço de Detecção Facial não armazene imagens de clientes, os recursos faciais extraídos serão armazenados no servidor. A Face ID é um identificador do recurso facial e será usada em [Rosto – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Rosto – Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) e [Rosto – Encontrar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Os recursos faciais armazenados expirarão e serão excluídos 24 horas após a chamada de detecção original. Agora, os clientes podem determinar o período de tempo em que essas Face IDs são armazenadas em cache. O valor máximo ainda é de até 24 horas, mas um valor mínimo de 60 segundos agora pode ser definido. Os novos intervalos de tempo para as Face IDs que estão sendo armazenadas em cache são qualquer valor entre 60 segundos e 24 horas. Mais detalhes podem ser encontrados na referência de API [Rosto – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (o parâmetro *faceIdTimeToLive*).

## <a name="november-2020"></a>Novembro de 2020
### <a name="sample-face-enrollment-app"></a>Aplicativo de registro de Detecção Facial de exemplo
* A equipe publicou um exemplo de aplicativo de registro de Detecção Facial para demonstrar as melhores práticas para estabelecer um consentimento significativo e criar sistemas de reconhecimento facial de alta precisão por meio de registros de alta qualidade. O exemplo de software livre pode ser encontrado no guia [Criar um aplicativo de registro](build-enrollment-app.md) e no [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), pronto para os desenvolvedores implantarem ou personalizarem. 

## <a name="august-2020"></a>Agosto de 2020
### <a name="customer-managed-encryption-of-data-at-rest"></a>Criptografia de dados inativos gerenciada pelo cliente
* O serviço de Detecção Facial criptografa automaticamente seus dados ao mantê-los na nuvem. A criptografia do serviço de Detecção Facial protege seus dados para ajudar você a atender aos compromissos de conformidade e segurança de sua organização. Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Também há uma opção para gerenciar sua assinatura com suas próprias chaves chamada CMK (chaves gerenciadas pelo cliente). Mais detalhes podem ser encontrados em [Chaves gerenciadas pelo cliente](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>Abril de 2020
### <a name="new-face-api-recognition-model"></a>Novo modelo de reconhecimento de API de Detecção Facial
* O novo modelo de reconhecimento 03 é o modelo mais preciso disponível no momento. Se você é um novo cliente, recomendamos usar esse modelo. O reconhecimento 03 fornecerá precisão aprimorada para comparações de semelhanças e comparações de correspondência de pessoas. Mais detalhes podem ser encontrados em [Especificar um modelo de reconhecimento facial](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Junho de 2019

### <a name="new-face-api-detection-model"></a>Novo modelo de detecção de API de Detecção Facial
* O novo modelo de detecção 02 apresenta precisão aprimorada em imagens com rostos pequenos, obstruídos, borrados e de perfil. Use-o por meio de [Rosto – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [Pessoa de PersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [Pessoa de LargePersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) especificando o novo nome do modelo de detecção facial `detection_02` no parâmetro `detectionModel`. Mais detalhes em [Como especificar um modelo de detecção](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>Abril de 2019

### <a name="improved-attribute-accuracy"></a>Precisão de atributo aprimorada
* Precisão geral aprimorada dos atributos `age` e `headPose`. O atributo `headPose` também é atualizado com o valor `pitch` habilitado agora. Use esses atributos especificando-os no parâmetro `returnFaceAttributes` do parâmetro `returnFaceAttributes` de [Rosto – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 
### <a name="improved-processing-speeds"></a>Velocidades de processamento aprimoradas
* Velocidades aprimoradas das operações [Rosto – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [Pessoa de PersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [Pessoa de LargePersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2019"></a>Março de 2019

### <a name="new-face-api-recognition-model"></a>Novo modelo de reconhecimento de API de Detecção Facial
* O modelo de reconhecimento 02 está com a precisão aprimorada. Use-o por meio de [Rosto – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) e [LargePersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) especificando o novo nome do modelo de reconhecimento facial `recognition_02` no parâmetro `recognitionModel`. Mais detalhes em [Como especificar um modelo de reconhecimento](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Janeiro de 2019

### <a name="face-snapshot-feature"></a>Recurso Instantâneo de Detecção Facial
* Esse recurso permite que o serviço dê suporte à migração de dados entre assinaturas: [Instantâneo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Mais detalhes em [Como migrar seus dados faciais para uma assinatura diferente de Detecção Facial](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Outubro de 2018

### <a name="api-messages"></a>Mensagens de API
* Descrição refinada para `status`, `createdDateTime`, `lastActionDateTime` e `lastSuccessfulTrainingDateTime` em [PersonGroup – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) e [LargeFaceList – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Maio de 2018

### <a name="improved-attribute-accuracy"></a>Precisão de atributo aprimorada
* Atributo `gender` aprimorado significativamente e também atributos `age`, `glasses`, `facialHair`, `hair`, `makeup` aprimorados. Use-os por meio do parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.
### <a name="increased-file-size-limit"></a>Limite de tamanho do arquivo aprimorado
* Limite de tamanho do arquivo de imagem de entrada maior, passando 4 MB para 6 MB em [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [Pessoa de LargePersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Março de 2018

### <a name="new-data-structure"></a>Nova estrutura de dados
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mais detalhes sobre [Como usar o recurso de larga escala](Face-API-How-to-Topics/how-to-use-large-scale.md).
* Parâmetro [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` aumentado de [1, 5] para [1, 100] e o padrão para 10.

## <a name="may-2017"></a>Maio de 2017

### <a name="new-detectable-face-attributes"></a>Novos atributos de Detecção Facial detectáveis
* Adicionados os atributos `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` e `noise` no parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.
* Suporte para dez mil pessoas em um PersonGroup e [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
* Paginação compatível em [PersonGroup pessoa – Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) com parâmetros opcionais: `start` e `top`.
* Simultaneidade compatível na adição/exclusão de faces com relação a diferentes FaceLists e diferentes pessoas em PersonGroup.

## <a name="march-2017"></a>Março de 2017

### <a name="new-detectable-face-attribute"></a>Novo atributo de Detecção Facial detectável
* Adicionado o atributo `emotion` no parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.
### <a name="fixed-issues"></a>Problemas corrigidos
* O rosto não era detectado com o retângulo retornado de [Rosto – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como `targetFace` em [FaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [Pessoa de PersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).
* O tamanho facial detectável é definido para garantir que fique estritamente entre 36 x 36 e 4.096 x 4.096 pixels.

## <a name="november-2016"></a>Novembro de 2016
### <a name="new-subscription-tier"></a>Nova camada de assinatura
* Adicionada assinatura Armazenamento Facial Padrão para armazenar faces persistentes adicionais ao usar [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) para correspondência de similaridade ou identificação. As imagens armazenadas são cobradas a US$ 0,5 por mil faces, e essa taxa é dividida proporcionalmente por dia. As assinaturas da camada gratuita continuam limitadas a mil pessoas no total.

## <a name="october-2016"></a>Outubro de 2016
### <a name="api-messages"></a>Mensagens de API
* Alterada a mensagem de erro de mais de um rosto em `targetFace`, passando de 'Existem mais de um rosto na imagem' para 'Existe mais de um rosto na imagem' em [FaceList – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [Pessoa de PersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="july-2016"></a>Julho de 2016
### <a name="new-features"></a>Novos recursos
* Face compatível para autenticação de objeto em [Face – Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
* Adicionado o parâmetro `mode` opcional possibilitando a seleção de dois modos de trabalho: `matchPerson` e `matchFace`, em [Face – Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e o padrão é `matchPerson`.
* Adicionado o parâmetro `confidenceThreshold` opcional para o usuário definir o limite de se uma face pertence a um objeto de Pessoa em [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
* Adicionados os parâmetros `start` e `top` opcionais em [PersonGroup – Listar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) para permitir que o usuário especifique o ponto inicial e o número total de PersonGroups para a lista.

## <a name="v10-changes-from-v0"></a>Alterações à V1.0 da V0

* Atualizado o ponto de extremidade da raiz do serviço de ```https://westus.api.cognitive.microsoft.com/face/v0/``` para ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Alterações aplicadas a: [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face – Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Face – Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
* Atualizado o tamanho mínimo face detectável para 36 x 36 pixels. Faces menores que 36 x 36 pixels não serão detectadas.
* Preteridos os dados de PersonGroup e Pessoa de Detecção Facial V0. Esses dados não podem ser acessados com o serviço Detecção Facial V1.0.
* Preterido o ponto de extremidade V0 da API de Detecção Facial em 30 de junho de 2016.