---
title: Considerações de rede dos Arquivos do Azure | Microsoft Docs
description: Uma visão geral das opções de rede para Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1dce7795b8c62c36b80c51d5ba0dd8bc9b667e0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759679"
---
# <a name="azure-files-networking-considerations"></a>Considerações de rede dos Arquivos do Azure 
Você pode se conectar a um compartilhamento de arquivo do Azure de duas maneiras:

- Acessando o compartilhamento diretamente por meio do protocolo SMB, NFS (Network File System – versão prévia) ou protocolos FileREST. Esse padrão de acesso é empregado principalmente quando é possível eliminar o máximo possível de servidores locais.
- Criando um cache do compartilhamento de arquivo do Azure em um servidor local (ou em uma VM do Azure) com a Sincronização de Arquivos do Azure e acessando os dados do compartilhamento de arquivo do servidor local com o protocolo de sua escolha (SMB, NFS, FTPS etc.) para seu caso de uso. Esse padrão de acesso é útil porque ele combina o melhor de desempenho local e escala de nuvem e serviços anexáveis sem servidor, como o Backup do Azure.

Este artigo se concentra em como configurar a rede para quando seu caso de uso chamar o acesso ao compartilhamento de arquivo do Azure diretamente, em vez de usar a Sincronização de Arquivos do Azure. Para obter mais informações sobre considerações de rede para uma implantação de Sincronização de Arquivos do Azure, confira [Considerações de rede de Sincronização de Arquivos do Azure](../file-sync/file-sync-networking-overview.md).

A configuração de rede para compartilhamentos de arquivos do Azure é feita na conta de armazenamento do Azure. Uma conta de armazenamento é um constructo de gerenciamento que representa um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivos bem como outros recursos de armazenamento, como filas ou contêineres de blob. As contas de armazenamento expõem várias configurações que ajudam a proteger o acesso à rede para seus compartilhamentos de arquivos: pontos de extremidade de rede, configurações de firewall da conta de armazenamento e criptografia em trânsito. 

Recomendamos ler [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md) antes de ler este guia conceitual.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Este vídeo é um guia e uma demonstração de como expor com segurança compartilhamentos de arquivos do Azure diretamente para os operadores de informações e aplicativos em cinco etapas simples. As seções abaixo fornecem links e contexto adicional para a documentação referenciada no vídeo.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Acessar seus compartilhamentos de arquivos do Azure
Quando você implanta um compartilhamento de arquivo do Azure em uma conta de armazenamento, o compartilhamento de arquivo é imediatamente acessível por meio do ponto de extremidade público da conta de armazenamento. Isso significa que as solicitações autenticadas, como solicitações autorizadas pela identidade de logon de um usuário, podem se originar de maneira segura de dentro ou fora do Azure. 

Em muitos ambientes de clientes, uma montagem inicial do compartilhamento de arquivo do Azure em sua estação de trabalho local falhará, mesmo que as montagens de VMs do Azure tenham tido êxito. O motivo disso é que muitas organizações e ISPs (provedores de serviços de Internet) bloqueiam a porta que o SMB usa para se comunicar, a porta 445. Os compartilhamentos NFS não têm esse problema. Essa prática se origina de diretrizes de segurança sobre versões herdadas e preteridas do protocolo SMB. Embora o SMB 3.0 seja um protocolo seguro para a Internet, versões mais antigas do SMB, principalmente o SMB 1.0, não são. Os compartilhamentos de arquivo do Azure só podem ser acessados externamente por meio do SMB 3.0 e do protocolo FileREST (que também é um protocolo seguro para a Internet) por meio do ponto de extremidade público.

Como a maneira mais fácil de acessar o compartilhamento de arquivo SMB do Azure local é abrir sua rede local na porta 445, a Microsoft recomenda as seguintes etapas para remover o SMB 1.0 do seu ambiente:

