---
title: Configurar nós de transação do serviço Blockchain do Azure
description: Como configurar os nós de transação do serviço Blockchain do Azure
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 50f9b2aa0f5e1a1b1095b6c799ce0706b6e541de
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150874"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurar nós de transação do serviço Blockchain do Azure

Nós de transação são usados para enviar transações blockchain para o serviço Blockchain do Azure por meio de um ponto de extremidade público. O nó de transação padrão contém a chave privada da conta Ethereum registrada no blockchain e, como tal, não pode ser excluída.

Para exibir os detalhes do nó de transação padrão:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue para o membro do serviço Azure Blockchain. Selecione **nós de transação**.

    ![Selecionar o nó de transação padrão](./media/configure-transaction-nodes/nodes.png)

    Os detalhes da visão geral incluem endereços de ponto de extremidade públicos e chave pública.

## <a name="create-transaction-node"></a>Criar nó de transação

Você pode adicionar até nove nós de transação adicionais ao seu membro blockchain, para um total de 10 nós de transação. Ao adicionar nós de transação, você pode aumentar a escalabilidade ou distribuir a carga. Por exemplo, você poderia ter um ponto de extremidade de nó de transação para diferentes aplicativos cliente.

Para adicionar um nó de transação:

1. Na portal do Azure, navegue até o membro do serviço Blockchain do Azure e selecione **nós de transação > adicionar**.
1. Conclua as configurações para o novo nó de transação.

    ![Adicionar nó de transação](./media/configure-transaction-nodes/add-node.png)

    | Configuração | Descrição |
    |---------|-------------|
    | Name | Nome do nó de transação. O nome é usado para criar o endereço DNS para o ponto de extremidade do nó de transação. Por exemplo, `newnode-myblockchainmember.blockchain.azure.com`. O nome do nó não pode ser alterado depois de ser criado. |
    | Senha | Defina uma senha forte. Use a senha para acessar o ponto de extremidade do nó de transação com a autenticação básica.

