---
title: Gerenciar redes virtuais-CLI do Azure-banco de dados do Azure para MySQL-servidor flexível
description: Criar e gerenciar redes virtuais para o banco de dados do Azure para MySQL-servidor flexível usando o CLI do Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a41cd2ce14ceb452d783b472955de347199d0870
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109463"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Criar e gerenciar redes virtuais para o banco de dados do Azure para MySQL-servidor flexível usando o CLI do Azure

> [!IMPORTANT]
> Atualmente, o Servidor Flexível do Banco de Dados do Azure para MySQL está em versão prévia pública

O servidor flexível do banco de dados do Azure para MySQL é compatível com tipos de métodos de conectividade de rede mutuamente exclusivos, para se conectar ao seu servidor flexível. As duas opções são:

- Acesso público (endereços IP permitidos)
- Acesso privado (Integração VNet)

Neste artigo, vamos nos concentrar na criação do MySQL Server com **acesso privado (integração VNet)** usando CLI do Azure. Com o *acesso privado (integração VNet)*, você pode implantar seu servidor flexível em sua própria [rede virtual do Azure](../../virtual-network/virtual-networks-overview.md). As redes virtuais do Azure fornecem comunicação de rede privada e segura. No acesso privado, as conexões com o servidor MySQL são restritas apenas dentro de sua rede virtual. Para saber mais sobre isso, consulte [acesso privado (integração VNet)](./concepts-networking.md#private-access-vnet-integration).

No banco de dados do Azure para MySQL servidor flexível, você só pode implantar o servidor em uma rede virtual e uma sub-rede durante a criação do servidor. Depois que o servidor flexível for implantado em uma rede virtual e sub-rede, você não poderá movê-lo para outra rede virtual, sub-rede ou *acesso público (endereços IP permitidos)*.

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

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Criar um servidor flexível do banco de dados do Azure para MySQL usando a CLI
Você pode usar o `az mysql flexible-server` comando para criar o servidor flexível com *acesso privado (integração VNet)*. Esse comando usa o acesso privado (integração VNet) como o método de conectividade padrão. Uma rede virtual e uma sub-rede serão criadas para você se nenhuma for fornecida. Você também pode fornecer a rede virtual e a sub-rede já existentes usando a ID de sub-rede. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Há várias opções para criar um servidor flexível usando a CLI, conforme mostrado nos exemplos abaixo.

>[!Important]
> O uso desse comando delegará a sub-rede a **Microsoft. DBforMySQL/flexibleServers**. Essa delegação significa que somente os servidores flexíveis do banco de dados do Azure para MySQL podem usar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada.
>

Consulte a documentação de [referência](/cli/azure/mysql/flexible-server) do CLI do Azure para obter a lista completa de parâmetros da CLI configuráveis. Por exemplo, nos comandos abaixo, você pode opcionalmente especificar o grupo de recursos.

- Criar um servidor flexível usando a rede virtual padrão, a sub-rede com o prefixo de endereço padrão
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Crie um servidor flexível usando a rede virtual e a sub-rede já existentes. Se a rede virtual e a sub-rede fornecidas não existirem, a rede virtual e a sub-rede com o prefixo de endereço padrão serão criados.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Crie um servidor flexível usando a rede virtual já existente, a sub-rede e o usando a ID de sub-rede. A sub-rede fornecida não deve ter nenhum outro recurso implantado nela e essa sub-rede será delegada para **Microsoft. DBforMySQL/flexibleServers**, se ainda não tiver sido delegada.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > A rede virtual e a sub-rede devem estar na mesma região e assinatura que o servidor flexível.
<
- Crie um servidor flexível usando uma nova rede virtual, sub-rede com um prefixo de endereço não padrão.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Consulte a documentação de [referência](/cli/azure/mysql/flexible-server) do CLI do Azure para obter a lista completa de parâmetros da CLI configuráveis.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [rede no banco de dados do Azure para MySQL servidor flexível](./concepts-networking.md).
- [Crie e gerencie uma rede virtual do Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./how-to-manage-virtual-network-portal.md).
- Saiba mais sobre [a rede virtual do banco de dados do Azure para MySQL servidor flexível](./concepts-networking.md#private-access-vnet-integration).