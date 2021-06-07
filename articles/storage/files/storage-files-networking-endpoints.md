---
title: Configurar pontos de extremidade de rede dos Arquivos do Azure | Microsoft Docs
description: Saiba como configurar pontos de extremidade de rede de arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 079d7aa9b654a318c7269a41605c3e146b08f127
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621324"
---
# <a name="configuring-azure-files-network-endpoints"></a>Configurar pontos de extremidade de rede dos Arquivos do Azure

Os Arquivos do Azure fornecem dois tipos principais de pontos de extremidade para acessar compartilhamentos de arquivos do Azure: 
- Pontos de extremidade públicos, que têm um endereço IP público e podem ser acessados de qualquer lugar do mundo.
- Pontos de extremidade privados, que existem em uma rede virtual e têm um endereço IP privado dentro do espaço de endereço dessa rede virtual.

Pontos de extremidade públicos e privados existem na conta de armazenamento do Azure. Uma conta de armazenamento é um constructo de gerenciamento que representa um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivos bem como outros recursos de armazenamento, como filas ou contêineres de blob.

Este artigo se concentra em como configurar os pontos de extremidade de uma conta de armazenamento para acessar o compartilhamento de arquivos do Azure diretamente. A maior parte dos detalhes fornecidos neste documento também se aplica a como a Sincronização de Arquivos do Azure interopera com pontos de extremidade públicos e privados para a conta de armazenamento. No entanto, para saber mais sobre as considerações de rede para uma implantação da Sincronização de Arquivos do Azure, confira [Configurações de proxy e firewall da Sincronização de Arquivos do Azure](storage-sync-files-firewall-and-proxy.md).

É recomendável ler as [Considerações de rede dos Arquivos do Azure](storage-files-networking-overview.md) antes de ler este guia de instruções.

## <a name="prerequisites"></a>Pré-requisitos

