---
title: Arquitetura de conectividade
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a arquitetura de comunicação e conectividade do Azure SQL Instância Gerenciada, além de como os componentes direcionam o tráfego para uma instância gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: 58563629b30e7be764732a9810162e1a0b1931e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725829"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Arquitetura de conectividade de uma Instância Gerenciada de SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica a comunicação no Azure SQL Instância Gerenciada. Ele também descreve a arquitetura de conectividade e como os componentes direcionam o tráfego para uma instância gerenciada.  

O SQL Instância Gerenciada é colocado dentro da rede virtual do Azure e a sub-rede dedicada a instâncias gerenciadas. Essa implantação fornece:

- Um endereço IP privado seguro.
- A capacidade de conectar uma rede local ao SQL Instância Gerenciada.
- A capacidade de conectar o SQL Instância Gerenciada a um servidor vinculado ou a outro armazenamento de dados local.
- A capacidade de conectar o SQL Instância Gerenciada aos recursos do Azure.

## <a name="communication-overview"></a>Visão geral da comunicação

O diagrama a seguir mostra as entidades que se conectam ao SQL Instância Gerenciada. Ele também mostra os recursos que precisam se comunicar com uma instância gerenciada. O processo de comunicação na parte inferior do diagrama representa aplicativos de cliente e ferramentas que se conectam ao SQL Instância Gerenciada como fontes de dados.  

![Entidades na arquitetura de conectividade](./media/connectivity-architecture-overview/connectivityarch001.png)

O SQL Instância Gerenciada é uma oferta de PaaS (plataforma como serviço). O Azure usa agentes automatizados (gerenciamento, implantação e manutenção) para gerenciar esse serviço com base em fluxos de dados de telemetria. Como o Azure é responsável pelo gerenciamento, os clientes não podem acessar as máquinas de cluster virtual do SQL Instância Gerenciada por meio de protocolo RDP (RDP).

Algumas operações iniciadas por usuários finais ou aplicativos podem exigir que o SQL Instância Gerenciada interaja com a plataforma. Um caso é a criação de um banco de dados do SQL Instância Gerenciada. Esse recurso é exposto por meio do portal do Azure, do PowerShell, do CLI do Azure e da API REST.

O SQL Instância Gerenciada depende dos serviços do Azure, como o armazenamento do Azure para backups, hubs de eventos do Azure para telemetria, Azure Active Directory (Azure AD) para autenticação, Azure Key Vault para Transparent Data Encryption (TDE) e alguns serviços da plataforma Azure que fornecem recursos de segurança e suporte. O SQL Instância Gerenciada faz conexões com esses serviços.

Todas as comunicações são criptografadas e assinadas usando certificados. Para verificar a confiabilidade das partes de comunicação, o SQL Instância Gerenciada verifica constantemente esses certificados por meio de listas de certificados revogados. Se os certificados forem revogados, o SQL Instância Gerenciada fechará as conexões para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Em um alto nível, o SQL Instância Gerenciada é um conjunto de componentes de serviço. Esses componentes são hospedados em um conjunto dedicado de máquinas virtuais isoladas que são executadas dentro da sub-rede da rede virtual do cliente. Esses computadores formam um cluster virtual.

Um cluster virtual pode hospedar várias instâncias gerenciadas. Se necessário, o cluster expande automaticamente ou contrai quando o cliente altera o número de instâncias provisionadas na sub-rede.

Os aplicativos do cliente podem se conectar ao SQL Instância Gerenciada e podem consultar e atualizar bancos de dados dentro da rede virtual, rede virtual emparelhada ou rede conectada por VPN ou Azure ExpressRoute. Essa rede deve usar um ponto de extremidade e um endereço IP privado.  

![Diagrama de arquitetura de conectividade](./media/connectivity-architecture-overview/connectivityarch002.png)

