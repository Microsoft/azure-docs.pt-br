---
title: Gerenciar o Cache Redis do Azure usando a CLI do Azure | Microsoft Docs
description: "Saiba como instalar a CLI do Azure em qualquer plataforma, como usá-la para se conectar à sua conta do Azure e como criar e gerenciar um cache Redis da CLI do Azure."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: ba078a870a3998568170cc197bd6698b97b7fadb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Como criar e gerenciar o Cache Redis do Azure usando a Interface de Linha de Comando do Azure (CLI do Azure)
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [CLI do Azure](cache-manage-cli.md)
>
>

A CLI do Azure é uma ótima maneira de gerenciar sua infraestrutura do Azure de qualquer plataforma. Este artigo mostra como criar e gerenciar suas instâncias do Cache Redis do Azure usando a CLI do Azure.

> [!NOTE]
> Este artigo se aplica a uma versão anterior da CLI do Azure. Para os scripts de exemplo da CLI do Azure 2.0 mais recentes, consulte [Exemplos de Cache Redis da CLI do Azure](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para criar e gerenciar as instâncias do Cache Redis do Azure usando a CLI do Azure, você deverá concluir as etapas a seguir.

* Você deve ter uma conta do Azure. Se não tiver uma, você poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
* [Instale a CLI do Azure](../cli-install-nodejs.md).
* Conecte sua instalação da CLI do Azure a uma conta do Azure pessoal, ou a uma conta corporativa ou de estudante do Azure, e faça logon na CLI do Azure usando o comando `azure login` . Para entender as diferenças e escolher entre elas, veja [Conectar-se a uma assinatura do Azure a partir da interface de linha de comando do Azure (CLI do Azure)](../xplat-cli-connect.md).
* Antes de executar um dos comandos a seguir, alterne a CLI do Azure para o modo Gerenciador de Recursos executando o comando `azure config mode arm` . Para obter mais informações, consulte [Use a CLI do Azure para gerenciar recursos e grupos de recursos do Azure](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Propriedades do Cache Redis
As propriedades a seguir são usadas durante a criação e a atualização de instâncias do Cache Redis.

| Propriedade | Switch | Descrição |
| --- | --- | --- |
| name |-n, --name |Nome do Cache Redis. |
| grupo de recursos |-g, --resource-group |Nome do Grupo de Recursos. |
| location |-l, --location |Local para criar o cache. |
| tamanho |-z, --size |Tamanho do Cache Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |SKU Redis. Deve ser um destes: [Básico, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |Propriedade EnableNonSslPort do Cache Redis. Adicione este sinalizador se quiser habilitar a Porta Não SSL para o cache |
| Configuração do Redis |-c, --redis-configuration |Configuração do Redis. Insira uma cadeia de caracteres JSON formatada de valores e chaves de configuração aqui. Format:"{"":"","":""}" |
| Configuração do Redis |-f, --redis-configuration-file |Configuração do Redis. Insira o caminho de um arquivo que contenha os valores e as chaves de configuração aqui. Formato da entrada do arquivo: {"":"","":""} |
| Contagem de Fragmento |-r, --shard-count |Número de Fragmentos para criar um cache de Cluster Premium com clustering. |
| Rede Virtual |-v, --virtual-network |Ao hospedar o cache em uma VNET, especifica a ID de recurso ARM exata da rede virtual para implantar o cache redis. Exemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo de chave |-t, --key-type |Tipo de chave a ser renovada. Valores válidos: [Primary, Secondary] |
| StaticIP |-p, --static-ip <static-ip> |Ao hospedar o cache em uma VNET, especifica um endereço IP exclusivo na sub-rede do cache. Se ele não for fornecido, um será escolhido para você na sub-rede. |
| Sub-rede |t, --subnet <subnet> |Ao hospedar o cache em uma VNET, especifica o nome da sub-rede na qual implantar o cache. |
| VirtualNetwork |-v, --virtual-network <rede virtual> |Ao hospedar o cache em uma VNET, especifica a ID de recurso ARM exata da rede virtual para implantar o cache redis. Exemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Assinatura |-s, --subscription |O identificador da assinatura. |

## <a name="see-all-redis-cache-commands"></a>Ver todos os comandos do Cache Redis
Para ver todos os comandos do Cache Redis e seus parâmetros, use o comando `azure rediscache -h` .

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Criar um Cache Redis
Para criar um Cache Redis, use o seguinte comando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para saber mais sobre esse comando, execute o comando `azure rediscache create -h` .

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Excluir um Cache Redis existente
Para excluir um Cache Redis, use o seguinte comando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para saber mais sobre esse comando, execute o comando `azure rediscache delete -h` .

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Listar todos os Caches Redis em sua Assinatura ou Grupo de Recursos
Para listar todos os Caches Redis em sua Assinatura ou Grupo de Recursos, use o seguinte comando:

    azure rediscache list [options]

Para saber mais sobre esse comando, execute o comando `azure rediscache list -h` .

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
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

## <a name="show-properties-of-an-existing-redis-cache"></a>Mostrar as propriedades de um Cache Redis existente
Para mostrar as propriedades de um Cache Redis existente, use o seguinte comando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para saber mais sobre esse comando, execute o comando `azure rediscache show -h` .

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Alterar as configurações de um Cache Redis existente
Para alterar as propriedades de um Cache Redis existente, use o seguinte comando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para saber mais sobre esse comando, execute o comando `azure rediscache set -h` .

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Renovar a chave de autenticação para um Cache Redis existente
Para renovar a chave de autenticação para um Cache Redis existente, use o seguinte comando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especifique `Primary` ou `Secondary` como `key-type`.

Para saber mais sobre esse comando, execute o comando `azure rediscache renew-key -h`.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Listar as chaves Primária e Secundária de um Cache Redis existente
Para listar as chaves Primária e Secundária de um Cache Redis, use o seguinte comando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para saber mais sobre esse comando, execute o comando `azure rediscache list-keys -h` .

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
