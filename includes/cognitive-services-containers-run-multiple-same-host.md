---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598773"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Execução de vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, certifique-se de executar cada contêiner com uma porta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Substitua os `<container-registry>` e `<container-name>` com os valores dos contêineres que você usar. Eles não precisam estar no mesmo contêiner. Você pode ter o contêiner de Face e o contêiner de LUIS em execução no HOST juntos ou você pode ter vários contêineres de Face em execução. 

Execute o primeiro contêiner na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Execute o segundo contêiner na porta 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cada contêiner subsequente deve estar em uma porta diferente. 
