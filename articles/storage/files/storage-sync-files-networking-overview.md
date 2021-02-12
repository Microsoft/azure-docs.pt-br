---
title: Considerações sobre rede de Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como configurar a rede para usar Sincronização de Arquivos do Azure armazenar em cache arquivos locais.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629352"
---
# <a name="azure-file-sync-networking-considerations"></a>Considerações de rede para Sincronização de Arquivos do Azure
Você pode se conectar a um compartilhamento de arquivo do Azure de duas maneiras:

- Acessando o compartilhamento diretamente por meio dos protocolos SMB ou FileREST. Esse padrão de acesso é empregado principalmente quando é possível eliminar o máximo possível de servidores locais.
- Criando um cache do compartilhamento de arquivo do Azure em um servidor local (ou em uma VM do Azure) com a Sincronização de Arquivos do Azure e acessando os dados do compartilhamento de arquivo do servidor local com o protocolo de sua escolha (SMB, NFS, FTPS etc.) para seu caso de uso. Esse padrão de acesso é útil porque ele combina o melhor de desempenho local e escala de nuvem e serviços anexáveis sem servidor, como o Backup do Azure.

Este artigo se concentra em como configurar a rede para quando seu caso de uso requer o uso de Sincronização de Arquivos do Azure para armazenar em cache arquivos locais, em vez de montar diretamente o compartilhamento de arquivo do Azure por SMB. Para obter mais informações sobre considerações de rede para uma implantação de Arquivos do Azure, confira [Considerações de rede de Arquivos do Azure](storage-files-networking-overview.md).

A configuração de rede para a Sincronização de Arquivos do Azure abrange dois objetos diferentes do Azure: um Serviço de Sincronização de Armazenamento e uma conta de armazenamento do Azure. Uma conta de armazenamento é um constructo de gerenciamento que representa um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivos bem como outros recursos de armazenamento, como filas ou contêineres de blob. Um Serviço de Sincronização de Armazenamento é um constructo de gerenciamento que representa servidores registrados, que são servidores de arquivos do Windows com uma relação de confiança estabelecida com a Sincronização de Arquivos do Azure e grupos de sincronização, que definem a topologia do relacionamento de sincronização. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Como conectar o servidor de arquivos do Windows ao Azure com a Sincronização de Arquivos do Azure 
Para configurar e usar Arquivos do Azure e a Sincronização de Arquivos do Azure com um servidor de arquivos do Windows local, não é necessária nenhuma rede especial para o Azure além de uma conexão básica com a Internet. Para implantar a Sincronização de Arquivos do Azure, instale o agente de Sincronização de Arquivos do Azure no servidor de arquivos do Windows que você deseja sincronizar com o Azure. O agente de Sincronização de Arquivos do Azure obtém a sincronização com um compartilhamento de arquivo do Azure por meio de dois canais:

- O protocolo FileREST, que é um protocolo baseado em HTTPS para acessar o compartilhamento de arquivo do Azure. Como o protocolo FileREST usa HTTPS padrão para transferência de dados, somente a porta 443 deve ser acessível para saída. A Sincronização de Arquivos do Azure não usa o protocolo SMB para transferir dados de seus servidores do Windows locais para o compartilhamento de arquivo do Azure.
- O protocolo de sincronização da Sincronização de Arquivos do Azure, que é um protocolo baseado em HTTPS para a troca de conhecimento de sincronização, ou seja, as informações de versão sobre os arquivos e pastas em seu ambiente, entre pontos de extremidade em seu ambiente. Esse protocolo também é usado para trocar metadados sobre os arquivos e pastas no seu ambiente, como carimbos de data/hora e ACLs (listas de controle de acesso). 

