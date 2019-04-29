---
title: Exportar ou excluir dados - Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como exportar ou excluir dados no Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: e662e61a9df45cf3d57d5698337a26b7b8fc55a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605468"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportar ou excluir dados de usuário em Visão Personalizada

Visão personalizada coleta dados de usuário para operar o serviço, mas os clientes têm controle total sobre como exibir, exportar e excluir seus dados usando a visão personalizada [APIs de treinamento](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para saber como exportar e excluir dados de usuário na visão personalizada, consulte a tabela a seguir.

| Dados | Operação de exportar | Operação de Exclusão |
| ---- | ---------------- | ---------------- |
| Informações da Conta (Chaves de Assinatura) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Exclua usando o portal do Azure (Assinaturas do Azure). Ou usando o botão "Excluir sua Conta" na página de configurações de CustomVision.ai (Assinaturas de Conta Microsoft) | 
| Detalhes da iteração | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalhes de desempenho da iteração | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista de iterações | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projetos e detalhes do projeto | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Marcas de imagem | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Imagens | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o uri para download da imagem) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o uri para download da imagem) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Modelos exportados | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Excluído mediante a exclusão da conta |
