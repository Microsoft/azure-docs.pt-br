---
title: Como criar, gerenciar ou excluir uma conta de armazenamento no Portal do Azure | Microsoft Docs
description: Crie uma nova conta de armazenamento, gerencie as chaves de acesso de sua conta ou exclua uma conta de armazenamento no Portal do Azure. Saiba mais sobre contas de armazenamento standard e premium.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 87c37da0-6cc6-4d88-a330-ef2896a1531d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
f1_keywords: sql13.swb.windowsazurestorage.connect.f1
ms.date: 01/23/2017
ms.author: tamram
ms.openlocfilehash: cdca35f8ae339d02b44490cd61e2684d81d88365
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="about-azure-storage-accounts"></a>Sobre as contas de armazenamento do Azure
[!INCLUDE [storage-selector-portal-create-storage-account](../../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Visão geral
Uma conta de armazenamento do Azure fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure. Todos os objetos em uma conta de armazenamento são cobrados juntos como um grupo. Por padrão, os dados em sua conta estão disponíveis somente para você, o proprietário da conta.

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Cobrança de conta de armazenamento
[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

> [!NOTE]
> Ao criar uma máquina virtual do Azure, uma conta de armazenamento será criada automaticamente no local da implantação se você ainda não tiver uma conta de armazenamento nesse local. Assim, não é necessário seguir as etapas abaixo para criar uma conta de armazenamento para seus discos da máquina virtual. O nome da conta de armazenamento será baseado no nome da máquina virtual. Confira a [Documentação de máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.
> 
> 

## <a name="storage-account-endpoints"></a>Pontos de extremidade da conta de armazenamento
Cada objeto armazenado no Armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento forma o subdomínio desse endereço. A combinação do nome do subdomínio e do domínio, que é específico a cada serviço, forma um *ponto de extremidade* para a sua conta de armazenamento.

Por exemplo, se a sua conta de armazenamento se chamar *mystorageaccount*, os pontos de extremidade padrão para a conta de armazenamento serão:

* Serviço Blob: http://*mystorageaccount*.blob.core.windows.net
* Serviço Tabela: http://*mystorageaccount*.table.core.windows.net
* Serviço Fila: http://*mystorageaccount*.queue.core.windows.net
* Serviço Arquivo: http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> Uma conta de armazenamento de Blobs expõe apenas o ponto de extremidade do serviço Blob.
> 
> 

A URL para acessar um objeto em uma conta de armazenamento é compilada acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também é possível configurar um nome de domínio para usar com a conta de armazenamento. Para saber mais, confira [Configurar um nome de domínio personalizado para seu Ponto de Extremidade do Armazenamento de Blobs](../blobs/storage-custom-domain-name.md). Também é possível configurá-lo com o PowerShell. Para saber mais, confira o cmdlet [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) .  


## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Novo** -> **Armazenamento** -> **Conta de armazenamento**.
3. Insira um nome para a conta de armazenamento. Confira [Pontos de extremidade da conta de armazenamento](#storage-account-endpoints) para saber como o nome da conta de armazenamento será usado em seus objetos no Armazenamento do Azure.
   
   > [!NOTE]
   > Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
   > 
   > O nome da sua conta de armazenamento deve ser exclusivo no Azure. O portal do Azure indicará se o nome da conta de armazenamento selecionada já está em uso.
   > 
   > 
4. Especifique o modelo de implantação a ser usado: **Resource Manager** ou **Clássico**. **Gerenciador de Recursos** é o modelo de implantação recomendado. Para saber mais, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../../azure-resource-manager/resource-manager-deployment-model.md).
   
   > [!NOTE]
   > As contas de armazenamento de Blobs só podem ser criadas usando o modelo de implantação do Resource Manager.

5. Selecione o tipo de conta de armazenamento: **Finalidade geral** ou **Armazenamento de blobs**. **Finalidade geral** é o padrão.
   
    Se **Finalidade geral** tiver sido selecionado, especifique a camada de desempenho: **Standard** ou **Premium**. O padrão é **Standard**. Para saber mais sobre as contas de armazenamento standard e premium, confira [Introdução ao Armazenamento do Microsoft Azure](storage-introduction.md) e [Armazenamento Premium: Armazenamento de Alto Desempenho para Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage.md).
   
    Se **Armazenamento de Blobs** tiver sido selecionado, especifique o tipo de acesso: **Dinâmico** ou **Estático**. O padrão é **Dinâmica**. Confira [Armazenamento de Blobs do Azure: camadas Estática e Dinâmica](../blobs/storage-blob-storage-tiers.md) para saber mais.
6. Selecione a opção de replicação para a conta de armazenamento:**LRS**, **GRS**, **RA-GRS** ou **ZRS**. O padrão é **RA-GRS**. Para obter mais detalhes sobre as opções de replicação do Armazenamento do Azure, confira [Replicação do Armazenamento do Azure](storage-redundancy.md).
7. Selecione a assinatura na qual você deseja criar a nova conta de armazenamento.
8. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para saber mais sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
9. Selecione a região geográfica para sua conta de armazenamento. Veja [Regiões do Azure](https://azure.microsoft.com/regions/#services) para saber mais sobre quais serviços estão disponíveis em qual região.
10. Clique em **Criar** para criar a conta de armazenamento.

## <a name="manage-your-storage-account"></a>Gerenciar sua conta de armazenamento
### <a name="change-your-account-configuration"></a>Alterar a configuração da conta
Após criar a conta de armazenamento, você pode modificar sua configuração, por exemplo, alterando a opção de replicação usada para a conta ou a alterando a camada de acesso para uma conta de armazenamento de Blobs. No [portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento, encontre e clique em **Configuração** e clique em **CONFIGURAÇÕES** para exibir e/ou alterar a configuração da conta.

> [!NOTE]
> Dependendo do nível de desempenho que você escolheu durante a criação da conta de armazenamento, talvez algumas opções de replicação não estejam disponíveis.
> 
> 

A alteração da opção de replicação mudará seu preço. Para saber mais, veja a página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) .

Para contas de armazenamento de Blobs, a alteração da camada de acesso pode incorrer em encargos para a alteração, além de alterar seu preço. Veja as [Contas de armazenamento de blobs - Preços e Cobrança](../blobs/storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

### <a name="manage-your-storage-access-keys"></a>Gerenciar as chaves de acesso de armazenamento
Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

> [!NOTE]
> Recomendamos que você evite compartilhar suas chaves de acesso de armazenamento com outras pessoas. Para permitir o acesso a recursos de armazenamento sem dar as suas chaves de acesso, você pode usar uma *assinatura de acesso compartilhado*. Uma assinatura de acesso compartilhado fornece acesso a um recurso na sua conta para um intervalo que você definir e com as permissões que você especificar. Confira [Usando Assinaturas de Acesso Compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md) para saber mais.
> 
> 
<a id="view-and-copy-storage-access-keys"/></a>
#### <a name="view-and-copy-storage-access-keys"></a>Exibir e copiar as chaves de acesso de armazenamento
No [Portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento, clique em **Todas as configurações** e clique em **Chaves de acesso** para exibir, copiar e gerar novamente as chaves de acesso de sua conta. A folha **Chaves de Acesso** também inclui cadeias de conexão pré-configuradas usando suas chaves primária e secundária, que você pode copiar para usar em seus aplicativos.

#### <a name="regenerate-storage-access-keys"></a>Regenerar chaves de acesso de armazenamento
Você deve alterar as chaves de acesso de sua conta de armazenamento periodicamente para ajudar a manter as conexões de armazenamento seguras. Duas chaves de acesso são atribuídas para que você possa manter conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra.

> [!WARNING]
> A regeneração das chaves de acesso pode afetar os serviços no Azure, bem como seus próprios aplicativos que dependem da conta de armazenamento. Todos os clientes que usam a chave de acesso para acessar a conta de armazenamento precisam ser atualizados para usar a nova chave.
> 
> 

**Serviços de mídia** - se algum serviço de mídia depender de sua conta de armazenamento, você deverá ressincronizar as chaves de acesso com o serviço de mídia depois de regenerá-las.

**Aplicativos** - se você tiver algum aplicativo Web ou serviços de nuvem que usam a conta de armazenamento, perderá as conexões se regenerar as chaves, a menos que você as reverta.

**Gerenciadores de Armazenamento** - se você estiver usando qualquer [aplicativo do gerenciador de armazenamento](storage-explorers.md), provavelmente precisará atualizar a chave de armazenamento usada por esses aplicativos.

Veja o processo para trocar suas chaves de acesso de armazenamento:

1. Atualize as cadeias de conexão no código do aplicativo para fazer referência à chave de acesso secundária da conta de armazenamento.
2. Regenere a chave de acesso primária de sua conta de armazenamento. Na folha **Chaves de Acesso**, clique em **Regenerar Chave1** e clique em **Sim** para confirmar que você deseja gerar uma nova chave.
3. Atualize as cadeias de conexão em seu código para fazer referência à nova chave de acesso primária.
4. Regenere a chave de acesso secundária da mesma maneira.

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento
Para remover uma conta de armazenamento que você não está mais usando, navegue até a conta de armazenamento do [portal do Azure](https://portal.azure.com)e clique em **Excluir**. A exclusão de uma conta de armazenamento exclui a conta inteira, incluindo todos os dados na conta.

> [!WARNING]
> Não é possível restaurar uma conta de armazenamento excluída nem recuperar nenhuma parte de seu conteúdo antes da exclusão. Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Isso também é verdadeiro para todos os recursos na conta – depois que você excluir um blob, tabela, fila ou arquivo, ele será excluído permanentemente.
> 

Se você tentar excluir uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro indicando que a conta de armazenamento ainda está em uso. Para obter ajuda com a solução desse erro, confira [Solucionar erros ao excluir contas de armazenamento](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Próximas etapas
* [O Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
* [Armazenamento de Blobs do Azure: camadas estáticas e dinâmicas](../blobs/storage-blob-storage-tiers.md)
* [Replicação de Armazenamento do Azure](storage-redundancy.md)
* [Configurar cadeias de conexão do Armazenamento do Azure](../storage-configure-connection-string.md)
* [Transferir dados com o Utilitário da Linha de Comando AzCopy](storage-use-azcopy.md)
* Visite o [Blog da equipe do Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).

