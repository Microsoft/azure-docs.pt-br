---
title: incluir arquivo
description: incluir arquivo
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: mhopkins
ms.custom: include file
ms.openlocfilehash: 7dd22886d11c3a35a7a866ff7c9a4f56ea74cab7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "75351205"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar suas credenciais no Portal do Azure

Quando o aplicativo de exemplo faz uma solicitação para o Armazenamento do Azure, ele precisa ser autorizado. Para autenticar uma solicitação, adicione suas credenciais da conta de armazenamento ao aplicativo como uma cadeia de conexão. Exiba suas credenciais da conta de armazenamento seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Na seção **Configurações** da visão geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, você pode ver suas chaves de acesso da conta, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Cadeia de conexão** em **key1** e selecione o botão **Copiar** para copiar a cadeia de conexão. Você adicionará o valor de cadeia de conexão para uma variável de ambiente na próxima etapa.

    ![Captura de tela mostrando como copiar uma cadeia de conexão do portal do Azure](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

Depois de copiar a cadeia de conexão, grave-a em uma nova variável de ambiente no computador local que executa o aplicativo. Para definir a variável de ambiente, abra uma janela de console e siga as instruções do seu sistema operacional. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Após adicionar a variável de ambiente no Windows, é necessário iniciar uma nova instância da janela de comando.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Reiniciar programas

Depois de adicionar a variável de ambiente, reinicie todos os programas em execução que precisarem ler a variável de ambiente. Por exemplo, reinicie o seu ambiente ou editor de desenvolvimento antes de continuar.
