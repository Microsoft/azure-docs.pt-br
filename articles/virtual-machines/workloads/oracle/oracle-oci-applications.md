---
title: Arquiteturas para implantar aplicativos Oracle em máquinas virtuais do Azure | Microsoft Docs
description: Arquiteturas de aplicativos para implantar aplicativos Oracle, incluindo o E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft em Microsoft Azure máquinas virtuais com bancos de dados no Azure ou no OCI (Oracle Cloud Infrastructure).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: ca16dceae9ab1afab17b2893b61f6a1c3309cf93
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361577"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arquiteturas para implantar aplicativos Oracle no Azure

A Microsoft e a Oracle trabalharam em conjunto para permitir que os clientes implantem aplicativos Oracle, como Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft na nuvem. Com a introdução da interconectividade de [rede privada](configure-azure-oci-networking.md) de visualização entre o Microsoft Azure e o Oracle Cloud Infrastructure (OCI), os aplicativos Oracle agora podem ser implantados no Azure com seus bancos de dados back-end no Azure ou no OCI. Os aplicativos Oracle também podem ser integrados com o Azure Active Directory, permitindo que você configure o logon único para que os usuários possam entrar no aplicativo Oracle usando suas credenciais do Azure Active Directory (AD do Azure).

O OCI oferece várias opções de banco de dados Oracle para aplicativos Oracle, incluindo DBaaS, serviço de nuvem Exadata, Oracle RAC e infraestrutura como serviço (IaaS). Atualmente, o banco de dados autônomo não é um back-end com suporte para aplicativos Oracle.

Há [várias opções](oracle-overview.md) para implantar aplicativos Oracle no Azure, incluindo de maneira altamente disponível e segura. O Azure também oferece [imagens de VM de banco de dados Oracle](oracle-vm-solutions.md) que você pode implantar se optar por executar aplicativos Oracle totalmente no Azure.

As seções a seguir descrevem as recomendações de arquitetura da Microsoft e da Oracle para implantar o Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft em uma configuração de nuvem cruzada ou inteiramente no Azure. A Microsoft e a Oracle testaram esses aplicativos e confirmaram que o desempenho atende aos padrões definidos pela Oracle para esses aplicativos.

## <a name="architecture-considerations"></a>Considerações sobre arquitetura

Os aplicativos Oracle são compostos por vários serviços, que podem ser hospedados na mesma ou em várias máquinas virtuais no Azure e, opcionalmente, no OCI. 

As instâncias do aplicativo podem ser configuradas com pontos de extremidade públicos ou privados. A Microsoft e a Oracle recomendam a configuração de uma *VM host bastião* com um endereço IP público em uma sub-rede separada para o gerenciamento do aplicativo. Em seguida, atribua apenas endereços IP privados a outros computadores, incluindo a camada de banco de dados. 

Ao configurar um aplicativo em uma arquitetura de nuvem cruzada, o planejamento é necessário para garantir que o espaço de endereço IP na rede virtual do Azure não se sobreponha ao espaço de endereço IP privado na rede de nuvem virtual de OCI.

Para maior segurança, configure grupos de segurança de rede em um nível de sub-rede para garantir que apenas o tráfego em portas específicas e endereços IP seja permitido. Por exemplo, os computadores na camada intermediária só devem receber tráfego de dentro da rede virtual. Nenhum tráfego externo deve alcançar os computadores da camada intermediária diretamente.

Para alta disponibilidade, você pode configurar instâncias redundantes dos diferentes servidores no mesmo conjunto de disponibilidade ou zonas de disponibilidade diferentes. As zonas de disponibilidade permitem que você alcance um SLA de tempo de atividade de 99,99%, enquanto os conjuntos de disponibilidade permitem que você obtenha um SLA de tempo de atividade de 99,95% em região. As arquiteturas de exemplo mostradas neste artigo são implantadas em duas zonas de disponibilidade.

Ao implantar um aplicativo usando a interconexão entre nuvem, você pode continuar usando um circuito de ExpressRoute existente para conectar seu ambiente do Azure à sua rede local. No entanto, você precisa de um circuito de ExpressRoute separado para a interconexão de OCI do que a que se conecta à sua rede local.

## <a name="e-business-suite"></a>E-Business Suite

