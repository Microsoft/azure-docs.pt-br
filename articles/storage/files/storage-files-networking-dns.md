---
title: Como configurar o encaminhamento de DNS para os Arquivos do Azure | Microsoft Docs
description: Saiba como configurar o encaminhamento de DNS para arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630185"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Como configurar o encaminhamento de DNS para Arquivos do Azure
Os Arquivos do Azure permitem que você crie pontos de extremidade privados para as contas de armazenamento que contêm seus compartilhamentos de arquivos. Embora sejam úteis para muitas finalidades diferentes, os pontos de extremidade privados são especialmente úteis para se conectar aos compartilhamentos de arquivos do Azure da rede local usando uma conexão VPN ou do ExpressRoute com emparelhamento privado. 

Para que as conexões com a conta de armazenamento passem pelo seu túnel de rede, o FQDN (nome de domínio totalmente qualificado) da conta de armazenamento precisa ser resolvido para o endereço IP privado do ponto de extremidade privado. Para fazer isso, você precisa encaminhar o sufixo do ponto de extremidade de armazenamento (`core.windows.net` para regiões de nuvem pública) para o serviço de DNS privado do Azure acessível de dentro de sua rede virtual. Este guia mostrará como configurar encaminhamento de DNS para que ele seja resolvido corretamente para o endereço IP do ponto de extremidade privado de sua conta de armazenamento.

É altamente recomendável que você leia [Planejando uma implantação dos Arquivos do Azure](storage-files-planning.md) e [Considerações de rede dos Arquivos do Azure](storage-files-networking-overview.md) antes de seguir as etapas descritas neste artigo.

## <a name="overview"></a>Visão geral
Os Arquivos do Azure fornecem dois tipos principais de pontos de extremidade para acessar compartilhamentos de arquivos do Azure: 
- Pontos de extremidade públicos, que têm um endereço IP público e podem ser acessados de qualquer lugar do mundo.
- Pontos de extremidade privados, que existem em uma rede virtual e têm um endereço IP privado dentro do espaço de endereço dessa rede virtual.

Pontos de extremidade públicos e privados existem na conta de armazenamento do Azure. Uma conta de armazenamento é um constructo de gerenciamento que representa um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivos bem como outros recursos de armazenamento, como filas ou contêineres de blob.

Toda conta de armazenamento tem um FQDN (nome de domínio totalmente qualificado). Para as regiões de nuvem pública, esse FQDN segue o padrão `storageaccount.file.core.windows.net`, em que `storageaccount` é o nome da conta de armazenamento. Quando você faz solicitações para esse nome, como ao montar o compartilhamento em sua estação de trabalho usando SMB, o sistema operacional executa uma pesquisa de DNS para resolver o nome de domínio totalmente qualificado para um endereço IP que pode ser usado para envio das solicitações SMB.

Por padrão, `storageaccount.file.core.windows.net` é resolvido para o endereço IP do ponto de extremidade público. O ponto de extremidade público de uma conta de armazenamento é hospedado em um cluster de armazenamento do Azure que hospeda os pontos de extremidade públicos de muitas outras contas de armazenamento. Quando você cria um ponto de extremidade privado, uma zona DNS privada é vinculada à rede virtual à qual ele foi adicionado, com um registro CNAME que mapeia `storageaccount.file.core.windows.net` para uma entrada de registro A para o endereço IP privado do ponto de extremidade privado de sua conta de armazenamento. Isso permite que você use o FQDN `storageaccount.file.core.windows.net` na rede virtual e faça com que ele seja resolvido para o endereço IP do ponto de extremidade privado.

Como nosso objetivo final é acessar os compartilhamentos de arquivos do Azure hospedados na conta de armazenamento do local usando um túnel de rede, como uma conexão VPN ou do ExpressRoute, você precisa configurar seus servidores DNS locais para encaminhar solicitações feitas ao serviço do Arquivos do Azure para o serviço de DNS privado do Azure. Para fazer isso, você precisa configurar o *encaminhamento condicional* de `*.core.windows.net` (ou o sufixo de ponto de extremidade de armazenamento apropriado para as nuvens nacionais da China, da Alemanha ou do Governo dos EUA) para um servidor DNS hospedado em sua rede virtual do Azure. Esse servidor DNS, em seguida, encaminhará recursivamente a solicitação para o serviço DNS privado do Azure, que resolverá o nome de domínio totalmente qualificado da conta de armazenamento para o endereço IP privado apropriado.