1. Verifique se o SMB 1.0 foi removido ou desabilitado nos dispositivos de sua organização. Todas as versões atualmente compatíveis com o Windows e o Windows Server dão suporte à remoção ou à desabilitação do SMB 1.0 e, desde o lançamento do Windows 10, versão 1709, não há instalação do SMB 1.0 no Windows por padrão. Para saber mais sobre como desabilitar o SMB 1.0, consulte nossas páginas específicas do sistema operacional:
    - [Como proteger o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Como proteger o Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Certifique-se de que nenhum produto em sua organização exija o SMB 1.0 e remova aqueles que o fazem. Mantemos um [Centro de roteamento de produtos do SMB1](https://aka.ms/stillneedssmb1), que contém todos os produtos da Microsoft e de terceiros que exigem o SMB 1.0. 
1. (Opcional) Use um firewall de terceiros com a rede local de sua organização para impedir o tráfego do SMB 1.0 de sair de seu limite organizacional.

Se a organização exigir que a porta 445 seja bloqueada de acordo com a política ou o regulamento ou sua organização exigir que o tráfego para o Azure siga um caminho determinístico, você poderá usar o Gateway de VPN do Azure ou o ExpressRoute para criar um túnel de tráfego para seus compartilhamentos de arquivo do Azure. Os compartilhamentos NFS não exigem nada disso, pois não precisam da porta 445.

> [!Important]  
> Mesmo se você decidir usar um método alternativo para acessar seus compartilhamentos de arquivo do Azure, o Microsoft ainda recomendará remover o SMB 1.0 do seu ambiente.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Criação de um túnel de tráfego por uma rede privada virtual ou ExpressRoute
Quando você estabelecer um túnel de rede entre a rede local e o Azure, você emparelhará sua rede local com uma ou mais redes virtuais no Azure. Uma [rede virtual](../../virtual-network/virtual-networks-overview.md), ou VNet, é semelhante a uma rede tradicional que você operaria no local. Como uma conta de armazenamento do Azure ou uma VM do Azure, uma VNet é um recurso do Azure que é implantado em um grupo de recursos. 

Os Arquivos do Azure dão suporte aos seguintes mecanismos para criar túnel de tráfego entre as estações de trabalho e servidores locais e os compartilhamentos de arquivo SMB/NFS do Azure:

- Um gateway de VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego criptografado entre uma rede virtual do Azure e uma localização alternativa (como localmente) na Internet. Um Gateway de VPN do Azure é um recurso do Azure que pode ser implantado em um grupo de recursos junto com uma conta de armazenamento ou outros recursos do Azure. Os gateways de VPN expõem dois tipos diferentes de conexões:
    - Conexões de gateway de [VPN P2S (ponto a site)](../../vpn-gateway/point-to-site-about.md), que são conexões VPN entre o Azure e um cliente individual. Essa solução é útil principalmente para dispositivos que não fazem parte da rede local de sua organização, como telecomutadores que desejam poder montar o compartilhamento de arquivo do Azure de casa, de uma cafeteria ou de um hotel em trânsito. Para usar uma conexão VPN P2S com os Arquivos do Azure, será preciso configurar uma conexão VPN P2S para cada cliente que desejar se conectar. Para simplificar a implantação de uma conexão VPN P2S, confira [Configurar uma VPN P2S (Ponto a Site) no Windows para uso com os Arquivos do Azure](storage-files-configure-p2s-vpn-windows.md) e [Configurar uma VPN P2S (Ponto a Site) no Linux para uso com os Arquivos do Azure](storage-files-configure-p2s-vpn-linux.md).
    - [VPN S2S (site a site)](../../vpn-gateway/design.md#s2smulti), que são conexões VPN entre o Azure e a rede de sua organização. Uma conexão VPN S2S permite que você configure uma conexão VPN uma vez, para um servidor VPN ou dispositivo hospedado na rede de sua organização, em vez de fazer isso para cada dispositivo cliente que precisar acessar o compartilhamento de arquivo do Azure. Para simplificar a implantação de uma conexão VPN S2S, confira [Configurar uma VPN S2S (Site a Site) para uso com os Arquivos do Azure](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), que permite que você crie uma rota definida entre o Azure e sua rede local que não atravesse a Internet. Como o ExpressRoute fornece um caminho dedicado entre o datacenter local e o Azure, ele pode ser útil quando o desempenho da rede é importante. O ExpressRoute também é uma boa opção quando os requisitos regulatórios ou de política de sua organização exigem um caminho determinístico para seus recursos na nuvem.

Independentemente do método de túnel que você usa para acessar seus compartilhamentos de arquivo do Azure, você precisa de um mecanismo para verificar se o tráfego para sua conta de armazenamento passa pelo túnel, em vez de pela conexão de Internet regular. É tecnicamente possível rotear para o ponto de extremidade público da conta de armazenamento. No entanto, isso requer o hard-coding de todos os endereços IP dos clusters de armazenamento do Azure em uma região, pois as contas de armazenamento podem ser movidas entre clusters de armazenamento a qualquer momento. Isso também exige a atualização constante dos mapeamentos de endereços IP, já que novos clusters são adicionados o tempo todo.

Em vez de realizar hard-coding do endereço IP de suas contas de armazenamento para suas regras de roteamento de VPN, recomendamos usar pontos de extremidade privados, que oferecem à sua conta de armazenamento um endereço IP do espaço de endereço de uma rede virtual do Azure. Como a criação de um túnel para o Azure estabelece o emparelhamento entre a rede local e uma ou mais redes virtuais, isso habilita os roteiros corretos de maneira durável.

### <a name="private-endpoints"></a>Pontos de extremidade privados
Além do ponto de extremidade público padrão para uma conta de armazenamento, os Arquivos do Azure oferecem a opção de ter um ou mais pontos de extremidade privados. Um ponto de extremidade privado é um ponto de extremidade que só pode ser acessado dentro de uma rede virtual do Azure. Quando você cria um ponto de extremidade privado para sua conta de armazenamento, sua conta de armazenamento obtém um endereço IP privado de dentro do espaço de endereço de sua rede virtual, de maneira semelhante a como um servidor de arquivos local ou um dispositivo NAS recebe um endereço IP dentro do espaço de endereço dedicado de sua rede local. 

Um ponto de extremidade privado individual está associado a uma sub-rede de rede virtual do Azure específica. Uma conta de armazenamento pode ter pontos de extremidade privados em mais de uma rede virtual.

O uso de pontos de extremidade privados com Arquivos do Azure permite que você:
- Conecte-se com segurança aos compartilhamentos de arquivo do Azure de redes locais usando uma conexão VPN ou ExpressRoute com emparelhamento privado.
- Projeta seus compartilhamentos de arquivo do Azure configurando o firewall da conta de armazenamento para bloquear todas as conexões no ponto de extremidade público. Por padrão, a criação de um ponto de extremidade privado não bloqueia conexões com o ponto de extremidade público.
- Aumente a segurança da rede virtual permitindo que você bloqueie o vazamento de dados da rede virtual (e limites de emparelhamento).

Para criar um ponto de extremidade privado, confira [Configuração de pontos de extremidade privados para os Arquivos do Azure](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Pontos de extremidade privados e DNS
Quando você cria um ponto de extremidade privado, por padrão também criamos uma zona DNS privada (ou atualizamos uma existente) correspondente ao subdomínio `privatelink`. A rigor, a criação de uma zona DNS privada não é necessária para usar um ponto de extremidade privado para sua conta de armazenamento, mas é altamente recomendável em geral e explicitamente necessário ao montar seu compartilhamento de arquivo do Azure com uma entidade de usuário do Active Directory ou acessar da API FileREST.

> [!Note]  
> Este artigo usa o sufixo DNS da conta de armazenamento para as regiões Públicas do Azure, `core.windows.net`. Esse comentário também se aplica a nuvens soberanas do Azure, como a nuvem do Governo dos EUA para Azure e a nuvem do Azure China – só substitua os sufixos apropriados para seu ambiente. 

Em sua zona DNS privada, criamos um registro A para `storageaccount.privatelink.file.core.windows.net` e um registro CNAME para o nome regular da conta de armazenamento, que segue o padrão `storageaccount.file.core.windows.net`. Como a zona DNS privada do Azure está conectada à rede virtual que contém o ponto de extremidade privado, você pode observar a configuração de DNS ao chamar o cmdlet `Resolve-DnsName` do PowerShell em uma VM do Azure (ou `nslookup` no Windows e no Linux):

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

Isso reflete o fato de que a conta de armazenamento pode expor o ponto de extremidade público e um ou mais pontos de extremidade privados. Para verificar se o nome da conta de armazenamento resolve o endereço IP privado do ponto de extremidade privado, é necessário alterar a configuração em seus servidores DNS locais. Isso pode ser realizado de várias maneiras:

- Modificar os arquivos de hosts em seus clientes para fazer o `storageaccount.file.core.windows.net` resolver o endereço IP privado do ponto de extremidade privado desejado. Isso não é recomendável para ambientes de produção, já que você precisará fazer essas alterações em cada cliente que desejar montar seus compartilhamentos de arquivo do Azure e as alterações na conta de armazenamento ou no ponto de extremidade privado não serão manipuladas automaticamente.
- Criar um registro A para `storageaccount.file.core.windows.net` em seus servidores DNS locais. Isso tem a vantagem de que os clientes em seu ambiente local poderão resolver automaticamente a conta de armazenamento sem a necessidade de configurar cada cliente; no entanto, essa solução é igualmente frágil à modificação do arquivo de hosts porque as alterações não são refletidas. Embora essa solução seja frágil, ela pode ser a melhor opção para alguns ambientes.
- Encaminhe a zona `core.windows.net` de seus servidores DNS locais para a zona DNS privada do Azure. O host DNS privado do Azure pode ser acessado por meio de um endereço IP especial (`168.63.129.16`) que só pode ser acessado dentro de redes virtuais vinculadas à zona DNS privada do Azure. Para solucionar essa limitação, você pode executar servidores DNS adicionais dentro de sua rede virtual que encaminharão `core.windows.net` para a zona DNS privada do Azure. Para simplificar essa configuração, fornecemos os cmdlets do PowerShell que implantarão automaticamente os servidores DNS em sua rede virtual do Azure e os configurarão conforme desejado. Para saber como configurar o encaminhamento de DNS, confira [Configurar DNS com os Arquivos do Azure](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Configurações de firewall da conta de armazenamento
Um firewall é uma política de rede que controla quais solicitações têm permissão para acessar o ponto de extremidade público para uma conta de armazenamento. Usando o firewall da conta de armazenamento, você pode restringir o acesso ao ponto de extremidade público da conta de armazenamento a determinados endereços ou intervalos IP ou a uma rede virtual. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringirá o acesso de rede a uma ou mais redes virtuais. 

Há duas abordagens para a restrição do acesso a uma conta de armazenamento a uma rede virtual:
- Crie um ou mais pontos de extremidade privados para a conta de armazenamento e restrinja todo o acesso ao ponto de extremidade público. Com isso, apenas o tráfego proveniente de dentro das redes virtuais desejadas poderá acessar os compartilhamentos de arquivo do Azure dentro da conta de armazenamento.
- Restrinja o ponto de extremidade público a uma ou mais redes virtuais. Isso funciona usando uma funcionalidade da rede virtual denominada *pontos de extremidade de serviço*. Quando você restringir o tráfego a uma conta de armazenamento por meio de um ponto de extremidade de serviço, você ainda acessará a conta de armazenamento por meio do endereço IP público.

> [!NOTE]
> Os compartilhamentos NFS não podem acessar o ponto de extremidade público da conta de armazenamento por meio do endereço IP público; eles só podem acessar o ponto de extremidade público da conta de armazenamento usando redes virtuais. Os compartilhamentos NFS também podem acessar a conta de armazenamento usando pontos de extremidade privados.

Para saber mais sobre como configurar o firewall da conta de armazenamento, confira [configurar firewalls de armazenamento e redes virtuais do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Criptografia em trânsito

> [!IMPORTANT]
> Esta seção aborda os detalhes de criptografia em trânsito para compartilhamentos SMB. Para obter detalhes sobre a criptografia em trânsito com compartilhamentos NFS, confira [Segurança](storage-files-compare-protocols.md#security).

Por padrão, todas as contas de armazenamento do Azure têm criptografia em trânsito habilitada. Isso significa que quando você montar um compartilhamento de arquivo via SMB ou acessá-lo por meio do protocolo FileREST (por exemplo, por meio do portal do Azure, do PowerShell/CLI ou de SDKs do Azure), os Arquivos do Azure só permitirão a conexão se for feita com o SMB 3.0 e com criptografia ou HTTPS. Os clientes que não são compatíveis com SMB 3.0 ou os clientes que são compatíveis com SMB 3.0, mas não com a criptografia SMB, não poderão montar o compartilhamento de arquivo do Azure se a criptografia em trânsito estiver habilitada. Para obter mais informações sobre quais sistemas operacionais são compatíveis com o SMB 3.0 com criptografia, consulte nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) e [Linux](storage-how-to-use-files-linux.md). Todas as versões atuais do PowerShell, da CLI e dos SDKs são compatíveis com HTTPS.  

Você pode desabilitar a criptografia em trânsito para uma conta de armazenamento do Azure. Quando a criptografia estiver desabilitada, os Arquivos do Azure também permitirão o SMB 2.1, o SMB 3.0 sem criptografia e as chamadas à API FileREST não criptografadas sobre HTTP. O principal motivo para desabilitar a criptografia em trânsito é dar suporte a um aplicativo herdado que deve ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux. Os Arquivos do Azure só permitem conexões SMB 2.1 na mesma região do Azure que o compartilhamento de arquivo do Azure. Um cliente SMB 2.1 fora da região do Azure do compartilhamento de arquivo do Azure, como local ou em uma região diferente do Azure, não será capaz de acessar o compartilhamento de arquivo.

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Confira também
- [Visão geral do Arquivos do Azure](storage-files-introduction.md)
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)