O Oracle E-Business Suite (EBS) é um pacote de aplicativos, incluindo SCM (gerenciamento de cadeia de fornecedores) e CRM (gerenciamento de relacionamento com o cliente). Para aproveitar o portfólio de banco de dados gerenciado do OCI, o EBS pode ser implantado usando a interconexão entre nuvem entre o Microsoft Azure e o OCI. Nessa configuração, as camadas de apresentação e de aplicativo são executadas no Azure e na camada de banco de dados no OCI, conforme ilustrado no diagrama de arquitetura a seguir (Figura 1).

![Arquitetura entre nuvens do E-Business Suite](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: Arquitetura entre nuvens do E-Business Suite* 

Nessa arquitetura, a rede virtual no Azure é conectada a uma rede de nuvem virtual no OCI usando a interconexão entre nuvem. A camada de aplicativo é configurada no Azure, enquanto o banco de dados é configurado no OCI. É recomendável implantar cada componente em sua própria sub-rede com grupos de segurança de rede para permitir o tráfego somente de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O diagrama a seguir (Figura 2) é um exemplo desse padrão de arquitetura:

![Arquitetura do E-Business Suite somente Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: Arquitetura do E-Business Suite somente Azure*

As seções a seguir descrevem os diferentes componentes em um alto nível.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Camada do aplicativo (intermediária)

A camada de aplicativo é isolada em sua própria sub-rede. Há várias máquinas virtuais configuradas para tolerância a falhas e gerenciamento fácil de patches. Essas VMs podem ser apoiadas pelo armazenamento compartilhado, que é oferecido pelo Azure NetApp Files e pelo ultra SSDs. Essa configuração permite uma implantação mais fácil de patches sem tempo de inatividade. Os computadores na camada de aplicativo devem ser frontais por um balanceador de carga público, de modo que as solicitações para a camada de aplicativo EBS sejam processadas mesmo que um computador na camada esteja offline devido a uma falha.

### <a name="load-balancer"></a>Balanceador de carga

Um balanceador de carga do Azure permite distribuir o tráfego entre várias instâncias de sua carga de trabalho para garantir a alta disponibilidade. Nesse caso, um balanceador de carga público é configurado, pois os usuários têm permissão para acessar o aplicativo EBS pela Web. O balanceador de carga distribui a carga para ambos os computadores na camada intermediária. Para maior segurança, permita o tráfego somente de usuários que acessam o sistema de sua rede corporativa usando uma VPN site a site ou os grupos de segurança de rede e de ExpressRoute.

### <a name="database-tier"></a>Camada de banco de dados

Essa camada hospeda o banco de dados Oracle e é separada em sua própria sub-rede. É recomendável adicionar grupos de segurança de rede que permitam somente o tráfego da camada de aplicativo para a camada de banco de dados na porta de banco de dados específica da Oracle 1521.

A Microsoft e a Oracle recomendam uma configuração de alta disponibilidade. A alta disponibilidade no Azure pode ser obtida Configurando dois bancos de dados Oracle em duas zonas de disponibilidade com o Oracle Data Guard ou usando Oracle Database serviço de nuvem Exadata no OCI. Ao usar Oracle Database serviço de nuvem Exadata, seu banco de dados é implantado em duas sub-redes. Você também pode configurar Oracle Database em VMs no OCI em dois domínios de disponibilidade com o Oracle Data Guard.


### <a name="identity-tier"></a>Camada de identidade

A camada de identidade contém a VM de confirmador EBS. O EBS Asserter permite que você sincronize identidades do IDCS (serviço de nuvem de identidade) da Oracle e do Azure AD. O declarador EBS é necessário porque o EBS não oferece suporte a protocolos de logon único como SAML 2,0 ou OpenID Connect. O declarador EBS consome o token do OpenID Connect (gerado por IDCS), valida-o e, em seguida, cria uma sessão para o usuário no EBS. 

Embora essa arquitetura mostre a integração do IDCS, o acesso unificado do Azure AD e o logon único também podem ser habilitados com o Oracle Access Manager com o Oracle Internet Directory ou o Oracle Unified Directory. Para obter mais informações, consulte os White papers sobre [implantação do Oracle EBS com a integração do IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) ou [implantação do Oracle EBS com integração com o OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Para alta disponibilidade, é recomendável que você implante servidores redundantes do Declarador do EBS em várias zonas de disponibilidade com um balanceador de carga na frente dele.

Depois que sua infraestrutura for configurada, o E-Business Suite poderá ser instalado seguindo o guia de instalação fornecido pela Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

O JD Edwards EnterpriseOne da Oracle é um pacote de aplicativos integrado de software de planejamento de recursos empresariais abrangente. É um aplicativo de várias camadas que pode ser configurado com um back-end de banco de dados Oracle ou SQL Server. Esta seção aborda detalhes sobre como implantar o JD Edwards EnterpriseOne com um back-end de banco de dados Oracle no OCI ou no Azure.

Na arquitetura recomendada a seguir (Figura 3), a administração, a apresentação e as camadas intermediárias são implantadas na rede virtual no Azure. O banco de dados é implantado em uma rede de nuvem virtual no OCI.

Assim como acontece com o E-Business Suite, você pode configurar uma camada de bastiões opcional para fins administrativos seguros. Use o host da VM de bastiões como um servidor de salto para acessar as instâncias de aplicativo e banco de dados.

![Arquitetura entre nuvens do JD Edwards EnterpriseOne](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: Arquitetura entre nuvens do JD Edwards EnterpriseOne*

Nessa arquitetura, a rede virtual no Azure é conectada à rede de nuvem virtual em OCI usando a interconexão entre nuvem. A camada de aplicativo é configurada no Azure, enquanto o banco de dados é configurado no OCI. É recomendável implantar cada componente em sua própria sub-rede com grupos de segurança de rede para permitir o tráfego somente de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O diagrama a seguir (Figura 4) é um exemplo desse padrão de arquitetura:

![JD Edwards EnterpriseOne somente arquitetura do Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: JD Edwards EnterpriseOne somente arquitetura do Azure*

As seções a seguir descrevem os diferentes componentes em um alto nível.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Camada administrativa

Como o nome sugere, essa camada é usada para tarefas administrativas. Você pode enfileirar uma sub-rede separada para a camada administrativa. Os serviços e servidores nessa camada são usados principalmente para a instalação e a administração do aplicativo. Portanto, instâncias únicas desses servidores são suficientes. Instâncias redundantes não são necessárias para a alta disponibilidade do seu aplicativo.

Os componentes dessa camada são os seguintes:
    
 - **Servidor de provisionamento** -esse servidor é usado para implantação de ponta a ponta dos diferentes componentes do aplicativo. Ele se comunica com as instâncias nas outras camadas, incluindo as instâncias na camada de banco de dados, na porta 22. Ele hospeda o console Gerenciador do Servidor para JD Edwards EnterpriseOne.
 - **Servidor de implantação** -este servidor é necessário principalmente para a instalação do JD Edwards EnterpriseOne. Durante o processo de instalação, esse servidor atua como o repositório central para os arquivos necessários e os pacotes de instalação. O software é distribuído ou implantado em outros servidores e clientes deste servidor.
 - **Cliente de desenvolvimento** – este servidor contém componentes que são executados em um navegador da Web, bem como aplicativos nativos.

### <a name="presentation-tier"></a>Camada de apresentação

Essa camada contém vários componentes, como o AIS (serviços de interface de aplicativo), o ADF (estrutura de desenvolvimento de aplicativos) e os JAS (servidores de aplicativos Java). Os servidores nessa camada se comunicam com os servidores na camada intermediária. Eles são frontais por um balanceador de carga que roteia o tráfego para o servidor necessário com base no número da porta e na URL em que o tráfego é recebido. É recomendável que você implante várias instâncias de cada tipo de servidor para alta disponibilidade.

Estes são os componentes desta camada:
    
- **AIS (Application interface Services)** – o servidor AIS fornece a interface de comunicação entre JD Edwards EnterpriseOne Mobile Enterprise Applications e JD Edwards EnterpriseOne.
- **Servidor de aplicativos Java (Jas)** – o Jas recebe solicitações do balanceador de carga e as transmite para a camada intermediária para executar tarefas complicadas. O JAS tem a capacidade de executar uma lógica de negócios simples.
- **Servidor do Publicador de BI (bip)** -este servidor apresenta relatórios com base nos dados coletados pelo aplicativo JD Edwards EnterpriseOne. Você pode criar e controlar como o relatório apresenta os dados com base em modelos diferentes.
- **Servidor de serviços corporativos (BSS)** -o BSS habilita a troca de informações e a interoperabilidade com outros aplicativos Oracle.
- **Servidor de eventos em tempo real (RTE)** – o servidor RTE permite que você configure notificações para sistemas externos sobre transações que ocorrem no sistema JDE EnterpriseOne. Ele usa um modelo de assinante e permite que sistemas de terceiros assinem eventos. Para balancear a carga de solicitações para ambos os servidores RTE, verifique se os servidores estão em um cluster.
- **Servidor de estrutura de desenvolvimento de aplicativos (ADF)** -o servidor ADF é usado para executar JD Edwards EnterpriseOne aplicativos desenvolvidos com o Oracle ADF. Isso é implantado em um Oracle WebLogic Server com tempo de execução do ADF.

### <a name="middle-tier"></a>Camada intermediária

A camada intermediária contém o servidor lógico e o servidor de lote. Nesse caso, ambos os servidores são instalados na mesma máquina virtual. No entanto, para cenários de produção, é recomendável que você implante servidor lógico e servidor de lote em servidores separados. Vários servidores são implantados na camada intermediária em duas zonas de disponibilidade para maior disponibilidade. Um Azure Load Balancer deve ser criado e esses servidores devem ser colocados em seu pool de back-end para garantir que ambos os servidores estejam ativos e processando solicitações.

Os servidores na camada intermediária recebem solicitações dos servidores na camada de apresentação e do balanceador de carga público. As regras do grupo de segurança de rede devem ser configuradas para negar o tráfego de qualquer endereço que não seja a sub-rede da camada de apresentação e o balanceador de carga. Uma regra NSG também pode ser configurada para permitir o tráfego na porta 22 do host bastião para fins de gerenciamento. Talvez você possa usar o balanceador de carga público para balancear a carga de solicitações entre as VMs na camada intermediária.

Os dois componentes a seguir estão na camada intermediária:

- **Servidor lógico** – contêm a lógica de negócios ou as funções de negócios.
- **Servidor de lote** -usado para processamento em lotes

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

O pacote de aplicativos PeopleSoft da Oracle contém software para recursos humanos e gerenciamento financeiro. O pacote de aplicativos é multicamada e os aplicativos incluem sistemas de gerenciamento de recursos humanos (HRMS), gerenciamento de relacionamento com o cliente (CRM), finanças e gerenciamento de cadeia de fornecedores (FSCM) e Enterprise Performance Management (EPM).

É recomendável que cada camada do pacote de software seja implantada em sua própria sub-rede. Um banco de dados Oracle ou Microsoft SQL Server é necessário como o banco de dados de back-end para o aplicativo. Esta seção aborda detalhes sobre como implantar o PeopleSoft com um back-end do banco de dados Oracle.

Veja a seguir uma arquitetura canônica para implantar o pacote de aplicativos PeopleSoft em uma arquitetura de nuvem cruzada (Figura 5).

![Arquitetura de nuvem cruzada da PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Arquitetura de nuvem cruzada da PeopleSoft*

Nesta arquitetura de exemplo, a rede virtual no Azure está conectada à rede de nuvem virtual em OCI usando a interconexão entre nuvem. A camada de aplicativo é configurada no Azure, enquanto o banco de dados é configurado no OCI. É recomendável implantar cada componente em sua própria sub-rede com grupos de segurança de rede para permitir o tráfego somente de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente no Azure com bancos de dados Oracle altamente disponíveis configurados usando o Oracle Data Guard em duas zonas de disponibilidade em uma região. O diagrama a seguir (Figura 6) é um exemplo desse padrão de arquitetura:

![Arquitetura somente do PeopleSoft Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Arquitetura somente do PeopleSoft Azure*

As seções a seguir descrevem os diferentes componentes em um alto nível.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Camada de aplicativo

A camada de aplicativo contém instâncias dos servidores de aplicativos PeopleSoft, servidores Web da PeopleSoft, pesquisa elástica e Agendador de processos do PeopleSoft. Um Azure Load Balancer é configurado para aceitar solicitações de usuários que são roteadas para o servidor apropriado na camada de aplicativo.

Para alta disponibilidade, considere configurar instâncias redundantes de cada servidor na camada de aplicativo em diferentes zonas de disponibilidade. O Azure Load Balancer pode ser configurado com vários pools de back-end para direcionar cada solicitação ao servidor certo.

### <a name="peopletools-client"></a>Cliente PeopleTools

O cliente PeopleTools é usado para executar atividades de administração, como desenvolvimento, migração e atualização. Como o cliente PeopleTools não é necessário para obter alta disponibilidade do seu aplicativo, servidores redundantes do cliente PeopleTools não são necessários.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Próximas etapas

Use [scripts Terraform](https://github.com/microsoft/azure-oracle) para configurar aplicativos Oracle no Azure e estabelecer conectividade entre nuvem com o OCI.

Para obter mais informações e White papers sobre o OCI, consulte a documentação da [nuvem do Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
