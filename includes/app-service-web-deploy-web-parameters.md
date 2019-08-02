---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171730"
---
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro.
Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados. 

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Descreveremos cada parâmetro no modelo.

### <a name="sitename"></a>siteName
O nome do aplicativo Web que você deseja criar.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
O nome do plano do Serviço de Aplicativo a usar para hospedar o aplicativo Web.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
A camada de preços do plano de hospedagem.

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

O modelo define os valores permitidos para esse parâmetro e atribui um valor padrão (S1) se nenhum valor é especificado.

### <a name="workersize"></a>workerSize
O tamanho da instância do plano de hospedagem (pequeno, médio ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

O modelo define os valores que são permitidos para esse parâmetro (0, 1 ou 2) e atribui um valor padrão (0) se nenhum valor é especificado. Os valores correspondem a pequeno, médio e grande.

