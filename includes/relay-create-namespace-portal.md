---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76020929"
---
1. Entre no [portal do Azure][Azure portal].
1. Selecione **Criar um recurso**. Em seguida, selecione **Integração** > **Retransmissão**. Se a opção **Retransmissão** não for exibida na lista, selecione **Ver Tudo** no canto superior direito.
1. Selecione **Criar** e insira um nome de namespace no campo **Nome**. O portal do Azure verifica se o nome está disponível.
1. Escolha uma assinatura do Azure na qual criar o namespace.
1. Em [Grupo de recursos](../articles/azure-resource-manager/management/manage-resource-groups-portal.md), escolha um grupo de recursos existente no qual colocar o namespace ou crie um.  
1. Selecione o país ou região no qual o namespace deve ser hospedado.

    ![Criar um namespace][create-namespace]

1. Selecione **Criar**. O portal do Azure cria seu namespace e o habilita. Depois de alguns minutos, o sistema provisiona recursos para sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gerenciamento

1. Selecione **Todos os recursos** e, então, escolha o nome do namespace recém-criado.
1. Selecione **Políticas de acesso compartilhado**.  
1. Em **Políticas de acesso compartilhado**, selecione **RootManageSharedAccessKey**.
1. Em **Política de SAS: RootManageSharedAccessKey**, clique no botão **Copiar** ao lado da opção **Cadeia de Conexão Primária**. Isso copiará a cadeia de conexão para a área de transferência para uso posterior. Cole esse valor no Bloco de notas ou em outro local temporário.
1. Repita a etapa anterior para copiar e colar o valor de **Chave primária** para um local temporário para uso posterior.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
