---
title: Configurando o encaminhamento de DNS para arquivos do Azure | Microsoft Docs
description: Saiba como configurar o encaminhamento de DNS para arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94630185"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Como configurar o encaminhamento de DNS para Arquivos do Azure
Os arquivos do Azure permitem que você crie pontos de extremidade privados para as contas de armazenamento que contêm os compartilhamentos de arquivos. Embora seja útil para muitos aplicativos diferentes, os pontos de extremidade privados são especialmente úteis para se conectar aos compartilhamentos de arquivos do Azure de sua rede local usando uma conexão VPN ou ExpressRoute usando o emparelhamento privado. 

Para que as conexões com sua conta de armazenamento passem pelo seu túnel de rede, o FQDN (nome de domínio totalmente qualificado) da sua conta de armazenamento deve ser resolvido para o endereço IP privado do ponto de extremidade privado. Para fazer isso, você deve encaminhar o sufixo do ponto de extremidade de armazenamento ( `core.windows.net` para regiões de nuvem pública) para o serviço DNS privado do Azure acessível de dentro de sua rede virtual. Este guia mostrará como configurar e configurar o encaminhamento de DNS para resolver corretamente o endereço IP do ponto de extremidade privado da sua conta de armazenamento.

É altamente recomendável que você leia [planejando uma implantação de arquivos do Azure](storage-files-planning.md) e [considerações de rede de arquivos do Azure](storage-files-networking-overview.md) antes de concluir as etapas descritas neste artigo.

## <a name="overview"></a>Visão geral
Os Arquivos do Azure fornecem dois tipos principais de pontos de extremidade para acessar compartilhamentos de arquivos do Azure: 
- Pontos de extremidade públicos, que têm um endereço IP público e podem ser acessados de qualquer lugar do mundo.
- Pontos de extremidade privados, que existem em uma rede virtual e têm um endereço IP privado dentro do espaço de endereço dessa rede virtual.

Pontos de extremidade públicos e privados existem na conta de armazenamento do Azure. Uma conta de armazenamento é um constructo de gerenciamento que representa um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivos bem como outros recursos de armazenamento, como filas ou contêineres de blob.

Cada conta de armazenamento tem um FQDN (nome de domínio totalmente qualificado). Para as regiões de nuvem pública, esse FQDN segue o padrão `storageaccount.file.core.windows.net` em que `storageaccount` é o nome da conta de armazenamento. Quando você faz solicitações com relação a esse nome, como a montagem do compartilhamento em sua estação de trabalho usando SMB, o sistema operacional executa uma pesquisa de DNS para resolver o nome de domínio totalmente qualificado para um endereço IP que pode ser usado para enviar as solicitações SMB para o.

Por padrão, `storageaccount.file.core.windows.net` o é resolvido para o endereço IP do ponto de extremidade público. O ponto de extremidade público para uma conta de armazenamento é hospedado em um cluster de armazenamento do Azure que hospeda muitos outros pontos de extremidade públicos de contas de armazenamento. Quando você cria um ponto de extremidade privado, uma zona DNS privada é vinculada à rede virtual à qual foi adicionada, com um mapeamento de registro CNAME `storageaccount.file.core.windows.net` para uma entrada de registro para o endereço IP privado do ponto de extremidade privado da sua conta de armazenamento. Isso permite que você use o `storageaccount.file.core.windows.net` FQDN dentro da rede virtual e faça com que ele seja resolvido para o endereço IP do ponto de extremidade privado.

Como nosso objetivo final é acessar os compartilhamentos de arquivos do Azure hospedados na conta de armazenamento do local usando um túnel de rede, como uma conexão VPN ou ExpressRoute, você deve configurar seus servidores DNS locais para encaminhar solicitações feitas ao serviço de arquivos do Azure para o serviço DNS privado do Azure. Para fazer isso, você precisa configurar o *encaminhamento condicional* de `*.core.windows.net` (ou o sufixo de ponto de extremidade de armazenamento apropriado para as nuvens nacionais do governo dos EUA, Alemanha ou China) para um servidor DNS hospedado em sua rede virtual do Azure. Esse servidor DNS, em seguida, encaminhará recursivamente a solicitação para o serviço DNS privado do Azure que resolverá o nome de domínio totalmente qualificado da conta de armazenamento para o endereço IP privado apropriado.