Para configurar o encaminhamento de DNS para os Arquivos do Azure, será necessário executar uma máquina virtual para hospedar um servidor DNS para encaminhar as solicitações. No entanto, essa etapa é realizada apenas uma vez para todos os compartilhamentos de arquivos do Azure hospedados em sua rede virtual. Além disso, esse requisito não é exclusivo dos Arquivos do Azure – qualquer serviço do Azure com suporte para pontos de extremidade privados que você desejar acessar do local poderá usar o encaminhamento de DNS que você vai configurar neste guia: Armazenamento de Blobs do Azure, SQL do Azure, Cosmos DB etc. 

Este guia mostra as etapas para configurar o encaminhamento de DNS para o ponto de extremidade de armazenamento do Azure, sendo assim, além dos Arquivos do Azure, as solicitações de resolução de nomes DNS para todos os outros serviços de Armazenamento do Azure (Armazenamento de Blobs do Azure, Armazenamento de Tabelas do Azure, Armazenamento de Filas do Azure etc.) serão encaminhados para o serviço DNS privado do Azure. Pontos de extremidade adicionais para outros serviços do Azure também poderão ser adicionados, se desejado. O encaminhamento de DNS de volta para seus servidores DNS locais também será configurado, permitindo que os recursos de nuvem em sua rede virtual (como um servidor DFS-N) resolvam nomes de computadores locais. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de configurar o encaminhamento de DNS para os Arquivos do Azure, você precisa concluir as seguintes etapas:

- Uma conta de armazenamento contendo um compartilhamento de arquivo do Azure que você gostaria de montar. Para saber como criar uma conta de armazenamento e um compartilhamento de arquivo do Azure, confira [Criar um compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md).
- Um ponto de extremidade privado para a conta de armazenamento. Para saber como criar um ponto de extremidade privado para os Arquivos do Azure, confira [Criar um ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint).
- A [versão mais recente](/powershell/azure/install-az-ps) do módulo do Azure PowerShell.

> [!Important]  
> Este guia pressupõe que você esteja usando o servidor DNS no Windows Server em seu ambiente local. Todas as etapas descritas neste guia são possíveis com qualquer servidor DNS, não apenas com o servidor DNS do Windows.

## <a name="manually-configuring-dns-forwarding"></a>Configurando manualmente o encaminhamento de DNS
Se já tem servidores DNS em funcionamento em sua rede virtual do Azure, ou se simplesmente prefere implantar suas próprias máquinas virtuais para os servidores DNS seguindo qualquer metodologia adotada por sua organização, você pode configurar o DNS manualmente com os cmdlets do PowerShell do servidor DNS interno.

