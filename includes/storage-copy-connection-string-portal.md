---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4170671b1dbcb8d2932b54f101cf5691b2461e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86035631"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar suas credenciais no Portal do Azure

O aplicativo de exemplo precisa autenticar o acesso à sua conta de armazenamento. Para autenticar, forneça suas credenciais de conta de armazenamento ao aplicativo como uma cadeia de conexão. Exiba suas credenciais da conta de armazenamento seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Na seção **Configurações** da visão geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, você pode ver suas chaves de acesso da conta, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Cadeia de conexão** em **key1** e selecione o botão **Copiar** para copiar a cadeia de conexão. Você adicionará o valor de cadeia de conexão para uma variável de ambiente na próxima etapa.

    ![Captura de tela mostrando como copiar uma cadeia de conexão do portal do Azure](media/storage-copy-connection-string-portal/portal-connection-string.png)