Configurar o encaminhamento de DNS para arquivos do Azure exigirá a execução de uma máquina virtual para hospedar um servidor DNS para encaminhar as solicitações. no entanto, essa é uma etapa única para todos os compartilhamentos de arquivos do Azure hospedados em sua rede virtual. Além disso, esse não é um requisito exclusivo para arquivos do Azure – qualquer serviço do Azure que dê suporte a pontos de extremidade privados que você deseja acessar do local pode fazer uso do encaminhamento de DNS que você irá configurar neste guia: armazenamento de BLOBs do Azure, SQL Azure, Cosmos DB, etc. 

Este guia mostra as etapas para configurar o encaminhamento DNS para o ponto de extremidade de armazenamento do Azure, assim, além dos arquivos do Azure, as solicitações de resolução de nomes DNS para todos os outros serviços de armazenamento do Azure (armazenamento de BLOBs do Azure, armazenamento de tabelas do Azure, armazenamento de filas do Azure etc.) serão encaminhadas para o serviço DNS privado do Azure. Pontos de extremidade adicionais para outros serviços do Azure também podem ser adicionados, se desejado. O encaminhamento de DNS de volta para seus servidores DNS locais também será configurado, permitindo que os recursos de nuvem em sua rede virtual (como um servidor DFS-N) resolvam nomes de computadores locais. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder configurar o encaminhamento de DNS para os arquivos do Azure, você precisa concluir as seguintes etapas:

- Uma conta de armazenamento que contém um compartilhamento de arquivos do Azure que você deseja montar. Para saber como criar uma conta de armazenamento e um compartilhamento de arquivos do Azure, confira [criar um compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md).
- Um ponto de extremidade privado para a conta de armazenamento. Para saber como criar um ponto de extremidade privado para arquivos do Azure, consulte [criar um ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint).
- A [versão mais recente](/powershell/azure/install-az-ps) do módulo Azure PowerShell.

> [!Important]  
> Este guia pressupõe que você esteja usando o servidor DNS no Windows Server em seu ambiente local. Todas as etapas descritas neste guia são possíveis com qualquer servidor DNS, não apenas o servidor DNS do Windows.

## <a name="manually-configuring-dns-forwarding"></a>Configurando manualmente o encaminhamento de DNS
Se você já tiver servidores DNS em vigor em sua rede virtual do Azure, ou se simplesmente preferir implantar suas próprias máquinas virtuais para servidores DNS por qualquer metodologia usada pela sua organização, você poderá configurar o DNS manualmente com os cmdlets do PowerShell do servidor DNS interno.

