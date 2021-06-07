---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032918"
---
Consultas ao contêiner são cobradas pelo tipo de preço do recurso do Azure usado para `ApiKey`.

Os contêineres de serviços cognitivas do Azure não são licenciados para serem executados sem serem conectados ao ponto de extremidade de medição/cobrança. Você precisa permitir que os contêineres comuniquem as informações de cobrança com o ponto de extremidade de cobrança em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam dados do cliente, como imagem ou texto que está sendo analisado, para a Microsoft.

### <a name="connect-to-azure"></a>Conectar-se ao Azure

O contêiner precisa dos valores de argumento de cobrança para ser executado. Esses valores permitem que o contêiner se conecte ao ponto de extremidade de cobrança. O contêiner relata o uso a cada 10 a 15 minutos. Se o contêiner não se conectar ao Azure dentro da janela de tempo permitida, ele continuará sendo executado, mas não atenderá a consultas até que o ponto de extremidade de cobrança seja restaurado. Serão realizadas 10 tentativas de conexão no mesmo intervalo de tempo de 10 a 15 minutos. Se ele não puder se conectar ao ponto de extremidade de cobrança dentro de 10 tentativas, o contêiner parará de atender solicitações. Consulte as [perguntas frequentes do contêiner de serviços cognitivas](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work) para obter um exemplo das informações enviadas à Microsoft para cobrança.

### <a name="billing-arguments"></a>Argumentos de cobrança

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` O <span class="docon docon-navigate-external x-hidden-focus"></span> </a> comando iniciará o contêiner quando todas as três opções a seguir forem fornecidas com valores válidos:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave de API do recurso dos Serviços Cognitivos usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso provisionado especificado em `Billing`. |
| `Billing` | O ponto de extremidade do recurso dos Serviços Cognitivos usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como o URI do ponto de extremidade de um recurso do Azure provisionado.|
| `Eula` | Indica que você aceitou a licença do contêiner.<br/>O valor dessa opção deve ser definido como **aceitar**. |
