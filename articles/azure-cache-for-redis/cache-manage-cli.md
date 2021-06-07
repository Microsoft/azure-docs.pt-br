---
title: Gerenciar o cache do Azure para Redis usando a CLI clássica do Azure
description: Saiba como instalar a CLI clássica do Azure em qualquer plataforma, como usá-lo para conectar-se a conta do Azure e como criar e gerenciar um Azure Cache para Redis clássico.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537687"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Como criar e gerenciar o Azure Cache para Redis usando a CLI clássica do Azure
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [CLI clássica do Azure](cache-manage-cli.md)
>

A CLI clássica do Azure é uma ótima maneira de gerenciar a infraestrutura do Azure em qualquer plataforma. Este artigo mostra como criar e gerenciar as instâncias do Azure Cache para Redis usando a CLI clássica do Azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Para obter os scripts de exemplo mais recentes da CLI do Azure, Azure Cache para Redis, consulte [Exemplos de CLI do Azure, Azure Cache para Redis](cli-samples.md).

## <a name="prerequisites"></a>Pré-requisitos
Para criar e gerenciar instâncias do Azure Cache para Redis usando a CLI clássica do Azure, é necessário concluir as etapas a seguir.

* Você deve ter uma conta do Azure. Se não tiver uma, você poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
* [Instale a CLI clássica do Azure](/cli/azure/install-classic-cli).
* Conecte a instalação da CLI do Azure a uma conta pessoal do Azure, ou a uma conta corporativa ou de estudante do Azure, e faça logon a partir da CLI clássica usando o comando `azure login`.
* Antes de executar qualquer um dos comandos a seguir, alterne a CLI clássica para o modo Gerenciador de Recursos, executando o comando `azure config mode arm`. Para obter mais informações, consulte [Use a CLI clássica do Azure para gerenciar recursos e grupos de recursos do Azure](../azure-resource-manager/management/manage-resources-cli.md).

## <a name="azure-cache-for-redis-properties"></a>Propriedades do Cache Redis do Azure
As propriedades a seguir são usadas durante a criação e a atualização de instâncias do Azure Cache para Redis.

| Propriedade | Opção | Descrição |
| --- | --- | --- |
| name |-n, --name |Nome do Azure Cache para Redis. |
| grupo de recursos |-g, --resource-group |Nome do Grupo de Recursos. |
| local |-l, --location |Local para criar o cache. |
| tamanho |-z, --size |Tamanho do Azure Cache para Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |SKU Redis. Deve ser um destes: [Básico, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |Propriedade EnableNonSslPort do Azure Cache para Redis. Adicione esse sinalizador se você quiser habilitar a porta não TLS/SSL para o cache |
| Configuração do Redis |-c, --redis-configuration |Configuração do Redis. Insira uma cadeia de caracteres JSON formatada de valores e chaves de configuração aqui. Format:"{"":"","":""}" |
| Configuração do Redis |-f, --redis-configuration-file |Configuração do Redis. Insira o caminho de um arquivo que contenha os valores e as chaves de configuração aqui. Formato da entrada do arquivo: {"":"","":""} |
| Contagem de Fragmento |-r, --shard-count |Número de Fragmentos para criar um cache de Cluster Premium com clustering. |
| Rede Virtual |-v, --virtual-network |Ao hospedar o cache em uma VNET, especifica a ID de recurso ARM exata da rede virtual para implantar o Azure Cache para Redis. Exemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo de chave |-t, --key-type |Tipo de chave a ser renovada. Valores válidos: [Primary, Secondary] |
| StaticIP |-p,--static-IP \<static-ip\> |Ao hospedar o cache em uma VNET, especifica um endereço IP exclusivo na sub-rede do cache. Se ele não for fornecido, um será escolhido para você na sub-rede. |
| Sub-rede |t, --subnet \<subnet\> |Ao hospedar o cache em uma VNET, especifica o nome da sub-rede na qual implantar o cache. |
| VirtualNetwork |-v,--Virtual-Network \<virtual-network\> |Ao hospedar o cache em uma VNET, especifica a ID de recurso ARM exata da rede virtual para implantar o Azure Cache para Redis. Exemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscription |-s, --subscription |O identificador da assinatura. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Consulte todos os comandos do Azure Cache para Redis
Para ver todos os comandos do Azure Cache para Redis e seus parâmetros, use o comando `azure rediscache -h`.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Criar um Cache do Azure para Redis
Para criar um Azure Cache para Redis, use o seguinte comando:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

Para saber mais sobre esse comando, execute o comando `azure rediscache create -h`.

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Excluir um Azure Cache para Redis
Para excluir um Azure Cache para Redis, use o seguinte comando:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

Para saber mais sobre esse comando, execute o comando `azure rediscache delete -h`.

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Listar todos os Azure Cache para Redis na Assinatura ou Grupo de Recursos
Para listar todos os Azure Cache para Redis na Assinatura ou Grupo de Recursos, use o comando a seguir:

```azurecli
    azure rediscache list [options]
```

Para saber mais sobre esse comando, execute o comando `azure rediscache list -h`.

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Mostrar as propriedades de um Azure Cache para Redis existente
Para mostrar as propriedades de um Azure Cache para Redis existente, use o seguinte comando:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

Para saber mais sobre esse comando, execute o comando `azure rediscache show -h`.

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Alterar as configurações de um Azure Cache para Redis existente
Para alterar as configurações de um Azure Cache para Redis, use o seguinte comando:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

Para saber mais sobre esse comando, execute o comando `azure rediscache set -h`.

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Renovar a chave de autenticação para um Azure Cache para Redis
Para renovar a chave de autenticação para um Azure Cache para Redis existente, use o seguinte comando:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

Especifique `Primary` ou `Secondary` como `key-type`.

Para saber mais sobre esse comando, execute o comando `azure rediscache renew-key -h`.

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Listar chaves primárias e secundárias de um Azure Cache para Redis existente
Para listar as chaves Primárias e Secundárias de um Azure Cache para Redis, use o seguinte comando:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

Para saber mais sobre esse comando, execute o comando `azure rediscache list-keys -h`.

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```