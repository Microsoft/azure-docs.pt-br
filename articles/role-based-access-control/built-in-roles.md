---
title: Funções internas do Azure - RBAC do Azure
description: Este artigo descreve as funções internas do Azure para o controle de acesso baseado em função do Azure (RBAC do Azure). Ele lista Actions, NotActions, DataActions e NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 02/25/2021
ms.custom: generated
ms.openlocfilehash: baa783d0dfb54e0e606e3f6c6d5397941ae7b7bc
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011622"
---
# <a name="azure-built-in-roles"></a>Funções internas do Azure

[O controle de acesso baseado em função do Azure (RBAC do Azure)](overview.md) tem várias funções internas do Azure que você pode atribuir a usuários, grupos, entidades de serviço e identidades gerenciadas. Atribuições de função são a maneira de controlar o acesso aos recursos do Azure. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar [funções personalizadas do Azure](custom-roles.md) próprias.

Este artigo lista as funções internas do Azure, que estão sempre em evolução. Para obter as funções mais recentes, use [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [az role definition list](/cli/azure/role/definition#az-role-definition-list). Se você estiver procurando por funções de administrador para Azure Active Directory (AD do Azure), confira [Permissões de função de administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md).

A tabela a seguir fornece uma breve descrição e a ID exclusiva de cada função interna. Clique no nome de função para ver a lista de `Actions`, `NotActions`, `DataActions` e `NotDataActions` para cada função. Para obter informações sobre o que essas ações significam e como elas se aplicam aos planos de gerenciamento e de dados, consulte [Entender as definições de função do Azure](role-definitions.md).

## <a name="all"></a>Todos

> [!div class="mx-tableFixed"]
> | Função interna | Descrição | ID |
> | --- | --- | --- |
> | **Geral** |  |  |
> | [Colaborador](#contributor) | Concede acesso completo para gerenciar todos os recursos, mas não permite que você atribua funções no RBAC do Azure, gerencie atribuições em plantas do Azure ou compartilhe galerias de imagens. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Proprietário](#owner) | Concede acesso completo para gerenciar todos os recursos, incluindo a capacidade de atribuir funções no RBAC do Azure. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Leitor](#reader) | Exibir todos os recursos, mas não permite que você faça nenhuma alteração. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrador de Acesso do Usuário](#user-access-administrator) | Permite que você gerencie o acesso do usuário aos recursos do Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Computação** |  |  |
> | [Colaborador de Máquina Virtual Clássica](#classic-virtual-machine-contributor) | Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Logon de administrador da Máquina Virtual](#virtual-machine-administrator-login) | Máquinas Virtuais do Microsoft Azure no portal e logon como administrador | 1c0163c0-47E6-4577-8991-ea5c82e286e4 |
> | [Colaborador de Máquina Virtual](#virtual-machine-contributor) | Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Logon de usuário da Máquina Virtual](#virtual-machine-user-login) | Visualize as Máquinas Virtuais do Microsoft Azure no portal e faça logon como usuário. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Rede** |  |  |
> | [Colaborador de ponto de extremidade de CDN](#cdn-endpoint-contributor) | Pode gerenciar os pontos de extremidade de CDN, mas não pode conceder acesso a outros usuários. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Leitor de ponto de extremidade de CDN](#cdn-endpoint-reader) | Pode exibir os pontos de extremidade de CDN, mas não pode fazer alterações. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Colaborador de perfil de CDN](#cdn-profile-contributor) | Pode gerenciar os perfis de CDN e os respectivos pontos de extremidade, mas não pode conceder acesso a outros usuários. | ec156ff8-a8d1-4d15-830C-5b80698ca432 |
> | [Leitor de perfis de CDN](#cdn-profile-reader) | Pode exibir os perfis de CDN e os respectivos pontos de extremidade, mas não pode fazer alterações. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Colaborador de rede clássica](#classic-network-contributor) | Permite que você gerencie redes clássicas, mas não acessá-las. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Colaborador de zona DNS](#dns-zone-contributor) | Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Colaborador de rede](#network-contributor) | Permite gerenciar redes, mas não acessá-las. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Colaborador de zona de DNS privado](#private-dns-zone-contributor) | Permite que você gerencie recursos de zona DNS privada, mas não as redes virtuais às quais eles estão vinculados. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Colaborador do Gerenciador de Tráfego](#traffic-manager-contributor) | Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Colaborador do Avere](#avere-contributor) | Pode criar e gerenciar um cluster do Avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operador do Avere](#avere-operator) | Usado pelo cluster do Avere vFXT para gerenciar o cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Colaborador de Backup](#backup-contributor) | Permite que você gerencie o serviço de backup, mas não pode criar cofres e fornecer acesso a outras pessoas | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operador de Backup](#backup-operator) | Permite que você gerencie serviços de backup, exceto a remoção de backup, a criação de cofres e o fornecimento de acesso a outras pessoas | 00c29273-979b-4161-815C-10b084fb9324 |
> | [Leitor de Backup](#backup-reader) | Pode exibir serviços de backup, mas não pode fazer alterações | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Colaborador da conta de armazenamento clássica](#classic-storage-account-contributor) | Permite que você gerencie contas de armazenamento clássico, mas não acessá-las. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](#classic-storage-account-key-operator-service-role) | Os Operadores da Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves nas Contas de Armazenamento Clássicas | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Colaborador do Data Box](#data-box-contributor) | Permite que você gerencie tudo sob o serviço Data Box exceto fornecer acesso a outras pessoas. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Leitor do Data Box](#data-box-reader) | Permite que você gerencie o serviço do Azure Data Box, exceto a ordem de criação ou edição de detalhes do pedido e fornecer acesso a outras pessoas. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Desenvolvedor do Data Lake Analytics](#data-lake-analytics-developer) | Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Acesso a Dados e Leitor](#reader-and-data-access) | Permite que você exiba tudo, mas não permitirá que exclua ou crie uma conta de armazenamento ou um recurso contido. Ele também permitirá o acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio de acesso às chaves de conta de armazenamento. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Colaborador da Conta de Armazenamento](#storage-account-contributor) | Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Função do Serviço de Operador da Chave da Conta de Armazenamento](#storage-account-key-operator-service-role) | Permite listar e regenerar chaves de acesso da conta de armazenamento. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Colaborador de dados de blob de armazenamento](#storage-blob-data-contributor) | Ler, gravar e excluir contêineres e blobs de Armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Proprietário de Dados do Blob de Armazenamento](#storage-blob-data-owner) | Fornece acesso completo aos dados e contêineres de blob do Armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Leitor de Dados do Blob de Armazenamento](#storage-blob-data-reader) | Leia e liste contêineres e blobs do Armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegador de blob de armazenamento](#storage-blob-delegator) | Obtenha uma chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou blob que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [Criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Colaborador de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-contributor) | Permite o acesso de leitura, gravação e exclusão em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função não tem equivalente interno nos servidores de arquivos do Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-elevated-contributor) | Permite ler, gravar, excluir e modificar ACLs em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de alteração em servidores de arquivos do Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Leitor de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-reader) | Permite acesso de leitura em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de leitura em servidores de arquivos do Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Colaborador de dados da fila de armazenamento](#storage-queue-data-contributor) | Lê, grava e exclui filas do Armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processador de mensagens de dados da fila de armazenamento](#storage-queue-data-message-processor) | Espia, recupera e exclui uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Remetente da mensagem de dados da fila de armazenamento](#storage-queue-data-message-sender) | Adiciona mensagens a uma fila de Armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Leitor de dados da fila de armazenamento](#storage-queue-data-reader) | Lê e lista as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Colaborador de dados do Azure Maps](#azure-maps-data-contributor) | Concede acesso ao acesso de leitura, gravação e exclusão para mapear dados relacionados de uma conta do Azure Maps. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Leitor de dados do Azure Mapas](#azure-maps-data-reader) | Concede acesso para ler dados relacionados ao mapa de uma conta do Azure Mapas. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Colaborador do Serviço de Pesquisa](#search-service-contributor) | Permite gerenciar serviços de pesquisa, mas não acessá-las. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Leitor de AccessKey do signalr](#signalr-accesskey-reader) | Ler chaves de acesso do serviço Signalr | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Servidor de aplicativo signalr (visualização)](#signalr-app-server-preview) | Permite que o servidor de aplicativos acesse o serviço de Signaler com opções de autenticação do AAD. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Colaborador do signalr](#signalr-contributor) | Criar, ler, atualizar e excluir recursos do serviço Signalr | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Colaborador sem servidor do signalr (versão prévia)](#signalr-serverless-contributor-preview) | Permite que seu aplicativo acesse o serviço no modo sem servidor com opções de autenticação do AAD. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [Proprietário do serviço signalr (versão prévia)](#signalr-service-owner-preview) | Acesso completo às APIs REST do serviço de Signaler do Azure | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Leitor de serviço do signalr (visualização)](#signalr-service-reader-preview) | Acesso somente leitura às APIs REST do serviço de Signaler do Azure | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Colaborador do Plano de Web](#web-plan-contributor) | Permite gerenciar os planos da Web para sites, mas não o acesso a eles. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Colaborador do Site](#website-contributor) | Permite gerenciar sites (não planos da Web), mas não acessá-los. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Contêineres** |  |  |
> | [AcrDelete](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | signatário de imagem ACR | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | leitor de dados de quarentena acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | gravador de dados de quarentena acr | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Função de Administrador do Cluster do Serviço de Kubernetes do Azure](#azure-kubernetes-service-cluster-admin-role) | Liste a ação de credencial de administrador de cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Função de Usuário do Cluster do Serviço de Kubernetes do Azure](#azure-kubernetes-service-cluster-user-role) | Liste a ação de credencial de usuário de cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Função colaborador do serviço kubernetes do Azure](#azure-kubernetes-service-contributor-role) | Concede acesso para ler e gravar clusters do serviço kubernetes do Azure | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Administrador de RBAC do serviço kubernetes do Azure](#azure-kubernetes-service-rbac-admin) | Permite que você gerencie todos os recursos em cluster/namespace, exceto atualizar ou excluir cotas de recursos e namespaces. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Administrador de cluster do RBAC do serviço kubernetes do Azure](#azure-kubernetes-service-rbac-cluster-admin) | Permite que você gerencie todos os recursos no cluster. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Leitor de RBAC do serviço kubernetes do Azure](#azure-kubernetes-service-rbac-reader) | Permite acesso somente leitura para ver a maioria dos objetos em um namespace. Ele não permite a exibição de funções ou associações de função. Essa função não permite a exibição de segredos, pois a leitura do conteúdo de segredos permite o acesso a credenciais de uma conta no namespace, o que permitiria o acesso à API como qualquer uma das contas no namespace (uma forma de elevação de privilégio). A aplicação dessa função no escopo do cluster fornecerá acesso em todos os namespaces. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Gravador RBAC do serviço kubernetes do Azure](#azure-kubernetes-service-rbac-writer) | Permite acesso de leitura/gravação à maioria dos objetos em um namespace. Essa função não permite exibir ou modificar funções ou associações de função. No entanto, essa função permite acessar segredos e executar pods como qualquer uma das contas no namespace, para que possa ser usada para obter os níveis de acesso de API de qualquer conta no namespace. A aplicação dessa função no escopo do cluster fornecerá acesso em todos os namespaces. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Bancos de dados** |  |  |
> | [Função de leitor de conta do Cosmos DB](#cosmos-db-account-reader-role) | Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operador de Cosmos DB](#cosmos-db-operator) | Permite que você gerencie contas do Azure Cosmos DB, mas não acesse os dados nelas. Impede o acesso a chaves de conta e cadeias de conexão. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Pode enviar solicitação de restauração de um banco de dados Cosmos DB ou de um contêiner em uma conta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | É possível executar a ação de restauração para Cosmos DB conta de banco de dados com o modo de backup contínuo | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [Colaborador de Conta do DocumentDB](#documentdb-account-contributor) | Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Colaborador do Cache Redis](#redis-cache-contributor) | Permite gerenciar caches Redis, mas não acessá-los. | e0f68234-74aa-48ED-b826-c38b57376e17 |
> | [Colaborador do banco de dados SQL](#sql-db-contributor) | Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Colaborador da instância gerenciada do SQL](#sql-managed-instance-contributor) | Permite que você gerencie instâncias gerenciadas do SQL e a configuração de rede necessária, mas não pode conceder acesso a outras pessoas. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gerenciador de Segurança do SQL](#sql-security-manager) | Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Colaborador do SQL Server](#sql-server-contributor) | Permite gerenciar servidores e bancos de dados SQL, mas não acessá-los, nem as políticas relacionadas à segurança. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analytics** |  |  |
> | [Proprietário de dados dos Hubs de Eventos do Azure](#azure-event-hubs-data-owner) | Permite acesso completo aos recursos dos Hubs de Eventos do Azure. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Receptor de dados dos Hubs de Eventos do Azure](#azure-event-hubs-data-receiver) | Permite acesso de recebimento aos recursos dos Hubs de Eventos do Azure. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Remetente de dados dos Hubs de Eventos do Azure](#azure-event-hubs-data-sender) | Permite acesso de envio aos recursos dos Hubs de Eventos do Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Colaborador da fábrica de dados](#data-factory-contributor) | Cria e gerencia data factories, assim como os recursos filhos neles. | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | [Limpador de Dados](#data-purger) | Exclua dados privados de um espaço de trabalho Log Analytics. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operador de Cluster do HDInsight](#hdinsight-cluster-operator) | Permite que você leia e modifique as configurações de cluster do HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Colaborador dos serviços de domínio do HDInsight](#hdinsight-domain-services-contributor) | Pode ler, criar, modificar e excluir operações relacionadas aos serviços de domínio necessárias para o Enterprise Security Package do HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Colaborador do Log Analytics](#log-analytics-contributor) | O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Leitor do Log Analytics](#log-analytics-reader) | Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Curador de dados alcance](#purview-data-curator) | O curador de dados Microsoft. alcance pode criar, ler, modificar e excluir objetos de dados de catálogo e estabelecer relações entre objetos. Esta função está em visualização e está sujeita a alterações. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Leitor de dados do alcance](#purview-data-reader) | O leitor de dados Microsoft. alcance pode ler objetos de dados do catálogo. Esta função está em visualização e está sujeita a alterações. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Administrador de fonte de dados alcance](#purview-data-source-administrator) | O administrador da fonte de dados Microsoft. alcance pode gerenciar fontes de dados e verificações de dados. Esta função está em visualização e está sujeita a alterações. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Colaborador do Registro de Esquema (Versão Prévia)](#schema-registry-contributor-preview) | Ler, gravar e excluir grupos e esquemas do Registro de Esquema. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Leitor do Registro de Esquema (Versão Prévia)](#schema-registry-reader-preview) | Ler e listar os grupos e os esquemas do Registro de Esquema. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Acesso ao nó de membro Blockchain (versão prévia)](#blockchain-member-node-access-preview) | Permite acesso a nós de Membro do Blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **IA + aprendizado de máquina** |  |  |
> | [Colaborador dos Serviços Cognitivos](#cognitive-services-contributor) | Permite criar, ler, atualizar, excluir e gerenciar chaves dos Serviços Cognitivos. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Visão Personalizada colaborador de serviços cognitivas](#cognitive-services-custom-vision-contributor) | Acesso completo ao projeto, incluindo a capacidade de exibir, criar, editar ou excluir projetos. | c1ff6cc2-C111-46fe-8896-e0ef812ad9f3 |
> | [Serviços cognitivas Visão Personalizada implantação](#cognitive-services-custom-vision-deployment) | Publicar, cancelar publicação ou exportar modelos. A implantação pode exibir o projeto, mas não pode atualizar. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Serviços cognitivas Visão Personalizada Labeler](#cognitive-services-custom-vision-labeler) | Exiba, Edite imagens de treinamento e crie, adicione, remova ou exclua as marcas de imagem. Rotuladores podem exibir o projeto, mas não podem atualizar nada além de imagens e marcas de treinamento. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Visão Personalizada leitor de serviços cognitivas](#cognitive-services-custom-vision-reader) | Ações somente leitura no projeto. Os leitores não podem criar nem atualizar o projeto. | 93586559-c37d-4a6b-BA08-b9f0940c2d73 |
> | [Serviços cognitivas Visão Personalizada treinador](#cognitive-services-custom-vision-trainer) | Exiba, edite projetos e treine os modelos, incluindo a capacidade de publicar, cancelar a publicação e exportar os modelos. Os treinadores não podem criar ou excluir o projeto. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Leitor de Dados de Serviços Cognitivos (Versão Prévia)](#cognitive-services-data-reader-preview) | Permite que você leia os dados dos Serviços Cognitivos. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Administrador do Orientador de métricas de serviços cognitivas](#cognitive-services-metrics-advisor-administrator) | Acesso completo ao projeto, incluindo a configuração no nível do sistema. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Editor de QnA Maker de serviços cognitivas](#cognitive-services-qna-maker-editor) | Permite criar, editar, importar e exportar um KB. Não é possível publicar ou excluir um KB. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [QnA Maker leitor de serviços cognitivas](#cognitive-services-qna-maker-reader) | Vamos ler e testar apenas um KB. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Usuário dos Serviços Cognitivos](#cognitive-services-user) | Permite ler e listar as chaves dos Serviços Cognitivos. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Realidade misturada** |  |  |
> | [Administrador de renderização remoto](#remote-rendering-administrator) | Fornece ao usuário recursos de conversão, gerenciamento de sessão, renderização e diagnóstico para renderização remota do Azure | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Cliente de renderização remota](#remote-rendering-client) | Fornece ao usuário recursos de gerenciamento de sessão, renderização e diagnóstico para a renderização remota do Azure. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Colaborador da conta de âncoras espaciais](#spatial-anchors-account-contributor) | Permite que você gerencie âncoras espaciais em sua conta, exceto excluí-las | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Proprietário da conta das âncoras espaciais](#spatial-anchors-account-owner) | Permite gerenciar âncoras espaciais em sua conta, inclusive excluí-las | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Leitor de conta de âncoras espaciais](#spatial-anchors-account-reader) | Permite localizar e ler propriedades de âncoras espaciais em sua conta | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integração** |  |  |
> | [Colaborador de serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerenciar o serviço e as APIs | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Função do operador de serviço de gerenciamento da API](#api-management-service-operator-role) | Pode gerenciar serviços, mas não as APIs | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Função do leitor do serviço de gerenciamento da API](#api-management-service-reader-role) | Acesso somente leitura ao serviço e APIs | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Proprietário de dados da configuração de aplicativos](#app-configuration-data-owner) | Permite o acesso completo aos dados de Configuração de Aplicativos. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Leitor de dados da configuração de aplicativos](#app-configuration-data-reader) | Permite o acesso de leitura aos dados de Configuração de Aplicativos. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Proprietário de dados do Barramento de Serviço do Azure](#azure-service-bus-data-owner) | Permite acesso completo aos recursos do Barramento de Serviço do Azure. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Receptor de dados do Barramento de Serviço do Azure](#azure-service-bus-data-receiver) | Permite acesso de recebimento aos recursos do Barramento de Serviço do Azure. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Remetente de dados do Barramento de Serviço do Azure](#azure-service-bus-data-sender) | Permite o acesso de envio aos recursos do Barramento de Serviço do Azure. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Proprietário de registro do Microsoft Azure Stack](#azure-stack-registration-owner) | Permite que você gerencie registros do Microsoft Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Colaborador de EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Permite que você gerencie operações de assinatura de evento EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Leitor de EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Permite que você gerencie operações de assinatura de evento EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Colaborador de dados do FHIR](#fhir-data-contributor) | A função permite o acesso completo do usuário ou principal aos dados do FHIR | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [Exportador de dados FHIR](#fhir-data-exporter) | A função permite que o usuário ou entidade de segurança Leia e exporte dados do FHIR | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Leitor de dados do FHIR](#fhir-data-reader) | A função permite que o usuário ou a entidade de segurança Leia dados do FHIR | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [Gravador de dados FHIR](#fhir-data-writer) | A função permite que o usuário ou a entidade de segurança Leia e grave dados do FHIR | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Colaborador de Ambiente de Serviço de Integração](#integration-service-environment-contributor) | Permite que você gerencie ambientes de serviço de integração, mas não tem acesso a eles. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Ambiente de Serviço de Integração Developer](#integration-service-environment-developer) | Permite que os desenvolvedores criem e atualizem fluxos de trabalho, contas de integração e conexões de API em ambientes de serviço de integração. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Colaborador de conta do sistemas inteligentes](#intelligent-systems-account-contributor) | Permite gerenciar contas do Intelligent Systems, mas não acessá-las. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Colaborador de Aplicativo Lógico](#logic-app-contributor) | Permite o gerenciamento de aplicativos lógicos, mas você não pode alterar o acesso a eles. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operador de Aplicativo Lógico](#logic-app-operator) | Permite a leitura, habilitação e desabilitação de aplicativos lógicos, mas você não pode editá-los ou atualizá-los. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identidade** |  |  |
> | [Colaborador de Identidade Gerenciada](#managed-identity-contributor) | Criar, ler, atualizar e excluir a identidade atribuída pelo usuário | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operador de Identidade Gerenciada](#managed-identity-operator) | Ler e atribuir identidade atribuída pelo usuário | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Segurança** |  |  |
> | [Colaborador de atestado](#attestation-contributor) | Pode ler gravar ou excluir a instância do provedor de atestado | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Leitor de atestado](#attestation-reader) | Pode ler as propriedades do provedor de atestado | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Colaborador do Azure Sentinel](#azure-sentinel-contributor) | Colaborador do Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Leitor do Azure Sentinel](#azure-sentinel-reader) | Leitor do Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Respondente do Azure Sentinel](#azure-sentinel-responder) | Respondente do Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Administrador de Key Vault](#key-vault-administrator) | Execute todas as operações de plano de dados em um cofre de chaves e todos os objetos nela, incluindo certificados, chaves e segredos. Não é possível gerenciar os recursos do cofre de chaves nem gerenciar atribuições de função. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Diretor de certificados Key Vault](#key-vault-certificates-officer) | Execute qualquer ação nos certificados de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Colaborador do Key Vault](#key-vault-contributor) | Gerencie cofres de chaves, mas não permite que você atribua funções no RBAC do Azure e não permite que você acesse segredos, chaves ou certificados. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault o analista de criptografia](#key-vault-crypto-officer) | Execute qualquer ação nas chaves de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault usuário de criptografia do serviço de criptografia](#key-vault-crypto-service-encryption-user) | Ler metadados de chaves e executar operações de encapsulamento/desencapsulamento. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault usuário de criptografia](#key-vault-crypto-user) | Executar operações criptográficas usando chaves. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Leitor de Key Vault](#key-vault-reader) | Ler metadados de cofres de chaves e seus certificados, chaves e segredos. Não é possível ler valores confidenciais, como conteúdo secreto ou material de chave. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Diretor de segredos Key Vault](#key-vault-secrets-officer) | Execute qualquer ação nos segredos de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Usuário de Key Vault segredos](#key-vault-secrets-user) | Ler conteúdo secreto. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Colaborador de HSM gerenciado](#managed-hsm-contributor) | Permite que você gerencie pools HSM gerenciados, mas não tem acesso a eles. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Administrador de Segurança](#security-admin) | Visualiza e atualiza permissões para a Central de Segurança. Mesmas permissões que a função de leitor de segurança e também podem atualizar a política de segurança e ignorar alertas e recomendações. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Colaborador de avaliação de segurança](#security-assessment-contributor) | Permite enviar avaliações por push para a Central de Segurança | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gerenciador de Segurança (Herdado)](#security-manager-legacy) | Esta é uma função herdada. Em vez disso, use o Administrador de Segurança. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Leitor de Segurança](#security-reader) | Visualiza permissões para a Central de Segurança. Pode exibir recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Usuário do DevTest Labs](#devtest-labs-user) | Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Criador de laboratório](#lab-creator) | Permite criar novos laboratórios em suas contas de laboratório do Azure. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitorar** |  |  |
> | [Colaborador de componente do Application Insights](#application-insights-component-contributor) | Pode gerenciar os componentes do Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Depurador de Instantâneos do Application Insights](#application-insights-snapshot-debugger) | Concede permissão ao usuário para exibir e baixar os instantâneos de depuração coletados com o Depurador de Instantâneos do Application Insights. Observe que essas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Colaborador](#contributor). Quando você concede aos usuários a função de Depurador de Instantâneos do Application Insights, deve conceder a função diretamente ao usuário. A função não é reconhecida quando adicionada a uma função personalizada. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Colaborador de monitoramento](#monitoring-contributor) | Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Publicador de Métricas de Monitoramento](#monitoring-metrics-publisher) | Habilita a publicação de métricas com base nos recursos do Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Leitor de monitoramento](#monitoring-reader) | Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Colaborador da pasta de trabalho](#workbook-contributor) | Pode salvar as pastas de trabalho compartilhadas. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Leitor de pasta de trabalho](#workbook-reader) | Pode ler as pastas de trabalho. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Gerenciamento + governança** |  |  |
> | [Operador do Trabalho de Automação](#automation-job-operator) | Criar e gerenciar trabalhos usando runbooks de Automação. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operador de automação](#automation-operator) | Os Operadores de Automação podem iniciar, interromper, suspender e retomar trabalhos | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operador de runbook de Automação](#automation-runbook-operator) | Ler propriedades do Runbook - para poder criar Trabalhos do runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Integração de Azure Connected Machine](#azure-connected-machine-onboarding) | Pode integrar Azure Connected Machines. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrador de recursos de Azure Connected Machine](#azure-connected-machine-resource-administrator) | Pode ler, gravar, excluir e reintegrar Azure Connected Machines. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Leitor de cobrança](#billing-reader) | Permite o acesso de leitura aos dados de cobrança | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Colaborador do Blueprint](#blueprint-contributor) | Pode gerenciar definições de blueprint, mas não as atribuir. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operador do Blueprint](#blueprint-operator) | Pode atribuir blueprints publicados existentes, mas não pode criar novos blueprints. Observe que isso só funcionará se a atribuição for feita com uma identidade gerenciada atribuída pelo usuário. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Colaborador do Gerenciamento de Custos](#cost-management-contributor) | Pode exibir os custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Leitor do Gerenciamento de Custos](#cost-management-reader) | Pode exibir dados e configuração de custos (por exemplo, orçamentos, exportações) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Administrador de configurações de hierarquia](#hierarchy-settings-administrator) | Permite que os usuários editem e excluam Configurações de hierarquia | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Cluster kubernetes – integração do arco do Azure](#kubernetes-cluster---azure-arc-onboarding) | Definição de função para autorizar qualquer usuário/serviço a criar o recurso connectedClusters | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Função de Colaborador de Aplicativos Gerenciados](#managed-application-contributor-role) | Permite a criação de recursos de aplicativos gerenciados. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Função do Operador de Aplicativos Gerenciado](#managed-application-operator-role) | Permite que você leia e execute as ações nos recursos de aplicativo gerenciado | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Leitor de aplicativos gerenciados](#managed-applications-reader) | Permite ler os recursos de um aplicativo gerenciado e solicitar acesso JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Função de Exclusão de Atribuição de Registro de Serviços Gerenciados](#managed-services-registration-assignment-delete-role) | A Função de Exclusão de Atribuição de Registro de Serviços Gerenciados permite que os usuários do locatário de gerenciamento excluam a atribuição de registro atribuída aos locatários. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Colaborador do Grupo de Gerenciamento](#management-group-contributor) | Função de Colaborador do Grupo de Gerenciamento | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Leitor do Grupo de Gerenciamento](#management-group-reader) | Função de Leitor do Grupo de Gerenciamento | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Colaborador de Conta APM do New Relic](#new-relic-apm-account-contributor) | Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Gravador de Dados de Insights de Política (Versão Prévia)](#policy-insights-data-writer-preview) | Permite o acesso de leitura às políticas de recurso e o acesso de gravação aos eventos de política de componente de recurso. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Comprador de reserva](#reservation-purchaser) | Permite que você compre reservas | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Colaborador da Política de Recurso](#resource-policy-contributor) | Usuários com direitos para criar ou modificar a política de recursos, criar tíquete de suporte e ler recursos ou hierarquias. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Colaborador do Site Recovery](#site-recovery-contributor) | Permite gerenciar o serviço do Azure Site Recovery, exceto a criação de cofre e atribuição de função | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operador do Site Recovery](#site-recovery-operator) | Permite failover e failback, mas não executa outras operações de gerenciamento do Azure Site Recovery | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Leitor do Site Recovery](#site-recovery-reader) | Permite visualizar o status do Azure Site Recovery, mas não executar outras operações de gerenciamento | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Colaborador de solicitação de suporte](#support-request-contributor) | Permite criar e gerenciar Solicitações de Suporte | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Colaborador de Marca](#tag-contributor) | Permite que você gerencie marcas em entidades, sem fornecer acesso às entidades propriamente ditas. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Outros** |  |  |
> | [Proprietário de Dados dos Gêmeos Digitais do Azure](#azure-digital-twins-data-owner) | Função de acesso completo para o plano de dados gêmeos digital | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Leitor de dados do Azure digital gêmeos](#azure-digital-twins-data-reader) | Função somente leitura para propriedades do plano de dados gêmeos digital | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Colaborador do BizTalk](#biztalk-contributor) | Permite gerenciar serviços do BizTalk, mas não acessá-los. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Colaborador do grupo de aplicativos de virtualização de desktop](#desktop-virtualization-application-group-contributor) | Colaborador do grupo de aplicativos de virtualização de desktop. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Leitor do grupo de aplicativos de virtualização de desktop](#desktop-virtualization-application-group-reader) | Leitor do grupo de aplicativos de virtualização de desktop. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Colaborador de virtualização de desktop](#desktop-virtualization-contributor) | Colaborador da virtualização de desktops. | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [Colaborador do pool de hosts de virtualização de desktop](#desktop-virtualization-host-pool-contributor) | Colaborador do pool de hosts de virtualização de desktop. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Leitor do pool de hosts de virtualização de desktop](#desktop-virtualization-host-pool-reader) | Leitor do pool de hosts de virtualização de desktop. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Leitor de virtualização de desktop](#desktop-virtualization-reader) | Leitor de virtualização de desktop. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Operador de host de sessão de virtualização de desktop](#desktop-virtualization-session-host-operator) | Operador do host da sessão de virtualização de desktop. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Usuário de virtualização de desktop](#desktop-virtualization-user) | Permite que o usuário use os aplicativos em um grupo de aplicativos. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Operador de sessão de usuário de virtualização de desktop](#desktop-virtualization-user-session-operator) | Operador da sessão uesr de virtualização de desktop. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Colaborador do espaço de trabalho do Desktop Virtualization](#desktop-virtualization-workspace-contributor) | Colaborador do espaço de trabalho de virtualização de desktop. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Leitor de espaço de trabalho do Desktop Virtualization](#desktop-virtualization-workspace-reader) | Leitor do espaço de trabalho de virtualização de desktop. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Leitor de backup em disco](#disk-backup-reader) | Fornece permissão para o cofre de backup para executar o backup em disco. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Operador de restauração de disco](#disk-restore-operator) | Fornece permissão para o cofre de backup para executar a restauração do disco. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Colaborador de instantâneo de disco](#disk-snapshot-contributor) | Fornece permissão para o cofre de backup para gerenciar instantâneos de disco. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Colaborador de Coleções de Trabalho do Agendador](#scheduler-job-collections-contributor) | Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Operador de Hub de serviços](#services-hub-operator) | Operador de Hub de serviços permite que você execute todas as operações de leitura, gravação e exclusão relacionadas aos conectores do hub de serviços. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Geral


### <a name="contributor"></a>Colaborador

Concede acesso completo para gerenciar todos os recursos, mas não permite que você atribua funções no RBAC do Azure, gerencie atribuições em plantas do Azure ou compartilhe galerias de imagens. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Excluir funções, atribuições de política, definições de política e definições de conjunto de políticas |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Criar funções, atribuições de função, atribuições de política, definições de política e definições de conjunto de políticas |
> | /ElevateAccess/Action [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Concede ao chamador o acesso de Administrador de Acesso do Usuário no escopo do locatário |
> | /BlueprintAssignments/Write [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint) | Criar ou atualizar quaisquer atribuições de blueprint |
> | /BlueprintAssignments/Delete [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint) | Excluir quaisquer atribuições de blueprint |
> | /Galleries/share/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Compartilha uma galeria com escopos diferentes |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Proprietário

Concede acesso completo para gerenciar todos os recursos, incluindo a capacidade de atribuir funções no RBAC do Azure. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Leitor

Exibir todos os recursos, mas não permite que você faça nenhuma alteração. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Administrador de Acesso do Usuário

Permite que você gerencie o acesso do usuário aos recursos do Azure. [Saiba mais](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/* | Gerenciar autorização |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Computação


### <a name="classic-virtual-machine-contributor"></a>Colaborador de Máquina Virtual Clássica

Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Criar e gerenciar nomes de domínio de computação clássica |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/Join/Action |  |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/Action | Vincular um IP reservado |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Read | Obter os IPs reservados |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Join/Action | Ingressar na rede virtual. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Read | Obter a rede virtual. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/Read | Retornar o disco da conta de armazenamento. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/Read | Retornar a imagem da conta de armazenamento. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Listar as chaves de acesso das contas de armazenamento. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Retornar a conta de armazenamento com a conta fornecida. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Logon de administrador da Máquina Virtual

Exibir máquinas virtuais no portal e fazer logon como administrador [saiba mais](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /PublicIPAddresses/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de endereço IP público. |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | /LoadBalancers/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de balanceador de carga |
> | /NetworkInterfaces/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de adaptador de rede.  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /VirtualMachines/login/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Faça logon em uma máquina virtual como um usuário normal |
> | /VirtualMachines/loginAsAdmin/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Faça logon em uma máquina virtual com os privilégios de administrador do Windows ou de usuário raiz do Linux |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Colaborador de Máquina Virtual

Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. [Saiba mais](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/Locations/* | Criar e gerenciar locais de computação |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Execute todas as ações de máquina virtual, incluindo criar, atualizar, excluir, iniciar, reiniciar e desligar máquinas virtuais. Executar scripts predefinidos em máquinas virtuais. |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de dimensionamento de máquinas virtuais |
> | /Disks/Write [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Criar um novo disco ou atualizar um existente |
> | /Disks/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter as propriedades de um disco |
> | /Disks/Delete [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Excluir o disco |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Schedules/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /ApplicationGateways/backendAddressPools/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | /LoadBalancers/backendAddressPools/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | /LoadBalancers/inboundNatPools/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um pool NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | /LoadBalancers/inboundNatRules/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | /LoadBalancers/Probes/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Permitir o uso de investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode referenciar a investigação. Não é possível alertá-lo. |
> | /LoadBalancers/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de balanceador de carga |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Locations/* | Criar e gerenciar locais de rede |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Criar e gerenciar as interfaces de rede |
> | /NetworkSecurityGroups/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um grupo de segurança de rede. Não é possível alertá-lo. |
> | /NetworkSecurityGroups/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de um grupo de segurança de rede |
> | /PublicIPAddresses/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um endereço IP público. Não é possível alertá-lo. |
> | /PublicIPAddresses/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de endereço IP público. |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | /VirtualNetworks/Subnets/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em uma rede virtual. Não é possível alertá-lo. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Criar uma Intenção de Proteção de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Retornar detalhes do objeto do item protegido |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Criar um item protegido de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Retornar todas as políticas de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Write | Criar uma política de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | /StorageAccounts/listKeys/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Logon de usuário da Máquina Virtual

Visualize as Máquinas Virtuais do Microsoft Azure no portal e faça logon como usuário. [Saiba mais](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /PublicIPAddresses/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de endereço IP público. |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | /LoadBalancers/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de balanceador de carga |
> | /NetworkInterfaces/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de adaptador de rede.  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /VirtualMachines/login/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Faça logon em uma máquina virtual como um usuário normal |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Rede


### <a name="cdn-endpoint-contributor"></a>Colaborador de ponto de extremidade de CDN

Pode gerenciar os pontos de extremidade de CDN, mas não pode conceder acesso a outros usuários.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Edgenodes/Read [Microsoft. CDN](resource-provider-operations.md#microsoftcdn) |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Leitor de ponto de extremidade de CDN

Pode exibir os pontos de extremidade de CDN, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Edgenodes/Read [Microsoft. CDN](resource-provider-operations.md#microsoftcdn) |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/*/Read |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Colaborador de perfil de CDN

Pode gerenciar os perfis de CDN e os respectivos pontos de extremidade, mas não pode conceder acesso a outros usuários. [Saiba mais](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Edgenodes/Read [Microsoft. CDN](resource-provider-operations.md#microsoftcdn) |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Leitor de perfil de CDN

Pode exibir os perfis de CDN e os respectivos pontos de extremidade, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Edgenodes/Read [Microsoft. CDN](resource-provider-operations.md#microsoftcdn) |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/Read |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Colaborador de rede clássica

Permite que você gerencie redes clássicas, mas não acessá-las. [Saiba mais](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Criar e gerenciar redes clássicas |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Colaborador de zona DNS

Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. [Saiba mais](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Criar e gerenciar zonas e registros DNS |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Colaborador de rede

Permite gerenciar redes, mas não acessá-las.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/* | Criar e gerenciar redes |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>Colaborador de zona de DNS privado

Permite que você gerencie recursos de zona DNS privada, mas não as redes virtuais às quais eles estão vinculados. [Saiba mais](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | /VirtualNetworks/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em uma rede virtual. Não é possível alertá-lo. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Colaborador do Gerenciador de Tráfego

Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Armazenamento


### <a name="avere-contributor"></a>Colaborador do Avere

Pode criar e gerenciar um cluster do Avere vFXT. [Saiba mais](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/*/Read |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/Read |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | /VirtualNetworks/Subnets/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de sub-rede da rede virtual |
> | /VirtualNetworks/Subnets/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em uma rede virtual. Não é possível alertá-lo. |
> | /VirtualNetworks/Subnets/joinViaServiceEndpoint/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | /NetworkSecurityGroups/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um grupo de segurança de rede. Não é possível alertá-lo. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/*/Read |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | /Subscriptions/resourceGroups/Resources/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os recursos do grupo de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/blobServices/containers/BLOBs/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da exclusão de um blob |
> | /StorageAccounts/blobServices/containers/BLOBs/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna um blob ou uma lista de blobs |
> | /StorageAccounts/blobServices/containers/BLOBs/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Operador do Avere

Usado pelo cluster avere vFXT para gerenciar o cluster [saiba mais](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /VirtualMachines/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter as propriedades de uma máquina virtual |
> | /NetworkInterfaces/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de adaptador de rede.  |
> | /NetworkInterfaces/Write [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | /VirtualNetworks/Subnets/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de sub-rede da rede virtual |
> | /VirtualNetworks/Subnets/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em uma rede virtual. Não é possível alertá-lo. |
> | /NetworkSecurityGroups/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um grupo de segurança de rede. Não é possível alertá-lo. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /StorageAccounts/blobServices/containers/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar o resultado da exclusão de um contêiner |
> | /StorageAccounts/blobServices/containers/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna a lista de contêineres |
> | /StorageAccounts/blobServices/containers/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado do contêiner de put blob |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/blobServices/containers/BLOBs/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da exclusão de um blob |
> | /StorageAccounts/blobServices/containers/BLOBs/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna um blob ou uma lista de blobs |
> | /StorageAccounts/blobServices/containers/BLOBs/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Colaborador de Backup

Permite que você gerencie o serviço de backup, mas não pode criar cofres e conceder acesso a outras pessoas [saiba mais](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Gerenciar os resultados da operação no gerenciamento de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Criar e gerenciar contêineres de backup em malhas de backup do cofre de Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Atualizar a lista de contêineres |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportar trabalhos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Criar e gerenciar políticas de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Criar e gerenciar itens submetidos a backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Criar e gerenciar contêineres que armazenam itens de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/* | Criar e gerenciar certificados relacionados a backup em um cofre de Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/* | Criar e gerenciar o uso do cofre dos Serviços de Recuperação |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/BackupConfig/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Validar operação no Item protegido |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Obter todos os contêineres protegidos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Validar recursos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Resolver o alerta. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operação retorna a lista de operações para um provedor de recursos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Obtém o Status da operação para uma determinada operação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Listar todas as Intenções de Proteção de backup |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operador de Backup

Permite que você gerencie serviços de backup, exceto remoção de backup, criação de cofre e acesso a outros [saiba mais](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Retornar o status da operação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Obter o resultado da operação executada no contêiner de proteção. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/Action | Executar um backup para um item protegido. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Obter o resultado da operação executada em itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Retornar o status da operação executada em itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Retornar detalhes do objeto do item protegido |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Provisionar recuperação de item instantânea para item protegido |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/Action | Obter AccessToken para a restauração entre regiões. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Obter pontos de recuperação para itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Restaurar pontos de recuperação para itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Revogar a recuperação de item instantânea para item protegido |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Criar um item protegido de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Retornar todos os contêineres registrados |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Atualizar a lista de contêineres |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportar trabalhos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Obter os resultados da operação de política. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Retornar todas as políticas de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Retornar a lista de todos os itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Retornar todos os contêineres pertencentes à assinatura |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Write | A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Validar operação no Item protegido |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Obter o status da operação de política. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Write | Criar um contêiner registrado |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/Action | Consultar cargas de trabalho em um contêiner |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Criar uma Intenção de Proteção de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Obter uma Intenção de Proteção de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Obter todos os contêineres protegidos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Obter todos os itens em um contêiner |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Validar recursos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupAadProperties/Read | Obtenha as propriedades do AAD para autenticação na terceira região para a restauração entre regiões. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrJobs/Action | Listar trabalhos de restauração entre regiões na região secundária para o cofre dos serviços de recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrJob/Action | Obter detalhes do trabalho de restauração entre regiões na região secundária para o cofre dos serviços de recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrossRegionRestore/Action | Disparar a restauração entre regiões. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrOperationResults/Read | Retorna o resultado da operação de CRR para o cofre dos serviços de recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrOperationsStatus/Read | Retorna o status da operação de CRR para o cofre dos serviços de recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Resolver o alerta. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operação retorna a lista de operações para um provedor de recursos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Obtém o Status da operação para uma determinada operação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Listar todas as Intenções de Proteção de backup |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Leitor de Backup

Pode exibir serviços de backup, mas não pode fazer alterações [saiba mais](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Retornar o status da operação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Obter o resultado da operação executada no contêiner de proteção. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Obter o resultado da operação executada em itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Retornar o status da operação executada em itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Retornar detalhes do objeto do item protegido |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Obter pontos de recuperação para itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Retornar todos os contêineres registrados |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/Read | Retornar o resultado da operação do trabalho. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/Read | Retornar todos os objetos de trabalho |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportar trabalhos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/Read | Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Obter os resultados da operação de política. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Retornar todas as políticas de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Retornar a lista de todos os itens protegidos. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Retornar todos os contêineres pertencentes à assinatura |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/Read | Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/BackupConfig/Read | Retornar a configuração para cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Obter o status da operação de política. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Obter uma Intenção de Proteção de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Obter todos os itens em um contêiner |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Resolver o alerta. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operação retorna a lista de operações para um provedor de recursos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Obtém o Status da operação para uma determinada operação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Listar todas as Intenções de Proteção de backup |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Validar recursos |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Colaborador da conta de armazenamento clássica

Permite que você gerencie contas de armazenamento clássico, mas não acessá-las.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica

Os operadores de chave de conta de armazenamento clássico têm permissão para listar e regenerar chaves em contas de armazenamento clássicas [saiba mais](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/Action | Listar as chaves de acesso das contas de armazenamento. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/Action | Regenera as chaves de acesso existentes da conta de armazenamento. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Colaborador do Data Box

Permite que você gerencie tudo sob o serviço Data Box exceto fornecer acesso a outras pessoas. [Saiba mais](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Leitor do Data Box

Permite que você gerencie o serviço do Azure Data Box, exceto a ordem de criação ou edição de detalhes do pedido e fornecer acesso a outras pessoas. [Saiba mais](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/*/Read |  |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/Jobs/listsecrets/Action |  |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/Jobs/listcredentials/Action | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/Locations/availableSkus/Action | Este método retorna a lista de SKUs disponíveis. |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/Locations/validateInputs/Action | Este método faz todos os tipos de validações. |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/Locations/regionConfiguration/Action | Este método retorna as configurações da região. |
> | [Microsoft. Data Box](resource-provider-operations.md#microsoftdatabox)/Locations/validateAddress/Action | Validará o endereço de entrega e fornecerá endereços alternativos, se houver algum. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Desenvolvedor do Data Lake Analytics

Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics. [Saiba mais](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Excluir uma conta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/Action | Conceder permissões para cancelar trabalhos enviados por outros usuários. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Desvincular uma conta do Data Lake Store de uma conta do Data Lake Analytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Excluir uma política de computação. |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Acesso a Dados e Leitor

Permite que você exiba tudo, mas não permitirá que exclua ou crie uma conta de armazenamento ou um recurso contido. Ele também permitirá o acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio de acesso às chaves de conta de armazenamento.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /StorageAccounts/listKeys/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | /StorageAccounts/ListAccountSas/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar o token SAS da conta para a conta de armazenamento especificada. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Colaborador da Conta de Armazenamento

Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada. [Saiba mais](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | /VirtualNetworks/Subnets/joinViaServiceEndpoint/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento

Permite listar e regenerar chaves de acesso da conta de armazenamento. [Saiba mais](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /StorageAccounts/listkeys/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | /StorageAccounts/regeneratekey/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Regenerar as chaves de acesso da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Colaborador de dados de blob de armazenamento

Ler, gravar e excluir contêineres e blobs de Armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /StorageAccounts/blobServices/containers/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Excluir um contêiner. |
> | /StorageAccounts/blobServices/containers/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar um contêiner ou uma lista de contêineres. |
> | /StorageAccounts/blobServices/containers/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Modificar os metadados ou as propriedades de um contêiner. |
> | /StorageAccounts/blobServices/generateUserDelegationKey/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna uma chave de delegação de usuário para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/blobServices/containers/BLOBs/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Excluir um blob. |
> | /StorageAccounts/blobServices/containers/BLOBs/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar um blob ou uma lista de blobs. |
> | /StorageAccounts/blobServices/containers/BLOBs/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Gravar em um blob. |
> | /StorageAccounts/blobServices/containers/BLOBs/move/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Move o blob de um caminho para outro |
> | /StorageAccounts/blobServices/containers/BLOBs/Add/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da adição de conteúdo do blob |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Proprietário de Dados do Blob de Armazenamento

Fornece acesso completo aos dados e contêineres de blob do Armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Permissões completas em contêineres. |
> | /StorageAccounts/blobServices/generateUserDelegationKey/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna uma chave de delegação de usuário para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/BLOBs/* | Permissões completas em blobs. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Leitor de Dados do Blob de Armazenamento

Leia e liste contêineres e blobs do Armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /StorageAccounts/blobServices/containers/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar um contêiner ou uma lista de contêineres. |
> | /StorageAccounts/blobServices/generateUserDelegationKey/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna uma chave de delegação de usuário para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/blobServices/containers/BLOBs/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar um blob ou uma lista de blobs. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Delegador do Blob de Armazenamento

Obtenha uma chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou blob que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [Criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas). [Saiba mais](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /StorageAccounts/blobServices/generateUserDelegationKey/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna uma chave de delegação de usuário para o serviço Blob. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Colaborador de Compartilhamento SMB de Dados do Arquivo de Armazenamento

Permite o acesso de leitura, gravação e exclusão em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função não tem equivalente interno nos servidores de arquivos do Windows. [Saiba mais](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/fileServices/fileshares/files/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna um arquivo, uma pasta ou uma lista de arquivos/pastas. |
> | /StorageAccounts/fileServices/fileshares/files/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da gravação em um arquivo ou da criação de uma pasta. |
> | /StorageAccounts/fileServices/fileshares/files/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da exclusão de um arquivo ou uma pasta. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento

Permite ler, gravar, excluir e modificar ACLs em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de alteração em servidores de arquivos do Windows. [Saiba mais](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/fileServices/fileshares/files/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna um arquivo, uma pasta ou uma lista de arquivos/pastas. |
> | /StorageAccounts/fileServices/fileshares/files/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da gravação em um arquivo ou da criação de uma pasta. |
> | /StorageAccounts/fileServices/fileshares/files/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da exclusão de um arquivo ou uma pasta. |
> | /StorageAccounts/fileServices/fileshares/files/modifypermissions/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado da modificação da permissão em um arquivo ou uma pasta. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Leitor de Compartilhamento SMB de Dados do Arquivo de Armazenamento

Permite acesso de leitura em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de leitura em servidores de arquivos do Windows. [Saiba mais](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/fileServices/fileshares/files/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna um arquivo, uma pasta ou uma lista de arquivos/pastas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Colaborador de Dados da Fila de Armazenamento

Lê, grava e exclui filas do Armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /StorageAccounts/queueServices/Queues/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Excluir uma fila. |
> | /StorageAccounts/queueServices/Queues/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar uma fila ou uma lista de filas. |
> | /StorageAccounts/queueServices/Queues/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Modificar metadados ou propriedades da fila. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/queueServices/Queues/messages/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Excluir uma ou mais mensagens de uma fila. |
> | /StorageAccounts/queueServices/Queues/messages/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | /StorageAccounts/queueServices/Queues/messages/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Adicionar uma mensagem a uma fila. |
> | /StorageAccounts/queueServices/Queues/messages/Process/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retorna o resultado do processamento de uma mensagem |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Processador de Mensagens de Dados da Fila de Armazenamento

Espia, recupera e exclui uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/queueServices/Queues/messages/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Espiar uma mensagem. |
> | /StorageAccounts/queueServices/Queues/messages/Process/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Recuperar e excluir uma mensagem. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Remetente da Mensagem de Dados da Fila de Armazenamento

Adiciona mensagens a uma fila de Armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/queueServices/Queues/messages/Add/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Adicionar uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Leitor de Dados da Fila de Armazenamento

Lê e lista as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, consulte [Permissões para chamar blob e operações de dados de fila](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Saiba mais](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /StorageAccounts/queueServices/Queues/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar uma fila ou uma lista de filas. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | /StorageAccounts/queueServices/Queues/messages/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-contributor"></a>Colaborador de dados do Azure Maps

Concede acesso ao acesso de leitura, gravação e exclusão para mapear dados relacionados de uma conta do Azure Maps. [Saiba mais](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Write |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Delete |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Leitor de Dados do Azure Mapas

Concede acesso para ler dados relacionados ao mapa de uma conta do Azure Mapas. [Saiba mais](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Colaborador do Serviço de Pesquisa

Permite gerenciar serviços de pesquisa, mas não acessá-las. [Saiba mais](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Criar e gerenciar serviços de pesquisa |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>Leitor de AccessKey do signalr

Ler chaves de acesso do serviço Signalr

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/Read |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/Action | Exibe o valor das chaves de acesso do SignalR no portal de gerenciamento ou por meio da API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>Servidor de aplicativo signalr (visualização)

Permite que o servidor de aplicativos acesse o serviço de Signaler com opções de autenticação do AAD.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/AccessKey/Action | Gere um AccessKey temporário para a assinatura de ClientTokens. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/Write | Inicie uma conexão de servidor. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>Colaborador do signalr

Criar, ler, atualizar e excluir recursos do serviço Signalr

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>Colaborador sem servidor do signalr (versão prévia)

Permite que seu aplicativo acesse o serviço no modo sem servidor com opções de autenticação do AAD.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/Action | Gere um ClientToken para iniciar uma conexão de cliente. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>Proprietário do serviço signalr (versão prévia)

Acesso completo às APIs REST do serviço de Signaler do Azure

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Hub/Send/Action | Transmita mensagens para todas as conexões de cliente no Hub. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Send/Action | Transmitir mensagem para o grupo. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Verifique a existência do grupo ou a existência do usuário no grupo. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Write | Grupo de junção/saída. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Send/Action | Enviar mensagens diretamente para uma conexão de cliente. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Verifique a existência da conexão do cliente. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Write | Feche a conexão do cliente. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Send/Action | Envie mensagens para o usuário, que podem consistir em várias conexões de cliente. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Verifique a existência do usuário. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Write |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>Leitor de serviço do signalr (visualização)

Acesso somente leitura às APIs REST do serviço de Signaler do Azure

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Verifique a existência do grupo ou a existência do usuário no grupo. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Verifique a existência da conexão do cliente. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Verifique a existência do usuário. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Colaborador do Plano de Web

Permite gerenciar os planos da Web para sites, mas não o acesso a eles.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Criar e gerenciar farms de servidores |
> | /HostingEnvironments/Join/Action [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Unir um Ambiente do Serviço de Aplicativo |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Colaborador do Site

Permite gerenciar sites (não planos da Web), mas não acessá-los.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Components/* | Criar e gerenciar componentes do Insights |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Certificates/* | Criar e gerenciar certificados de site da Web |
> | /ListSitesAssignedToHostName/Read [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Obter nomes dos sites atribuídos ao nome de host. |
> | /ServerFarms/Join/Action [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Une um plano do serviço de aplicativo |
> | /ServerFarms/Read [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/* | Criar e gerenciar sites (a criação de sites também requer permissões de gravação para o Plano do Serviço de Aplicativo associado) |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Contêineres


### <a name="acrdelete"></a>AcrDelete

exclusão de ACR [saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Artifacts/Delete | Excluir o artefato em um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

signatário de imagem ACR [saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Sign/Write | Efetuar push/pull de metadados de conteúdo confiável para um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

extração de ACR [saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/pull/Read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

Informações sobre o ACR Push [saiba mais](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/pull/Read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Push/Write | Efetuar push ou Gravar imagens para um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

leitor de dados de quarentena acr

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Read | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

gravador de dados de quarentena acr

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Read | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Write | Gravar/Modificar o estado de quarentena das imagens em quarentena |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Função de Administrador do Cluster do Serviço de Kubernetes do Azure

Liste a ação de credencial de administrador de cluster. [Saiba mais](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/Action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/Action | Obtém um perfil de acesso do cluster gerenciado por nome de função usando a credencial de lista |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Obtém um cluster gerenciado |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Função de Usuário do Cluster do Serviço de Kubernetes do Azure

Liste a ação de credencial de usuário de cluster. [Saiba mais](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Listar a credencial clusterUser de um cluster gerenciado |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Obtém um cluster gerenciado |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Função colaborador do serviço kubernetes do Azure

Concede acesso para ler e gravar clusters do serviço kubernetes do Azure [saiba mais](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Obtém um cluster gerenciado |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Write | Cria um novo cluster gerenciado ou atualiza um existente |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Administrador de RBAC do serviço kubernetes do Azure

Permite que você gerencie todos os recursos em cluster/namespace, exceto atualizar ou excluir cotas de recursos e namespaces. [Saiba mais](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Deployments/Write [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Criar ou atualizar uma implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Listar a credencial clusterUser de um cluster gerenciado |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Write | Grava resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Delete | Exclui resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Write | Grava namespaces |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Delete | Exclui namespaces |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Administrador de cluster do RBAC do serviço kubernetes do Azure

Permite que você gerencie todos os recursos no cluster. [Saiba mais](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Deployments/Write [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Criar ou atualizar uma implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Listar a credencial clusterUser de um cluster gerenciado |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Leitor de RBAC do serviço kubernetes do Azure

Permite acesso somente leitura para ver a maioria dos objetos em um namespace. Ele não permite a exibição de funções ou associações de função. Essa função não permite a exibição de segredos, pois a leitura do conteúdo de segredos permite o acesso a credenciais de uma conta no namespace, o que permitiria o acesso à API como qualquer uma das contas no namespace (uma forma de elevação de privilégio). A aplicação dessa função no escopo do cluster fornecerá acesso em todos os namespaces. [Saiba mais](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Deployments/Write [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Criar ou atualizar uma implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/Read | Lê controllerrevisions |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/Read | Lê daemonsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/Deployments/Read | Lê implantações |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/Read | Lê replicasets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/Read | Lê statefulsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/Read | Lê horizontalpodautoscalers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/Read | Lê cronjobs |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Jobs/Read | Lê os trabalhos |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/Read | Lê configmaps |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/Read | Lê pontos de extremidade |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.K8S.Io/Events/Read | Lê eventos |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Lê eventos |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/Read | Lê daemonsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/Read | Lê implantações |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/Read | Lê insere |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/Read | Lê networkpolicies |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/Read | Lê replicasets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Lê limitranges |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Read | Lê namespaces |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.Io/ingresses/Read | Lê insere |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.Io/networkpolicies/Read | Lê networkpolicies |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/Read | Lê persistentvolumeclaims |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/Read | Ler pods |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/Read | Lê poddisruptionbudgets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Lê replicationcontrollers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Lê replicationcontrollers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Lê resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/Read | Lê as contas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/Read | Lê serviços |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Gravador RBAC do serviço kubernetes do Azure

Permite acesso de leitura/gravação à maioria dos objetos em um namespace. Essa função não permite exibir ou modificar funções ou associações de função. No entanto, essa função permite acessar segredos e executar pods como qualquer uma das contas no namespace, para que possa ser usada para obter os níveis de acesso de API de qualquer conta no namespace. A aplicação dessa função no escopo do cluster fornecerá acesso em todos os namespaces. [Saiba mais](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Deployments/Write [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Criar ou atualizar uma implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/Read | Lê controllerrevisions |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Jobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.K8S.Io/Events/Read | Lê eventos |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Lê eventos |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Lê limitranges |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Read | Lê namespaces |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.Io/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.Io/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Lê resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Secrets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Bancos de dados


### <a name="cosmos-db-account-reader-role"></a>Função de leitor de conta do Cosmos DB

Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB. [Saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/Read | Ler qualquer coleção |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/Action | Ler as chaves somente leitura da conta do banco de dados. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Ler definições de métrica |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ler métrica |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Operador do Cosmos DB

Permite que você gerencie contas do Azure Cosmos DB, mas não acesse os dados nelas. Impede o acesso a chaves de conta e cadeias de conexão. [Saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | /VirtualNetworks/Subnets/joinViaServiceEndpoint/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/Write | Criar ou atualizar uma definição de função SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/Delete | Excluir uma definição de função SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/Write | Criar ou atualizar uma atribuição de função SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/Delete | Excluir uma atribuição de função SQL |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Pode enviar solicitação de restauração para um banco de dados Cosmos DB ou um contêiner para uma conta [saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/Action | Envie uma solicitação para configurar o backup |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Restore/Action | Enviar uma solicitação de restauração |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

É possível executar a ação de restauração para Cosmos DB conta de banco de dados com o modo de backup contínuo

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/Restore/Action | Enviar uma solicitação de restauração |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/*/Read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/Read | Ler uma conta de banco de dados restaurável ou listar todas as contas de banco de dados restauráveis |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Colaborador de Conta do DocumentDB

Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB. [Saiba mais](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Criar e gerenciar contas do Azure Cosmos DB |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | /VirtualNetworks/Subnets/joinViaServiceEndpoint/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Colaborador do Cache Redis

Permite gerenciar caches Redis, mas não acessá-los.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Register/Action [Microsoft. cache](resource-provider-operations.md#microsoftcache) | Registra o provedor de recursos 'Microsoft.Cache' com uma assinatura |
> | /Redis/ [do Microsoft. cache](resource-provider-operations.md#microsoftcache)* | Criar e gerenciar caches Redis |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Colaborador do banco de dados SQL

Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai. [Saiba mais](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/* | Criar e gerenciar bancos de dados SQL |
> | /Servers/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ler métrica |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Ler definições de métrica |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Editar configurações de auditoria |
> | /Servers/databases/auditRecords/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Recuperar os registros de auditoria do blob do banco de dados |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento dos dados |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Editar métricas de segurança |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Colaborador da Instância Gerenciada do SQL

Permite que você gerencie instâncias gerenciadas do SQL e a configuração de rede necessária, mas não pode conceder acesso a outras pessoas.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/instanceFailoverGroups/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ler métrica |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Ler definições de métrica |
> | **NotActions** |  |
> | /ManagedInstances/azureADOnlyAuthentications/Delete [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Exclui um servidor gerenciado específico Azure Active Directory somente o objeto de autenticação |
> | /ManagedInstances/azureADOnlyAuthentications/Write [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Adiciona ou atualiza um servidor gerenciado específico Azure Active Directory apenas objeto de autenticação |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Gerenciador de Segurança do SQL

Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los. [Saiba mais](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /VirtualNetworks/Subnets/joinViaServiceEndpoint/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Locations/administratorAzureAsyncOperation/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Obtém a instância gerenciada resultado de operações do administrador assíncrono do Azure. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Criar e gerenciar a configuração de auditoria do servidor SQL |
> | /Servers/extendedAuditingSettings/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do servidor SQL |
> | /Servers/databases/auditRecords/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Recuperar os registros de auditoria do blob do banco de dados |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Criar e gerenciar políticas de mascaramento de dados do banco de dados do servidor SQL |
> | /Servers/databases/extendedAuditingSettings/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | /Servers/databases/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | /Servers/databases/schemas/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Obter um esquema de banco de dados. |
> | /Servers/databases/schemas/Tables/Columns/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Obter uma coluna de banco de dados. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | /Servers/databases/schemas/Tables/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Obter uma tabela de banco de dados. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do banco de dados do servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Criar e gerenciar métricas de segurança do banco de dados do servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/firewallRules/* |  |
> | /Servers/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança de servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/* |  |
> | /ManagedInstances/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Retornar a lista de instâncias gerenciadas ou obter as propriedades para a instância gerenciada especificada. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | /ManagedInstances/Administrators/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Obter uma lista de administradores de instância gerenciada. |
> | /Servers/Administrators/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Obtém um objeto de administrador de Azure Active Directory específico |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Colaborador do SQL Server

Permite gerenciar servidores e bancos de dados SQL, mas não acessá-los, nem as políticas relacionadas à segurança. [Saiba mais](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/* | Criar e gerenciar servidores SQL |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ler métrica |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Ler definições de métrica |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Editar configurações de auditoria do servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Editar configurações de auditoria de banco de dados do servidor SQL |
> | /Servers/databases/auditRecords/Read [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Recuperar os registros de auditoria do blob do banco de dados |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento de banco de dados do servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança de banco de dados do servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Editar métricas de segurança de banco de dados do servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Editar políticas de alerta de segurança de servidor SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | /Servers/azureADOnlyAuthentications/Delete [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Exclui um servidor específico Azure Active Directory apenas o objeto de autenticação |
> | /Servers/azureADOnlyAuthentications/Write [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Adiciona ou atualiza um servidor específico Azure Active Directory apenas o objeto de autenticação |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Análise


### <a name="azure-event-hubs-data-owner"></a>Proprietário de Dados de Hubs de Eventos do Azure

Permite acesso completo aos recursos dos Hubs de Eventos do Azure. [Saiba mais](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Receptor de Dados dos Hubs de Eventos do Azure

Permite acesso de recebimento aos recursos dos Hubs de Eventos do Azure. [Saiba mais](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Remetente de Dados dos Hubs de Eventos do Azure

Permite acesso de envio aos recursos dos Hubs de Eventos do Azure. [Saiba mais](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Eventhubs/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Colaborador da fábrica de dados

Cria e gerencia data factories, assim como os recursos filhos neles. [Saiba mais](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. datafactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Criar e gerenciar data factories e recursos filho dentro deles. |
> | [Microsoft. datafactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Criar e gerenciar data factories e recursos filho dentro deles. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | Criar ou atualizar um eventSubscription |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Limpador de Dados

Exclua dados privados de um espaço de trabalho Log Analytics. [Saiba mais](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Components/Purge/Action | Limpe dados do Application Insights |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Exibir dados da análise de logs |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Purge/Action | Excluir dados especificados do workspace |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Operador de Cluster do HDInsight

Permite que você leia e modifique as configurações de cluster do HDInsight. [Saiba mais](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/Read |  |
> | /Clusters/getGatewaySettings/Action [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight) | Obter configurações de gateway para o cluster HDInsight |
> | /Clusters/updateGatewaySettings/Action [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight) | Atualizar as configurações do gateway para o cluster HDInsight |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/Configurations/* |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Deployments/Operations/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista operações de implantação. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Colaborador dos serviços de domínio do HDInsight

Pode ler, criar, modificar e excluir as operações relacionadas ao serviço de domínio necessárias para o HDInsight Enterprise Security Package [saiba mais](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Colaborador do Log Analytics

O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. [Saiba mais](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Listar as chaves de acesso das contas de armazenamento. |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Instala ou atualiza uma extensão do Azure Arc |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/Deployments/* |  |
> | /StorageAccounts/listKeys/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. [Saiba mais](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Pesquisar usando o novo mecanismo. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Executar uma consulta de pesquisa |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Read | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator"></a>Curador de dados alcance

O curador de dados Microsoft. alcance pode criar, ler, modificar e excluir objetos de dados de catálogo e estabelecer relações entre objetos. Esta função está em visualização e está sujeita a alterações.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/Read | Ler recurso de conta para o provedor Microsoft alcance. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/data/Read | Ler objetos de dados. |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/data/Write | Criar, atualizar e excluir objetos de dados. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>Leitor de dados do alcance

O leitor de dados Microsoft. alcance pode ler objetos de dados do catálogo. Esta função está em visualização e está sujeita a alterações.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/Read | Ler recurso de conta para o provedor Microsoft alcance. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/data/Read | Ler objetos de dados. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>Administrador de fonte de dados alcance

O administrador da fonte de dados Microsoft. alcance pode gerenciar fontes de dados e verificações de dados. Esta função está em visualização e está sujeita a alterações.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/Read | Ler recurso de conta para o provedor Microsoft alcance. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/Scan/Read | Ler fontes de dados e verificações. |
> | [Microsoft. alcance](resource-provider-operations.md#microsoftpurview)/accounts/Scan/Write | Criar, atualizar e excluir fontes de dados e gerenciar verificações. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Colaborador do Registro de Esquema (Versão Prévia)

Ler, gravar e excluir grupos e esquemas do Registro de Esquema.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Leitor do Registro de Esquema (Versão Prévia)

Ler e listar os grupos e os esquemas do Registro de Esquema.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/Read | Obter lista de descrições de recursos de um de esquema |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/Read | Recuperar esquemas |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Acesso de Nó de Membro do Blockchain (Versão Prévia)

Permite o acesso a nós membros do Blockchain [saiba mais](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Connect/Action | Conecta-se a um nó de transação de membro Blockchain. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>IA + aprendizado de máquina


### <a name="cognitive-services-contributor"></a>Colaborador dos Serviços Cognitivos

Permite criar, ler, atualizar, excluir e gerenciar chaves dos Serviços Cognitivos. [Saiba mais](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. Cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | /Features/Read [Microsoft. Features](resource-provider-operations.md#microsoftfeatures) | Obter os recursos de uma assinatura. |
> | /Providers/Features/Read [Microsoft. Features](resource-provider-operations.md#microsoftfeatures) | Obter o recurso de uma assinatura em determinado provedor de recursos. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Ler definições de log |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Ler definições de métrica |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ler métrica |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Deployments/Operations/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista operações de implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/Deployments/* |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-contributor"></a>Visão Personalizada colaborador de serviços cognitivas

Acesso completo ao projeto, incluindo a capacidade de exibir, criar, editar ou excluir projetos. [Saiba mais](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Serviços cognitivas Visão Personalizada implantação

Publicar, cancelar publicação ou exportar modelos. A implantação pode exibir o projeto, mas não pode atualizar. [Saiba mais](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Predictions/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/iterations/Publish/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/iterations/Export/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/QuickTest/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Classify/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Exporta um projeto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Serviços cognitivas Visão Personalizada Labeler

Exiba, Edite imagens de treinamento e crie, adicione, remova ou exclua as marcas de imagem. Rotuladores podem exibir o projeto, mas não podem atualizar nada além de imagens e marcas de treinamento. [Saiba mais](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Predictions/Query/Action | Obtenha imagens que foram enviadas ao ponto de extremidade de previsão. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/images/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Tags/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/images/Suggested/* |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/tagsandregions/Suggestions/Action | Essa API receberá marcas e regiões sugeridas para uma matriz/lote de imagens não marcadas, juntamente com confianças para as marcas. Ele retornará uma matriz vazia se nenhuma marca for encontrada. |
> | **NotDataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Exporta um projeto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Visão Personalizada leitor de serviços cognitivas

Ações somente leitura no projeto. Os leitores não podem criar nem atualizar o projeto. [Saiba mais](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Predictions/Query/Action | Obtenha imagens que foram enviadas ao ponto de extremidade de previsão. |
> | **NotDataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Exporta um projeto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Serviços cognitivas Visão Personalizada treinador

Exiba, edite projetos e treine os modelos, incluindo a capacidade de publicar, cancelar a publicação e exportar os modelos. Os treinadores não podem criar ou excluir o projeto. [Saiba mais](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Action | Criar um projeto. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Delete | Excluir um projeto específico. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Import/Action | Importa um projeto. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Projects/Export/Read | Exporta um projeto. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Leitor de Dados de Serviços Cognitivos (Versão Prévia)

Permite que você leia os dados dos Serviços Cognitivos.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-metrics-advisor-administrator"></a>Administrador do Orientador de métricas de serviços cognitivas

Acesso completo ao projeto, incluindo a configuração no nível do sistema. [Saiba mais](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Editor de QnA Maker de serviços cognitivas

Permite criar, editar, importar e exportar um KB. Não é possível publicar ou excluir um KB. [Saiba mais](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | /RoleAssignments/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma atribuição de função. |
> | /RoleDefinitions/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma definição de função. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Read | Obtém a lista de bases de conhecimentos ou detalhes de uma base de conhecimento específica. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/Read | Baixe a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Create/Write | Operação assíncrona para criar uma nova base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Write | Operação assíncrona para modificar uma base de conhecimento ou substituir o conteúdo da base de conhecimento. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer chamada para consultar a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Train/Action | Treine a chamada para adicionar sugestões à base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Read | Baixar alterações do tempo de execução. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Write | Substituir dados de alteração. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/Read | Obtém as chaves do ponto de extremidade para um ponto de extremidade |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/Action | Gera novamente uma chave de ponto de extremidade. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Read | Obtém as configurações do ponto de extremidade para um ponto de extremidade |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Write | Atualize o ponto de extremidade Seettings para um ponto de extremidade. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Operations/Read | Obtém detalhes de uma operação de execução prolongada específica. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Read | Obtém a lista de bases de conhecimentos ou detalhes de uma base de conhecimento específica. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/Read | Baixe a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Create/Write | Operação assíncrona para criar uma nova base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Write | Operação assíncrona para modificar uma base de conhecimento ou substituir o conteúdo da base de conhecimento. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | GenerateAnswer chamada para consultar a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Train/Action | Treine a chamada para adicionar sugestões à base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Read | Baixar alterações do tempo de execução. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Write | Substituir dados de alteração. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/Read | Obtém as chaves do ponto de extremidade para um ponto de extremidade |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/Action | Gera novamente uma chave de ponto de extremidade. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Read | Obtém as configurações do ponto de extremidade para um ponto de extremidade |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Write | Atualize o ponto de extremidade Seettings para um ponto de extremidade. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Operations/Read | Obtém detalhes de uma operação de execução prolongada específica. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>QnA Maker leitor de serviços cognitivas

Vamos ler e testar apenas um KB. [Saiba mais](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | /RoleAssignments/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma atribuição de função. |
> | /RoleDefinitions/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma definição de função. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Read | Obtém a lista de bases de conhecimentos ou detalhes de uma base de conhecimento específica. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/Read | Baixe a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer chamada para consultar a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Read | Baixar alterações do tempo de execução. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/Read | Obtém as chaves do ponto de extremidade para um ponto de extremidade |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Read | Obtém as configurações do ponto de extremidade para um ponto de extremidade |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Read | Obtém a lista de bases de conhecimentos ou detalhes de uma base de conhecimento específica. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/Read | Baixe a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | GenerateAnswer chamada para consultar a base de conhecimentos. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Read | Baixar alterações do tempo de execução. |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/Read | Obtém as chaves do ponto de extremidade para um ponto de extremidade |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Read | Obtém as configurações do ponto de extremidade para um ponto de extremidade |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Usuário dos Serviços Cognitivos

Permite ler e listar as chaves dos Serviços Cognitivos. [Saiba mais](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/Action | Listar chaves |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Ler alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Ler definições de log |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Ler definições de métrica |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Ler métrica |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | /Deployments/Operations/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista operações de implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. Cognitivaservices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Realidade misturada


### <a name="remote-rendering-administrator"></a>Administrador de renderização remoto

Fornece ao usuário recursos de conversão, gerenciamento de sessão, renderização e diagnóstico para a renderização remota do Azure [saiba mais](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Action | Iniciar conversão de ativo |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Read | Obter propriedades de conversão de ativo |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Delete | Parar conversão de ativo |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Obter propriedades da sessão |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Iniciar sessões |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Parar sessões |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/Read | Conectar-se a um servidor |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Conectar-se ao inspetor de renderização remoto |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Cliente de renderização remota

Fornece ao usuário recursos de gerenciamento de sessão, renderização e diagnóstico para a renderização remota do Azure. [Saiba mais](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Obter propriedades da sessão |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Iniciar sessões |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Parar sessões |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/Read | Conectar-se a um servidor |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Conectar-se ao inspetor de renderização remoto |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Colaborador da Conta de Âncoras Espaciais

Permite gerenciar âncoras espaciais em sua conta, mas não excluí-las [saiba mais](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Criar âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Descobrir âncoras espaciais próximas |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Localizar âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço de Âncoras Espaciais do Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Proprietário da Conta de Âncoras Espaciais

Permite gerenciar âncoras espaciais em sua conta, incluindo excluí-las [saiba mais](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Criar âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Delete | Excluir âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Descobrir âncoras espaciais próximas |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Localizar âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço de Âncoras Espaciais do Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Leitor da Conta de Âncoras Espaciais

Permite localizar e ler propriedades de âncoras espaciais em sua conta [saiba mais](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Descobrir âncoras espaciais próximas |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Localizar âncoras espaciais |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço de Âncoras Espaciais do Azure |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integração


### <a name="api-management-service-contributor"></a>Colaborador de serviço de gerenciamento de API

Pode gerenciar o serviço e as APIs [saiba mais](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/* | Criar e gerenciar o serviço de gerenciamento da API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Função do operador de serviço de gerenciamento da API

Pode gerenciar o serviço, mas não as APIs [saiba mais](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Ler instâncias do serviço de gerenciamento da API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/backup/Action | Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Delete | Excluir uma instância do Serviço de Gerenciamento de API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/managedeployments/Action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Restore/Action | Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatecertificate/Action | Carregar certificado TLS/SSL para um serviço de Gerenciamento de API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatehostname/Action | Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Write | Criar ou atualizar a instância do serviço de Gerenciamento de API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Obter chaves associadas ao usuário |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Função de leitor do Serviço de Gerenciamento de API

Acesso somente leitura ao serviço e às APIs [saiba mais](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Ler instâncias do serviço de gerenciamento da API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Obter chaves associadas ao usuário |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Proprietário de Dados da Configuração de Aplicativos

Permite o acesso completo aos dados de Configuração de Aplicativos. [Saiba mais](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Delete |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Leitor de Dados da Configuração de Aplicativos

Permite o acesso de leitura aos dados de Configuração de Aplicativos. [Saiba mais](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Proprietário de Dados de Barramento de Serviço do Azure

Permite acesso completo aos recursos do Barramento de Serviço do Azure. [Saiba mais](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Receptor de Dados do Barramento de Serviço do Azure

Permite acesso de recebimento aos recursos do Barramento de Serviço do Azure. [Saiba mais](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Remetente de Dados do Barramento de Serviço do Azure

Permite o acesso de envio aos recursos do Barramento de Serviço do Azure. [Saiba mais](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Proprietário de registro do Azure Stack

Permite que você gerencie registros do Microsoft Azure Stack.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/Read | Obter as propriedades de uma assinatura do Azure Stack Edge |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/*/Action |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/Read | Obter as propriedades de um produto do Marketplace do Azure Stack |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Read | Obter as propriedades de um registro do Microsoft Azure Stack |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Colaborador de EventGrid EventSubscription

Permite que você gerencie operações de assinatura de evento EventGrid. [Saiba mais](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Listar assinaturas de eventos globais por tipo de tópico |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/Read | Listar assinaturas de eventos regionais |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/Read | Listar assinaturas de eventos regionais por tipo de tópico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Leitor de EventGrid EventSubscription

Permite que você gerencie operações de assinatura de evento EventGrid. [Saiba mais](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Ler um eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Listar assinaturas de eventos globais por tipo de tópico |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/Read | Listar assinaturas de eventos regionais |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/Read | Listar assinaturas de eventos regionais por tipo de tópico |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>Colaborador de dados do FHIR

A função permite acesso completo de usuário ou principal aos dados do FHIR [saiba mais](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>Exportador de dados FHIR

A função permite que o usuário ou a entidade de segurança Leia e exporte dados do FHIR [saiba mais](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/Read | Ler recursos do FHIR (inclui pesquisa e histórico com controle de versão).  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/Export/Action | Operação de exportação ($export). |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>Leitor de dados do FHIR

A função permite que o usuário ou a entidade de segurança Leia FHIR dados [saiba mais](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/Read | Ler recursos do FHIR (inclui pesquisa e histórico com controle de versão).  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>Gravador de dados FHIR

A função permite que o usuário ou a entidade de segurança Leia e grave dados do FHIR [saiba mais](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/hardDelete/Action | Exclusão impressa (incluindo o histórico de versão). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Colaborador de Ambiente de Serviço de Integração

Permite que você gerencie ambientes de serviço de integração, mas não tem acesso a eles. [Saiba mais](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Ambiente de Serviço de Integração Developer

Permite que os desenvolvedores criem e atualizem fluxos de trabalho, contas de integração e conexões de API em ambientes de serviço de integração. [Saiba mais](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | /IntegrationServiceEnvironments/Read [Microsoft. Logic](resource-provider-operations.md#microsoftlogic) | Lê o ambiente do serviço de integração. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/Join/Action |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Colaborador de conta do sistemas inteligentes

Permite gerenciar contas do Intelligent Systems, mas não acessá-las.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.IntelligentSystems/accounts/* | Criar e gerenciar contas do Sistemas Inteligentes |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Colaborador de aplicativo lógico

Permite o gerenciamento de aplicativos lógicos, mas você não pode alterar o acesso a eles. [Saiba mais](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Listar as chaves de acesso das contas de armazenamento. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Retornar a conta de armazenamento com a conta fornecida. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/* | Gerenciar recursos de Aplicativos Lógicos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /StorageAccounts/listkeys/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Criar e gerenciar um Gateway de Conexão. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/* | Criar e gerenciar uma Conexão. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | Criar e gerenciar uma API Personalizada. |
> | /ServerFarms/Join/Action [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Une um plano do serviço de aplicativo |
> | /ServerFarms/Read [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | /Sites/Functions/listSecrets/Action [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Listar segredos de função. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Operador de aplicativo lógico

Permite a leitura, habilitação e desabilitação de aplicativos lógicos, mas você não pode editá-los ou atualizá-los. [Saiba mais](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/Read | Ler regras de alerta do Insights |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/Read |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/Read | Obter configurações de diagnóstico para Aplicativos Lógicos |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/Read | Obter as métricas disponíveis para Aplicativos Lógicos. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/*/Read | Ler recursos de Aplicativos Lógicos. |
> | /Workflows/Disable/Action [Microsoft. Logic](resource-provider-operations.md#microsoftlogic) | Desabilitar o fluxo de trabalho. |
> | /Workflows/Enable/Action [Microsoft. Logic](resource-provider-operations.md#microsoftlogic) | Permitir o fluxo de trabalho. |
> | /Workflows/Validate/Action [Microsoft. Logic](resource-provider-operations.md#microsoftlogic) | Valida o fluxo de trabalho. |
> | /Deployments/Operations/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista operações de implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/Read | Ler gateways de conexão. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/Read | Ler conexões. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/Read | Ler API personalizada. |
> | /ServerFarms/Read [Microsoft. Web](resource-provider-operations.md#microsoftweb) | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identidade


### <a name="managed-identity-contributor"></a>Colaborador de Identidade Gerenciada

Criar, ler, atualizar e excluir identidade atribuída ao usuário [saiba mais](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Read | Obter uma identidade atribuída ao usuário existente |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Write | Criar uma nova identidade atribuída ao usuário ou atualizar as marcas associadas a uma identidade atribuída a um usuário existente |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Delete | Excluir uma identidade atribuída ao usuário existente |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Operador de Identidade Gerenciada

Ler e atribuir identidade atribuída ao usuário [saiba mais](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Read |  |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Assign/Action |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Segurança


### <a name="attestation-contributor"></a>Colaborador de atestado

Pode ler gravar ou excluir a instância do provedor de atestado [saiba mais](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Leitor de atestado

Pode ler as propriedades do provedor de atestado [saiba mais](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Colaborador do Azure Sentinel

Colaborador do Azure Sentinel [saiba mais](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Pesquisar usando o novo mecanismo. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Exibir dados da análise de logs |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Obter solução OMS de saída |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Executar consultas dos dados no workspace |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Obter fontes de dados em um workspace. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Ler uma pasta de trabalho particular |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Leitor do Azure Sentinel

Leitor do Azure Sentinel [saiba mais](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Verificar a autorização e a licença do usuário |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Consultar indicadores de inteligência contra ameaças |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Consultar indicadores de inteligência contra ameaças |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Pesquisar usando o novo mecanismo. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Exibir dados da análise de logs |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/LinkedServices/Read | Obter serviços vinculados em um determinado workspace. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Obter uma consulta de pesquisa salva |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Obter solução OMS de saída |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Executar consultas dos dados no workspace |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Obter fontes de dados em um workspace. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Ler uma pasta de trabalho |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Ler uma pasta de trabalho particular |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Respondente do Azure Sentinel

Respondente do Azure Sentinel [saiba mais](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Verificar a autorização e a licença do usuário |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Incidents/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Acrescentar marcas ao indicador de inteligência contra ameaças |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Consultar indicadores de inteligência contra ameaças |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/Action | Inteligência contra ameaças de marcas em massa |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Acrescentar marcas ao indicador de inteligência contra ameaças |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/replaceTags/Action | Substituir marcas do indicador de inteligência contra ameaças |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Consultar indicadores de inteligência contra ameaças |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Pesquisar usando o novo mecanismo. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Exibir dados da análise de logs |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Obter fontes de dados em um workspace. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Obter uma consulta de pesquisa salva |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Obter solução OMS de saída |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Executar consultas dos dados no workspace |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Obter fontes de dados em um workspace. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Ler uma pasta de trabalho |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Ler uma pasta de trabalho particular |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Administrador de Key Vault

Execute todas as operações de plano de dados em um cofre de chaves e todos os objetos nela, incluindo certificados, chaves e segredos. Não é possível gerenciar os recursos do cofre de chaves nem gerenciar atribuições de função. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Verificar se um nome de chave do cofre é válido e não está em uso |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/Read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Diretor de certificados Key Vault

Execute qualquer ação nos certificados de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Verificar se um nome de chave do cofre é válido e não está em uso |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/Read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/certificatecas/* |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Certificates/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Colaborador do Key Vault

Gerencie cofres de chaves, mas não permite que você atribua funções no RBAC do Azure e não permite que você acesse segredos, chaves ou certificados. [Saiba mais](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Locations/deletedVaults/Purge/Action | Limpar um cofre de chaves com exclusão reversível |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer"></a>Key Vault o analista de criptografia

Execute qualquer ação nas chaves de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Verificar se um nome de chave do cofre é válido e não está em uso |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/Read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault usuário de criptografia do serviço de criptografia

Ler metadados de chaves e executar operações de encapsulamento/desencapsulamento. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | Criar ou atualizar um eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Ler um eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Delete | Excluir um eventSubscription |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Read | Listar chaves no cofre especificado ou ler propriedades e material público de uma chave. Para chaves assimétricas, essa operação expõe a chave pública e inclui a capacidade de executar algoritmos de chave pública, como criptografar e verificar assinatura. Chaves privadas e chaves simétricas nunca são expostas. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Wrap/Action | Encapsula uma chave simétrica com uma chave de Key Vault. Observe que, se a chave de Key Vault for assimétrica, essa operação poderá ser executada por entidades de segurança com acesso de leitura. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Unwrap/Action | Desencapsula uma chave simétrica com uma chave de Key Vault. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault usuário de criptografia

Executar operações criptográficas usando chaves. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Read | Listar chaves no cofre especificado ou ler propriedades e material público de uma chave. Para chaves assimétricas, essa operação expõe a chave pública e inclui a capacidade de executar algoritmos de chave pública, como criptografar e verificar assinatura. Chaves privadas e chaves simétricas nunca são expostas. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/UPDATE/Action | Atualiza os atributos especificados associados à chave especificada. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/backup/Action | Cria o arquivo de backup de uma chave. O arquivo pode ser usado para restaurar a chave em um Key Vault da mesma assinatura. Restrições podem ser aplicadas. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Encrypt/Action | Criptografa texto sem formatação com uma chave. Observe que, se a chave for assimétrica, essa operação poderá ser executada por entidades de segurança com acesso de leitura. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Decrypt/Action | Descriptografa o texto cifrado com uma chave. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Wrap/Action | Encapsula uma chave simétrica com uma chave de Key Vault. Observe que, se a chave de Key Vault for assimétrica, essa operação poderá ser executada por entidades de segurança com acesso de leitura. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Unwrap/Action | Desencapsula uma chave simétrica com uma chave de Key Vault. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Sign/Action | Assina um resumo de mensagem (hash) com uma chave. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/Verify/Action | Verifica a assinatura de um resumo de mensagem (hash) com uma chave. Observe que, se a chave for assimétrica, essa operação poderá ser executada por entidades de segurança com acesso de leitura. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Leitor de Key Vault

Ler metadados de cofres de chaves e seus certificados, chaves e segredos. Não é possível ler valores confidenciais, como conteúdo secreto ou material de chave. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Verificar se um nome de chave do cofre é válido e não está em uso |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/Read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/readMetadata/Action | Listar ou exibir as propriedades de um segredo, mas não seu valor. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Diretor de segredos Key Vault

Execute qualquer ação nos segredos de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/Read | Verificar se um nome de chave do cofre é válido e não está em uso |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/Read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Locations/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Operations/Read | Lista as operações disponíveis no provedor de recursos Microsoft.KeyVault |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Usuário de Key Vault segredos

Ler conteúdo secreto. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/getSecret/Action | Obtém o valor de um segredo. |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/readMetadata/Action | Listar ou exibir as propriedades de um segredo, mas não seu valor. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Colaborador de HSM gerenciado

Permite que você gerencie pools HSM gerenciados, mas não tem acesso a eles. [Saiba mais](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. keyvault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Administrador de Segurança

Visualiza e atualiza permissões para a Central de Segurança. Mesmas permissões que a função de leitor de segurança e também podem atualizar a política de segurança e ignorar alertas e recomendações. [Saiba mais](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Criar e gerenciar atribuições de política |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | Criar e gerenciar definições de política |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Criar e gerenciar isenções de política |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Criar e gerenciar conjuntos de política |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /ManagementGroups/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Listar grupos de gerenciamento para o usuário autenticado. |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Exibir dados da análise de logs |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Criar e gerenciar políticas e componentes de segurança |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Colaborador de Avaliação de Segurança

Permite enviar avaliações por push para a Central de Segurança

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /Assessments/Write [Microsoft. Security](resource-provider-operations.md#microsoftsecurity) | Criar ou atualizar avaliações de segurança em sua assinatura |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Gerenciador de Segurança (Herdado)

Esta é uma função herdada. Em vez disso, use o Administrador de Segurança.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/Read | Ler informações de configuração de máquinas virtuais clássicas |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/Write | Escrever configurações para máquinas virtuais clássicas |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/Read | Ler informações de configuração sobre a rede clássica |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Criar e gerenciar políticas e componentes de segurança |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Leitor de segurança

Visualiza permissões para a Central de Segurança. Pode exibir recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações. [Saiba mais](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Ler alerta de métrica clássico |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Exibir dados da análise de logs |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/*/Read |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/Read | Ler componentes de segurança e políticas |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/*/Read |  |
> | /IotDefenderSettings/packageDownloads/Action [Microsoft. Security](resource-provider-operations.md#microsoftsecurity) | Obter informações para download de pacotes do IoT defender |
> | /IotDefenderSettings/downloadManagerActivation/Action [Microsoft. Security](resource-provider-operations.md#microsoftsecurity) | Baixar o arquivo de ativação do Gerenciador com dados de cota de assinatura |
> | /IotSensors/downloadResetPassword/Action [Microsoft. Security](resource-provider-operations.md#microsoftsecurity) | Baixa redefinição do arquivo de senha para sensores IoT |
> | /ManagementGroups/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Usuário do DevTest Labs

Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. [Saiba mais](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /AvailabilitySets/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter as propriedades de um conjunto de disponibilidade |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read | Ler as propriedades de uma máquina virtual (tamanhos de VM, status de runtime, extensões de VM etc.) |
> | /VirtualMachines/DEALLOCATE/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Desligar a máquina virtual e liberar os recursos de computação |
> | /VirtualMachines/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter as propriedades de uma máquina virtual |
> | /VirtualMachines/Restart/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Reinicia a máquina virtual |
> | /VirtualMachines/Start/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Inicia a máquina virtual |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/Read | Ler as propriedades de um laboratório |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimAnyVm/Action | Declarar uma máquina virtual aleatória declarável no laboratório. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/createEnvironment/Action | Criar máquinas virtuais em um laboratório. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensureCurrentUserProfile/Action | Verificar se o usuário atual tem um perfil válido no laboratório. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Delete | Excluir fórmulas. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Read | Ler fórmulas. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Write | Adicionar ou modificar fórmulas. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Avaliar a política de laboratório. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/Claim/Action | Assumir o controle de uma máquina virtual existente |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/VirtualMachines/listApplicableSchedules/Action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | /LoadBalancers/backendAddressPools/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | /LoadBalancers/inboundNatRules/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/Read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga dos quais o adaptador de rede faz parte) |
> | /NetworkInterfaces/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Adicionar uma Máquina Virtual a um adaptador de rede. Não é possível alertá-lo. |
> | /NetworkInterfaces/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de adaptador de rede.  |
> | /NetworkInterfaces/Write [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/Read | Ler as propriedades de um endereço IP público |
> | /PublicIPAddresses/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em um endereço IP público. Não é possível alertá-lo. |
> | /PublicIPAddresses/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter uma definição de endereço IP público. |
> | /VirtualNetworks/Subnets/Join/Action [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Ingressar em uma rede virtual. Não é possível alertá-lo. |
> | /Deployments/Operations/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista operações de implantação. |
> | /Deployments/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista implantações. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /StorageAccounts/listKeys/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | **NotActions** |  |
> | /VirtualMachines/vmSizes/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Criador de laboratório

Permite criar novos laboratórios em suas contas de laboratório do Azure. [Saiba mais](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/Read |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/Action | Criar um laboratório em uma conta de laboratório. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | Obter o preço e a disponibilidade de combinações de tamanhos, geografias e sistemas operacionais para a conta de laboratório. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Obter restrições básicas e uso para esta assinatura |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Monitoramento


### <a name="application-insights-component-contributor"></a>Colaborador de componente do Application Insights

Pode gerenciar Application Insights componentes [saiba mais](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar regras de alerta clássicas |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/Read | Tokens de obtenção de métricas ao vivo |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Criar e gerenciar novas regras de alerta |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Components/* | Criar e gerenciar componentes do Insights |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Topology/Read | Ler topologia |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Transactions/Read | Transações de leitura |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Criar e gerenciar testes na Web do insights |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Depurador de Instantâneos do Application Insights

Concede permissão ao usuário para exibir e baixar os instantâneos de depuração coletados com o Depurador de Instantâneos do Application Insights. Observe que essas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Colaborador](#contributor). Quando você concede aos usuários a função de Depurador de Instantâneos do Application Insights, deve conceder a função diretamente ao usuário. A função não é reconhecida quando adicionada a uma função personalizada. [Saiba mais](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Colaborador de monitoramento

Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Saiba mais](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Components/* | Criar e gerenciar componentes do Insights |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/EventTypes/* | Listar eventos do Log de atividades (eventos de gerenciamento) em um assinatura. Essa permissão é aplicável ao acesso programático e ao portal para o Log de atividades. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Ler as métricas para um recurso. |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registrar o provedor do Microsoft Insights |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Criar e gerenciar testes na Web do insights |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Write | Criar um novo workspace ou links para um workspace existente fornecendo a ID do cliente do workspace existente. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/intelligencepacks/* | Ler/gravar/excluir pacotes de solução de análise de logs. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* | Ler/gravar/excluir pesquisas salvas do de análise de logs. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Executar uma consulta de pesquisa |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Action | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/storageinsightconfigs/* | Ler/gravar/excluir configurações de insight de armazenamento da análise de logs. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/Monitors/* | Obtenha informações sobre monitores de integridade de VM convidadas. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Publicador de Métricas de Monitoramento

Permite a publicação de métricas em relação aos recursos do Azure [saiba mais](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registrar o provedor do Microsoft Insights |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Gravar métricas |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Leitor de monitoramento

Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Saiba mais](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Executar uma consulta de pesquisa |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Colaborador da Pasta de Trabalho

Pode salvar as pastas de trabalho compartilhadas. [Saiba mais](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Write | Criar ou atualizar uma pasta de trabalho |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Delete | Excluir uma pasta de trabalho |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Ler uma pasta de trabalho |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Leitor de Pasta de Trabalho

Pode ler as pastas de trabalho. [Saiba mais](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Ler uma pasta de trabalho |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Gerenciamento + governança


### <a name="automation-job-operator"></a>Operador do Trabalho de Automação

Criar e gerenciar trabalhos usando runbooks de Automação. [Saiba mais](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /AutomationAccounts/hybridRunbookWorkerGroups/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Ler recursos do Hybrid Runbook Worker |
> | /AutomationAccounts/Jobs/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/resume/Action [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Reinicia um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/Stop/Action [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Interrompe um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/streams/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter um fluxo de trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/Suspend/Action [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Suspende um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/Write [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Criar um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/output/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter a saída de um trabalho |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operador de automação

Os operadores de automação podem iniciar, parar, suspender e retomar trabalhos [saiba mais](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /AutomationAccounts/hybridRunbookWorkerGroups/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Ler recursos do Hybrid Runbook Worker |
> | /AutomationAccounts/Jobs/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/resume/Action [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Reinicia um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/Stop/Action [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Interrompe um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/streams/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter um fluxo de trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/Suspend/Action [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Suspende um trabalho da Automação do Azure |
> | /AutomationAccounts/Jobs/Write [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Criar um trabalho da Automação do Azure |
> | /AutomationAccounts/jobSchedules/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter uma agenda de trabalho da Automação do Azure |
> | /AutomationAccounts/jobSchedules/Write [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Criar uma agenda de trabalho da Automação do Azure |
> | /AutomationAccounts/linkedWorkspace/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obtém o workspace vinculado à conta de automação |
> | /AutomationAccounts/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter uma conta da Automação do Azure |
> | /AutomationAccounts/runbooks/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter um runbook da Automação do Azure |
> | /AutomationAccounts/Schedules/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter um ativo de agendamento da Automação do Azure |
> | /AutomationAccounts/Schedules/Write [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Criar ou atualizar um ativo de agendamento da Automação do Azure |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /AutomationAccounts/Jobs/output/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter a saída de um trabalho |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Operador de Runbook de Automação

Ler propriedades do Runbook - para poder criar Trabalhos do runbook. [Saiba mais](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /AutomationAccounts/runbooks/Read [Microsoft. Automation](resource-provider-operations.md#microsoftautomation) | Obter um runbook da Automação do Azure |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Integração de Azure Connected Machine

Pode integrar Azure Connected Machines. [Saiba mais](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Ler qualquer computador do Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Grava um computador do Azure Arc |
> | [Microsoft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/Read | Obter atribuição de configuração de convidado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Administrador de Recursos de Azure Connected Machine

Pode ler, gravar, excluir e reintegrar Azure Connected Machines.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Ler qualquer computador do Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Grava um computador do Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Delete | Exclui um computador do Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/reconnect/Action |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Instala ou atualiza uma extensão do Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Leitor de cobrança

Permite o acesso de leitura aos dados de cobrança [saiba mais](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. billing](resource-provider-operations.md#microsoftbilling)/*/Read | Ler Informações de cobrança |
> | [Microsoft. Commerce](resource-provider-operations.md#microsoftcommerce)/*/Read |  |
> | [Microsoft. consumo](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | /ManagementGroups/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Listar grupos de gerenciamento para o usuário autenticado. |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Colaborador do Blueprint

Pode gerenciar definições de blueprint, mas não as atribuir. [Saiba mais](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/Blueprints/* | Criar e gerenciar definições de blueprint ou artefatos de blueprint. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Operador de Blueprint

Pode atribuir blueprints publicados existentes, mas não pode criar novos blueprints. Observe que isso só funcionará se a atribuição for feita com uma identidade gerenciada atribuída pelo usuário. [Saiba mais](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Criar e gerenciar atribuições de blueprint. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Colaborador do Gerenciamento de Custos

Pode exibir custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações) [saiba mais](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. consumo](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | /BillingPeriods/Read [Microsoft. billing](resource-provider-operations.md#microsoftbilling) |  |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | /Configurations/Read [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor) | Obter configurações |
> | /Recommendations/Read [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor) | Ler recomendações |
> | /ManagementGroups/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Listar grupos de gerenciamento para o usuário autenticado. |
> | /BillingProperty/Read [Microsoft. billing](resource-provider-operations.md#microsoftbilling) |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Leitor do Gerenciamento de Custos

Pode exibir dados de custo e configuração (por exemplo, orçamentos, exportações) [saiba mais](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. consumo](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | /BillingPeriods/Read [Microsoft. billing](resource-provider-operations.md#microsoftbilling) |  |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | /Configurations/Read [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor) | Obter configurações |
> | /Recommendations/Read [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor) | Ler recomendações |
> | /ManagementGroups/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Listar grupos de gerenciamento para o usuário autenticado. |
> | /BillingProperty/Read [Microsoft. billing](resource-provider-operations.md#microsoftbilling) |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Administrador de configurações de hierarquia

Permite que os usuários editem e excluam Configurações de hierarquia

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /ManagementGroups/Settings/Write [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Cria ou atualiza configurações de hierarquia do grupo de gerenciamento. |
> | /ManagementGroups/Settings/Delete [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Exclui as configurações de hierarquia do grupo de gerenciamento. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Cluster kubernetes – integração do arco do Azure

Definição de função para autorizar qualquer usuário/serviço a criar o recurso connectedClusters [saiba mais](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /Deployments/Write [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Criar ou atualizar uma implantação. |
> | /Subscriptions/operationresults/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os resultados da operação da assinatura. |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Grava connectedClusters |
> | [Microsoft. kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Read | Ler connectedClusters |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Função de Colaborador de Aplicativos Gerenciados

Permite a criação de recursos de aplicativos gerenciados.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Register/Action | Registrar para Soluções. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Função do Operador de Aplicativos Gerenciado

Permite que você leia e execute as ações nos recursos de aplicativo gerenciado

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/Read | Recuperar uma lista de aplicativos. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/*/Action |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Leitor de aplicativos gerenciados

Permite ler os recursos de um aplicativo gerenciado e solicitar acesso JIT.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Função de Exclusão de Atribuição de Registro de Serviços Gerenciados

A Função de Exclusão de Atribuição de Registro de Serviços Gerenciados permite que os usuários do locatário de gerenciamento excluam a atribuição de registro atribuída aos locatários. [Saiba mais](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. managedservices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Read | Recupera uma lista das atribuições de registro de Serviços Gerenciados. |
> | [Microsoft. managedservices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Delete | Remove a atribuição de registro de Serviços Gerenciados. |
> | [Microsoft. managedservices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/Read | Ler o status da operação do recurso. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Colaborador do Grupo de Gerenciamento

Função de colaborador do grupo de gerenciamento [saiba mais](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /ManagementGroups/Delete [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Excluir um grupo de gerenciamento. |
> | /ManagementGroups/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Listar grupos de gerenciamento para o usuário autenticado. |
> | /ManagementGroups/subscriptions/Delete [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Desassociar a assinatura do grupo de gerenciamento. |
> | /ManagementGroups/subscriptions/Write [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Associar a assinatura existente ao grupo de gerenciamento. |
> | /ManagementGroups/Write [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Criar ou atualizar um grupo de gerenciamento. |
> | /ManagementGroups/subscriptions/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Lista a assinatura sob o grupo de gerenciamento especificado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Leitor do Grupo de Gerenciamento

Função de Leitor do Grupo de Gerenciamento

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /ManagementGroups/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Listar grupos de gerenciamento para o usuário autenticado. |
> | /ManagementGroups/subscriptions/Read [Microsoft. Management](resource-provider-operations.md#microsoftmanagement) | Lista a assinatura sob o grupo de gerenciamento especificado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Colaborador de Conta APM do New Relic

Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Gravador de Dados de Insights de Política (Versão Prévia)

Permite o acesso de leitura às políticas de recurso e o acesso de gravação aos eventos de política de componente de recurso. [Saiba mais](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /Policyassignments/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma atribuição de política. |
> | /PolicyDefinitions/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma definição de política. |
> | /Policyexemptions/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma isenção de política. |
> | /Policysetdefinitions/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma definição de conjunto de políticas. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Verificar o status de conformidade de um determinado componente em relação às políticas de dados. |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/Action | Registrar os eventos da política do componente do recurso. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Comprador de reserva

Permite que você compre reservas para [saber mais](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | /Subscriptions/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter a lista de assinaturas. |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Register/Action [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity) | Registrar o provedor de recursos de Capacidade e permitir a criação de recursos de Capacidade. |
> | /Register/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Registra a assinatura com o provedor de recursos Microsoft.Compute |
> | /Register/Action [Microsoft. SQL](resource-provider-operations.md#microsoftsql) | Registrar a assinatura do provedor de recursos do Banco de Dados SQL do Microsoft Azure e habilitar a criação do Banco de Dados SQL do Microsoft Azure. |
> | /Register/Action [Microsoft. consumo](resource-provider-operations.md#microsoftconsumption) | Registra-se no RP de Consumo |
> | /Catalogs/Read [Microsoft. Capacity](resource-provider-operations.md#microsoftcapacity) | Lê o catálogo de Reservas |
> | /RoleAssignments/Read [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization) | Obter informações sobre uma atribuição de função. |
> | /ReservationRecommendations/Read [Microsoft. consumo](resource-provider-operations.md#microsoftconsumption) | Lista recomendações únicas ou compartilhadas para Instâncias Reservadas para uma assinatura. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/supporttickets/Write | Permite criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Colaborador da política de recurso

Usuários com direitos para criar ou modificar a política de recursos, criar tíquete de suporte e ler recursos ou hierarquias. [Saiba mais](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Criar e gerenciar atribuições de política |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | Criar e gerenciar definições de política |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Criar e gerenciar isenções de política |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Criar e gerenciar conjuntos de política |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Colaborador do Site Recovery

Permite que você gerencie Site Recovery serviço, exceto criação de cofre e atribuição de função [saiba mais](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | AllocateStamp é uma operação interna usada pelo serviço |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/* | Criar ou atualizar as configurações de alerta de replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Ler quaisquer eventos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/* | Criar e gerenciar malhas de replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/* | Criar e gerenciar políticas de replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/* | Criar e gerenciar planos de recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Criar e gerenciar a configuração de armazenamento do cofre de Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationOperationStatus/Read | Ler qualquer status da operação de replicação do cofre |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operador do Site Recovery

Permite failover e failback, mas não executa outras operações de gerenciamento de Site Recovery [saiba mais](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | /VirtualNetworks/Read [Microsoft. Network](resource-provider-operations.md#microsoftnetwork) | Obter a definição de rede virtual |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | AllocateStamp é uma operação interna usada pelo serviço |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Ler configurações de alertas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Ler quaisquer eventos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/checkConsistency/Action | Verificar consistência da malha |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Ler quaisquer malhas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/reassociateGateway/Action | Reassociar gateway |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/renewcertificate/Action | Renovar Certificado para malha do Microsoft Azure |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Ler quaisquer redes |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Ler quaisquer mapeamentos de rede |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Ler quaisquer contêineres de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Ler quaisquer itens que podem ser protegidos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Aplicar ponto de recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Confirmação de failover |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Failover planejado |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Ler quaisquer itens protegidos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Ler quaisquer pontos de recuperação de replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Reparar replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Proteger item protegido novamente |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Alterar contêiner de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Failover de Teste |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Limpeza do Failover de teste |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Failover |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Atualizar serviço de mobilidade |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Ler quaisquer mapeamentos de contêiner de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Atualizar provedor |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Ler quaisquer classificações de armazenamento |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Ler quaisquer vCenters |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Ler quaisquer políticas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/failoverCommit/Action | Plano de recuperação de confirmação de failover |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/plannedFailover/Action | Plano de recuperação de failover planejado |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Ler quaisquer planos de recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/reProtect/Action | Proteja plano de recuperação novamente |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailover/Action | Testar plano de recuperação de failover |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailoverCleanup/Action | Testar plano de recuperação de limpeza do failover |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/unplannedFailover/Action | Plano de recuperação de failover |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Leitor do Site Recovery

Permite exibir Site Recovery status, mas não executar outras operações de gerenciamento [saiba mais](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Ler configurações de alertas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Ler quaisquer eventos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Ler quaisquer malhas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Ler quaisquer redes |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Ler quaisquer mapeamentos de rede |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Ler quaisquer contêineres de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Ler quaisquer itens que podem ser protegidos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Ler quaisquer itens protegidos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Ler quaisquer pontos de recuperação de replicação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Ler quaisquer mapeamentos de contêiner de proteção |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Ler quaisquer classificações de armazenamento |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Ler quaisquer vCenters |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/Read | Ler todos os trabalhos |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Ler quaisquer políticas |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Ler quaisquer planos de recuperação |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | [Microsoft. recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Colaborador de solicitação de suporte

Permite que você crie e gerencie solicitações de suporte [saiba mais](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Colaborador de Marca

Permite que você gerencie marcas em entidades, sem fornecer acesso às entidades propriamente ditas. [Saiba mais](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Subscriptions/resourceGroups/Resources/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os recursos do grupo de recursos. |
> | /Subscriptions/Resources/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter os recursos de uma assinatura. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Outros


### <a name="azure-digital-twins-data-owner"></a>Proprietário de Dados dos Gêmeos Digitais do Azure

Função de acesso completo para digital gêmeos data-plano [saiba mais](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Ler, excluir, criar ou atualizar qualquer rota de evento |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Ler, criar, atualizar ou excluir qualquer atualização digital |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Commands/* | Invocar qualquer comando em um teledigital |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/* | Ler, criar, atualizar ou excluir qualquer relacionamento de atualização digital |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Models/* | Ler, criar, atualizar ou excluir qualquer modelo |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/* | Consultar qualquer grafo gêmeos digital |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Leitor de dados do Azure digital gêmeos

Função somente leitura para propriedades do plano de dados gêmeos digital [saiba mais](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Read | Ler qualquer "digital" |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/Read | Ler qualquer relação de FileUp digital |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/Read | Ler qualquer rota de evento |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Models/Read | Ler qualquer modelo |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/Action | Consultar qualquer grafo gêmeos digital |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>Colaborador do BizTalk

Permite gerenciar serviços do BizTalk, mas não acessá-los.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerenciar serviços BizTalk |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-contributor"></a>Colaborador do grupo de aplicativos de virtualização de desktop

Colaborador do grupo de aplicativos de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Ler hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Ler hostpools/sessionhosts |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Leitor do grupo de aplicativos de virtualização de desktop

Leitor do grupo de aplicativos de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/Read |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Ler applicationgroups |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Ler hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Ler hostpools/sessionhosts |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Deployments/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista implantações. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Ler alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Colaborador de virtualização de desktop

Colaborador da virtualização de desktops. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Colaborador do pool de hosts de virtualização de desktop

Colaborador do pool de hosts de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Leitor do pool de hosts de virtualização de desktop

Leitor do pool de hosts de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/Read |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Ler hostpools |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Deployments/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista implantações. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Ler alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Leitor de virtualização de desktop

Leitor de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/Read |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Deployments/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista implantações. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Ler alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Operador de host de sessão de virtualização de desktop

Operador do host da sessão de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Ler hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Usuário de virtualização de desktop

Permite que o usuário use os aplicativos em um grupo de aplicativos. [Saiba mais](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/Action | Usar o fileapplication |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>Operador de sessão de usuário de virtualização de desktop

Operador da sessão uesr de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Ler hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Ler hostpools/sessionhosts |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Colaborador do espaço de trabalho do Desktop Virtualization

Colaborador do espaço de trabalho de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/* |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Ler applicationgroups |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Leitor de espaço de trabalho do Desktop Virtualization

Leitor do espaço de trabalho de virtualização de desktop. [Saiba mais](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/Read | Ler espaços de trabalho |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Ler applicationgroups |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Deployments/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou lista implantações. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Ler alerta de métrica clássico |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Leitor de backup em disco

Fornece permissão para o cofre de backup para executar o backup em disco. [Saiba mais](../backup/disk-backup-faq.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Disks/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter as propriedades de um disco |
> | /Disks/beginGetAccess/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter o URI de SAS do disco para acesso de blob |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Operador de restauração de disco

Fornece permissão para o cofre de backup para executar a restauração do disco. [Saiba mais](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Disks/Write [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Criar um novo disco ou atualizar um existente |
> | /Disks/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter as propriedades de um disco |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Colaborador de instantâneo de disco

Fornece permissão para o cofre de backup para gerenciar instantâneos de disco. [Saiba mais](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | /Snapshots/Delete [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Excluir um instantâneo |
> | /Snapshots/Write [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Criar um novo instantâneo ou atualizar um existente |
> | /Snapshots/Read [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter as propriedades de um instantâneo |
> | /Snapshots/beginGetAccess/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter o URI de SAS do Instantâneo para acesso de blob |
> | /Snapshots/endGetAccess/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Revogar o URI de SAS do Instantâneo |
> | /Disks/beginGetAccess/Action [Microsoft. Compute](resource-provider-operations.md#microsoftcompute) | Obter o URI de SAS do disco para acesso de blob |
> | /StorageAccounts/listkeys/Action [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | /StorageAccounts/Write [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Criar uma conta de armazenamento com os parâmetros especificados, atualizar as propriedades ou marcas ou adicionar um domínio personalizado à conta de armazenamento especificada. |
> | /StorageAccounts/Read [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | /StorageAccounts/Delete [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) | Excluir uma conta de armazenamento existente. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Colaborador de Coleções de Trabalho do Agendador

Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las.

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | [Microsoft. insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Criar e gerenciar coleções de trabalhos |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="services-hub-operator"></a>Operador de Hub de serviços

Operador de Hub de serviços permite que você execute todas as operações de leitura, gravação e exclusão relacionadas aos conectores do hub de serviços. [Saiba mais](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Ações | Descrição |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Ler funções e atribuições de função |
> | /Subscriptions/resourceGroups/Read [Microsoft. Resources](resource-provider-operations.md#microsoftresources) | Obter ou listar de grupos de recursos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Criar e gerenciar uma implantação |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Write | Criar ou atualizar um conector de Hub de serviços |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Read | Exibir ou listar conectores de Hub de serviços |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Delete | Excluir conectores do hub de serviços |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/checkAssessmentEntitlement/Action | Lista os direitos de avaliação para um determinado espaço de trabalho do hub de serviços |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/Read | Exibir os direitos de oferta de suporte para um determinado espaço de trabalho do hub de serviços |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Workspaces/Read | Listar os espaços de trabalho do hub de serviços para um determinado usuário |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Próximas etapas

- [Corresponder provedor de recursos ao serviço](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Funções personalizadas do Azure](custom-roles.md)
- [Permissões na Central de Segurança do Azure](../security-center/security-center-permissions.md)