Como os arquivos do Azure oferecem acesso direto ao protocolo SMB nos compartilhamentos de arquivos do Azure, os clientes costumam imaginar se precisam configurar a rede especial para montar os compartilhamentos de arquivos do Azure com SMB para o agente de Sincronização de Arquivos do Azure acessar. Isso não só é desnecessário, como também é desencorajado, exceto para cenários de administrador, devido à falta de detecção rápida de alterações nas mudanças feitas diretamente ao compartilhamento de arquivo do Azure (as alterações não podem ser descobertas por mais de 24 horas, dependendo do tamanho e do número de itens no compartilhamento de arquivo do Azure). Se você quiser usar o compartilhamento de arquivo do Azure diretamente, ou seja, não usar Sincronização de Arquivos do Azure para cache local, poderá saber mais sobre as considerações de rede para acesso direto conferindo a [Visão geral da rede de Arquivos do Azure](storage-files-networking-overview.md).

Embora a Sincronização de Arquivos do Azure não exija nenhuma configuração de rede especial, alguns clientes talvez queiram definir configurações avançadas de rede para habilitar os seguintes cenários:

- Interoperação com a configuração do servidor proxy da sua organização.
- Abra o firewall local da sua organização para os Arquivos do Azure e os serviços Sincronização de Arquivos do Azure.
- Encapsular Arquivos do Azure e Sincronização de Arquivos do Azure por meio do ExpressRoute ou de uma conexão VPN.

### <a name="configuring-proxy-servers"></a>Configurar servidores proxy
Muitas organizações usam um servidor proxy como um intermediário entre recursos dentro da rede local e recursos fora da rede, como no Azure. Os servidores proxy são úteis para muitos aplicativos, como segurança e isolamento de rede, além de monitoramento e registro em log. Sincronização de Arquivos do Azure pode interoperar totalmente com um servidor proxy, no entanto, você deve configurar de modo manual as configurações de ponto de extremidade de proxy para seu ambiente com Sincronização de Arquivos do Azure. Isso deve ser feito usando o PowerShell com os cmdlets do Sincronização de Arquivos do Azure Server. 

