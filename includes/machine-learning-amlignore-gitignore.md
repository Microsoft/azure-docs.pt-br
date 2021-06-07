---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 05/06/2020
ms.author: larryfr
ms.openlocfilehash: 83898744610cd27989e6fa0d1c6b1cca6286cb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103021460"
---
Para impedir que arquivos desnecessários sejam incluídos no instantâneo, faça um arquivo ignorar ( `.gitignore` ou `.amlignore` ) no diretório. Adicione os arquivos e diretórios a serem excluídos para esse arquivo. Para obter mais informações sobre a sintaxe a ser usada dentro desse arquivo, consulte [sintaxe e padrões](https://git-scm.com/docs/gitignore) para `.gitignore` . O `.amlignore` arquivo usa a mesma sintaxe. _Se ambos os arquivos existirem, o `.amlignore` arquivo será usado e o `.gitignore` arquivo não será usado._
