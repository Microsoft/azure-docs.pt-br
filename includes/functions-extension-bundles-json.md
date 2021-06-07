---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689533"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

As propriedades a seguir estão disponíveis em `extensionBundle`:

| Propriedade | Descrição |
| -------- | ----------- |
| id | O namespace dos pacotes de extensão do Microsoft Azure Functions. |
| version | A versão do pacote a ser instalado. O runtime do Functions sempre escolhe a versão máxima permitida definida pelo intervalo de versões. O valor de versão acima permite todas as versões de pacote da 1.0.0 até a 2.0.0, mas sem incluir a última. Para obter mais informações, confira a [notação de intervalo para especificar intervalos de versões](/nuget/reference/package-versioning#version-ranges). |