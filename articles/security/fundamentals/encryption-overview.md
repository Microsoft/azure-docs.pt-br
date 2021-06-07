---
title: Visão geral da criptografia do Azure | Microsoft Docs
description: Saiba mais sobre as opções de criptografia no Azure. Veja informações de criptografia em repouso, criptografia em trânsito e gerenciamento de chaves com Azure Key Vault.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879707"
---
# <a name="azure-encryption-overview"></a>Visão geral da criptografia do Azure

Este artigo fornece uma visão geral de como a criptografia é usada no Microsoft Azure. Ele aborda as principais áreas da criptografia, incluindo criptografia em repouso, criptografia em trânsito e gerenciamento de chaves com o Azure Key Vault. Cada seção inclui links para informações mais detalhadas.

## <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso

Os dados em repouso incluem informações que residem no armazenamento persistente da mídia física, em qualquer formato digital. A mídia pode incluir arquivos em mídia magnética ou óptica, dados arquivados e backups de dados. O Microsoft Azure oferece uma variedade de soluções de armazenamento de dados para atender às diferentes necessidades, incluindo armazenamento de arquivo, disco, blob e tabela. A Microsoft também fornece criptografia para proteger o [Banco de Dados SQL do Azure](../../azure-sql/database/sql-database-paas-overview.md), o [Azure Cosmos DB](../../data-factory/introduction.md) e o Azure Data Lake.

A criptografia de dados em repouso está disponível para serviços nos modelos de nuvem SaaS (software como serviço), PaaS (plataforma como serviço) e IaaS (infraestrutura como serviço). Este artigo resume e fornece recursos para ajudá-lo a usar as opções de criptografia do Azure.

