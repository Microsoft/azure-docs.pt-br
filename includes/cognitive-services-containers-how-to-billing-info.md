---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704176"
---
Consultas ao contêiner são cobradas pelo tipo de preço do recurso do Azure usado para `<ApiKey>`.

Os contêineres dos Serviços Cognitivos do Azure não estão licenciados para execução sem estarem conectados ao ponto de extremidade de cobrança para medição. Você precisa permitir que os contêineres comuniquem as informações de cobrança com o ponto de extremidade de cobrança em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam dados do cliente, como imagem ou texto que está sendo analisado, para a Microsoft. 

### <a name="connect-to-azure"></a>Conecte-se ao Azure

O contêiner precisa dos valores de argumento de cobrança para ser executado. Esses valores permitem que o contêiner se conecte ao ponto de extremidade de cobrança. O contêiner relata o uso a cada 10 a 15 minutos. Se o contêiner não se conectar ao Azure dentro da janela de tempo permitida, ele continuará sendo executado, mas não atenderá a consultas até que o ponto de extremidade de cobrança seja restaurado. Serão realizadas 10 tentativas de conexão no mesmo intervalo de tempo de 10 a 15 minutos. Se não for possível conectar-se ao ponto de extremidade de cobrança dentro das 10 tentativas, o contêiner interromperá a execução. 

### <a name="billing-arguments"></a>Argumentos de cobrança

Todas as três opções a seguir devem ser especificadas com valores válidos para que o comando `docker run` inicie o contêiner:

| Opção | DESCRIÇÃO |
|--------|-------------|
| `ApiKey` | A chave de API do recurso dos Serviços Cognitivos usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso provisionado especificado em `Billing`. |
| `Billing` | O ponto de extremidade do recurso dos Serviços Cognitivos usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como o URI do ponto de extremidade de um recurso do Azure provisionado.|
| `Eula` | Indica que você aceitou a licença do contêiner.<br/>O valor dessa opção deve ser definido como **aceitar**. |