Para obter mais informações sobre como configurar Sincronização de Arquivos do Azure com um servidor proxy, confira [Como configurar a Sincronização de Arquivos do Azure com um servidor proxy](storage-sync-files-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Como configurar firewalls e marcas de serviço
Você pode isolar os servidores de arquivos do local da Internet para fins de segurança. Para usar Sincronização de Arquivos do Azure em seu ambiente, você precisa ajustar o firewall para abri-lo para selecionar os serviços do Azure. Você pode fazer isso recuperando os intervalos de endereços IP para os serviços necessários por meio de um mecanismo chamado [marcas de serviço](../../virtual-network/service-tags-overview.md).

A Sincronização de Arquivos do Azure requer os intervalos de endereços IP para os seguintes serviços, conforme identificado pelas marcas de serviço:

| Serviço | Descrição | Marca de serviço |
|---------|-------------|-------------|
| Sincronização de Arquivos do Azure | O serviço de Sincronização de Arquivos do Azure, conforme representado pelo objeto Serviço de Sincronização de Armazenamento, é responsável pela atividade principal de sincronização de dados entre um compartilhamento de arquivo do Azure e um servidor de arquivos do Windows. | `StorageSyncService` |
| Arquivos do Azure | Todos os dados sincronizados por meio da Sincronização de Arquivos do Azure são armazenados no compartilhamento de arquivo do Azure. Os arquivos alterados nos servidores de arquivos do Windows são replicados para o compartilhamento de arquivo do Azure e os arquivos em camadas no servidor de arquivos local são baixados diretamente quando um usuário os solicita. | `Storage` |
| Azure Resource Manager | O Azure Resource Manager é a interface de gerenciamento do Azure. Todas as chamadas de gerenciamento, incluindo o registro do Sincronização de Arquivos do Azure Server e as tarefas de servidor de sincronização contínua, são feitas por meio do Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | O Azure Active Directory, ou Azure AD, contém as entidades de usuário necessárias para autorizar o registro do servidor em um serviço de sincronização de armazenamento e as entidades de serviço necessárias para que Sincronização de Arquivos do Azure seja autorizado a acessar seus recursos de nuvem. | `AzureActiveDirectory` |

Se você estiver usando a Sincronização de Arquivos do Azure no Azure, mesmo que esteja em uma região diferente, poderá usar o nome da marca de serviço diretamente no grupo de segurança de rede para permitir o tráfego para esse serviço. Para saber mais sobre como fazer isso, confira [Grupos de segurança de rede](../../virtual-network/network-security-groups-overview.md). 

Se você estiver usando a Sincronização de Arquivos do Azure local, poderá usar a API de marca de serviço para obter intervalos de endereços IP específicos para a lista de permissões do firewall. Há dois métodos para obter essas informações:

- A lista atual de intervalos de endereços IP para todos os serviços do Azure compatíveis com marcas de serviço é publicada semanalmente no centro de download da Microsoft na forma de um documento JSON. Cada nuvem do Azure tem o próprio documento JSON com os intervalos de endereços IP relevantes para essa nuvem:
    - [Público do Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Governo dos EUA para Azure](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China:](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
- A API de descoberta de marca de serviço (versão prévia) permite a recuperação programática da lista atual de marcas de serviço. Na versão prévia, a API de descoberta de marca de serviço pode retornar informações menos atualizadas do que as retornadas pelos documentos JSON publicados no centro de download da Microsoft. Você pode usar a superfície da API conforme sua preferência de automação:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [PowerShell do Azure](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [CLI do Azure](/cli/azure/network#az-network-list-service-tags)

Para saber mais sobre como usar a API de marca de serviço para recuperar os endereços de seus serviços, confira [Lista de permissões para endereços IP da Sincronização de Arquivos do Azure](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Criação de um túnel de tráfego por uma rede privada virtual ou ExpressRoute
Algumas organizações exigem comunicação com o Azure para passar por um túnel de rede, como uma VPN (rede privada virtual) ou ExpressRoute, para uma camada adicional de segurança ou para garantir que a comunicação com o Azure siga uma rota determinística. 

Quando você estabelecer um túnel de rede entre a rede local e o Azure, você emparelhará sua rede local com uma ou mais redes virtuais no Azure. Uma [rede virtual](../../virtual-network/virtual-networks-overview.md), ou VNet, é semelhante a uma rede tradicional que você operaria no local. Como uma conta de armazenamento do Azure ou uma VM do Azure, uma VNet é um recurso do Azure que é implantado em um grupo de recursos. 

Os Arquivos do Azure e a Sincronização de Arquivos dão suporte aos seguintes mecanismos para criar túnel de tráfego entre os servidores locais e o Azure:

- [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Um gateway de VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego criptografado entre uma rede virtual do Azure e uma localização alternativa (como localmente) na Internet. Um Gateway de VPN do Azure é um recurso do Azure que pode ser implantado em um grupo de recursos junto com uma conta de armazenamento ou outros recursos do Azure. Como a Sincronização de Arquivos do Azure foi feita para ser usada com um servidor de arquivos do Windows local, normalmente você usaria uma [VPN S2S (site a site)](../../vpn-gateway/design.md#s2smulti), embora tecnicamente seja possível usar uma [VPN P2S (ponto a site)](../../vpn-gateway/point-to-site-about.md). 

    As conexões VPN S2S (site a site) conectam sua rede virtual do Azure e a rede local da sua organização. Uma conexão VPN S2S permite que você configure uma conexão VPN uma vez, para um servidor VPN ou dispositivo hospedado na rede de sua organização, em vez de fazer isso para cada dispositivo cliente que precisar acessar o compartilhamento de arquivo do Azure. Para simplificar a implantação de uma conexão VPN S2S, confira [Configurar uma VPN S2S (Site a Site) para uso com os Arquivos do Azure](storage-files-configure-s2s-vpn.md).

- [ExpressRoute](../../expressroute/expressroute-introduction.md), que permite que você crie uma rota definida entre o Azure e sua rede local que não atravesse a Internet. Como o ExpressRoute fornece um caminho dedicado entre o datacenter local e o Azure, ele pode ser útil quando o desempenho da rede é importante. O ExpressRoute também é uma boa opção quando os requisitos regulatórios ou de política de sua organização exigem um caminho determinístico para seus recursos na nuvem.

### <a name="private-endpoints"></a>Pontos de extremidade privados
Além dos pontos de extremidade públicos padrão que os Arquivos do Azure e a Sincronização de Arquivos fornecem por meio da conta de armazenamento e do serviço de sincronização de armazenamento, os Arquivos do Azure e a Sincronização de Arquivos fornecem a opção de ter um ou mais pontos de extremidade privados por recurso. Quando você cria um ponto de extremidade privado para um recurso do Azure, ele obtém um endereço IP privado dentro do espaço de endereço de sua rede virtual de modo muito semelhante a como o servidor de arquivos do Windows local tem um endereço IP dentro do espaço de endereço dedicado da rede local. 

> [!Important]  
> Para usar pontos de extremidade privados no recurso de serviço de sincronização de armazenamento, você deve usar o agente de Sincronização de Arquivos do Azure versão 10.1 ou superior. As versões do agente anteriores à 10.1 não dão suporte a pontos de extremidade privados no serviço de sincronização de armazenamento. Todas as versões anteriores de agentes dão suporte a pontos de extremidade privados no recurso da conta de armazenamento.

Um ponto de extremidade privado individual está associado a uma sub-rede de rede virtual do Azure específica. As contas de armazenamento e os Serviços de Sincronização de Armazenamento podem ter pontos de extremidade privados em mais de uma rede virtual.

O uso de pontos de extremidade privados permite que você:
- Conecte-se com segurança aos seus recursos do Azure de redes locais usando uma conexão VPN ou ExpressRoute com emparelhamento privado.
- Proteja seus recursos do Azure desabilitando os pontos de extremidade públicos para Arquivos do Azure e Sincronização de Arquivos. Por padrão, a criação de um ponto de extremidade privado não bloqueia conexões com o ponto de extremidade público.
- Aumente a segurança da rede virtual permitindo que você bloqueie o vazamento de dados da rede virtual (e limites de emparelhamento).

Para criar um ponto de extremidade privado, confira [Configuração de pontos de extremidade privados para os Sincronização de Arquivos do Azure](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Pontos de extremidade privados e DNS
Quando você cria um ponto de extremidade privado, por padrão também criamos uma zona DNS privada (ou atualizamos uma existente) correspondente ao subdomínio `privatelink`. Para regiões de nuvem pública, essas zonas DNS são `privatelink.file.core.windows.net` para Arquivos do Azure e `privatelink.afs.azure.net` para Sincronização de Arquivos do Azure.

> [!Note]  
> Este artigo usa o sufixo DNS da conta de armazenamento para as regiões Públicas do Azure, `core.windows.net`. Esse comentário também se aplica a nuvens soberanas do Azure, como a nuvem do Governo dos EUA para Azure e a nuvem do Azure China – só substitua os sufixos apropriados para seu ambiente. 

Quando você cria pontos de extremidade privados para uma conta de armazenamento e um Serviço de Sincronização de Armazenamento, criamos registros para eles nas respectivas zonas DNS privado. Também atualizamos a entrada DNS pública de modo que os nomes de domínio totalmente qualificados regulares sejam CNAMEs para o nome de privatelink relevante. Isso permite que os nomes de domínio totalmente qualificados apontem para os endereços IP do ponto de extremidade privado quando o solicitante está dentro da rede virtual e apontem para os endereços IP do ponto de extremidade público quando o solicitante está fora da rede virtual. 

Para Arquivos do Azure, cada ponto de extremidade privado tem um único nome de domínio totalmente qualificado, seguindo o padrão `storageaccount.privatelink.file.core.windows.net`, mapeado para um endereço IP privado para o ponto de extremidade privado. Para a Sincronização de Arquivos do Azure, cada ponto de extremidade privado tem quatro nomes de domínio totalmente qualificados para os quatro pontos de extremidade diferentes que a Sincronização de Arquivos do Azure expõe: gerenciamento, sincronização (primário), sincronização (secundário) e monitoramento. Os nomes de domínio totalmente qualificados para esses pontos de extremidade costumam seguir o nome do Serviço de Sincronização de Armazenamento, a menos que o nome contenha caracteres não ASCII. Por exemplo, se o nome do Serviço de Sincronização de Armazenamento for `mysyncservice` na região oeste dos EUA 2, os pontos de extremidade equivalentes serão `mysyncservicemanagement.westus2.afs.azure.net`, `mysyncservicesyncp.westus2.afs.azure.net`, `mysyncservicesyncs.westus2.afs.azure.net` e `mysyncservicemonitoring.westus2.afs.azure.net`. Cada ponto de extremidade privado para um Serviço de Sincronização de Armazenamento conterá quatro endereços IP distintos. 

Como a zona DNS privada do Azure está conectada à rede virtual que contém o ponto de extremidade privado, você pode observar a configuração de DNS ao chamar o cmdlet `Resolve-DnsName` do PowerShell em uma VM do Azure (ou `nslookup` no Windows e no Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Nesse exemplo, a conta de armazenamento `storageaccount.file.core.windows.net` resolve para o endereço IP privado do ponto de extremidade privado, que é `192.168.0.4`.

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


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Se você executar o mesmo comando localmente, verá que a mesma conta de armazenamento resolve o endereço IP público da conta de armazenamento; `storageaccount.file.core.windows.net` é um registro CNAME para `storageaccount.privatelink.file.core.windows.net`, que, por sua vez, é um registro CNAME do cluster de armazenamento do Azure que hospeda a conta de armazenamento:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Isso reflete o fato de que os Arquivos do Azure e a Sincronização de Arquivos do Azure podem expor os pontos de extremidade públicos e um ou mais pontos de extremidade privados por recurso. Para garantir que os nomes de domínio totalmente qualificados para seus recursos sejam resolvidos para os endereços IP privados dos pontos de extremidade privados, você deve alterar a configuração nos servidores DNS locais. Isso pode ser realizado de várias maneiras:

- Modificar o arquivo de hosts em seus clientes para fazer com que os nomes de domínio totalmente qualificados para suas contas de armazenamento e serviços de sincronização de armazenamento sejam resolvidos para os endereços IP privados desejados. Isso é fortemente desencorajado para ambientes de produção, já que você precisará fazer essas alterações em todos os clientes que precisam acessar seus pontos de extremidade privados. As alterações em seus pontos de extremidade/recursos privados (exclusões, modificações etc.) não serão manipuladas automaticamente.
- Criar zonas DNS em seus servidores locais para `privatelink.file.core.windows.net` e `privatelink.afs.azure.net` com registros A para os recursos do Azure. Isso tem a vantagem de que os clientes em seu ambiente local poderão resolver automaticamente recursos do Azure sem a necessidade de configurar cada cliente; no entanto, essa solução é igualmente frágil à modificação do arquivo de hosts porque as alterações não são refletidas. Embora essa solução seja frágil, ela pode ser a melhor opção para alguns ambientes.
- Encaminhe as zonas `core.windows.net` e `afs.azure.net` de seus servidores DNS locais para a zona DNS privado do Azure. O host DNS privado do Azure pode ser acessado por meio de um endereço IP especial (`168.63.129.16`) que só pode ser acessado dentro de redes virtuais vinculadas à zona DNS privada do Azure. Para solucionar essa limitação, você pode executar servidores DNS adicionais dentro de sua rede virtual que encaminharão `core.windows.net` e `afs.azure.net` para as zonas DNS privado do Azure equivalentes. Para simplificar essa configuração, fornecemos os cmdlets do PowerShell que implantarão automaticamente os servidores DNS em sua rede virtual do Azure e os configurarão conforme desejado. Para saber como configurar o encaminhamento de DNS, confira [Configurar DNS com os Arquivos do Azure](storage-files-networking-dns.md).

## <a name="encryption-in-transit"></a>Criptografia em trânsito
As conexões feitas do agente de Sincronização de Arquivos do Azure para o compartilhamento de arquivo do Azure ou o Serviço de Sincronização de Armazenamento são sempre criptografadas. Embora as contas de armazenamento do Azure tenham uma configuração para desabilitar a exigência de criptografia em trânsito para comunicações com Arquivos do Azure (e os outros serviços de armazenamento do Azure que são gerenciados fora da conta de armazenamento), desabilitar essa configuração não afetará a criptografia da Sincronização de Arquivos do Azure ao se comunicar com os Arquivos do Azure. Por padrão, todas as contas de armazenamento do Azure têm criptografia em trânsito habilitada. 

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Confira também
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)