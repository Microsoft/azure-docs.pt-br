---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "70737481"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Erro ConditionHeadersNotSupported de um aplicativo Web usando arquivos do Azure do navegador

O erro ConditionHeadersNotSupported ocorre ao acessar o conteúdo hospedado nos arquivos do Azure por meio de um aplicativo que utiliza cabeçalhos condicionais, como um navegador da Web, o acesso falha. O erro informa que os cabeçalhos de condição não têm suporte.

![Erro nos cabeçalhos condicionais dos arquivos do Azure](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Ainda não há suporte para cabeçalhos condicionais. Os aplicativos que a implementam precisarão solicitar o arquivo completo toda vez que o arquivo for acessado.

### <a name="workaround"></a>Solução alternativa

Quando um novo arquivo é carregado, a propriedade Cache-Control por padrão é "no-cache". Para forçar o aplicativo a solicitar o arquivo a cada vez, a propriedade Cache-Control do arquivo precisa ser atualizada de "no-cache" para "no-cache, no-Store, deve-revalidate". Isso pode ser feito usando [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

![Modificação do cache de conteúdo do Gerenciador de armazenamento para cabeçalhos condicionais de arquivos do Azure](media/storage-files-condition-headers/storage-explorer-cache.png)