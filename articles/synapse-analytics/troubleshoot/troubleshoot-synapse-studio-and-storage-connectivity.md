---
title: Solucionar problemas de conectividade entre o Synapse Studio e o armazenamento
description: Solucionar problemas de conectividade entre o Synapse Studio e o armazenamento
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117054"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Solucionar problemas de conectividade entre o Azure Synapse Studio e o armazenamento

No Synapse Studio, você pode explorar os recursos de dados localizados no armazenamento vinculado. Este guia o ajudará a resolver problemas de conectividade quando você estiver tentando acessar seus recursos de dados. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>#1 de caso: a conta de armazenamento não tem as permissões adequadas

Se sua conta de armazenamento não tiver as permissões corretas, você não poderá expandir a estrutura de armazenamento por meio de "dados"--> "vinculado" no Synapse Studio. Consulte a captura de tela de sintoma de problema abaixo. 

A mensagem de erro detalhada pode variar, mas o significado geral da mensagem de erro é: "esta solicitação não está autorizada a executar esta operação.".

No nó de armazenamento vinculado:  
![Problema de conectividade de armazenamento 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

No nó do contêiner de armazenamento:  
![Problema de conectividade de armazenamento 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**Solução**: para atribuir sua conta à função adequada, consulte [usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de BLOB e de fila](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>#2 de caso: falha ao enviar a solicitação para o servidor de armazenamento

Ao selecionar a seta para expandir a estrutura de armazenamento em "dados"--> "vinculada" no Synapse Studio, você poderá ver o problema de "REQUEST_SEND_ERROR" no painel esquerdo. Consulte a captura de tela de sintoma do problema abaixo:

No nó de armazenamento vinculado:  
![Problema de conectividade de armazenamento 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

No nó do contêiner de armazenamento:  
![Problema de conectividade de armazenamento 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Pode haver vários motivos possíveis por trás desse problema:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>O recurso de armazenamento está atrás de uma vNet e um ponto de extremidade de armazenamento privado precisa configurar

**Solução**: nesse caso, você precisa configurar o ponto de extremidade particular de armazenamento para sua conta de armazenamento. Para saber como configurar o ponto de extremidade privado de armazenamento para vNet, consulte [usar o portal do Azure para atribuir uma função do Azure para acesso a dados de BLOB e de fila](../security/how-to-connect-to-workspace-from-restricted-network.md).

Você pode usar o comando "nslookup \<storage-account-name\> . DFS.Core.Windows.net" para verificar a conectividade após o ponto de extremidade de armazenamento privado ser configurado. Ele deve retornar uma cadeia de caracteres semelhante a: " \<storage-account-name\> . privatelink.DFS.Core.Windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>O recurso de armazenamento não está atrás de uma vNet, mas o ponto de extremidade do serviço BLOB (AD do Azure) não está acessível devido ao firewall configurado

**Solução**: nesse caso, você precisa abrir sua conta de armazenamento no portal do Azure. No painel de navegação esquerdo, role para baixo até **suporte + solução de problemas** e selecione **verificação de conectividade** para verificar o status de conectividade do **serviço BLOB (AD do Azure)** . Se não estiver acessível, siga o guia promovido para verificar a configuração de **firewalls e redes virtuais** na página da sua conta de armazenamento. Para obter mais informações sobre firewalls de armazenamento, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Outros problemas a serem verificados 

* O recurso de armazenamento que você está acessando é Azure Data Lake Storage Gen2 e está atrás de um firewall e vNet (com o ponto de extremidade de armazenamento privado configurado) ao mesmo tempo.
* O recurso de contêiner que você está acessando foi excluído ou não existe.
* Cruzamento-locatário: o locatário do espaço de trabalho que o usuário usou para fazer logon não é o mesmo com o locatário da conta de armazenamento. 


## <a name="next-steps"></a>Próximas etapas
Se as etapas anteriores não ajudarem a resolver o problema, [crie um tíquete de suporte](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).