Os serviços de gerenciamento e implantação do Azure são executados fora da rede virtual. O SQL Instância Gerenciada e os serviços do Azure se conectam nos pontos de extremidade que têm endereços IP públicos. Quando o SQL Instância Gerenciada cria uma conexão de saída, a NAT (conversão de endereços de rede final) de recebimento faz com que a conexão pareça ser proveniente desse endereço IP público.

O tráfego de gerenciamento flui pela rede virtual do cliente. Isso significa que os elementos da infraestrutura da rede virtual podem prejudicar o tráfego de gerenciamento, fazendo com que a instância falhe e se torne indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, o Azure aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual do Azure. A política pode afetar o funcionamento do SQL Instância Gerenciada. Esse mecanismo de plataforma comunica de forma transparente os requisitos de rede para os usuários. A principal meta da política é impedir a configuração incorreta da rede e garantir as operações normais de Instância Gerenciada do SQL. Quando você exclui uma instância gerenciada, a política de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do cluster virtual

Vamos aprofundar-se na arquitetura de conectividade para o SQL Instância Gerenciada. O diagrama a seguir mostra o layout conceitual do cluster virtual.

![Arquitetura de conectividade do cluster virtual](./media/connectivity-architecture-overview/connectivityarch003.png)

Os clientes se conectam ao SQL Instância Gerenciada usando um nome de host que tem o formulário `<mi_name>.<dns_zone>.database.windows.net` . Esse nome de host é resolvido para um endereço IP privado, embora esteja registrado em uma zona DNS (sistema de nomes de domínio) público e possa ser resolvido publicamente. O `zone-id` é gerado automaticamente quando você cria o cluster. Se um cluster recém-criado hospedar uma instância gerenciada secundária, ele compartilhará sua ID de zona com o cluster primário. Para obter mais informações, consulte [usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Esse endereço IP privado pertence ao balanceador de carga interno para o SQL Instância Gerenciada. O balanceador de carga direciona o tráfego para o gateway do SQL Instância Gerenciada. Como várias instâncias gerenciadas podem ser executadas dentro do mesmo cluster, o gateway usa o nome de host do SQL Instância Gerenciada para redirecionar o tráfego para o serviço de mecanismo do SQL correto.

Os serviços de gerenciamento e implantação se conectam ao SQL Instância Gerenciada usando um [ponto de extremidade de gerenciamento](#management-endpoint) que é mapeado para um balanceador de carga externo. O tráfego será roteado para os nós somente se ele for recebido em um conjunto predefinido de portas que apenas os componentes de gerenciamento do SQL Instância Gerenciada usam. Um firewall interno nos nós é configurado para permitir o tráfego somente de intervalos de IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre os componentes de gerenciamento e o plano de gerenciamento.

## <a name="management-endpoint"></a>Ponto de extremidade de gerenciamento

O Azure gerencia o SQL Instância Gerenciada usando um ponto de extremidade de gerenciamento. Esse ponto de extremidade está dentro do cluster virtual de uma instância. O ponto de extremidade de gerenciamento é protegido por um firewall interno no nível da rede. No nível do aplicativo, ele é protegido pela verificação de certificado mútuo. Para localizar o endereço IP do ponto de extremidade, consulte [determinar o endereço IP do ponto de extremidade de gerenciamento](management-endpoint-find-ip-address.md).

Quando as conexões começam dentro do SQL Instância Gerenciada (como acontece com backups e logs de auditoria), o tráfego parece iniciar no endereço IP público do ponto de extremidade de gerenciamento. Você pode limitar o acesso a serviços públicos do SQL Instância Gerenciada definindo as regras de firewall para permitir apenas o endereço IP do SQL Instância Gerenciada. Para obter mais informações, consulte [verificar o firewall interno do SQL instância gerenciada](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> O tráfego que vai para os serviços do Azure que estão dentro da região do SQL Instância Gerenciada é otimizado e, por esse motivo, não é NATe ao endereço IP público para o ponto de extremidade de gerenciamento. Por esse motivo, se você precisar usar regras de firewall baseadas em IP, mais comumente para armazenamento, o serviço precisa estar em uma região diferente do SQL Instância Gerenciada.

## <a name="service-aided-subnet-configuration"></a>Configuração de sub-rede auxiliada por serviço

Para atender aos requisitos de segurança e gerenciamento do cliente, o SQL Instância Gerenciada está em transição de manual para configuração de sub-rede auxiliada por serviço.

Com a configuração de sub-rede auxiliada por serviço, o usuário está no controle total do tráfego de dados (TDS), enquanto o SQL Instância Gerenciada é responsável por garantir o fluxo ininterrupto do tráfego de gerenciamento para atender a um SLA.

A configuração de sub-rede auxiliada por serviço se baseia no recurso de [delegação de sub-rede](../../virtual-network/subnet-delegation-overview.md) de rede virtual para fornecer gerenciamento automático de configuração de rede e habilitar pontos de extremidade de serviço. 

Os pontos de extremidade de serviço podem ser usados para configurar regras de firewall de rede virtual em contas de armazenamento que mantêm backups e logs de auditoria. Mesmo com os pontos de extremidade de serviço habilitados, os clientes são incentivados a usar o [link privado](../../private-link/private-link-overview.md) que fornece segurança adicional sobre os pontos de extremidade de serviço.

> [!IMPORTANT]
> Devido às especificidades de configuração do plano de controle, a configuração de sub-rede auxiliada por serviço não habilitará pontos de extremidade de serviço em nuvens nacionais. 

### <a name="network-requirements"></a>Requisitos de rede

Implante o SQL Instância Gerenciada em uma sub-rede dedicada dentro da rede virtual. A sub-rede deve ter estas características:

- **Sub-rede dedicada:** A sub-rede do SQL Instância Gerenciada não pode conter nenhum outro serviço de nuvem associado a ela e não pode ser uma sub-rede de gateway. A sub-rede não pode conter nenhum recurso, mas o SQL Instância Gerenciada, e você não pode adicionar outros tipos de recursos na sub-rede posteriormente.
- **Delegação de sub-rede:** A sub-rede do SQL Instância Gerenciada precisa ser delegada ao `Microsoft.Sql/managedInstances` provedor de recursos.
- **NSG (grupo de segurança de rede):** Um NSG precisa ser associado à sub-rede do SQL Instância Gerenciada. Você pode usar um NSG para controlar o acesso ao ponto de extremidade de dados do SQL Instância Gerenciada filtrando o tráfego na porta 1433 e as portas 11000-11999 quando o SQL Instância Gerenciada estiver configurado para conexões de redirecionamento. O serviço provisionará e manterá automaticamente [as regras](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) atuais necessárias para permitir o fluxo ininterrupto do tráfego de gerenciamento.
- **Tabela de rota definida pelo usuário (UDR):** Uma tabela UDR precisa ser associada à sub-rede do SQL Instância Gerenciada. Você pode adicionar entradas à tabela de rotas para rotear o tráfego que tem intervalos IP privados locais como um destino por meio do gateway de rede virtual ou do NVA (dispositivo de rede virtual). O serviço provisionará e manterá automaticamente as [entradas](#user-defined-routes-with-service-aided-subnet-configuration) atuais necessárias para permitir o fluxo ininterrupto do tráfego de gerenciamento.
- **Endereços IP suficientes:** A sub-rede do SQL Instância Gerenciada deve ter pelo menos 32 endereços IP. Para obter mais informações, consulte [determinar o tamanho da sub-rede para o SQL instância gerenciada](vnet-subnet-determine-size.md). Você pode implantar instâncias gerenciadas na [rede existente](vnet-existing-add-subnet.md) depois de configurá-las para atender aos [requisitos de rede do SQL instância gerenciada](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Quando você cria uma instância gerenciada, uma política de intenção de rede é aplicada na sub-rede para evitar alterações não compatíveis na configuração de rede. Depois que a última instância for removida da sub-rede, a política de intenção de rede também será removida. As regras a seguir são apenas para fins informativos e você não deve implantá-las usando o modelo ARM/PowerShell/CLI. Se você quiser usar o modelo oficial mais recente, sempre poderá [recuperá-lo no portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança de entrada obrigatórias com configuração de sub-rede auxiliada pelo serviço

| Nome       |Porta                        |Protocolo|Fonte           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |SUB-REDE DA MI  |Allow |
|            |9000, 9003                  |TCP     |CorpnetSaw       |SUB-REDE DA MI  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |SUB-REDE DA MI  |Allow |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE DA MI  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança de saída obrigatórias com configuração de sub-rede auxiliada por serviço

| Nome       |Porta          |Protocolo|Fonte           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gerenciamento  |443, 12000    |TCP     |SUB-REDE DA MI        |AzureCloud |Allow |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Rotas definidas pelo usuário com configuração de sub-rede auxiliada por serviço

|Nome|Prefixo de endereço|Próximo salto|
|----|--------------|-------|
|sub-rede para vnetlocal|SUB-REDE DA MI|Rede virtual|
|Mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|Mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|Mi-20-33-16-nexthop-Internet|20.33.0.0/16|Internet|
|Mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|Mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|Mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|Mi-20-48-12-nexthop-Internet|20.48.0.0/12|Internet|
|Mi-20-64-10-nexthop-Internet|20.64.0.0/10|Internet|
|Mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|Mi-20-135-16-nexthop-Internet|20.135.0.0/16|Internet|
|Mi-20-136-16-nexthop-Internet|20.136.0.0/16|Internet|
|Mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|Mi-20-143-16-nexthop-Internet|20.143.0.0/16|Internet|
|Mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|Mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|Mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|Mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|Mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|Mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|Mi-20-192-10-nexthop-Internet|20.192.0.0/10|Internet|
|Mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|Mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|Mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|Mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|Mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|Mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|Mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|Mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|Mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
|Mi-51-132-16-nexthop-Internet|51.132.0.0/16|Internet|
|Mi-51-136-15-nexthop-Internet|51.136.0.0/15|Internet|
|Mi-51-138-16-nexthop-Internet|51.138.0.0/16|Internet|
|Mi-51-140-14-nexthop-Internet|51.140.0.0/14|Internet|
|Mi-51-144-15-nexthop-Internet|51.144.0.0/15|Internet|
|Mi-52-96-12-nexthop-Internet|52.96.0.0/12|Internet|
|Mi-52-112-14-nexthop-Internet|52.112.0.0/14|Internet|
|Mi-52-125-16-nexthop-Internet|52.125.0.0/16|Internet|
|Mi-52-126-15-nexthop-Internet|52.126.0.0/15|Internet|
|Mi-52-130-15-nexthop-Internet|52.130.0.0/15|Internet|
|Mi-52-132-14-nexthop-Internet|52.132.0.0/14|Internet|
|Mi-52-136-13-nexthop-Internet|52.136.0.0/13|Internet|
|Mi-52-145-16-nexthop-Internet|52.145.0.0/16|Internet|
|Mi-52-146-15-nexthop-Internet|52.146.0.0/15|Internet|
|Mi-52-148-14-nexthop-Internet|52.148.0.0/14|Internet|
|Mi-52-152-13-nexthop-Internet|52.152.0.0/13|Internet|
|Mi-52-160-11-nexthop-Internet|52.160.0.0/11|Internet|
|Mi-52-224-11-nexthop-Internet|52.224.0.0/11|Internet|
|Mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|Mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|Mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|Mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|Mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|Mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|Mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|Mi-131-107-16-nexthop-Internet|131.107.0.0/16|Internet|
|Mi-131-253-1-24-nexthop-Internet|131.253.1.0/24|Internet|
|Mi-131-253-3-24-nexthop-Internet|131.253.3.0/24|Internet|
|Mi-131-253-5-24-nexthop-Internet|131.253.5.0/24|Internet|
|Mi-131-253-6-24-nexthop-Internet|131.253.6.0/24|Internet|
|Mi-131-253-8-24-nexthop-Internet|131.253.8.0/24|Internet|
|Mi-131-253-12-22-nexthop-Internet|131.253.12.0/22|Internet|
|Mi-131-253-16-23-nexthop-Internet|131.253.16.0/23|Internet|
|Mi-131-253-18-24-nexthop-Internet|131.253.18.0/24|Internet|
|Mi-131-253-21-24-nexthop-Internet|131.253.21.0/24|Internet|
|Mi-131-253-22-23-nexthop-Internet|131.253.22.0/23|Internet|
|Mi-131-253-24-21-nexthop-Internet|131.253.24.0/21|Internet|
|Mi-131-253-32-20-nexthop-Internet|131.253.32.0/20|Internet|
|Mi-131-253-61-24-nexthop-Internet|131.253.61.0/24|Internet|
|Mi-131-253-62-23-nexthop-Internet|131.253.62.0/23|Internet|
|Mi-131-253-64-18-nexthop-Internet|131.253.64.0/18|Internet|
|Mi-131-253-128-17-nexthop-Internet|131.253.128.0/17|Internet|
|Mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|Mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|Mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|Mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|Mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|Mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|Mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|Mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|Mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|Mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|Mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|Mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|Mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|Mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|Mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|Mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|Mi-167-105-16-nexthop-Internet|167.105.0.0/16|Internet|
|Mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|Mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|Mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|Mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|Mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|Mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|Mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|Mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|Mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|Mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|Mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|Mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|Mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|Mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|Mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|Mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|Mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|Mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|Mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|Mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|Mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|Mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|Mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|Mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|Mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|Mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|Mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|Mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|Mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|Mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|Mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|Mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|Mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|Mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|Mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|Mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|Mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|Mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|Mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|Mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|Mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|Mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|Mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
|Mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|Mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|Mi-51-13-17-nexthop-Internet|51.13.0.0/17|Internet|
|Mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|Mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|Mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|Mi-51-120-128-17-nexthop-Internet|51.120.128.0/17|Internet|
|Mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|Mi-102-37-18-nexthop-Internet|102.37.0.0/18|Internet|
|Mi-102-133-16-nexthop-Internet|102.133.0.0/16|Internet|
|Mi-199-30-16-20-nexthop-Internet|199.30.16.0/20|Internet|
|Mi-204-79-180-24-nexthop-Internet|204.79.180.0/24|Internet|
||||

\* A sub-rede MI refere-se ao intervalo de endereços IP para a sub-rede no formato x.x.x. x/y. Você pode encontrar essas informações na portal do Azure, em Propriedades da sub-rede.

\** Se o endereço de destino for para um dos serviços do Azure, o Azure roteia o tráfego diretamente para o serviço pela rede de backbone do Azure, em vez de rotear o tráfego para a Internet. O tráfego entre os serviços do Azure não percorre a Internet, independentemente de em qual região do Azure a rede virtual existe ou em qual região do Azure uma instância do serviço do Azure está implantada. Para obter mais detalhes, consulte a [página de documentação do UDR](../../virtual-network/virtual-networks-udr-overview.md).

Além disso, você pode adicionar entradas à tabela de rotas para rotear o tráfego que tem intervalos IP privados locais como um destino por meio do gateway de rede virtual ou NVA (dispositivo de rede virtual).

Se a rede virtual incluir um DNS personalizado, o servidor DNS personalizado deverá ser capaz de resolver os registros DNS públicos. O uso de recursos adicionais, como a autenticação do Azure AD, pode exigir a resolução de FQDNs adicionais. Para obter mais informações, consulte [configurar um DNS personalizado](custom-dns-configure.md).

### <a name="networking-constraints"></a>Restrições de rede

O **tls 1,2 é imposto em conexões de saída**: em Janeiro 2020 Microsoft imposto TLS 1,2 para tráfego dentro de serviço em todos os serviços do Azure. Para o Azure SQL Instância Gerenciada, isso resultou no TLS 1,2 sendo imposto em conexões de saída usadas para replicação e conexões de servidor vinculado a SQL Server. Se você estiver usando versões do SQL Server mais antigas do que 2016 com o SQL Instância Gerenciada, verifique se [as atualizações específicas do TLS 1,2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) foram aplicadas.

Atualmente, *não há suporte para* os seguintes recursos de rede virtual com o SQL instância gerenciada:

- **Emparelhamento da Microsoft**: habilitar o [emparelhamento da Microsoft](../../expressroute/expressroute-faqs.md#microsoft-peering) em circuitos de ExpressRoute emparelhados direta ou transitivamente com uma rede virtual em que o SQL instância gerenciada reside afeta o fluxo de tráfego entre os componentes do SQL instância gerenciada dentro da rede virtual e dos serviços dos quais depende, causando problemas de disponibilidade. As implantações do SQL Instância Gerenciada na rede virtual com o emparelhamento da Microsoft já habilitado devem falhar.
- **Emparelhamento de rede virtual global**: a conectividade de [emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md) entre regiões do Azure não funciona para instâncias gerenciadas do SQL colocadas em sub-redes criadas antes de 9/22/2020.
- **AzurePlatformDNS**: usar a [marca de serviço](../../virtual-network/service-tags-overview.md) AzurePlatformDNS para bloquear a resolução de DNS da plataforma renderizaria o SQL instância gerenciada não disponível. Embora o SQL Instância Gerenciada dê suporte ao DNS definido pelo cliente para a resolução DNS dentro do mecanismo, há uma dependência no DNS da plataforma para operações de plataforma.
- **Gateway NAT**: usar o [NAT da rede virtual do Azure](../../virtual-network/nat-overview.md) para controlar a conectividade de saída com um endereço IP público específico renderizaria o SQL instância gerenciada indisponível. No momento, o serviço SQL Instância Gerenciada está limitado ao uso do balanceador de carga básico que não fornece a coexistência de fluxos de entrada e saída com NAT de rede virtual.
- **IPv6 para rede virtual do Azure**: espera-se que a implantação de SQL instância gerenciada em [redes virtuais IPv4/IPv6 de pilha dupla](../../virtual-network/ipv6-overview.md) falhe. Associar o grupo de segurança de rede (NSG) ou a tabela de rotas (UDR) que contém prefixos de endereço IPv6 à sub-rede do SQL Instância Gerenciada ou adicionar prefixos de endereço IPv6 a NSG ou UDR que já está associado à sub-rede da instância gerenciada, renderizaria o SQL Instância Gerenciada indisponível. Espera-se que as implantações do SQL Instância Gerenciada em uma sub-rede com NSG e UDR que já tenham prefixos IPv6 falhem.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [o que é o Azure SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
- Saiba como [Configurar uma nova rede virtual do Azure](virtual-network-subnet-create-arm-template.md) ou uma [rede virtual do Azure existente](vnet-existing-add-subnet.md) onde você pode implantar o SQL instância gerenciada.
- [Calcule o tamanho da sub-rede](vnet-subnet-determine-size.md) em que você deseja implantar o SQL instância gerenciada.
- Saiba como criar uma instância gerenciada:
  - Do [portal do Azure](instance-create-quickstart.md).
  - Usando o [PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Usando [um modelo de Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [um modelo de Azure Resource Manager (usando Jumpbox, com o SSMS incluído)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).