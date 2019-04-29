---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074274"
---
## <a name="roles-versus-hierarchical-entities"></a>Papéis versus entidades hierárquicas

Você deve usar uma entidade hierárquica ou um padrão com uma entidade simples com funções? 

Isso depende. Os padrões e as declarações de exemplo são comparáveis na medida em que representam uma declaração de usuário e são específicos para uma intenção.  

Se as declarações não tiverem um padrão claro, use as entidades hierárquicas. 

|Entidades hierárquicas|As entidade simples com funções|
|--|--|
|Disponível no exemplo de declarações e padrões de intenção|Apenas disponível em padrões|
|Deve ter exemplos de declarações em intenção com entidades filhas rotuladas|Os papéis não podem ser rotulados em declarações de exemplo em intenção|
|Pode precisar de **mais** exemplos de declarações na intenção de extrair entidade|Deve precisar de **menos** exemplos de declarações de intenção|
