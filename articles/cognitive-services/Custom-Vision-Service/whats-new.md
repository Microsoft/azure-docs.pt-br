---
title: Novidades na Visão Personalizada.
titleSuffix: Azure Cognitive Services
description: Este artigo contém notícias sobre a Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 57f3cf0cb15243d054da0111366f3a1dc0fb5349
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95739750"
---
# <a name="whats-new-in-custom-vision"></a>Novidades na Visão Personalizada

Conheça o que há de novo no serviço. Esses itens podem ser notas sobre a versão, vídeos, postagens no blog e outros tipos de informações. Marque esta página para manter-se atualizado quanto ao serviço.


## <a name="october-2020"></a>Outubro de 2020 

### <a name="custom-base-model"></a>Modelo base personalizado

- Alguns aplicativos têm uma grande quantidade de dados de treinamento conjuntos, mas precisam ajustar seus modelos separadamente. Isso resulta em um melhor desempenho para imagens de diferentes fontes com pequenas diferenças. Nesse caso, você pode treinar o primeiro modelo como de costume, com um grande volume de dados de treinamento. Depois, chame **TrainProject** na API de versão prévia pública 3.4 com _CustomBaseModelInfo_ no corpo da solicitação para usar o modelo treinado de primeiro estágio como modelo de base para projetos downstream. Quando o projeto de origem e o projeto de destino downstream têm características de imagens semelhantes, você pode esperar um melhor desempenho. 

### <a name="new-domain-information"></a>Novas informações de domínio

- As informações de domínio retornadas por **GetDomains** na API de versão prévia pública 3.4 da Visão Personalizada agora incluem as plataformas exportáveis com suporte, uma breve descrição da arquitetura do modelo e o tamanho do modelo para domínios compactos.

### <a name="training-divergence-feedback"></a>Comentários sobre divergências no treinamento

- A API de versão prévia pública 3.4 da Visão Personalizada agora retorna **TrainingErrorDetails** da chamada **GetIteration**. Em iterações com falha, isso revela se a falha foi causada por divergências de treinamento, que podem ser remediadas com informações de treinamento de qualidade mais elevada.

## <a name="july-2020"></a>Julho de 2020

### <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

* A Visão Personalizada dá suporte ao RBAC (controle de acesso baseado em função) do Azure, um sistema de autorização para gerenciar o acesso individual aos recursos do Azure. Para saber como gerenciar o acesso aos seus projetos de Visão Personalizada, confira [Controle de acesso baseado em função do Azure](./role-based-access-control.md).

### <a name="subset-training"></a>Treinamento de subconjunto

* Quando você estiver treinando um projeto de detecção de objetos, você poderá opcionalmente treinar apenas um subconjunto de suas marcas aplicadas. Talvez seja interessante fazer isso se você ainda não tiver aplicado um número suficiente de determinadas marcas, mas tiver outras delas. Siga o [Início rápido da biblioteca de clientes](./quickstarts/object-detection.md) para C# ou Python para saber mais.

### <a name="azure-storage-notifications"></a>Notificações de armazenamento do Azure

* Você pode integrar seu projeto de Visão Personalizada a uma fila de armazenamento de blobs do Azure para receber notificações por push de uma atividade de treinamento/exportação de projetos e cópias de backup de modelos publicados. Esse recurso é útil para evitar a sondagem contínua do serviço em busca de resultados quando operações longas estão em execução. Em vez disso, você pode integrar as notificações de fila de armazenamento ao seu fluxo de trabalho. Confira o guia [Integração de armazenamento](./storage-integration.md) para saber mais.

### <a name="copy-and-move-projects"></a>Copiar e mover projetos

* Agora você pode copiar projetos de uma conta de Visão Personalizada para outras. Talvez seja interessante mover um projeto de um ambiente de desenvolvimento para um de produção ou fazer backup de um projeto para uma conta em uma região do Azure diferente a fim de aumentar a segurança dos dados. Confira o guia [Copiar e mover projetos](./copy-move-projects.md) para saber mais.

## <a name="september-2019"></a>Setembro de 2019

### <a name="suggested-tags"></a>Marcas sugeridas

* A ferramenta Smart Labeler no [site da Visão Personalizada](https://www.customvision.ai/) gera marcas sugeridas para suas imagens de treinamento. Isso permite que você rotule um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada. Para obter instruções sobre como usar esse recurso, confira [Marcas sugeridas](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Atualizações dos Serviços Cognitivos

[Comunicados de atualização do Azure para os Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)