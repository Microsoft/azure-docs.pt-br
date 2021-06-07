---
title: Arquiteturas para implantar aplicativos Oracle em Máquinas Virtuais do Azure | Microsoft Docs
description: Arquiteturas de aplicativo para implantar aplicativos Oracle, incluindo o E-Business Suite, o JD Edwards EnterpriseOne e o PeopleSoft, em máquinas virtuais do Microsoft Azure com bancos de dados no Azure ou na OCI (Oracle Cloud Infrastructure).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: 862bb886c7ec5dfd40c7acdbae2f70f6698a711b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669926"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arquiteturas para implantar aplicativos Oracle no Azure

A Microsoft e a Oracle trabalharam em conjunto para habilitar os clientes a implantar aplicativos Oracle, como o Oracle E-Business Suite, o JD Edwards EnterpriseOne e o PeopleSoft, na nuvem. Com a introdução da versão prévia da [interconectividade de rede privada](configure-azure-oci-networking.md) entre o Microsoft Azure e a OCI (Oracle Cloud Infrastructure), aplicativos Oracle agora podem ser implantados no Azure com seus bancos de dados de back-end no Azure ou na OCI. Aplicativos Oracle também podem ser integrados ao Azure Active Directory, permitindo que você configure o logon único para que os usuários possam entrar no aplicativo Oracle usando credenciais do Azure AD (Azure Active Directory).

A OCI oferece várias opções de banco de dados da Oracle para aplicativos Oracle, incluindo o DBaaS, o Exadata Cloud Service, o Oracle RAC e a IaaS (infraestrutura como serviço). Atualmente, o banco de dados autônomo não é um back-end com suporte para aplicativos Oracle.

Há [várias opções](oracle-overview.md) para implantar aplicativos Oracle no Azure, incluindo uma forma altamente disponível e segura. O Azure também oferece [imagens de VM de banco de dados Oracle](oracle-vm-solutions.md) que você pode implantar caso prefira executar aplicativos Oracle totalmente no Azure.

As seções a seguir descrevem as recomendações de arquitetura da Microsoft e da Oracle para implantar o E-Business Suite, o JD Edwards EnterpriseOne e o PeopleSoft da Oracle em uma configuração entre nuvens ou inteiramente no Azure. A Microsoft e a Oracle testaram os aplicativos e confirmaram que o desempenho atende aos padrões definidos pela Oracle para eles.

## <a name="architecture-considerations"></a>Considerações sobre arquitetura

Os aplicativos Oracle são compostos por vários serviços, que podem ser hospedados na mesma ou em várias máquinas virtuais no Azure e, opcionalmente, na OCI. 

As instâncias de aplicativo podem ser configuradas com pontos de extremidade públicos ou privados. A Microsoft e a Oracle recomendam configurar uma *VM bastion* com um endereço IP público em uma sub-rede separada para gerenciamento do aplicativo. Em seguida, atribua apenas endereços IP privados aos outros computadores, incluindo a camada de banco de dados. 

Ao configurar um aplicativo em uma arquitetura entre nuvens, é necessário planejamento para garantir que o espaço de endereços IP na rede virtual do Azure não se sobreponha ao espaço de endereços IP privado na rede da nuvem virtual da OCI.

Para maior segurança, configure grupos de segurança de rede no nível da sub-rede para garantir que apenas o tráfego em portas e em endereços IP específicos seja permitido. Por exemplo, computadores na camada intermediária só devem receber tráfego de dentro da rede virtual. Nenhum tráfego externo deve alcançar diretamente os computadores da camada intermediária.

Para alta disponibilidade, você pode configurar instâncias redundantes dos diferentes servidores no mesmo conjunto de disponibilidade ou em zonas de disponibilidade diferentes. As zonas de disponibilidade permitem que você atinja um SLA de tempo de atividade de 99,99%, enquanto os conjuntos de disponibilidade permitem atingir um SLA de tempo de atividade de 99,95% dentro da região. As arquiteturas de exemplo mostradas neste artigo são implantadas em duas zonas de disponibilidade.

Ao implantar um aplicativo usando a interconexão entre nuvens, você pode continuar usando um circuito do ExpressRoute existente para conectar o ambiente do Azure à rede local. No entanto, para interconexão da OCI, você precisa de um circuito do ExpressRoute separado daquele que se conecta à rede local.

## <a name="e-business-suite"></a>E-Business Suite

