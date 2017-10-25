---
title: "Visão geral da criptografia do Azure | Microsoft Docs"
description: "Saiba mais sobre as várias opções de criptografia no Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-overview"></a>Visão geral da criptografia do Azure

Este artigo fornece uma visão geral de como a criptografia é usada no Microsoft Azure. Ele aborda as principais áreas da criptografia, incluindo criptografia em repouso, criptografia em trânsito e gerenciamento de chaves com o Key Vault. Cada seção inclui links para informações mais detalhadas.

## <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso

Os dados em repouso incluem informações que residem no armazenamento persistente da mídia física, em qualquer formato digital. Isso inclui arquivos em mídia magnética ou óptica, dados arquivados e backups de dados. O Microsoft Azure oferece uma variedade de soluções de armazenamento de dados para atender às diferentes necessidades, incluindo armazenamento de arquivo, disco, blob e tabela. A Microsoft também fornece criptografia para proteger o [Banco de Dados SQL do Azure](../sql-database/sql-database-technical-overview.md), o [CosmosDB](../cosmos-db/introduction.md) e o Azure Data Lake.

A criptografia de dados em repouso está disponível para serviços entre os modelos de nuvem SaaS (Software como Serviço), PaaS (Plataforma como Serviço) e IaaS (Infraestrutura como Serviço) do Azure. Este documento resume e fornece recursos que ajudam a usar as opções de criptografia do Azure.

Para ver uma discussão mais detalhada de como os dados em repouso são criptografados no Azure, confira o documento [Criptografia de dados em repouso no Azure](azure-security-encryption-atrest.md)

## <a name="azure-encryption-models"></a>Modelos de criptografia do Azure

O Azure dá suporte a vários modelos de criptografia, incluindo criptografia no servidor usando chaves gerenciadas pelo serviço, chaves gerenciadas pelo cliente no Azure Key Vault ou chaves gerenciadas pelo cliente no hardware controlado pelo cliente. A criptografia do lado do cliente permite gerenciar e armazenar chaves no local ou em outro local seguro.

### <a name="client-side-encryption"></a>Criptografia do cliente

A criptografia no cliente é realizada fora do Azure. A criptografia no cliente inclui:

- Dados criptografados por um aplicativo que está em execução no data center do cliente ou por um aplicativo de serviço
- Dados já criptografados quando recebidos pelo Azure.

Com a criptografia no cliente, o provedor de serviços de nuvem não tem acesso às chaves de criptografia e não pode descriptografar esses dados. Você mantém total controle das chaves.

### <a name="server-side-encryption"></a>Criptografia no servidor

Os três modelos de criptografia no servidor apresentam características diferentes de gerenciamento de chaves, que podem ser escolhidas de acordo com seus requisitos.

- **Chaves gerenciadas pelo serviço**: fornecem uma combinação de controle e conveniência com baixa sobrecarga

- **Chaves gerenciadas pelo cliente**: proporcionam controle das chaves, incluindo a capacidade BYOK (traga suas próprias chaves) ou de gerar novas.

- **Chaves gerenciados pelo serviço em ccustomer-controlledhardware**: permitem gerenciar chaves no seu repositório patenteado que está fora do controle da Microsoft. Isso é chamado de HYOK (hospede sua própria chave). No entanto, a configuração é complexa e a maioria dos serviços do Azure não dá suporte a esse modelo.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

As máquinas virtuais Windows e Linux podem ser protegidas usando o [Azure Disk Encryption](azure-security-disk-encryption.md), que usa a tecnologia [BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) do Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para proteger os discos do sistema operacional e os discos de dados com a criptografia de volume completo.

As chaves de criptografia e os segredos são protegidos na sua assinatura do [Azure Key Vault](../key-vault/key-vault-whatis.md). É possível fazer backup e restaurar VMs criptografadas que são criptografadas com a configuração KEK usando o serviço Backup do Azure.

### <a name="azure-storage-service-encryption"></a>Criptografia do serviço de Armazenamento do Microsoft Azure

Os dados em repouso no Armazenamento do Microsoft Azure (Blobs e Arquivos) podem ser criptografados em cenários do servidor e do cliente.

