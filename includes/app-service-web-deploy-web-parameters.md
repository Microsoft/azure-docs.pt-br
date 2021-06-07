---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 329db6b3fc0bd6d11e5fbac9472aa03899caec2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050403"
---
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro.
Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados. 

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Descreveremos cada parâmetro no modelo.

### <a name="sitename"></a>siteName
O nome do aplicativo Web que você deseja criar.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName
O nome do plano do Serviço de Aplicativo a usar para hospedar o aplicativo Web.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku
A camada de preços do plano de hospedagem.

```config
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

O modelo define os valores permitidos para esse parâmetro e atribui um valor padrão (S1) se nenhum valor é especificado.

### <a name="workersize"></a>workerSize
O tamanho da instância do plano de hospedagem (pequeno, médio ou grande).

```config
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

O modelo define os valores que são permitidos para esse parâmetro (0, 1 ou 2) e atribui um valor padrão (0) se nenhum valor é especificado. Os valores correspondem a pequeno, médio e grande.

