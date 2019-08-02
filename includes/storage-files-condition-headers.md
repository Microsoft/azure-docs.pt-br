---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171990"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Erro ConditionHeadersNotSupported de um aplicativo Web usando arquivos do Azure do navegador

Ao acessar o conteúdo hospedado nos arquivos do Azure por meio de um aplicativo que faz uso de cabeçalhos condicionais, como um navegador da web, acesso falhará, exibindo um erro ConditionHeadersNotSupported.

![Erro ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Ainda não há suporte para cabeçalhos condicionais. Aplicativos implementá-los serão necessário solicitar o arquivo completo sempre que o arquivo é acessado.

### <a name="workaround"></a>Solução alternativa

Quando um novo arquivo for carregado, a propriedade de controle de cache por padrão é "no-cache". Para forçar o aplicativo para solicitar o arquivo de cada vez, propriedade de controle de cache do arquivo precisa ser atualizado de "no-cache" para "sem cache, no-store must-revalidate". Isso pode ser obtido usando [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Modificação de cache de conteúdo do Gerenciador de armazenamento](media/storage-files-condition-headers/storage-explorer-cache.png)