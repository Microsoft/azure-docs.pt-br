---
title: Solucionar problemas de políticas de instantâneo para Azure NetApp Files | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a solucionar problemas de gerenciamento de política de instantâneo para Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650997"
---
# <a name="troubleshoot-snapshot-policies"></a>Solucionar problemas de políticas de instantâneo

Este artigo descreve os cenários de erro que você pode encontrar ao gerenciar Azure NetApp Files políticas de instantâneo. Ele também fornece soluções que podem ajudá-lo a resolver os problemas.

## <a name="error-conditions-and-resolutions"></a>Condições de erro e resoluções 

|     Condição de erro    |     Resolução    |
|-|-|
| Falha na criação da política de instantâneo com nome de política de instantâneo inválido. | Ocorrerá um erro durante a criação da política de instantâneo se o nome da política de instantâneo for inválido. As diretrizes a seguir se aplicam a nomes de política de instantâneo:  <ul><li> O nome da política de instantâneo não pode conter caracteres não ASCII ou especiais. </li> <li> O nome da política de instantâneo deve começar com uma letra ou um número e pode conter letras, números, sublinhado (' _ ') e hifens ('-') somente. </li> <li> O nome da política de instantâneo deve ter entre 1 e 64 caracteres.  </li></ul> Revise o nome da política de instantâneo de acordo com as diretrizes.  |
| Falha na criação da política de instantâneo com valores inválidos. | Azure NetApp Files falha ao criar uma política de instantâneo se você inserir um valor inválido para um campo, como `Number of snapshots to keep` ou `Minute on the hour to take snapshot` . Os valores válidos são os seguintes:  <ul><li>O valor deve ser um número válido.</li> <li>O valor deve estar entre 0 e 59.</li></ul> Certifique-se de que um valor válido seja fornecido para os campos. | 
| Falha na criação da política de instantâneo com o erro `Total number of snapshots to keep exceeds 255` . | Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). O máximo inclui a soma de todos os instantâneos por hora, diários, semanais e mensais. <br> Diminua o `Snapshots to keep` valor e tente novamente. |
| A atribuição de política a um volume falha com o erro `Total snapshot policy is over the max '255'` . | Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). Quando a soma de todos os instantâneos sob demanda, por hora, diariamente, semanais e mensais excede o máximo, ocorre um erro. <br> Diminua o `snapshots to keep` valor ou exclua alguns instantâneos sob demanda e tente novamente. | 

## <a name="next-steps"></a>Próximas etapas  

* [Gerenciar políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
