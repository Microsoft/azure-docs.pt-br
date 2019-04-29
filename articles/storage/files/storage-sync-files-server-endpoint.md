---
title: Adicionar/remover um ponto de extremidade do servidor da Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar uma implantação de Arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 31bb71f016dd7f9dd37c766ece25caf8f300754b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766966"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Adicionar/remover um ponto de extremidade do servidor de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

Um *ponto de extremidade do servidor* representa uma localização específica em um *servidor registrado*, como uma pasta em um volume do servidor ou a raiz do volume. Pode haver vários pontos de extremidade do servidor no mesmo volume se seus namespaces não forem sobrepostos (por exemplo, F:\sync1 e F:\sync2). Você pode configurar políticas de disposição em camadas de nuvem individualmente para cada ponto de extremidade do servidor. Se você adicionar uma localização de servidor com um conjunto existente de arquivos como um ponto de extremidade de servidor a um Grupo de Sincronização, esses arquivos serão mesclados com quaisquer outros arquivos que já estiverem em outros pontos de extremidade no Grupo de Sincronização.

Consulte [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md) para obter informações sobre como implantar a Sincronização de Arquivos do Azure de ponta a ponta.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um ponto de extremidade do servidor, primeiro você deve garantir que os seguintes critérios sejam atendidos: 
- O servidor tem o agente de Sincronização de Arquivo do Azure instalado e foi registrado. As instruções para instalar o Agente de Sincronização de Arquivos do Azure podem ser encontradas no artigo [Registrar/cancelar o registro de um servidor com a Sincronização de Arquivos do Azure](storage-sync-files-server-registration.md). 
- Certifique-se de que um Serviço de Sincronização de Armazenamento foi implantado. Consulte [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md) para obter detalhes sobre como implantar um Serviço de Sincronização de Armazenamento. 
- Certifique-se de que um Grupo de Sincronização tenha sido implantado. Saiba como [Criar um Grupo de Sincronização](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Certifique-se de que o servidor está conectado à Internet e que o Azure está acessível. Usamos a porta 443 para toda a comunicação entre o servidor e o nosso serviço.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto de extremidade do servidor
Para adicionar um ponto de extremidade do servidor, navegue até o Grupo de Sincronização desejado e selecione “Adicionar ponto de extremidade do servidor”.

![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

As informações a seguir são necessárias em **Adicionar ponto de extremidade do servidor**:

- **Servidor registrado**: O nome do servidor ou cluster no qual criar o ponto de extremidade do servidor.
- **Caminho**: O caminho no Windows Server a ser sincronizado como parte do grupo de sincronização.
- **Camada de nuvem**: Uma opção para habilitar ou desabilitar a camada de nuvem. Quando habilitada, será camada na nuvem *camada* arquivos para os compartilhamentos de arquivos do Azure. Isso converte os compartilhamentos de arquivos local em um cache, em vez de uma cópia completa do conjunto de dados, para ajudá-lo a gerenciar a eficiência de espaço em seu servidor.
- **Espaço Livre no Volume**: a quantidade de espaço livre para reservar no volume no qual reside o ponto de extremidade do servidor. Por exemplo, se o espaço livre do volume estiver definido como 50% em um volume com um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será disposta em camadas para os Arquivos do Azure. Independentemente de as camadas na nuvem estarem habilitadas, o Compartilhamento de Arquivos do Azure sempre terá uma cópia completa dos dados no Grupo de Sincronização.

Selecione **Criar** para adicionar o ponto de extremidade do servidor. Agora, os arquivos dentro de um namespace de um Grupo de Sincronização serão mantidos sincronizados. 

## <a name="remove-a-server-endpoint"></a>Remover um ponto de extremidade do servidor
Se você quiser interromper o uso da Azure File Sync - Sincronização de arquivos do Azure para um determinado servidor de ponto de extremidade, você pode remover o ponto de extremidade do servidor. 

> [!Warning]  
> Não tente solucionar problemas com a sincronização, camadas de nuvem ou qualquer outro aspecto da Azure File Sync - Sincronização de arquivos do Azure removendo e recriando o ponto de extremidade do servidor, a menos que explicitamente instruído pelo engenheiro de Microsoft. Remover um ponto de extremidade do servidor é uma operação destrutiva e arquivos em camadas em que o ponto de extremidade do servidor não "retornará" em seus locais no compartilhamento de arquivos do Azure após o ponto de extremidade do servidor é recriado, o que resultará em erros em sincronia. Observe também, arquivos em camadas que existem fora do namespace de ponto de extremidade do servidor poderão ser perdidos permanentemente. Arquivos em camadas podem existir em seu se até mesmo de ponto de extremidade do servidor nuvem camadas nunca foi habilitada.

Para garantir que todos os arquivos em camadas são recuperados antes de remover o ponto de extremidade do servidor, desabilitar nuvem camadas no ponto de extremidade do servidor e, em seguida, execute o seguinte cmdlet do PowerShell para recuperar todos os arquivos em camadas no seu namespace de ponto de extremidade do servidor:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Se o volume local que hospeda o servidor não tiver espaço livre suficiente para realizar o recall de todos os dados em camadas, o cmdlet `Invoke-StorageSyncFileRecall` falha.  

Para remover o ponto de extremidade do servidor:

1. Navegue até o Serviço de Sincronização do Armazenamento no qual o servidor está registrado.
2. Navegue até o Grupo de Sincronização desejado.
3. Remova o ponto de extremidade do servidor que você desejar no Grupo de Sincronização no Serviço de Sincronização de Armazenamento. Isso pode ser feito clicando com o botão direito do mouse no ponto de extremidade do servidor relevante no painel Grupo de Sincronização.

    ![Removendo um ponto de extremidade do servidor de um grupo de sincronização](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Próximas etapas
- [Registrar/cancelar o registro de um servidor com a Sincronização de Arquivos do Azure](storage-sync-files-server-registration.md)
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
