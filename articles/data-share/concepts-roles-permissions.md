---
title: Funções e requisitos do Azure Data Share
description: Saiba mais sobre as permissões necessárias para compartilhar e receber dados usando o compartilhamento de dados do Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a832c8956f7a3d4f8669209d7ed311e7555e1e75
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644251"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Funções e requisitos do Azure Data Share 

Este artigo descreve as funções e as permissões necessárias para compartilhar e receber dados usando o serviço de compartilhamento de dados do Azure. 

## <a name="roles-and-requirements"></a>Requisitos e funções

Com o serviço de compartilhamento de dados do Azure, você pode compartilhar dados sem trocar credenciais entre o provedor de dados e o consumidor. Para o compartilhamento baseado em instantâneo, o serviço de compartilhamento de dados do Azure usa identidades gerenciadas (anteriormente conhecidas como MSIs) para autenticar no repositório de dados do Azure. A identidade gerenciada do recurso de compartilhamento de dados do Azure precisa receber acesso ao armazenamento de dados do Azure para ler ou gravar dados.

Para compartilhar ou receber dados de um armazenamento de dados do Azure, o usuário precisa de pelo menos as permissões a seguir. 

* Permissão para gravar no armazenamento de dados do Azure. Normalmente, essa permissão existe na função **colaborador** .

Para o compartilhamento baseado em instantâneo do data Lake e armazenamento, você também precisa de permissão para criar a atribuição de função no armazenamento de dados do Azure. Normalmente, a permissão para criar atribuições de função existe na função **proprietário** , na função Administrador de acesso do usuário ou em uma função personalizada com a permissão *Microsoft. Authorization/role assignments/Write* atribuída. Essa permissão não será necessária se a identidade gerenciada do recurso de compartilhamento de dados já tiver acesso concedido ao armazenamento de dados do Azure. Veja abaixo um resumo das funções atribuídas à identidade gerenciada do recurso de compartilhamento de dados:

|**Tipo de repositório de dados**|**Armazenamento de dados de origem Provedor de Dados**|**Armazenamento de dados de destino de consumidor de dados**|
|---|---|---|
|Armazenamento do Blobs do Azure| Leitor de Dados do Blob de Armazenamento | Colaborador de dados de blob de armazenamento
|Azure Data Lake Gen1 | Proprietário | Sem suporte
|Azure Data Lake Gen2 | Leitor de Dados do Blob de Armazenamento | Colaborador de dados de blob de armazenamento
|

Para o compartilhamento baseado em instantâneo do SQL, um usuário do SQL precisa ser criado a partir de um provedor externo no banco de dados SQL do Azure com o mesmo nome que o recurso de compartilhamento do Azure data share. Azure Active Directory permissão de administrador é necessária para criar esse usuário. Veja abaixo um resumo da permissão exigida pelo usuário do SQL.

|**Tipo de banco de dados SQL**|**Permissão de usuário do Provedor de Dados SQL**|**Permissão de usuário SQL de consumidor de dados**|
|---|---|---|
|Banco de Dados SQL do Azure | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Provedor de dados
Para o compartilhamento baseado em instantâneo do data Lake e do armazenamento, para adicionar um conjunto de dados no compartilhamento do Azure data share, a identidade gerenciada do recurso de compartilhamento de dados do provedor precisa receber acesso ao armazenamento de dados do Azure de origem. Por exemplo, no caso da conta de armazenamento, a identidade gerenciada do recurso de compartilhamento de dados recebe a função de *leitor de dados de blob de armazenamento* . Isso é feito automaticamente pelo serviço de compartilhamento de dados do Azure quando o usuário está adicionando um conjunto por portal do Azure e o usuário tem a permissão apropriada. Por exemplo, o usuário é um proprietário do armazenamento de dados do Azure ou é um membro de uma função personalizada que tem a permissão *Microsoft. Authorization/role/Write* atribuída. 

Como alternativa, o usuário pode ter o proprietário do armazenamento de dados do Azure adicionar a identidade gerenciada do recurso de compartilhamento de dados ao armazenamento de dados do Azure manualmente. Essa ação só precisa ser executada uma vez por recurso de compartilhamento de dados. Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados manualmente, siga as etapas abaixo.  

1. Navegue até o armazenamento de dados do Azure.
1. Selecione **Controle de Acesso (IAM)** .
1. Selecione **Adicionar uma atribuição de função**.
1. Em *função*, selecione a função na tabela atribuição de função acima (por exemplo, para conta de armazenamento, selecione *leitor de dados de blob de armazenamento*).
1. Em *selecionar*, digite o nome do recurso de compartilhamento de dados do Azure.
1. Clique em *Save* (Salvar).

