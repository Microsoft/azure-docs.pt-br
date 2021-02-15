---
title: Exibir ou excluir seus dados-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Você mantém controle total sobre seus dados. Este artigo explica como você pode exibir, exportar ou excluir seus dados no Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527387"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Exibir ou excluir dados do usuário no Visão Personalizada

O Visão Personalizada coleta dados do usuário para operar o serviço, mas os clientes têm controle total sobre a exibição e a exclusão de seus dados usando as [APIs de treinamento](https://go.microsoft.com/fwlink/?linkid=865446)de visão personalizada.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para saber como exibir e excluir dados do usuário no Visão Personalizada, consulte a tabela a seguir.

| Dados | Exibir operação | Operação de exclusão |
| ---- | ---------------- | ---------------- |
| Informações da conta (chaves de assinatura) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Exclua usando o portal do Azure (Assinaturas do Azure). Ou usando o botão "excluir sua conta" na página de configurações do CustomVision.ai (assinaturas de conta da Microsoft) | 
| Detalhes da iteração | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalhes de desempenho da iteração | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista de iterações | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projetos e detalhes do projeto | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Marcas de imagem | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Imagens | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o uri para download da imagem) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o uri para download da imagem) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Iterações exportadas | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Excluído mediante a exclusão da conta |
