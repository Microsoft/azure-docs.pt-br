---
title: O que é a API de Reconhecimento do Locutor?
titleSuffix: Azure Cognitive Services
description: Use algoritmos avançados para a verificação do locutor e identificação de locutor com a API de Reconhecimento do Locutor em Serviços Cognitivos.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 15fc320a5b76a50def634d937a02fa639dce3739
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845882"
---
# <a name="speaker-recognition-api"></a>API de Reconhecimento do Locutor

Bem-vindo às APIs de Reconhecimento de Locutor dos Serviços Cognitivos do Azure. APIs de Reconhecimento do Locutor são APIs baseadas em nuvem que fornecem os algoritmos mais avançados para a verificação e identificação do locutor. O Reconhecimento do Locutor pode ser dividido em duas categorias: verificação do locutor e identificação do locutor.


## <a name="speaker-verification"></a>Verificação do locutor

A voz tem características exclusivas que podem ser usadas para identificar uma pessoa, assim como uma impressão digital.  O uso da voz como um sinal para cenários de autenticação e controle de acesso surgiu como uma nova ferramenta inovadora – oferecendo, basicamente, mais um nível na segurança que simplifica a experiência de autenticação para os clientes.

As APIs de Verificação do Locutor podem verificar e autenticar usuários usando suas vozes ou falas automaticamente.

### <a name="enrollment"></a>Registro

O registro de verificação do locutor depende do texto, o que significa que os locutores precisam escolher uma frase secreta específica que será usada durante as fases de registro e de verificação.

No registro, a voz do locutor é registrada com uma frase específica e, em seguida, vários recursos são extraídos e a frase escolhida é reconhecida. Juntos, os recursos extraídos e a frase escolhida formam uma assinatura de voz exclusiva.

### <a name="verification"></a>Verificação

Na verificação, uma voz e frase de entrada são comparadas com a assinatura de voz e a frase do registro, a fim de verificar se elas são da mesma pessoa e se estão falando a frase correta.

Para obter mais detalhes sobre a verificação do locutor, veja a API [Locutor – verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identificação do locutor

As APIs de Identificação do Locutor podem identificar automaticamente a pessoa que está falando em um arquivo de áudio, dado um grupo de locutores potenciais. O áudio de entrada é emparelhado com um grupo de locutores fornecidos e, caso seja encontrada uma correspondência, a identidade do locutor será retornada.

Todos os locutores devem passar por um processo de registro para que suas vozes sejam registradas no sistema e ter uma impressão de voz criada.


### <a name="enrollment"></a>Registro

O registro de identificação do locutor não depende do texto, o que significa que não há restrições sobre o que é dito pelo locutor no áudio. A voz do orador é registrada e vários recursos são extraídos para formar uma assinatura de voz exclusiva.


### <a name="recognition"></a>Reconhecimento

O áudio de orador desconhecido, junto com o grupo potencial de locutores, é fornecido durante o reconhecimento. A voz de entrada é comparada com todos os locutores para determinar o autor dessa voz e, se for encontrada uma correspondência, a identidade do orador será retornada.

Para obter mais detalhes sobre a identificação do locutor, veja a API [Locutor – Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