O EBS (Oracle E-Business Suite) é um conjunto de aplicativos que inclui SCM (gerenciamento da cadeia de fornecedores) e CRM (gerenciamento de relacionamento com o cliente). Para tirar proveito do portfólio de banco de dados gerenciado da OCI, o EBS pode ser implantado usando a interconexão entre nuvens entre o Microsoft Azure e a OCI. Nessa configuração, as camadas de apresentação e de aplicativo são executadas no Azure e a camada de banco de dados na OCI, conforme ilustrado no diagrama de arquitetura a seguir (Figura 1).

![Arquitetura entre nuvens do E-Business Suite](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: Arquitetura entre nuvens do E-Business Suite* 

Nessa arquitetura, a rede virtual no Azure está conectada a uma rede de nuvem virtual na OCI usando a interconexão entre nuvens. A camada de aplicativo está configurada no Azure, enquanto o banco de dados está configurado na OCI. É recomendável implantar cada componente na própria sub-rede com grupos de segurança de rede para permitir o tráfego somente de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure, com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O seguinte diagrama (Figura 2) é um exemplo desse padrão de arquitetura:

![Arquitetura do E-Business Suite somente no Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: Arquitetura do E-Business Suite somente no Azure*

As seções a seguir descrevem os diferentes componentes em alto nível.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Camada de aplicativo (intermediária)

A camada de aplicativo fica isolada na própria sub-rede. Várias máquinas virtuais são configuradas para fins de tolerância a falhas e gerenciamento fácil de patches. Essas VMs podem ter suporte do armazenamento compartilhado, que é oferecido pelo Azure NetApp Files e pelos SSDs Ultra. Essa configuração facilita a implantação de patches sem tempo de inatividade. Os computadores na camada de aplicativo devem contar com um balanceador de carga público para que as solicitações para a camada de aplicativo do EBS sejam processadas mesmo que um computador na camada esteja offline devido a uma falha.

### <a name="load-balancer"></a>Balanceador de carga

Um balanceador de carga do Azure permite distribuir o tráfego entre várias instâncias da carga de trabalho para garantir a alta disponibilidade. Nesse caso, um balanceador de carga público está configurado pois os usuários têm permissão para acessar o aplicativo EBS pela Web. O balanceador de carga distribui a carga para os dois computadores na camada intermediária. Para maior segurança, permita o tráfego somente de usuários que acessam o sistema na rede corporativa usando uma VPN site a site ou o ExpressRoute com grupos de segurança de rede.

### <a name="database-tier"></a>Camada de banco de dados

Essa camada hospeda o banco de dados Oracle e é separada na própria sub-rede. É recomendável adicionar grupos de segurança de rede que permitam somente tráfego da camada de aplicativo para a camada de banco de dados na porta de banco de dados 1521, que é específica da Oracle.

A Microsoft e a Oracle recomendam uma configuração de alta disponibilidade. A alta disponibilidade no Azure pode ser obtida configurando dois bancos de dados Oracle em duas zonas de disponibilidade com o Oracle Data Guard ou usando o Oracle Database Exadata Cloud Service na OCI. Ao usar o Oracle Database Exadata Cloud Service, seu banco de dados é implantado em duas sub-redes. Você também pode configurar o Oracle Database em VMs na OCI em dois domínios de disponibilidade com o Oracle Data Guard.


### <a name="identity-tier"></a>Camada de identidade

A camada de identidade contém a VM do EBS Asserter. O EBS Asserter permite que você sincronize identidades do IDCS (serviço de nuvem de identidade) da Oracle e do Azure AD. O EBS Asserter é necessário porque o EBS não dá suporte a protocolos de logon único como SAML 2.0 ou OpenID Connect. O EBS Asserter consome o token do OpenID Connect (gerado pelo IDCS), valida-o e cria uma sessão para o usuário no EBS. 

Embora essa arquitetura mostre a integração do IDCS, o acesso unificado e o logon único do Azure AD também podem ser habilitados com o Oracle Access Manager com o Oracle Internet Directory ou o Oracle Unified Directory. Para obter mais informações, confira os white papers sobre [Como implantar o Oracle EBS com a integração do IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) ou [Como implantar o Oracle EBS com integração do OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Para alta disponibilidade, é recomendável que você implante servidores redundantes do EBS Asserter em várias zonas de disponibilidade com um balanceador de carga na frente.

Quando sua infraestrutura estiver configurada, o E-Business Suite poderá ser instalado seguindo o guia de instalação fornecido pela Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

O JD Edwards EnterpriseOne da Oracle é um conjunto de aplicativos integrados composto por uma ampla gama de programas de software de planejamento de recursos empresariais. Trata-se de um aplicativo de várias camadas que pode ser configurado com um back-end de banco de dados Oracle ou SQL Server. Esta seção aborda detalhes de como implantar o JD Edwards EnterpriseOne com um back-end de banco de dados Oracle na OCI ou no Azure.

Na arquitetura recomendada a seguir (Figura 3), as camadas de administração, de apresentação e intermediárias estão implantadas na rede virtual no Azure. O banco de dados está implantado em uma rede de nuvem virtual na OCI.

Assim como acontece com o E-Business Suite, você pode configurar uma camada bastion opcional para fins de administração segura. Use o host da VM bastion como um servidor de salto para acessar as instâncias de aplicativo e de banco de dados.

![Arquitetura entre nuvens do JD Edwards EnterpriseOne](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: Arquitetura entre nuvens do JD Edwards EnterpriseOne*

Nessa arquitetura, a rede virtual no Azure está conectada à rede de nuvem virtual na OCI usando a interconexão entre nuvens. A camada de aplicativo está configurada no Azure, enquanto o banco de dados está configurado na OCI. É recomendável implantar cada componente na própria sub-rede com grupos de segurança de rede para permitir o tráfego somente de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure, com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O seguinte diagrama (Figura 4) é um exemplo desse padrão de arquitetura:

![Arquitetura do JD Edwards EnterpriseOne somente no Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: Arquitetura do JD Edwards EnterpriseOne somente no Azure*

As seções a seguir descrevem os diferentes componentes em alto nível.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Camada administrativa

Como o nome sugere, essa camada é usada para tarefas administrativas. Você pode separar uma sub-rede para a camada administrativa. Os serviços e servidores nessa camada são usados principalmente para a instalação e a administração do aplicativo. Sendo assim, instâncias únicas desses servidores são suficientes. Instâncias redundantes não são necessárias para que o aplicativo tenha alta disponibilidade.

Os componentes da camada são os seguintes:
    
 - **Servidor de provisionamento** – é usado para implantação de ponta a ponta dos diferentes componentes do aplicativo. Ele se comunica com as instâncias nas outras camadas, incluindo na camada de banco de dados, pela porta 22. Ele hospeda o Console do Gerenciador do Servidor do JD Edwards EnterpriseOne.
 - **Servidor de implantação** – é necessário principalmente para a instalação do JD Edwards EnterpriseOne. Durante o processo de instalação, ele atua como o repositório central dos arquivos e dos pacotes de instalação necessários. O software é distribuído ou implantado em outros servidores e clientes usando este servidor.
 - **Cliente de desenvolvimento** – este servidor contém componentes que são executados em um navegador da Web, bem como aplicativos nativos.

### <a name="presentation-tier"></a>Camada de apresentação

Essa camada contém vários componentes, como o AIS (Application Interface Services), a ADF (Application Development Framework) e os JAS (Java Application Servers). Os servidores nessa camada se comunicam com os servidores na camada intermediária. Eles contam com um balanceador de carga que roteia o tráfego para o servidor necessário com base no número da porta e na URL em que o tráfego é recebido. Para ter alta disponibilidade, é recomendável implantar várias instâncias de cada tipo de servidor.

Estes são os componentes desta camada:
    
- **AIS (Application Interface Services)** – o servidor AIS fornece a interface de comunicação entre os aplicativos empresariais móveis do JD Edwards EnterpriseOne e o JD Edwards EnterpriseOne.
- **JAS (Java Application Server)** – o JAS recebe solicitações do balanceador de carga e as transmite para a camada intermediária para execução de tarefas complicadas. O JAS executa lógica de negócios simples.
- **BIP (BI Publisher Server)** – este servidor apresenta relatórios baseados nos dados coletados pelo aplicativo JD Edwards EnterpriseOne. Você pode projetar e controlar como o relatório apresenta os dados com base em modelos diferentes.
- **BSS (Business Services Server)** – o BSS habilita a troca de informações e a interoperabilidade com outros aplicativos Oracle.
- **RTE (Real-Time Events Server)** – o Servidor RTE permite que você configure notificações para sistemas externos sobre transações que ocorrem no sistema do JDE EnterpriseOne. Ele usa um modelo de assinante e permite que sistemas de terceiros assinem eventos. Para balancear a carga das solicitações para ambos os servidores RTE, verifique se os servidores estão em um cluster.
- **Servidor ADF (Application Development Framework)** – o servidor ADF é usado para executar aplicativos do JD Edwards EnterpriseOne desenvolvidos com o ADF da Oracle. Ele é implantado em um servidor WebLogic da Oracle com o runtime do ADF.

### <a name="middle-tier"></a>Camada intermediária

A camada intermediária contém o servidor lógico e o servidor de lote. Neste caso, os dois servidores estão instalados na mesma máquina virtual. No entanto, para cenários de produção, é recomendável implantar o servidor lógico e o servidor de lote em servidores separados. Vários servidores são implantados na camada intermediária em duas zonas de disponibilidade para maior disponibilidade. Um balanceador de carga do Azure deve ser criado e esses servidores devem ser colocados em seu pool de back-end para garantir que ambos os servidores estejam ativos e processando solicitações.

Os servidores na camada intermediária recebem solicitações apenas dos servidores na camada de apresentação e do balanceador de carga público. Regras de grupo de segurança de rede devem ser configuradas para negar o tráfego de qualquer endereço diferente da sub-rede da camada de apresentação e do balanceador de carga. Uma regra de NSG também pode ser configurada para permitir o tráfego na porta 22 do bastion host para fins de gerenciamento. Talvez você possa usar o balanceador de carga público para balancear a carga das solicitações entre as VMs na camada intermediária.

Estes dois componentes estão na camada intermediária:

- **Servidor lógico** – contém a lógica de negócios ou as funções de negócios.
- **Servidor de lote** – usado para processamento em lotes

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

O conjunto de aplicativos PeopleSoft da Oracle contém programas de software de gerenciamento de recursos humanos e finanças. O conjunto de aplicativos tem várias camadas e os aplicativos incluem HRMS (sistemas de gerenciamento de recursos humanos), CRM (gerenciamento de relacionamento com o cliente), FSCM (gerenciamento da cadeia de fornecedores e finanças) e EPM (gerenciamento de desempenho empresarial).

É recomendável que cada camada do conjunto de software seja implantada na própria sub-rede. Um banco de dados Oracle ou Microsoft SQL Server é necessário como banco de dados de back-end do aplicativo. Esta seção aborda detalhes sobre como implantar o PeopleSoft com um back-end de banco de dados Oracle.

Veja a seguir uma arquitetura canônica para implantar o conjunto de aplicativos PeopleSoft em uma arquitetura entre nuvens (Figura 5).

![Arquitetura entre nuvens do PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Arquitetura entre nuvens do PeopleSoft*

Nessa arquitetura de exemplo, a rede virtual no Azure está conectada à rede de nuvem virtual na OCI usando a interconexão entre nuvens. A camada de aplicativo está configurada no Azure, enquanto o banco de dados está configurado na OCI. É recomendável implantar cada componente na própria sub-rede com grupos de segurança de rede para permitir o tráfego somente de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure, com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O seguinte diagrama (Figura 6) é um exemplo desse padrão de arquitetura:

![Arquitetura do PeopleSoft somente no Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Arquitetura do PeopleSoft somente no Azure*

As seções a seguir descrevem os diferentes componentes em alto nível.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Camada de aplicativo

A camada de aplicativo contém instâncias dos servidores de aplicativos do PeopleSoft, dos servidores Web do PeopleSoft, da pesquisa elástica e do PeopleSoft Process Scheduler. Um balanceador de carga do Azure está configurado para aceitar solicitações de usuários que são roteadas para o servidor apropriado na camada de aplicativo.

Para alta disponibilidade, considere configurar instâncias redundantes de cada servidor na camada de aplicativo em diferentes zonas de disponibilidade. O balanceador de carga do Azure pode ser configurado com vários pools de back-end para direcionar cada solicitação ao servidor certo.

### <a name="peopletools-client"></a>PeopleTools Client

O PeopleTools Client é usado para executar atividades de administração, como desenvolvimento, migração e atualização. Como o PeopleTools Client não é necessário para atingir alta disponibilidade do aplicativo, não são necessários servidores redundantes dele.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Próximas etapas

Use [scripts Terraform](https://github.com/microsoft/azure-oracle) para configurar aplicativos Oracle no Azure e estabelecer conectividade entre nuvens com a OCI.

Para saber mais e obter white papers sobre o OCI, confira a documentação do [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm).
