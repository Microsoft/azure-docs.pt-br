---
title: "Adicionar/remover um ponto de extremidade do servidor da Sincronização de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Saiba o que considerar ao planejar uma implantação de Arquivos do Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 92ac80953623a5a94d3104f30787c9636308c707
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Adicionar/remover um ponto de extremidade do servidor da Sincronização de Arquivo do Azure (versão prévia)
A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso, transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e você pode ter todos os caches que precisar em todo o mundo.

Um *ponto de extremidade do servidor* representa uma localização específica em um *servidor registrado*, como uma pasta em um volume do servidor ou a raiz do volume. Pode haver vários pontos de extremidade do servidor no mesmo volume se seus namespaces não forem sobrepostos (por exemplo, F:\sync1 e F:\sync2). Você pode configurar políticas de disposição em camadas de nuvem individualmente para cada ponto de extremidade do servidor. Se você adicionar uma localização de servidor com um conjunto existente de arquivos como um ponto de extremidade de servidor a um Grupo de Sincronização, esses arquivos serão mesclados com quaisquer outros arquivos que já estiverem em outros pontos de extremidade no Grupo de Sincronização.

Consulte [Como implantar a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-deployment-guide.md) para obter informações sobre como implantar a Sincronização de Arquivo do Azure de ponta a ponta.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um ponto de extremidade do servidor, primeiro você deve garantir que os seguintes critérios são atendidos: 
- O servidor tem o agente de Sincronização de Arquivo do Azure instalado e foi registrado. As instruções para instalar o agente de Sincronização de Arquivo do Azure podem ser encontradas no artigo [Registrar/cancelar o registro de um servidor com a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-server-registration.md). 
- Certifique-se de que um Serviço de Sincronização de Armazenamento foi implantado. Consulte [Como implantar a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-deployment-guide.md) para obter detalhes sobre como implantar um Serviço de Sincronização de Armazenamento. 
- Certifique-se de que um Grupo de Sincronização foi implantado. Saiba como [Criar um Grupo de Sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group).
- Certifique-se de que o servidor está conectado à Internet e que o Azure está acessível.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto de extremidade do servidor
Para adicionar um ponto de extremidade do servidor, navegue até o Grupo de Sincronização desejado e selecione “Adicionar ponto de extremidade do servidor”.

![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

As informações a seguir são necessárias em **Adicionar ponto de extremidade do servidor**:

- **Servidor Registrado**: o nome do servidor ou cluster no qual criar o ponto de extremidade do servidor.
- **Caminho**: o caminho no Windows Server a ser sincronizado como parte do grupo de sincronização.
- **Camadas de Nuvem**: uma opção para habilitar ou desabilitar as camadas de nuvem, o que permite que os arquivos pouco usados ou acessados sejam dispostos em camadas para os Arquivos do Azure.
- **Espaço Livre no Volume**: a quantidade de espaço livre para reservar no volume no qual reside o ponto de extremidade do servidor. Por exemplo, se o espaço livre do volume estiver definido como 50% em um volume com um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será disposta em camadas para os Arquivos do Azure. Independentemente de as camadas na nuvem estarem habilitadas, o Compartilhamento de Arquivos do Azure sempre terá uma cópia completa dos dados no Grupo de Sincronização.

Selecione **Criar** para adicionar o ponto de extremidade do servidor. Os arquivos dentro de um namespace de um Grupo de Sincronização agora serão mantidos sincronizados. 

## <a name="remove-a-server-endpoint"></a>Remover um ponto de extremidade do servidor
Quando habilitadas para um ponto de extremidade de servidor, as camadas de nuvem colocarão os arquivos em *camadas* para seus compartilhamentos de Arquivo do Azure. Isso permite que os compartilhamentos de arquivos locais atuem como um cache, em vez de uma cópia completa do conjunto de dados, para utilizar o espaço no servidor de arquivos de forma eficiente. No entanto, se um ponto de extremidade do servidor for removido com arquivos em camadas ainda localmente no servidor, esses arquivos se tornarão inacessíveis. Portanto, se o acesso contínuo ao arquivo for desejado, será necessário realizar o recall de todos os arquivos em camadas dos Arquivos do Azure antes de continuar com o cancelamento de registro. 

Isso pode ser feito com o cmdlet do PowerShell como mostrado abaixo:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Se o volume local que hospeda o servidor não tiver espaço livre suficiente para realizar o recall de todos os dados em camadas, o cmdlet `Invoke-StorageSyncFileRecall` falha.  

Para remover o ponto de extremidade do servidor:

1. Navegue até o Serviço de Sincronização do Armazenamento no qual o servidor está registrado.
2. Navegue até o Grupo de Sincronização desejado.
3. Remova o ponto de extremidade do servidor que você deseja no Grupo de Sincronização no Serviço de Sincronização de Armazenamento. Isso pode ser feito clicando com o botão direito do mouse no ponto de extremidade do servidor relevante no painel Grupo de Sincronização.

    ![Removendo um ponto de extremidade de servidor de um Grupo de Sincronização](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Próximas etapas
- [Registrar/cancelar o registro de um servidor com a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-server-registration.md)
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)