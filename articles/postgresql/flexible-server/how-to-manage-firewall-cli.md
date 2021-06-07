---
title: Gerenciar regras de firewall-CLI do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Criar e gerenciar regras de firewall para o banco de dados do Azure para PostgreSQL – servidor flexível usando CLI do Azure linha de comando.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 36249694c5a4de8a738853892f827c6d9e1e4aff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489465"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Criar e gerenciar o banco de dados do Azure para PostgreSQL – regras de firewall de servidor flexíveis usando o CLI do Azure

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Banco de dados do Azure para PostgreSQL-o servidor flexível dá suporte a dois tipos de métodos de conectividade de rede mutuamente exclusivos para se conectar ao seu servidor flexível. As duas opções são:

* Acesso público (endereços IP permitidos)
* Acesso privado (Integração VNet)

Neste artigo, nos concentraremos na criação do servidor PostgreSQL com **acesso público (endereços IP permitidos)** usando CLI do Azure e fornecerão uma visão geral sobre CLI do Azure comandos que podem ser usados para criar, atualizar, excluir, listar e mostrar regras de firewall após a criação do servidor. Com *acesso público (endereços IP permitidos)*, as conexões com o servidor PostgreSQL são restritas somente a endereços IP permitidos. Os endereços IP do cliente precisam ser permitidos em regras de firewall. Para saber mais sobre isso, consulte [acesso público (endereços IP permitidos)](./concepts-networking.md#public-access-allowed-ip-addresses). As regras de firewall podem ser definidas no momento da criação do servidor (recomendado), mas também podem ser adicionadas posteriormente.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O [Azure Cloud Shell](../../cloud-shell/overview.md) é um shell gratuito e interativo que poderá ser usado para executar as etapas deste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode abrir o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e selecione **Enter** para executá-lo.

Caso prefira instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará entrar na sua conta usando o comando [az login](/cli/azure/reference-index#az-login). Observe a propriedade **ID** , que se refere à **ID da assinatura** da sua conta do Azure.

```azurecli-interactive
az login
```

Selecione a assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az-account-set). Anote o valor de **ID** da saída de **logon AZ** para usar como o argumento valor para **assinatura** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Criar regra de firewall durante a criação de servidor flexível usando o CLI do Azure

Você pode usar o `az postgres flexible-server --public access` comando para criar o servidor flexível com *acesso público (endereços IP permitidos)* e configurar as regras de firewall durante a criação de um servidor flexível. Você pode usar a opção **--Public-Access** para fornecer os endereços IP permitidos que serão capazes de se conectar ao servidor. Você pode fornecer um único ou intervalo de endereços IP a ser incluído na lista de IPs permitidos. O intervalo de endereços IP deve ser separado por um traço e não contém espaços. Há várias opções para criar um servidor flexível usando a CLI, conforme mostrado no exemplo a seguir.

Consulte a documentação de referência do CLI do Azure <!--FIXME --> para obter uma lista completa de parâmetros configuráveis da CLI. Por exemplo, nos comandos abaixo, você pode opcionalmente especificar o grupo de recursos.

- Crie um servidor flexível com acesso público e adicione o endereço IP do cliente para ter acesso ao servidor
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Crie um servidor flexível com acesso público e adicione o intervalo de endereços IP para ter acesso a esse servidor

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Crie um servidor flexível com acesso público e permita que os aplicativos de endereços IP do Azure se conectem ao seu servidor flexível
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Esta opção configura o firewall para permitir acesso público de serviços e recursos do Azure no Azure para esse servidor, incluindo conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
    >
- - Criar um servidor flexível com acesso público e permitir todos os endereços IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > O comando acima criará uma regra de firewall com endereço IP inicial = 0.0.0.0, endereço IP final = 255.255.255.255 e nenhum endereço IP será bloqueado. Qualquer host na Internet pode acessar este servidor. É altamente recomendável usar essa regra apenas temporariamente e somente em servidores de teste que não contenham dados confidenciais.
- Criar um servidor flexível com acesso público e sem endereço IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > Não recomendamos criar um servidor sem nenhuma regra de firewall. Se você não adicionar nenhuma regra de firewall, nenhum cliente poderá se conectar ao servidor.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Criar e gerenciar a regra de firewall após a criação do servidor
O comando **AZ postgres flexível-Server firewall-Rule** é usado no CLI do Azure para criar, excluir, listar, mostrar e atualizar regras de firewall.

Comandos:
- **criar**: Crie uma regra de firewall de servidor flexível.
- **lista**: lista as regras de firewall de servidor flexíveis.
- **atualização**: Atualize uma regra de firewall de servidor flexível.
- **Mostrar**: Mostre os detalhes de uma regra de firewall de servidor flexível.
- **excluir**: excluir uma regra de firewall de servidor flexível.

Consulte a documentação de referência do CLI do Azure <!--FIXME --> para obter uma lista completa de parâmetros configuráveis da CLI. Por exemplo, nos comandos abaixo, você pode opcionalmente especificar o grupo de recursos.

### <a name="create-a-firewall-rule"></a>Criar uma regra de firewall
Use o `az postgres flexible-server firewall-rule create` comando para criar uma nova regra de firewall no servidor.
Para permitir o acesso a um intervalo de endereços IP, forneça o endereço IP como o endereço IP inicial e o endereço IP final, como neste exemplo.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso de um único endereço IP, basta fornecer um único endereço IP, como neste exemplo.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Para permitir que aplicativos de endereços IP do Azure se conectem ao seu servidor flexível, forneça o endereço IP 0.0.0.0 como o IP inicial, como neste exemplo.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura o firewall para permitir acesso público de serviços e recursos do Azure no Azure para esse servidor, incluindo conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 
Após o êxito, cada saída de comando listará os detalhes da regra de firewall que você criou, no formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

### <a name="list-firewall-rules"></a>Listar regras de firewall 
Use o `az postgres flexible-server firewall-rule list` comando para listar as regras de firewall de servidor existentes no servidor. Observe que o atributo de nome do servidor é especificado na opção **--Name** . 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
A saída listará as regras, se houver, no formato JSON (por padrão). Você pode usar a opção--output Table * * para gerar os resultados em um formato de tabela mais legível.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Atualizar uma regra de firewall
Use o `az postgres flexible-server firewall-rule update` comando para atualizar uma regra de firewall existente no servidor. Forneça o nome da regra de firewall existente como entrada, bem como os atributos endereço IP inicial e endereço IP final a serem atualizados.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall atualizada, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

> [!NOTE]
> Se a regra de firewall não existir, ela será criada pelo comando de atualização.
### <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra de firewall
Use o `az postgres flexible-server firewall-rule show` comando para mostrar os detalhes da regra de firewall existente do servidor. Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Após o êxito, a saída do comando listará os detalhes da regra de firewall especificado, em formato JSON (por padrão). Se houver uma falha, a saída mostrará o texto da mensagem de erro em vez disso.

### <a name="delete-a-firewall-rule"></a>Excluir uma regra de firewall
Use o `az postgres flexible-server firewall-rule delete` comando para excluir uma regra de firewall existente do servidor. Forneça o nome da regra de firewall existente.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Após o êxito, não haverá saída. Em caso de falha, o texto da mensagem de erro será exibido.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [rede no banco de dados do Azure para PostgreSQL – servidor flexível](./concepts-networking.md)
- Saiba mais sobre o [banco de dados do Azure para PostgreSQL – regras de firewall de servidor flexíveis](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Crie e gerencie o banco de dados do Azure para PostgreSQL – regras de firewall de servidor flexíveis usando o portal do Azure](./how-to-manage-firewall-portal.md).
