---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543543"
---
| Propriedade | Descrição | Exemplo | Obrigatório |
|---|---|---|---|
| `app_location` | Local do código do aplicativo. | Digite `/` se o código-fonte do aplicativo estiver na raiz do repositório ou `/app` se o código do aplicativo estiver em um diretório chamado `app`. | Sim |
| `api_location` | Local do seu código de Azure Functions. | Digite `/api` se o código do aplicativo estiver em uma pasta chamada `api`. Se nenhum aplicativo do Azure Functions for detectado na pasta, a compilação não falhará. O fluxo de trabalho assume que você não deseja uma API. | Não |
| `output_location` | Local do diretório de saída de compilação relativo ao `app_location`. | Se o código-fonte do aplicativo estiver localizado em `/app` e o script de compilação gerar arquivos para a pasta `/app/build`, defina `build` como o valor `output_location`. | Não |