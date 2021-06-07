---
title: O que é o serviço de Detecção Facial do Azure?
titleSuffix: Azure Cognitive Services
description: O serviço de Detecção Facial do Azure fornece algoritmos de IA usados para detectar, reconhecer e analisar rostos humanos em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: facial recognition, facial recognition software, facial analysis, face matching, face recognition app, face search by image, facial recognition search
ms.openlocfilehash: e159ead12179f86406fd7df22475229298f95ee8
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285460"
---
# <a name="what-is-the-azure-face-service"></a>O que é o serviço de Detecção Facial do Azure?

> [!WARNING]
> Em 11 de junho de 2020, a Microsoft comunicou que não venderá tecnologia de reconhecimento facial a departamentos de polícia nos Estados Unidos até que um sólido regulamento, com base nos direitos humanos, seja aprovado. Desse modo, os clientes não poderão usar os recursos de reconhecimento facial ou a funcionalidade incluída nos Serviços do Azure, como Detecção Facial ou Video Indexer, se forem um departamento de polícia nos Estados Unidos ou permitirem o uso de desses serviços para e pela polícia.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de Detecção Facial do Azure fornece algoritmos de IA para detectar, reconhecer e analisar rostos humanos em imagens. O software de reconhecimento facial é importante em diversos cenários, como segurança, interface natural do usuário, análise e gerenciamento de conteúdo de imagem, aplicativos móveis e robótica.

O serviço de Detecção Facial fornece várias funções de análise facial diferentes, que são descritas nas seções a seguir.

Esta documentação contém os seguintes tipos de artigos:
* Os [inícios rápidos](./Quickstarts/client-libraries.md) são instruções passo a passo que permitem fazer chamadas para o serviço e obter resultados em um período curto. 
* Os [guias de instruções](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) contêm instruções para usar o serviço de maneiras mais específicas ou personalizadas.
* Os [artigos conceituais](./concepts/face-detection.md) fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.
* Os [tutoriais](./Tutorials/FaceAPIinCSharpTutorial.md) são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.

## <a name="face-detection"></a>Detecção facial

O serviço de Detecção Facial detecta rostos humanos em uma imagem e retorna as coordenadas do retângulo dos locais. Opcionalmente, a detecção facial pode extrair uma série de atributos relacionados ao rosto, como pose da cabeça, gênero, idade, emoção, pelos do rosto e óculos.

> [!NOTE]
> O recurso de detecção facial também está disponível por meio do [serviço de Pesquisa Visual Computacional](../computer-vision/overview.md). No entanto, se quiser realizar outras operações usando dados faciais, você deverá usar esse serviço.

![Uma imagem de uma mulher e um homem, com retângulos desenhados em torno de seus rostos e informações de idade e gênero exibidas](./Images/Face.detection.jpg)

Para saber mais sobre a detecção facial, confira o artigo de conceitos [Detecção facial](concepts/face-detection.md). Consulte também a documentação de referência [Detectar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Verificação facial

A API de Verificação executa uma autenticação com relação a dois rostos detectados ou de um rosto detectado com relação a um objeto pessoa. Na prática, ela avalia se dois rostos pertencem à mesma pessoa. Essa capacidade pode ser útil em cenários de segurança. Para saber mais, confira o guia de conceitos de [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API de Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

A API de Localização de Semelhantes faz a correspondência facial entre o rosto de destino e um conjunto de rostos candidatos, encontrando um conjunto menor de rostos que são mais semelhantes ao rosto de destino. Isso é útil para fazer uma pesquisa facial por imagem. 

Dois modos de trabalho, **matchPerson** e **matchFace**, têm suporte. O modo **matchPerson** retorna rostos semelhantes após a filtragem pela mesma pessoa usando a [API de Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo **matchFace** ignora a filtragem pela mesma pessoa. Ele retorna uma lista de rostos candidatos parecidos que podem ou não pertencer à mesma pessoa.

O exemplo a seguir mostra o rosto selecionado:

![Uma mulher sorrindo](./Images/FaceFindSimilar.QueryFace.jpg)

E estas imagens são os rostos elegíveis:

![Cinco imagens de pessoas sorrindo. As imagens a e b mostram a mesma pessoa.](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar quatro rostos semelhantes, o modo **matchPerson** retorna a e b, que mostram a mesma pessoa que o rosto-alvo. O modo **matchFace** retorna a, b, c e d&mdash;exatamente quatro possibilidades, mesmo que algumas não sejam a pessoa selecionada ou tenham pouca semelhança. Para saber mais, confira o guia de conceitos de [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API de Localização de Semelhantes](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Agrupamento facial

A API de Grupo divide um conjunto de rostos desconhecidos em vários grupos com base na similaridade. Cada grupo é um devido subconjunto separado do conjunto original de rostos. Todas as faces em um grupo provavelmente pertencem à mesma pessoa. Pode haver vários grupos diferentes para uma única pessoa. Os grupos são diferenciados por outro fator, como a expressão, por exemplo. Para saber mais, confira o guia de conceitos de [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API de Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identificação pessoal

A API de Identificação é usada para identificar um rosto detectado comparado com um banco de dados de pessoas (pesquisa de reconhecimento facial). Esse recurso pode ser útil para marcação de imagem automática em software de gerenciamento de fotos. Crie o banco de dados com antecedência e edite-o ao longo do tempo.

A imagem a seguir mostra um exemplo de um banco de dados chamado `"myfriends"`. Cada grupo pode conter até 1 milhão de objetos person diferentes. Cada pessoa objeto pode ter até 248 faces registradas.

![Uma grade com três colunas para pessoas diferentes, cada uma com três linhas de imagens de rosto](./Images/person.group.clare.jpg)

Depois de criar e treinar um banco de dados, você poderá fazer a identificação em relação ao grupo com um novo rosto detectado. Se a face for identificada como uma pessoa no grupo, ela é retornada.

Para saber mais sobre a identificação da pessoa, confira o guia de conceitos de [Reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API de Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="deploy-on-premises-using-docker-containers"></a>Implantação local usando contêineres do Docker

[Use o contêiner da Detecção Facial (versão prévia)](face-how-to-install-containers.md) para implantar recursos de API no local. Esse contêiner do Docker permite que você aproxime o serviço dos seus dados para fins de conformidade, segurança ou outras razões operacionais.

## <a name="sample-apps"></a>Aplicativos de exemplo

Os seguintes aplicativos de exemplo mostram algumas maneiras de usar o serviço de Detecção Facial:

- [API de Detecção Facial: a Biblioteca de Clientes do Windows e amostra](https://github.com/Microsoft/Cognitive-Face-Windows) é um aplicativo WPF que demonstra vários cenários de detecção facial, análise e identificação.
- [Aplicativo UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes) é um aplicativo UWP (Plataforma Universal do Windows) que usa identificação facial com fala, Cortana, tinta e câmera em um cenário de compartilhamento de anotações em família.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os recursos dos Serviços Cognitivos, os desenvolvedores que usam o serviço de Detecção Facial devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Para saber mais, confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Siga um início rápido para codificar os componentes básicos de um aplicativo de reconhecimento facial no idioma de sua escolha.

- [Início rápido da biblioteca de clientes](quickstarts/client-libraries.md).