Nos servidores DNS locais, crie um encaminhador condicional usando `Add-DnsServerConditionalForwarderZone`. Esse encaminhador condicional precisa ser implantado em todos os servidores DNS locais para que seja capaz de encaminhar corretamente o tráfego para o Azure. Lembre-se de substituir `<azure-dns-server-ip>` pelos endereços IP adequados para seu ambiente.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Nos servidores DNS de sua rede virtual do Azure, você também precisará configurar um encaminhador de modo que as solicitações para a zona DNS da conta de armazenamento sejam direcionadas para o serviço DNS privado do Azure, que usa o endereço IP reservado `168.63.129.16`. (Lembre-se de popular `$storageAccountEndpoint` se estiver executando os comandos em uma sessão diferente do PowerShell.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Usando o módulo Híbrido dos Arquivos do Azure para configurar o encaminhamento de DNS
Para facilitar ao máximo a configuração do encaminhamento de DNS, fornecemos automação no módulo Híbrido dos Arquivos do Azure. Os cmdlets fornecidos para manipular o DNS neste módulo ajudarão você a implantar servidores DNS em sua rede virtual do Azure e a atualizar seus servidores DNS locais para encaminhá-los. 

Se nunca usou o módulo Híbrido dos Arquivos do Azure, você precisa instalá-lo em sua estação de trabalho. Baixe a [versão mais recente](https://github.com/Azure-Samples/azure-files-samples/releases) do módulo Híbrido do PowerShell dos Arquivos do Azure:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

A implantação da solução de encaminhamento de DNS tem duas etapas, criar um conjunto de regras de encaminhamento de DNS, que define a quais serviços do Azure você deseja encaminhar solicitações, e a implantação de fato dos encaminhadores de DNS. 

O exemplo a seguir encaminha as solicitações para a conta de armazenamento, inclusive solicitações para os Arquivos do Azure, o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure e o Armazenamento de Filas do Azure. Se desejar, você pode adicionar o encaminhamento para serviços adicionais do Azure por meio do parâmetro `-AzureEndpoints` do cmdlet `New-AzDnsForwardingRuleSet`. Lembre-se de substituir `<virtual-network-resource-group>`, `<virtual-network-name>`, e `<subnet-name>` pelos valores adequados para seu ambiente.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Além disso, você pode achar útil/necessário fornecer vários parâmetros adicionais:

| Nome do parâmetro | Type | Descrição |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Por padrão, os servidores DNS serão implantados no mesmo grupo de recursos que a rede virtual. Se isso não for desejado, esse parâmetro permitirá que você escolha um grupo de recursos alternativo no qual eles serão implantados. |
| `DnsForwarderRootName` | `string` | Por padrão, os servidores DNS implantados no Azure têm os nomes `DnsFwder-*`, em que o asterisco é populado por um iterador. Esse parâmetro altera a raiz do nome (ou seja, `DnsFwder`). |
| `VmTemporaryPassword` | `SecureString` | Por padrão, uma senha aleatória é escolhida para a conta padrão temporária que uma VM tem antes de ser ingressada no domínio. Após o ingresso no domínio, a conta padrão é desabilitada. |
| `DomainToJoin` | `string` | O domínio no qual ingressar as VMs deo DNS. Por padrão, esse domínio é escolhido com base no domínio do computador em que você está executando os cmdlets. |
| `DnsForwarderRedundancyCount` | `int` | O número de VMs de DNS a serem implantadas em sua rede virtual. Por padrão, `New-AzDnsForwarder` implanta dois servidores DNS em sua rede virtual do Azure, em um conjunto de disponibilidade, para garantir a redundância. Esse número pode ser modificado conforme desejado. |
| `OnPremDnsHostNames` | `HashSet<string>` | Uma lista especificada manualmente de nomes de host DNS locais nos quais criar encaminhadores. Esse parâmetro é útil quando você não deseja aplicar encaminhadores em todos os servidores DNS locais, por exemplo, quando você tem um intervalo de clientes com nomes DNS especificados manualmente. |
| `Credential` | `PSCredential` | Uma credencial a ser usada ao atualizar os servidores DNS. Isso é útil quando a conta de usuário com a qual você fez logon não tem permissões para modificar as configurações de DNS. |
| `SkipParentDomain` | `SwitchParameter` | Por padrão, os encaminhadores de DNS são aplicados ao domínio de nível mais alto que existe no ambiente. Por exemplo, se `northamerica.corp.contoso.com` for um domínio filho de `corp.contoso.com`, o encaminhador será criado para os servidores DNS associados a `corp.contoso.com`. Esse parâmetro fará com que os encaminhadores sejam criados em `northamerica.corp.contoso.com`. |

## <a name="confirm-dns-forwarders"></a>Confirmar encaminhadores de DNS
Antes de testar se os encaminhadores de DNS foram aplicados com êxito, é recomendável limpar o cache DNS da estação de trabalho local usando `Clear-DnsClientCache`. Para testar se você pode resolver com êxito o nome de domínio totalmente qualificado de sua conta de armazenamento, use `Resolve-DnsName` ou `nslookup`.

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Se a resolução de nomes for bem-sucedida, você verá que o endereço IP resolvido corresponde ao endereço IP de sua conta de armazenamento.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Se já tiver configurado uma conexão VPN ou do ExpressRoute, você também poderá usar `Test-NetConnection` para ver que uma conexão TCP pode ser estabelecida com êxito em sua conta de armazenamento.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Confira também
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Considerações de rede dos Arquivos do Azure](storage-files-networking-overview.md)
- [Configurar pontos de extremidade de rede dos Arquivos do Azure](storage-files-networking-endpoints.md)