A SSE ([Criptografia do Serviço de Armazenamento do Microsoft Azure](../storage/storage-service-encryption.md)) pode criptografar dados automaticamente antes de eles serem armazenados e descriptografá-los automaticamente quando você os recupera, tornando o processo completamente transparente aos usuários. A Criptografia do Serviço de Armazenamento usa [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, que é uma das codificações de bloco mais fortes disponíveis, e trata a criptografia, a descriptografia e o gerenciamento de chaves de maneira transparente.

### <a name="client-side-encryption-of-azure-blobs"></a>Criptografia de blobs do Azure no cliente

A criptografia de blobs do Azure no cliente pode ser realizada de maneiras diferentes.

Você pode usar a Biblioteca de Clientes do Armazenamento do Microsoft Azure para pacote NuGet .NET a fim de criptografar dados em seus aplicativos cliente antes de carregá-los no Armazenamento do Microsoft Azure.

Para saber mais e baixar a Biblioteca de Clientes do Armazenamento do Microsoft Azure para pacote NuGet .NET, confira o documento [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)

Quando você usa a criptografia no cliente com o Azure Key Vault, os dados são criptografados usando uma CEK (Chave de Criptografia de Conteúdo) simétrica ocasional que é gerada pelo SDK cliente do Armazenamento do Microsoft Azure. A CEK é criptografada usando uma KEK (Chave de Criptografia de Chaves), que pode ser uma chave simétrica ou um par de chaves assimétricas. Você pode gerenciá-la localmente ou armazená-la no Azure Key Vault. Os dados criptografados são carregados no serviço de Armazenamento do Microsoft Azure.

Para saber mais sobre a criptografia no cliente com o Azure Key Vault e obter uma introdução com instruções, confira o documento [Tutorial: criptografar e descriptografar blobs no Armazenamento do Microsoft Azure usando o Cofre da Chave do Azure](../storage/storage-encrypt-decrypt-blobs-key-vault.md)

Por fim, você também pode usar a Biblioteca de Clientes do Armazenamento do Microsoft Azure para Java de modo a executar a criptografia no cliente antes de carregar dados no Armazenamento do Microsoft Azure e descriptografar os dados ao baixá-los no cliente. Essa biblioteca também dá suporte à integração ao [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gerenciamento de chaves da conta de armazenamento.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Criptografia de dados em repouso com o banco de dados SQL do Azure

O [Banco de Dados SQL do Azure](../sql-database/sql-database-technical-overview.md)é um serviço de banco de dados relacional de uso geral no Microsoft Azure que dá suporte a estruturas como XML, JSON, espacial e dados relacionais. O SQL do Azure dá suporte à criptografia no servidor por meio do recurso TDE (Transparent Data Encryption) e à criptografia no cliente por meio do recurso Always Encrypted.

#### <a name="transparent-data-encryption"></a>Transparent Data Encryption

A [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) (Transparent Data Encryption) é usada para criptografar arquivos de dados do [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Banco de Dados SQL do Azure](../sql-database/sql-database-technical-overview.md) e [SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) em tempo real usando uma DEK (chave de criptografia de banco de dados), que é armazenada no registro de inicialização do banco de dados para disponibilidade durante a recuperação.

A TDE protege dados e arquivos de log usando os algoritmos de criptografia AES e 3DES. A criptografia do arquivo de banco de dados é realizada no nível de página; as páginas em um banco de dados criptografado são criptografadas antes de serem gravadas no disco e descriptografadas quando são lidas na memória. A TDE agora é habilitada por padrão em bancos de dados SQL do Azure recentemente criados.

#### <a name="always-encrypted"></a>Always Encrypted

O recurso [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) no SQL do Azure permite criptografar dados em aplicativos cliente antes de armazená-los no Banco de Dados SQL do Azure. Além disso, com esse recurso, você pode delegar a administração de um banco de dados local a terceiros e manter a separação entre aqueles que possuem e podem exibir os dados e aqueles que os gerenciam, mas não devem ter acesso a eles.

#### <a name="cellcolumn-level-encryption"></a>Criptografia no nível de célula/coluna

O Banco de Dados SQL do Azure permite aplicar criptografia simétrica a uma coluna de dados usando Transact-SQL. Isso é chamado de CLE ([criptografia no nível de célula ou criptografia no nível de coluna](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data)), pois você pode usá-la para criptografar colunas específicas ou até mesmo células de dados específicas com diferentes chaves de criptografia. Isso proporciona recurso de criptografia mais granular que a TDE, que criptografa dados em páginas.

A CLE tem funções internas que você pode usar para criptografar dados usando chaves simétricas ou assimétricas, com a chave pública de um certificado, ou com uma frase secreta usando 3DES.

### <a name="cosmos-db-database-encryption"></a>Criptografia de banco de dados do Cosmos DB

O [Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) é o banco de dados multimodelo globalmente distribuído da Microsoft. Os dados de usuário armazenados no Cosmos DB em armazenamento não volátil (unidades de estado sólido) são criptografados por padrão; não há controles para ativá-los nem desativá-los. A criptografia em repouso é implementada usando várias tecnologias de segurança, incluindo sistemas seguros de armazenamento de chaves, redes criptografadas e APIs criptográficas. As chaves de criptografia são gerenciadas pela Microsoft e alternadas por diretrizes internas da Microsoft.

### <a name="at-rest-encryption-in-azure-data-lake"></a>Criptografia em repouso no Azure Data Lake

O [Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) é um repositório para toda a empresa de todos os tipos de dados coletados em um único lugar antes de qualquer definição formal de requisitos ou esquema. O Azure Data Lake Store dá suporte "por padrão" à criptografia de dados transparente em repouso, que é configurada durante a criação da sua conta. Por padrão, o Data Lake Store gerencia as chaves para você, mas você tem a opção de gerenciá-las por si só.

Três tipos de chave são usados na criptografia e descriptografia de dados: a MEK (Chave de Criptografia Mestra), DEK (Chave de Criptografia de Dados) e BEK (Chave de Criptografia de Bloco). A MEK é usada para criptografar a DEK, que é armazenada na mídia persistente, e a BEK é derivada da DEK e do bloco de dados. Se estiver gerenciando suas próprias chaves, você poderá alternar a MEK.

## <a name="encryption-of-data-in-transit"></a>Criptografia de dados em trânsito

O Azure oferece muitos mecanismos para manter os dados privados à medida que eles são movidos de um local para outro.

### <a name="tlsssl-encryption-in-azure"></a>Criptografia TLS/SSL no Azure

A Microsoft usa o protocolo TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) para proteger dados quando eles estão viajando entre os serviços de nuvem e os clientes. Os data centers da Microsoft negociam uma conexão TLS com os sistemas cliente que se conectam aos serviços do Azure. O TLS fornece autenticação forte, privacidade de mensagem e integridade (habilitando a detecção de violação, interceptação e falsificação de mensagens), interoperabilidade, flexibilidade de algoritmo, facilidade de implantação e uso.

O PFS ([Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy)) protege conexões entre sistemas cliente dos clientes e serviços em nuvem da Microsoft por chaves exclusivas. As conexões também usam comprimentos de chave de criptografia de 2.048 bits baseados em RSA. Essa combinação dificulta a interceptação e o acesso a dados que estão em trânsito.

### <a name="azure-storage-transactions"></a>Transações do Armazenamento do Microsoft Azure

Quando você interage com o Armazenamento do Microsoft Azure por meio do Portal do Azure, todas as transações ocorrem por HTTPS. Também é possível usar a API REST do Armazenamento por HTTPS para interagir com o Armazenamento do Microsoft Azure. É possível impor o uso de HTTPS ao chamar as APIs REST para acessar objetos em contas de armazenamento habilitando a opção Transferência segura necessária na conta de armazenamento.

A [SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) (Assinatura de Acesso Compartilhado), que pode ser usada para delegar acesso aos objetos do Armazenamento do Microsoft Azure, inclui uma opção para especificar que apenas o protocolo HTTPS pode ser usado quando se usa as Assinaturas de Acesso Compartilhado. Isso garante que qualquer pessoa que esteja enviando links com tokens SAS use o protocolo adequado.

O [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) usado para acessar os Compartilhamentos de Arquivos do Azure dá suporte à criptografia e está disponível no Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo o acesso entre regiões e, até mesmo, o acesso na área de trabalho.

A criptografia no cliente criptografa os dados antes que eles sejam enviados ao Armazenamento do Microsoft Azure, de modo que eles são criptografados enquanto viajam pela rede.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Criptografia SMB pelas Redes Virtuais do Azure 

O [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) em VMs do Azure em execução no Windows Server 2012 e superiores permite que você proteja as transferências de dados criptografando os dados em trânsito pelas Redes Virtuais do Azure, de modo a proteger contra ataques de falsificação e interceptação. Os administradores podem habilitar a Criptografia SMB para todo o servidor ou apenas para compartilhamentos específicos.

Por padrão, assim que a Criptografia SMB é ativada para um compartilhamento ou servidor, somente clientes do SMB 3 podem acessar os compartilhamentos criptografados.

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Criptografia em trânsito nas Máquinas Virtuais do Azure

Os dados em trânsito, em todas as direções, entre as VMs do Azure em execução no Windows são criptografados de várias maneiras, dependendo da natureza da conexão.

### <a name="rdp-sessions"></a>Sessões RDP

Você pode se conectar e fazer logon em uma VM do Azure usando o [protocolo RDP](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) em um computador cliente do Windows ou em um Mac com um cliente RDP instalado. Os dados em trânsito pela rede em sessões RDP podem ser protegidos pelo TLS.

Você também pode usar a Área de Trabalho Remota para se conectar a uma VM Linux no Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Acesso seguro a VMs do Linux com SSH

Você pode usar SSH ([Secure Shell](../virtual-machines/linux/ssh-from-windows.md)) para se conectar às VMs Linux em execução no Azure para gerenciamento remoto. O SSH é um protocolo de conexão criptografada que permite logons seguros por conexões não protegidas. Ele é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Ao usar chaves SSH para autenticação, você elimina a necessidade de senhas para efetuar logon. O SSH usa um par de chaves pública/privada (criptografia assimétrica) para autenticação.

## <a name="azure-vpn-encryption"></a>Criptografia de VPN do Azure

Você pode se conectar ao Azure por meio de uma rede virtual privada que cria um túnel seguro para proteger a privacidade dos dados que estão sendo enviados pela rede.

### <a name="azure-vpn-gateway"></a>Gateway de VPN do Azure

O [gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) pode ser usado para enviar tráfego criptografado entre a rede virtual e seu local por uma conexão pública ou para enviar tráfego entre redes virtuais.

A VPN site a site usa [IPsec](https://en.wikipedia.org/wiki/IPsec) para criptografia de transporte. Os gateways de VPN do Azure usam um conjunto de propostas padrão. Você pode configurar os gateways de VPN do Azure para usar uma política personalizada IPsec/IKE com algoritmos de criptografia e intensidades de chave específicos, em vez de conjuntos de política padrão do Azure.

### <a name="point-to-site-vpn"></a>VPN ponto a site

As VPNs ponto a site permitem que computadores cliente individuais acessem uma Rede Virtual do Azure. O SSTP ([Secure Socket Tunneling Protocol](https://technet.microsoft.com/library/2007.06.cableguy.aspx)) é usado para criar o túnel VPN e pode percorrer firewalls (o túnel aparece como uma conexão HTTPS). Você pode usar sua própria AC raiz de PKI interna para conectividade ponto a site.

É possível configurar uma conexão VPN ponto a site com uma rede virtual usando o Portal do Azure com autenticação de certificado ou PowerShell.

Para saber mais sobre conexões VPN ponto a site com Redes Virtuais do Azure, confira: [Configurar uma conexão ponto a site com uma VNet usando autenticação de certificado: Portal do Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) e

[Configurar uma conexão ponto a site com uma VNet usando a autenticação de certificado: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>VPN site a site 

Uma conexão de gateway de VPN Site a Site é usada para conectar a rede local a uma rede virtual do Azure por um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele.

Você pode configurar uma conexão VPN site a site com uma rede virtual usando o Portal do Azure, o PowerShell ou a CLI (Interface de Linha de Comando) do Azure.

Leia estes artigos para saber mais:

[Criar uma conexão site a site no Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Criar uma conexão site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Criar uma rede virtual com uma conexão VPN site a site usando a CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Criptografia em trânsito no Azure Data Lake

Dados em trânsito (também conhecidos como dados em movimento) também são sempre criptografados no Data Lake Store. Além de criptografar os dados antes de armazenar em mídia persistente, os dados são sempre protegidos em trânsito usando HTTPS. HTTPS é o único protocolo com suporte para as interfaces REST do Data Lake Store.

Para saber mais sobre a criptografia de dados em trânsito no Azure Data Lake, confira o documento [Criptografia de dados no Azure Data Lake Store.](../data-lake-store/data-lake-store-encryption.md)

## <a name="key-management-with-azure-key-vault"></a>Gerenciamento de chaves com o Azure Key Vault

Sem a proteção e o gerenciamento de chaves adequados, a criptografia é inútil. O Azure Key Vault é a solução recomendada pela Microsoft para gerenciamento e controle de acesso para chaves de criptografia usadas pelos serviços de nuvem. As permissões às chaves de acesso podem ser atribuídas aos serviços ou aos usuários por meio das contas do Azure Active Directory.

O Azure Key Vault alivia para as organizações a necessidade de configurar, aplicar patches e manter HSMs (Módulos de Segurança de Hardware) e software de gerenciamento de chaves. Com o Azure Key Vault, a Microsoft nunca vê suas chaves e os aplicativos não têm acesso direto a elas; você mantém o controle. Você também pode importar ou gerar chaves em HSMs.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança do Azure](security-get-started-overview.md)
- [Visão geral da segurança de rede do Azure](security-network-overview.md)
- [Visão geral de segurança do banco de dados do Azure](azure-database-security-overview.md)
- [Visão geral de segurança de máquinas virtuais do Azure](security-virtual-machines-overview.md)
- [Criptografia de dados em repouso](azure-security-encryption-atrest.md)
- [Práticas recomendadas de segurança e criptografia de dados](azure-security-data-encryption-best-practices.md)