Em seus servidores DNS locais, crie um encaminhador condicional usando `Add-DnsServerConditionalForwarderZone` . Esse encaminhador condicional deve ser implantado em todos os seus servidores DNS locais para entrar em vigor no tráfego adequado para o Azure. Lembre-se de substituir `<azure-dns-server-ip>` pelos endereços IP apropriados para seu ambiente.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Nos servidores DNS em sua rede virtual do Azure, você também precisará colocar um encaminhador em vigor, de modo que as solicitações para a zona DNS da conta de armazenamento sejam direcionadas para o serviço DNS privado do Azure, que é encaminhado pelo endereço IP reservado `168.63.129.16` . (Lembre-se de popular `$storageAccountEndpoint` se você estiver executando os comandos em uma sessão diferente do PowerShell.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Usando o módulo híbrido de arquivos do Azure para configurar o encaminhamento de DNS
Para tornar a configuração de encaminhamento de DNS o mais fácil possível, fornecemos a automação no módulo híbrido de arquivos do Azure. Os cmdlets fornecidos para manipular o DNS neste módulo ajudarão você a implantar servidores DNS em sua rede virtual do Azure e atualizar seus servidores DNS locais para encaminhá-los. 

Se você nunca usou o módulo híbrido de arquivos do Azure, você deve primeiro instalá-lo em sua estação de trabalho. Baixe a [versão mais recente](https://github.com/Azure-Samples/azure-files-samples/releases) do módulo do PowerShell híbrido dos arquivos do Azure:

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

A implantação da solução de encaminhamento de DNS tem duas etapas, criando um conjunto de regras de encaminhamento de DNS, que define a quais serviços do Azure você deseja encaminhar solicitações e a implantação real dos encaminhadores de DNS. 

O exemplo a seguir encaminha as solicitações para a conta de armazenamento, inclusive solicitações para arquivos do Azure, armazenamento de BLOBs do Azure, armazenamento de tabelas do Azure e armazenamento de filas do Azure. Se desejar, você pode adicionar o encaminhamento para o serviço do Azure adicional à regra por meio do `-AzureEndpoints` parâmetro do `New-AzDnsForwardingRuleSet` cmdlet. Lembre-se de substituir `<virtual-network-resource-group>`, `<virtual-network-name>`, e `<subnet-name>` pelos valores adequados para seu ambiente.

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

Adicionalmente, você pode achar útil/necessário fornecer vários parâmetros adicionais:

| Nome do parâmetro | Type | Descrição |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Por padrão, os servidores DNS serão implantados no mesmo grupo de recursos que a rede virtual. Se isso não for desejado, esse parâmetro permitirá que você escolha um grupo de recursos alternativo para que eles sejam implantados no. |
| `DnsForwarderRootName` | `string` | Por padrão, os servidores DNS que são implantados no Azure têm os nomes `DnsFwder-*` , onde o asterisco é populado por um iterador. Esse parâmetro altera a raiz desse nome (ou seja, `DnsFwder` ). |
| `VmTemporaryPassword` | `SecureString` | Por padrão, uma senha aleatória é escolhida para a conta padrão temporária que uma VM tem antes de ser ingressada no domínio. Depois de ingressar no domínio, a conta padrão é desabilitada. |
| `DomainToJoin` | `string` | O domínio para ingressar as VMs de DNS a serem associadas. Por padrão, esse domínio é escolhido com base no domínio do computador em que você está executando os cmdlets. |
| `DnsForwarderRedundancyCount` | `int` | O número de VMs DNS a serem implantadas para sua rede virtual. Por padrão, `New-AzDnsForwarder` o implanta dois servidores DNS em sua rede virtual do Azure, em um conjunto de disponibilidade, para garantir a redundância. Esse número pode ser modificado conforme desejado. |
| `OnPremDnsHostNames` | `HashSet<string>` | Uma lista manualmente especificada de nomes de host DNS locais para criar encaminhadores. Esse parâmetro é útil quando você não deseja aplicar encaminhadores em todos os servidores DNS locais, como quando você tem um intervalo de clientes com nomes DNS especificados manualmente. |
| `Credential` | `PSCredential` | Uma credencial a ser usada ao atualizar os servidores DNS. Isso é útil quando a conta de usuário com a qual você fez logon não tem permissões para modificar as configurações de DNS. |
| `SkipParentDomain` | `SwitchParameter` | Por padrão, os encaminhadores DNS são aplicados ao domínio de nível mais alto que existe em seu ambiente. Por exemplo, se `northamerica.corp.contoso.com` for um domínio filho de `corp.contoso.com` , o encaminhador será criado para os servidores DNS associados ao `corp.contoso.com` . Esse parâmetro fará com que os encaminhadores sejam criados no `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>Confirmar encaminhadores DNS
Antes de testar para ver se os encaminhadores de DNS foram aplicados com êxito, é recomendável limpar o cache DNS na estação de trabalho local usando o `Clear-DnsClientCache` . Para testar para ver se você pode resolver com êxito o nome de domínio totalmente qualificado da sua conta de armazenamento, use `Resolve-DnsName` ou `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Se a resolução de nomes for bem-sucedida, você verá que o endereço IP resolvido corresponde ao endereço IP da sua conta de armazenamento.

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

Se você já tiver configurado uma conexão VPN ou ExpressRoute, também poderá usar o `Test-NetConnection` para ver que uma conexão TCP pode ser feita com êxito à sua conta de armazenamento.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Veja também
- [Como planejar uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Considerações de rede dos Arquivos do Azure](storage-files-networking-overview.md)
- [Configurar pontos de extremidade de rede dos Arquivos do Azure](storage-files-networking-endpoints.md)