Para saber mais sobre a atribuição de função, consulte [atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md). Se você estiver compartilhando dados usando APIs REST, poderá criar uma atribuição de função usando a API referenciando [atribuir funções do Azure usando a API REST](../role-based-access-control/role-assignments-rest.md). 

Para o compartilhamento baseado em instantâneo do SQL, um usuário do SQL precisa ser criado a partir de um provedor externo no banco de dados SQL com o mesmo nome que o recurso de compartilhamento de data do Azure ao se conectar ao banco de dados SQL usando a autenticação Azure Active Directory. Esse usuário precisa receber a permissão *db_datareader* . Um script de exemplo junto com outros pré-requisitos para o compartilhamento baseado em SQL pode ser encontrado no tutorial [compartilhamento do banco de dados SQL do Azure ou do Azure Synapse Analytics](how-to-share-from-sql.md) . 

### <a name="data-consumer"></a>Consumidor de dados
Para receber dados na conta de armazenamento, a identidade gerenciada do recurso de compartilhamento de dados do consumidor precisa receber acesso à conta de armazenamento de destino. A identidade gerenciada do recurso de compartilhamento de dados precisa receber a função de *colaborador de dados de blob de armazenamento* . Isso é feito automaticamente pelo serviço de compartilhamento de dados do Azure se o usuário especificar uma conta de armazenamento de destino por meio de portal do Azure e o usuário tiver a permissão adequada. Por exemplo, o usuário é um proprietário da conta de armazenamento ou é um membro de uma função personalizada que tem a permissão *Microsoft. Authorization/role assignments/Write* atribuída. 

Como alternativa, o usuário pode ter o proprietário da conta de armazenamento para adicionar manualmente a identidade gerenciada do recurso de compartilhamento de dados à conta de armazenamento. Essa ação só precisa ser executada uma vez por recurso de compartilhamento de dados. Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados manualmente, siga as etapas abaixo. 

1. Navegue até o armazenamento de dados do Azure.
1. Selecione **Controle de Acesso (IAM)** .
1. Selecione **Adicionar uma atribuição de função**.
1. Em *função*, selecione a função na tabela atribuição de função acima (por exemplo, para conta de armazenamento, selecione *leitor de dados de blob de armazenamento*).
1. Em *selecionar*, digite o nome do recurso de compartilhamento de dados do Azure.
1. Clique em *Save* (Salvar).

Para saber mais sobre a atribuição de função, consulte [atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md). Se você estiver recebendo dados usando APIs REST, poderá criar uma atribuição de função usando a API referenciando [atribuir funções do Azure usando a API REST](../role-based-access-control/role-assignments-rest.md). 

Para o destino baseado em SQL, um usuário do SQL precisa ser criado a partir de um provedor externo no banco de dados SQL com o mesmo nome que o recurso de compartilhamento do Azure data ao se conectar ao banco de dado SQL usando a autenticação Azure Active Directory. Esse usuário precisa receber a permissão *db_datareader, db_datawriter db_ddladmin* . Um script de exemplo junto com outros pré-requisitos para o compartilhamento baseado em SQL pode ser encontrado no tutorial [compartilhamento do banco de dados SQL do Azure ou do Azure Synapse Analytics](how-to-share-from-sql.md) . 

## <a name="resource-provider-registration"></a>Registro do provedor de recursos 

Talvez seja necessário registrar manualmente o provedor de recursos Microsoft. DataShare em sua assinatura do Azure nos seguintes cenários: 

* Exibir o convite de compartilhamento de dados do Azure pela primeira vez no seu locatário do Azure
* Compartilhar dados de um armazenamento de dados do Azure em uma assinatura do Azure diferente do recurso de compartilhamento de dados do Azure
* Receber dados em um armazenamento de dados do Azure em uma assinatura do Azure diferente do recurso de compartilhamento de dados do Azure

Siga estas etapas para registrar o provedor de recursos Microsoft. DataShare em sua assinatura do Azure. Você precisa de acesso de *colaborador* à assinatura do Azure para registrar o provedor de recursos.

1. No portal do Azure, navegue até **assinaturas**.
1. Selecione a assinatura que você está usando para o compartilhamento de dados do Azure.
1. Clique em **provedores de recursos**.
1. Procure Microsoft. DataShare.
1. Clique em **Registrar**.
 
Para saber mais sobre o provedor de recursos, consulte [tipos e provedores de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as funções no Azure – [entender as definições de função do Azure](../role-based-access-control/role-definitions.md)