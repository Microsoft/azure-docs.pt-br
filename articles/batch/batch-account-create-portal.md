---
title: Criar uma conta do Lote no portal do Azure | Microsoft Docs
description: Aprenda a criar uma conta do Lote do Azure no portal do Azure para executar cargas de trabalho paralelas em larga escala na nuvem.
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7629e496f2d73798b94acdc611014a8b3afead7
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Lote com o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [.NET de Gerenciamento do Lote](batch-management-dotnet.md)
>
>

Saiba como criar uma conta do Lote do Azure no [portal do Azure][azure_portal] e escolha as propriedades da conta que se ajustam ao seu cenário de computação. Saiba onde encontrar propriedades da conta importantes, como chaves de acesso e URLs de conta.

Para saber mais sobre contas do Lote e cenários, confira a [visão geral do recurso](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Criar uma conta do Batch



1. Entre no [Portal do Azure][azure_portal].
2. Clique em **Novo** e pesquise no Marketplace por **Serviço de Lote**.

    ![Lote no Marketplace][marketplace_portal]
3. Selecione **Serviço de Lote**, clique em **Criar** e insira as configurações da **Nova conta do Lote**. Confira os seguintes detalhes.

    ![Criar uma conta do Batch][account_portal]

    a. **Nome da conta**: o nome que você escolher deve ser exclusivo dentro da região do Azure onde a conta é criada (confira **Local** abaixo). O nome da conta pode conter apenas minúsculas ou números e deve ter 3 a 24 caracteres de comprimento.

    b. **Assinatura**: a assinatura na qual a conta do Lote será criada. Se você tiver somente uma assinatura, ela será selecionada por padrão.

    c. **Modo de alocação de pool**: se essa configuração for exibida, aceite o padrão **Serviço de Lote**.

    c. **Grupo de recursos**: selecione um grupo de recursos para sua nova conta do Lote ou, opcionalmente, crie um novo.

    d. **Local**: a região do Azure na qual a conta do Lote será criada. Somente as regiões com suporte da sua assinatura e do seu grupo de recursos são exibidas como opções.

    e. **Conta de armazenamento** (opcional): uma conta do Armazenamento do Azure de uso geral que você associa à sua nova conta do Lote. Isso é recomendado para a maioria das contas do Lote. Confira [Conta do Armazenamento do Azure vinculada](#linked-azure-storage-account) mais adiante neste artigo para obter detalhes.

4. Clique em **Criar** para criar a conta.



## <a name="view-batch-account-properties"></a>Exibir propriedades de conta do Lote
Após a criação da conta, clique na conta para acessar suas configurações e propriedades. Você pode acessar todas as propriedades e configurações de conta usando o menu à esquerda.

![Folha Conta do Lote no portal do Azure][account_blade]

* **URL de conta do Lote**: ao desenvolver um aplicativo com as [APIs do Lote](batch-apis-tools.md#azure-accounts-for-batch-development), você precisa de uma URL de conta para acessar os recursos do Lote. Uma URL de conta do Lote tem o seguinte formato:

    `https://<account_name>.<region>.batch.azure.com`

![URL de conta do Lote no portal][account_url]

* **Chaves de acesso**: para autenticar o acesso à conta do Lote do aplicativo, você pode usar uma chave de acesso da conta. (O Lote também dá suporte à autenticação do Azure Active Directory.)

    Para exibir ou regenerar as chaves de acesso, selecione **Chaves**.

    ![Chaves de conta do Lote no portal do Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Conta vinculada do Armazenamento do Azure

Você pode vincular uma conta de Armazenamento do Azure de finalidade geral à sua nova conta do Lote, o que é útil para várias situações. O recurso de [pacotes de aplicativos](batch-application-packages.md) do Lote usa o armazenamento de Blobs do Azure, como faz a biblioteca [.NET de Convenções de Arquivo do Lote](batch-task-output.md). Esses recursos opcionais ajudarão você a implantar os aplicativos executados por suas tarefas do Lote, persistindo os dados que eles produzem.

É recomendável que você crie uma nova conta de armazenamento exclusivamente para uso com sua conta do Lote. O Lote do Azure atualmente dá suporte apenas ao tipo de conta de Armazenamento de uso geral. Esse tipo de conta é descrito na etapa 5, [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md).

![Criando uma conta de armazenamento de uso geral][storage_account]

> [!NOTE]
> Tome cuidado ao regenerar as chaves de acesso de uma conta de Armazenamento vinculada. Regenere somente uma chave de conta do Armazenamento e clique em **Sincronizar Chaves** na folha Conta do Armazenamento vinculada. Aguarde cinco minutos para permitir que as chaves sejam propagadas para os nós de computação em seus pools e regenere e sincronize a outra chave, se necessário. Se você regenerar as chaves ao mesmo tempo, os nós de computação não poderão sincronizar nenhuma delas e, assim, perderão o acesso à conta de armazenamento.
>
>

![Regeneração de chaves da conta de armazenamento][4]

## <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote
Como aconteceu com sua assinatura do Azure e outros serviços do Azure, determinados [limites e cotas](batch-quota-limit.md) se aplicam a contas do Lote. As cotas atuais para uma conta do Lote aparecem em **Cotas**.

![Cotas de conta do Lote no portal do Azure][quotas]



Adicionalmente, muitas dessas cotas podem ser aumentadas com uma solicitação de suporte do produto gratuito enviada no Portal do Azure. Confira [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter detalhes sobre a solicitação de aumentos de cota.

## <a name="other-batch-account-management-options"></a>Outras opções de gerenciamento de conta do Lote
Além de usar o portal do Azure, você pode criar e gerenciar contas do Lote com o seguinte:

* [Cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](batch-cli-get-started.md)
* [.NET de Gerenciamento do Lote](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximas etapas
* Veja a [visão geral dos recursos do Lote](batch-api-basics.md) para saber mais sobre os conceitos e os recursos do serviço do Lote. O artigo aborda os recursos principais do Lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço para cargas de trabalho de computação em larga escala.
* Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](batch-dotnet-get-started.md) ou do [Python](batch-python-tutorial.md). O artigo introdutório orienta você por meio de um aplicativo de trabalho que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação e que inclui o uso do Armazenamento do Azure para preparação e recuperação de um arquivo de carga de trabalho.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regeneração de chaves da conta de armazenamento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png