Para obter uma discussão mais detalhada de como os dados em repouso são criptografados no Azure, confira [Criptografia de dados em repouso no Azure](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modelos de criptografia do Azure

O Azure dá suporte a vários modelos de criptografia, incluindo criptografia no servidor que usa chaves gerenciadas pelo serviço, chaves gerenciadas pelo cliente no Key Vault ou chaves gerenciadas pelo cliente no hardware controlado pelo cliente. Com a criptografia do lado do cliente, você pode gerenciar e armazenar chaves localmente ou em outro local seguro.

### <a name="client-side-encryption"></a>Criptografia do cliente

A criptografia no cliente é realizada fora do Azure. Ele inclui:

- Dados criptografados por um aplicativo em execução no datacenter do cliente ou por um aplicativo de serviço.
- Dados já criptografados quando recebidos pelo Azure.

Com a criptografia do lado do cliente, os provedores de serviços de nuvem não têm acesso às chaves de criptografia e não podem descriptografar esses dados. Você mantém total controle das chaves.

### <a name="server-side-encryption"></a>Criptografia no servidor

Os três modelos de criptografia no servidor apresentam características diferentes de gerenciamento de chaves, que você pode escolher de acordo com seus requisitos:

- **Chaves gerenciadas pelo serviço**: fornecem uma combinação de controle e conveniência com baixa sobrecarga.

- **Chaves gerenciadas pelo cliente**: proporcionam controle das chaves, incluindo o suporte a BYOK (Bring Your Own Key), ou permitem a geração de novas.

- **Chaves gerenciadas pelo serviço em hardware controlado pelo cliente**: permitem gerenciar as chaves no repositório proprietário, fora do controle da Microsoft. Essa característica é chamada de HYOK (Host Your Own Key). No entanto, a configuração é complexa e a maioria dos serviços do Azure não dá suporte a esse modelo.

### <a name="azure-disk-encryption"></a>Criptografia de disco do Azure

Você pode proteger as máquinas virtuais do Windows e do Linux usando o [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md), que usa a tecnologia [BitLocker do Windows](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para proteger os discos do sistema operacional e os discos de dados com a criptografia de volume completo.

As chaves de criptografia e os segredos são protegidos em sua [assinatura do Azure Key Vault](../../key-vault/general/overview.md). Usando o serviço Backup do Azure, você pode fazer backup e restaurar VMs (máquinas virtuais) criptografadas que usam a configuração KEK (chave de criptografia de chave).

### <a name="azure-storage-service-encryption"></a>Criptografia do Serviço de Armazenamento do Azure

Os dados em repouso no Armazenamento de Blobs do Azure e nos compartilhamentos de arquivos do Azure podem ser criptografados em cenários do servidor e do lado do cliente.

A [SSE (Criptografia do Serviço de Armazenamento) do Azure](../../storage/common/storage-service-encryption.md) pode criptografar os dados automaticamente antes de serem armazenados, descriptografando-os automaticamente quando são recuperados. O processo é completamente transparente para os usuários. A Criptografia do Serviço de Armazenamento usa a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, que é uma das codificações de bloco mais fortes disponíveis. O AES manipula a criptografia, a descriptografia e o gerenciamento de chaves com transparência.

### <a name="client-side-encryption-of-azure-blobs"></a>Criptografia de blobs do Azure no cliente

Execute a criptografia do lado do cliente de blobs do Azure de várias maneiras.

Use a Biblioteca de Clientes do Armazenamento do Azure para pacote NuGet do .NET para criptografar os dados nos aplicativos cliente antes de carregá-los no armazenamento do Azure.

Para saber mais sobre a Biblioteca de Clientes do Armazenamento do Azure para pacote NuGet do .NET e baixá-la, confira [Armazenamento do Microsoft Azure 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Quando você usa a criptografia do lado do cliente com o Key Vault, os dados são criptografados usando uma CEK (chave de criptografia de conteúdo) simétrica avulsa que é gerada pelo SDK cliente do Armazenamento do Azure. A CEK é criptografada usando uma KEK (Chave de Criptografia de Chaves), que pode ser uma chave simétrica ou um par de chaves assimétricas. Você pode gerenciá-la localmente ou armazená-la no Key Vault. Em seguida, os dados criptografados são carregados no Armazenamento do Azure.

Para saber mais sobre a criptografia do lado do cliente com o Key Vault e obter uma introdução com instruções, consulte [Tutorial: Criptografar e descriptografar blobs no Armazenamento do Azure usando o Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Por fim, use também a Biblioteca de Clientes do Armazenamento do Azure para Java para executar a criptografia do lado do cliente antes de fazer upload de dados no Armazenamento do Azure e para descriptografar os dados ao baixá-los no cliente. Essa biblioteca também dá suporte à integração com o [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves de contas de armazenamento.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Criptografia de dados em repouso com o Banco de Dados SQL do Azure

O [Banco de Dados SQL do Azure](../../azure-sql/database/sql-database-paas-overview.md) é um serviço de banco de dados relacional de uso geral no Azure que dá suporte a estruturas como dados relacionais e espaciais, JSON e XML. O Banco de Dados SQL dá suporte à criptografia no servidor por meio do recurso TDE (Transparent Data Encryption) e à criptografia do lado do cliente por meio do recurso Always Encrypted.

#### <a name="transparent-data-encryption"></a>Criptografia de Dados Transparente

O [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) é usado para criptografar [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), banco de dados [SQL do Azure](../../azure-sql/database/sql-database-paas-overview.md)e arquivos de data do [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) em tempo real, usando uma chave de criptografia de banco de dados (Dek), que é armazenada no registro de inicialização do banco de dados para disponibilidade durante a recuperação.

A TDE protege dados e arquivos de log usando os algoritmos de criptografia AES e 3DES (DES triplo). A criptografia do arquivo de banco de dados é executada em nível de página. As páginas de um banco de dados criptografado são criptografadas antes de serem gravadas em disco e são descriptografadas quando lidas na memória. A TDE agora é habilitada por padrão em bancos de dados SQL do Azure recentemente criados.

#### <a name="always-encrypted-feature"></a>Recurso Always Encrypted

Com o recurso [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) no SQL do Azure, criptografe dados em aplicativos cliente antes de armazená-los no Banco de Dados SQL do Azure. Habilite também a delegação da administração de banco de dados local para terceiros e mantenha a separação entre aqueles que têm e podem exibir os dados e aqueles que os gerenciam, mas que não devem ter acesso a eles.

#### <a name="cell-level-or-column-level-encryption"></a>Criptografia em nível de célula ou de coluna

Com o Banco de Dados SQL do Azure, você pode aplicar a criptografia simétrica a uma coluna de dados usando o Transact-SQL. Essa abordagem é chamada de [CLE](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (criptografia em nível de célula ou de coluna), pois você pode usá-la para criptografar colunas específicas ou até mesmo células específicas de dados com diferentes chaves de criptografia. Ao fazer isso, você tem uma funcionalidade de criptografia mais granular do que a TDE, que criptografa dados em páginas.

A CLE tem funções internas que você pode usar para criptografar dados usando chaves simétricas ou assimétricas, a chave pública de um certificado ou uma frase secreta usando 3DES.

### <a name="cosmos-db-database-encryption"></a>Criptografia de banco de dados do Cosmos DB

O [Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) é o banco de dados multimodelo globalmente distribuído da Microsoft. Os dados de usuário armazenados no Cosmos DB em um armazenamento não volátil (unidades de estado sólido) são criptografados por padrão. Não existem controles para ativá-lo nem desativá-lo. A criptografia em repouso é implementada usando várias tecnologias de segurança, incluindo sistemas seguros de armazenamento de chaves, redes criptografadas e APIs criptográficas. As chaves de criptografia são gerenciadas pela Microsoft e giradas conforme as diretrizes internas da Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Criptografia em repouso no Data Lake

O [Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) é um repositório para toda a empresa de todos os tipos de dados coletados em um único lugar antes de qualquer definição formal de requisitos ou esquema. O Data Lake Store dá suporte "por padrão" à criptografia transparente de dados em repouso, configurada durante a criação de sua conta. Por padrão, o Azure Data Lake Store gerencia as chaves para você, mas você tem a opção de gerenciá-las por conta própria.

Três tipos de chave são usados na criptografia e descriptografia de dados: a MEK (Chave de Criptografia Mestra), DEK (Chave de Criptografia de Dados) e BEK (Chave de Criptografia de Bloco). A MEK é usada para criptografar a DEK, que é armazenada na mídia persistente, e a BEK é derivada da DEK e do bloco de dados. Se estiver gerenciando suas próprias chaves, você poderá alternar a MEK.

## <a name="encryption-of-data-in-transit"></a>Criptografia de dados em trânsito

O Azure oferece muitos mecanismos para manter os dados privados à medida que eles são movidos de um local para outro.

### <a name="data-link-layer-encryption-in-azure"></a>Criptografia de camada de link de dados no Azure

Sempre que o tráfego do cliente do Azure se move entre data centers – os limites físicos externos não são controlados pela Microsoft (ou em nome da Microsoft) – um método de criptografia de camada de vínculo de dados que usa os [padrões de segurança do Mac IEEE 802.1 AE](https://1.ieee802.org/security/802-1ae/) (também conhecido como MACsec) é aplicado de ponto a ponto no hardware de rede subjacente. Os pacotes são criptografados e descriptografados nos dispositivos antes de serem enviados, impedindo ataques físicos "Man-in-the-middle" ou de espionagem/wiretapping. Como essa tecnologia é integrada ao próprio hardware de rede, ela fornece criptografia de taxa de linha no hardware de rede sem aumento de latência de link mensurável. Essa criptografia MACsec está ativada por padrão para todo o tráfego do Azure que viaja dentro de uma região ou entre regiões, e nenhuma ação é necessária na parte dos clientes para habilitar. 

### <a name="tls-encryption-in-azure"></a>Criptografia TLS no Azure

A Microsoft oferece aos clientes a capacidade [de usar o](https://en.wikipedia.org/wiki/Transport_Layer_Security) protocolo TLS para proteger os dados quando estão viajando entre os serviços de nuvem e os clientes. Os datacenters da Microsoft negociam uma conexão TLS com os sistemas cliente que se conectam aos serviços do Azure. O TLS fornece autenticação forte, privacidade de mensagem e integridade (habilitando a detecção de adulteração, interceptação e falsificação de mensagens), interoperabilidade, flexibilidade de algoritmo e facilidade de implantação e uso.

O [PFS](https://en.wikipedia.org/wiki/Forward_secrecy) protege as conexões entre os sistemas cliente dos clientes e os serviços em nuvem da Microsoft por chaves exclusivas. As conexões também usam comprimentos de chave de criptografia de 2.048 bits baseados em RSA. Essa combinação dificulta a interceptação e o acesso a dados que estão em trânsito.

### <a name="azure-storage-transactions"></a>Transações do Armazenamento do Microsoft Azure

Quando você interage com o Armazenamento do Microsoft Azure por meio do Portal do Azure, todas as transações ocorrem por HTTPS. Também é possível usar a API REST do Armazenamento por HTTPS para interagir com o Armazenamento do Microsoft Azure. Imponha o uso de HTTPS ao chamar as APIs REST para acessar objetos em contas de armazenamento habilitando a transferência segura necessária para a conta de armazenamento.

A [SAS](../../storage/common/storage-sas-overview.md) (Assinaturas de Acesso Compartilhado), que pode ser usada para delegar acesso aos objetos do Armazenamento do Azure, inclui uma opção para especificar que apenas o protocolo HTTPS pode ser usado com as Assinaturas de Acesso Compartilhado. Essa abordagem garante que qualquer pessoa que envia links com tokens SAS use o protocolo apropriado.

O [SMB 3.0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), que é usado para acessar compartilhamentos de Arquivos do Azure, dá suporte à criptografia e está disponível no Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10. Ele permite o acesso entre regiões e até mesmo o acesso na área de trabalho.

A criptografia do lado do cliente criptografa os dados antes de serem enviados à instância do Armazenamento do Azure, de modo que sejam criptografados enquanto viajam pela rede.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Criptografia SMB nas redes virtuais do Azure 

Usando o [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) em VMs que executam o Windows Server 2012 ou posterior, você pode tornar as transferências de dados seguras criptografando os dados em trânsito nas Redes Virtuais do Azure. Criptografando os dados, você ajuda a proteger contra ataques de adulteração e interceptação. Os administradores podem habilitar a criptografia SMB para todo o servidor ou apenas para compartilhamentos específicos.

Por padrão, depois que a criptografia SMB é ativada para um compartilhamento ou servidor, somente os clientes do SMB 3.0 podem acessar os compartilhamentos criptografados.

## <a name="in-transit-encryption-in-vms"></a>Criptografia em trânsito em VMs

Os dados em trânsito para, de e entre as VMs que executam o Windows podem ser criptografados de várias maneiras, dependendo da natureza da conexão.

### <a name="rdp-sessions"></a>Sessões RDP

Você pode se conectar e entrar em uma VM usando o [protocolo RDP](/windows/win32/termserv/remote-desktop-protocol) em um computador cliente do Windows ou em um Mac com um cliente RDP instalado. Os dados em trânsito pela rede em sessões RDP podem ser protegidos pelo TLS.

Você também pode usar a Área de Trabalho Remota para se conectar a uma VM Linux no Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Acesso seguro a VMs do Linux com SSH

Para gerenciamento remoto, use o [SSH](../../virtual-machines/linux/ssh-from-windows.md) (Secure Shell) para se conectar às VMs do Linux em execução no Azure. O SSH é um protocolo de conexão criptografada que permite entradas seguras em conexões não protegidas. Ele é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Usando chaves SSH para autenticação, você elimina a necessidade de senhas para entrada. O SSH usa um par de chaves pública/privada (criptografia assimétrica) para autenticação.

## <a name="azure-vpn-encryption"></a>Criptografia de VPN do Azure

Você pode se conectar ao Azure por meio de uma rede virtual privada que cria um túnel seguro para proteger a privacidade dos dados que estão sendo enviados pela rede.

### <a name="azure-vpn-gateways"></a>Gateways de VPN do Azure

Use um [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) para enviar o tráfego criptografado entre a rede virtual e o local em uma conexão pública ou para enviar o tráfego entre redes virtuais.

As VPNs site a site usam o [protocolo IPsec](https://en.wikipedia.org/wiki/IPsec) como a criptografia de transporte. Os gateways de VPN do Azure usam um conjunto de propostas padrão. Você pode configurar os gateways de VPN do Azure para usar uma política personalizada IPsec/IKE com algoritmos de criptografia e intensidades de chave específicos, em vez de conjuntos de política padrão do Azure.

### <a name="point-to-site-vpns"></a>VPNs ponto a site

As VPNs ponto a site permitem que computadores cliente individuais acessem uma Rede Virtual do Azure. [O SSTP (Secure Socket Tunneling Protocol)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) é usado para criar o túnel VPN. Ele pode atravessar firewalls (o túnel aparece como uma conexão HTTPS). Use sua própria AC (autoridade de certificação) raiz de PKI (infraestrutura de chave pública) interna para a conectividade ponto a site.

Configure uma conexão VPN ponto a site com uma rede virtual usando o portal do Azure com autenticação de certificado ou o PowerShell.

Para saber mais sobre as conexões VPN ponto a site com redes virtuais do Azure, confira:

[Configurar uma conexão ponto a site com uma rede virtual usando a autenticação de certificado: portal do Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Configurar uma conexão ponto a site com uma rede virtual usando a autenticação de certificado: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>VPNs site a site 

Use uma conexão do Gateway de VPN site a site para conectar a rede local a uma Rede Virtual do Azure em um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN local que tenha um endereço IP público externo atribuído a ele.

Configure uma conexão VPN site a site com uma rede virtual usando o portal do Azure, o PowerShell ou a CLI do Azure.

Para obter mais informações, consulte:

[Criar uma conexão site a site no portal do Azure](../../vpn-gateway/tutorial-site-to-site-portal.md)

[Criar uma conexão site a site no PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Criar uma rede virtual com uma conexão VPN site a site usando a CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Criptografia em trânsito no Data Lake

Dados em trânsito (também conhecidos como dados em movimento) também são sempre criptografados no Data Lake Store. Além de criptografar os dados antes de armazená-los em uma mídia persistente, os dados são sempre protegidos em trânsito usando HTTPS. HTTPS é o único protocolo com suporte para as interfaces REST do Data Lake Store.

Para saber mais sobre a criptografia de dados em trânsito no Data Lake, confira [Criptografia de dados no Data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Gerenciamento de chaves com o Key Vault

Sem a proteção e o gerenciamento de chaves adequados, a criptografia é inútil. O Key Vault é a solução recomendada pela Microsoft para gerenciamento e controle de acesso às chaves de criptografia usadas pelos serviços de nuvem. As permissões às chaves de acesso podem ser atribuídas aos serviços ou aos usuários por meio das contas do Azure Active Directory.

O Key Vault alivia as organizações da necessidade de configurar, aplicar patch e manter HSMs (módulos de segurança de hardware) e um software de gerenciamento de chaves. Quando você usa o Key Vault, você mantém o controle. A Microsoft nunca vê suas chaves, e os aplicativos não têm acesso direto a elas. Você também pode importar ou gerar chaves em HSMs.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança do Azure](./overview.md)
- [Visão geral da segurança de rede do Azure](network-overview.md)
- [Visão geral de segurança do banco de dados do Azure](../../azure-sql/database/security-overview.md)
- [Visão geral de segurança de máquinas virtuais do Azure](virtual-machines-overview.md)
- [Criptografia de dados em repouso](encryption-atrest.md)
- [Práticas recomendadas de segurança e criptografia de dados](data-encryption-best-practices.md)