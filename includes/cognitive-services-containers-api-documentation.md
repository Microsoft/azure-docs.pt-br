---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704273"
---
## <a name="validate-that-a-container-is-running"></a>Validar se um contêiner está em execução 

Há várias maneiras de validar se um contêiner está em execução. 

|Solicitação|Finalidade|
|--|--|
|`http://localhost:5000/`|O contêiner oferece uma home page.|
|`http://localhost:5000/status`|Solicitado com GET, para validar se o contêiner está em execução sem gerar uma consulta de ponto de extremidade. Essa solicitação pode ser usada para [testes de preparação e de execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do Kubernetes.|
|`http://localhost:5000/swagger`|O contêiner fornece um conjunto completo de documentação para os pontos de extremidades e um recurso `Try it now`. Com esse recurso, é possível inserir suas configurações em um formulário HTML baseado na Web e realizar a consulta sem precisar escrever nenhum código. Após a consulta ser retornada, um exemplo de comando CURL será fornecido para demonstrar o formato do corpo e dos cabeçalhos HTTP exigidos. |

![Home page do contêiner](./media/cognitive-services-containers-api-documentation/container-webpage.png)
