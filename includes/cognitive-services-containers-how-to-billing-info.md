---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598820"
---
Consultas para o contêiner são cobradas ao tipo de preço do recurso do Azure usado para o `<ApiKey>`.

Contêineres de serviços cognitivos não são licenciados para execução sem estar conectado ao ponto de extremidade de cobrança para a medição. Os clientes precisam habilitar contêineres para comunicar informações de cobrança com a cobrança do ponto de extremidade em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft. 

### <a name="connecting-to-azure"></a>Conectar-se ao Azure

O contêiner precisa os valores de argumento de cobrança para ser executado. Esses valores permitem que o contêiner para se conectar ao ponto de extremidade de cobrança. O contêiner relata o uso a cada 10 a 15 minutos. Se o contêiner não se conectar dentro da janela de tempo permitido para o Azure, o contêiner continuará executada, mas não atender a consultas até que o ponto de extremidade de cobrança seja restaurado. A conexão for tentada 10 vezes no mesmo intervalo de tempo de 10 a 15 minutos. Se ele não pode se conectar ao ponto de extremidade cobrança dentro de 10 tentativas, o contêiner de execução será interrompida. 

### <a name="billing-arguments"></a>Argumentos de cobrança

Todas as três opções a seguir devem ser especificados com os valores válidos para que o `docker run` comando para iniciar o contêiner:

| Opção | DESCRIÇÃO |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de Serviço Cognitivo usada para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso provisionado especificado em `Billing`. |
| `Billing` | O ponto de extremidade do recurso de Serviço Cognitivo usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como o ponto de extremidade do URI de um recurso do Azure provisionado.|
| `Eula` | Indica que você aceitou a licença para o contêiner.<br/>O valor dessa opção deve ser definido como `accept`. |