- Este artigo pressupõe que você já tenha criado uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Este artigo pressupõe que você já criou um compartilhamento de arquivos do Azure em uma conta de armazenamento à qual você deseja se conectar do local. Para saber como criar um compartilhamento de arquivo do Azure, confira [Criar um compartilhamento de arquivo do Azure](storage-how-to-create-file-share.md).
- Se pretende usar o Azure PowerShell, [instale a versão mais recente](/powershell/azure/install-az-ps).
- Se pretende usar a CLI do Azure, [instale a versão mais recente](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Configurações de ponto de extremidade

Você pode configurar seus pontos de extremidade para restringir o acesso à rede para sua conta de armazenamento. Há duas abordagens para a restrição do acesso a uma conta de armazenamento a uma rede virtual:

- [Criar um ou mais pontos de extremidade privados para a conta de armazenamento](#create-a-private-endpoint) e restringir todo o acesso ao ponto de extremidade público. Com isso, apenas o tráfego proveniente de dentro das redes virtuais desejadas poderá acessar os compartilhamentos de arquivo do Azure dentro da conta de armazenamento.
- [Restrinja o ponto de extremidade público a uma ou mais redes virtuais](#restrict-public-endpoint-access). Isso funciona usando uma funcionalidade da rede virtual denominada *pontos de extremidade de serviço*. Ao restringir o tráfego para uma conta de armazenamento por meio de um ponto de extremidade de serviço, você ainda está acessando a conta de armazenamento por meio do endereço IP público, mas o acesso só é possível dos locais que você especificar em sua configuração.

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

A criação de um ponto de extremidade privado para sua conta de armazenamento resultará na implantação dos seguintes recursos do Azure:

- **Um ponto de extremidade privado**: um recurso do Azure que representa o ponto de extremidade privado da conta de armazenamento. Considere-o como um recurso que conecta uma conta de armazenamento e uma interface de rede.
- **Uma NIC (adaptador de rede)** : o adaptador de rede que mantém um endereço IP privado dentro da rede virtual/sub-rede especificada. É exatamente o mesmo recurso que é implantado quando você implanta uma máquina virtual. No entanto, em vez de ser atribuído a uma VM, ele pertence ao ponto de extremidade privado.
- **Uma zona DNS privada**: se você nunca tiver implantado um ponto de extremidade privado para essa rede virtual, uma nova zona DNS privada será implantada em sua rede virtual. Um registro DNS A também será criado para a conta de armazenamento nesta zona DNS. Se você já tiver implantado um ponto de extremidade privado nessa rede virtual, um novo registro A para a conta de armazenamento será adicionado à zona DNS existente. A implantação da zona DNS é opcional, mas altamente recomendada e necessária se você estiver montando os compartilhamentos de arquivo do Azure com uma entidade de serviço do AD ou usando a API FileREST.

> [!Note]  
> Este artigo usa o sufixo DNS da conta de armazenamento para as regiões Públicas do Azure, `core.windows.net`. Esse comentário também se aplica a nuvens soberanas do Azure, como a nuvem do Governo dos EUA para Azure e a nuvem do Azure China – só substitua os sufixos apropriados para seu ambiente. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Verificar conectividade

# <a name="portal"></a>[Portal](#tab/azure-portal)

Se tiver uma máquina virtual dentro de sua rede virtual ou se tiver configurado o encaminhamento de DNS conforme descrito em [Como configurar o encaminhamento DNS para Arquivos do Azure](storage-files-networking-dns.md), você poderá testar se o ponto de extremidade privado foi instalado corretamente executando os seguintes comandos do PowerShell, na linha de comando ou no terminal (funciona para Windows, Linux e macOS). Substitua `<storage-account-name>` pelo nome da conta de armazenamento apropriada:

```
nslookup <storage-account-name>.file.core.windows.net
```

Se tudo tiver funcionado corretamente, você deverá ver a seguinte saída, em que `192.168.0.5` é o endereço IP privado do ponto de extremidade privado em sua rede virtual (saída mostrada para o Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se tiver uma máquina virtual dentro de sua rede virtual ou se tiver configurado o encaminhamento de DNS conforme descrito em [Como configurar o encaminhamento de DNS para Arquivos do Azure](storage-files-networking-dns.md), você poderá testar se o ponto de extremidade privado foi instalado corretamente executando os seguintes comandos:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Se tudo tiver funcionado corretamente, você deverá ver a seguinte saída, em que `192.168.0.5` é o endereço IP privado do ponto de extremidade privado em sua rede virtual:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se tiver uma máquina virtual dentro de sua rede virtual ou se tiver configurado o encaminhamento de DNS conforme descrito em [Como configurar o encaminhamento de DNS para Arquivos do Azure](storage-files-networking-dns.md), você poderá testar se o ponto de extremidade privado foi instalado corretamente executando os seguintes comandos:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Se tudo tiver funcionado corretamente, você deverá ver a saída a seguir, em que `192.168.0.5` é o endereço IP privado do ponto de extremidade privado em sua rede virtual. Você ainda deve usar o storageaccount.file.core.windows.net para montar o compartilhamento de arquivos em vez do `privatelink` caminho.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Restringir acesso de ponto de extremidade público

Limitar o acesso ao ponto de extremidade público primeiro exige que você desabilite o acesso geral ao ponto de extremidade público. Desabilitar o acesso ao ponto de extremidade público não afeta pontos de extremidades privados. Depois que o ponto de extremidade público tiver sido desabilitado, você poderá selecionar redes ou endereços IP específicos que podem continuar a acessá-lo. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringe o acesso à rede para uma ou mais redes virtuais.

#### <a name="disable-access-to-the-public-endpoint"></a>Desabilitar o acesso ao ponto de extremidade público

Quando o acesso ao ponto de extremidade público está desabilitado, a conta de armazenamento ainda pode ser acessada por meio de seus pontos de extremidade privados. Caso contrário, as solicitações válidas para o ponto de extremidade público da conta de armazenamento serão rejeitadas, a menos que sejam de [uma fonte especificamente permitida](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks). 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Restringir o acesso ao ponto de extremidade público para redes virtuais específicas

Ao restringir a conta de armazenamento para redes virtuais específicas, você permite solicitações ao ponto de extremidade público de dentro das redes virtuais especificadas. Isso funciona usando uma funcionalidade da rede virtual denominada *pontos de extremidade de serviço*. Isso pode ser usado com ou sem pontos de extremidade privados.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Confira também

- [Considerações de rede dos Arquivos do Azure](storage-files-networking-overview.md)
- [Como configurar o encaminhamento de DNS para Arquivos do Azure](storage-files-networking-dns.md)
- [Configurando a VPN S2S para os Arquivos do Azure](storage-files-configure-s2s-vpn.md)