1. Selecione **Criar**.

    O provisionamento de um novo nó de transação leva cerca de 10 minutos. Os nós de transação adicionais incorrem no custo. Para obter mais informações sobre os custos, consulte [preços do Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Pontos de extremidade

Os nós de transação têm um nome DNS exclusivo e pontos de extremidade públicos.

Para exibir os detalhes do ponto de extremidade de um nó de transação:

1. Na portal do Azure, navegue até um dos nós de transação de membro do serviço Blockchain do Azure e selecione **visão geral**.

    ![Captura de tela mostra a visão geral de nós de transação para um membro blockchain.](./media/configure-transaction-nodes/endpoints.png)

Os pontos de extremidade do nó de transação são seguros e exigem autenticação. Você pode se conectar a um ponto de extremidade de transação usando a autenticação do Azure AD, autenticação básica de HTTPS e usando uma chave de acesso sobre HTTPS ou WebSocket sobre TLS.

### <a name="azure-active-directory-access-control"></a>Controle de acesso Azure Active Directory

Os pontos de extremidade do nó de transação do serviço Blockchain do Azure dão suporte à autenticação do Azure Active Directory (Azure AD). Você pode conceder acesso de usuário, grupo e entidade de serviço do Azure AD ao seu ponto de extremidade.

Para conceder o controle de acesso do AD do Azure ao seu ponto de extremidade:

1. Na portal do Azure, navegue até o membro do serviço Blockchain do Azure e selecione **nós de transação > controle de acesso (iam) > adicionar > atribuição de função adicionar**.
1. Crie uma nova atribuição de função para um usuário, grupo ou entidade de serviço (funções de aplicativo).

    ![Adicionar função IAM](./media/configure-transaction-nodes/add-role.png)

    | Configuração | Ação |
    |---------|-------------|
    | Função | Selecione **proprietário**, **colaborador**ou **leitor**.
    | Atribuir acesso a | Selecione **usuário, grupo ou entidade de serviço do Azure ad**.
    | Selecionar | Pesquise pelo usuário, grupo ou entidade de serviço que você deseja adicionar.

1. Selecione **salvar** para adicionar a atribuição de função.

Para obter mais informações sobre o controle de acesso do AD do Azure, consulte [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md)

Para obter detalhes sobre como se conectar usando a autenticação do Azure AD, consulte [conectar-se ao seu nó usando a autenticação do AAD](configure-aad.md).

### <a name="basic-authentication"></a>Autenticação básica

Para autenticação básica HTTPS, as credenciais de nome de usuário e senha são passadas no cabeçalho HTTPS da solicitação para o ponto de extremidade.

Você pode exibir os detalhes do ponto de extremidade de autenticação básica de um nó de transação no portal do Azure. Navegue até um dos nós de transação de membro do serviço Blockchain do Azure e selecione **autenticação básica** em configurações.

![Autenticação básica](./media/configure-transaction-nodes/basic.png)

O nome de usuário é o nome do seu nó e não pode ser alterado.

Para usar a URL, substitua \<password\> pela senha definida quando o nó foi provisionado. Você pode atualizar a senha selecionando **Redefinir senha**.

### <a name="access-keys"></a>Chaves de acesso

Para a autenticação de chave de acesso, a chave de acesso é incluída na URL do ponto de extremidade. Quando o nó de transação é provisionado, duas chaves de acesso são geradas. A chave de acesso pode ser usada para autenticação. Duas chaves permitem que você altere e gire as chaves.

Você pode exibir os detalhes da chave de acesso de um nó de transação e copiar os endereços de ponto de extremidade que incluem as chaves de acesso. Navegue até um dos nós de transação de membro do serviço Blockchain do Azure e selecione **chaves de acesso** em configurações.

### <a name="firewall-rules"></a>Regras de firewall

As regras de firewall permitem limitar os endereços IP que podem tentar autenticar em seu nó de transação.  Se nenhuma regra de firewall estiver configurada para o nó de transação, ela não poderá ser acessada por nenhuma entidade.  

Para exibir as regras de firewall de um nó de transação, navegue até um dos nós de transação de membro do serviço Blockchain do Azure e selecione **regras de firewall** em configurações.

Você pode adicionar regras de firewall inserindo um nome de regra, endereço IP inicial e um endereço IP final na grade **regras de firewall** .

![Regras de firewall](./media/configure-transaction-nodes/firewall-rules.png)

Para habilitar:

* **Endereço IP único:** Configure o mesmo endereço IP para os endereços IP inicial e final.
* **Intervalo de endereços IP:** Configure o intervalo de endereços IP inicial e final. Por exemplo, um intervalo começando em 10.221.34.0 e terminando em 10.221.34.255 habilitaria toda a sub-rede 10.221.34.xxx.
* **Permitir todos os endereços IP:** Configure o endereço IP inicial para 0.0.0.0 e o endereço IP final para 255.255.255.255.

## <a name="connection-strings"></a>Cadeias de conexão

A sintaxe de cadeia de conexão para seu nó de transação é fornecida para a autenticação básica ou para o uso de chaves de acesso. São fornecidas cadeias de conexão, incluindo chaves de acesso sobre HTTPS e WebSockets.

Você pode exibir as cadeias de conexão de um nó de transação e copiar os endereços de ponto de extremidade. Navegue até um dos nós de transação de membro do serviço Blockchain do Azure e selecione **cadeias de conexão** em configurações.

![Cadeias de conexão](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Código de exemplo

O código de exemplo é fornecido para habilitar rapidamente a conexão com o nó de transação por meio de Web3, netaqui, Web3js e Truffle.

Você pode exibir o código de conexão de exemplo de um nó de transação e copiá-lo para usar com ferramentas de desenvolvedor populares. Vá para um dos nós de transação de membro do serviço Blockchain do Azure e selecione o **código de exemplo** em configurações.

Escolha a guia Web3, myhere, Truffle ou Web3j para exibir o exemplo de código que você deseja usar.

![Código de exemplo](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar nós de transação usando CLI do Azure](manage-cli.md)
