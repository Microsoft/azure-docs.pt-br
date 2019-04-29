---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419676"
---
1. Entre no [Portal do Azure][Azure portal].
2. No menu esquerdo, selecione **+ Criar um recurso**. Em seguida, selecione **Integração** > **Retransmissão**. Se a opção **Retransmissão** não for exibida na lista, selecione **Ver Tudo** no canto superior direito. 
3. Em **Criar um namespace**, insira um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
4. Na caixa **Assinatura**, selecione uma assinatura do Azure na qual criar o namespace.
5. Na caixa [Grupo de recursos](../articles/azure-resource-manager/manage-resource-groups-portal.md), selecione um grupo de recursos existente no qual colocar o namespace, ou então crie um novo.  
6. Em **Localização**, selecione o país ou região no qual o namespace deve ser hospedado.
   
    ![Criar um namespace][create-namespace]
7. Selecione **Criar**. O sistema cria o seu namespace e o habilita. Depois de alguns minutos, o sistema provisiona recursos para sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gerenciamento

1. Selecione **Todos os recursos** e depois selecione o nome do namespace recém-criado.
2. Em Namespace da Retransmissão, clique em **Políticas de acesso compartilhado**.  
3. Em **Políticas de acesso compartilhado**, selecione **RootManageSharedAccessKey**.
   
    ![informações de conexão][connection-info]
4. Em **Política: RootManageSharedAccessKey**, selecione o botão **Cópia** ao lado de **Cadeia de conexão – Chave primária**. Isso copiará a cadeia de conexão para a área de transferência para uso posterior. Cole esse valor no Bloco de notas ou em outro local temporário.
   
    ![connection-string][connection-string]

5. Repita a etapa anterior para copiar e colar o valor de **Chave primária** para um local temporário para uso posterior.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
