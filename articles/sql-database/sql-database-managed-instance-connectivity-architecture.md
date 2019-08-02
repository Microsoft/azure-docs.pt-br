---
title: Arquitetura de conectividade para uma instância gerenciada no banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre a arquitetura de conectividade e comunicação da instância gerenciada do banco de dados SQL do Azure, bem como os componentes direcionam o tráfego para a instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 960320e280a613a537f1918d93e4584a13a0b374
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309970"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Arquitetura de conectividade para uma instância gerenciada no banco de dados SQL do Azure

Este artigo explica a comunicação em uma instância gerenciada do banco de dados SQL do Azure. Ele também descreve a arquitetura de conectividade e como os componentes direcionam o tráfego para a instância gerenciada.  

A instância gerenciada do banco de dados SQL é colocada dentro da rede virtual do Azure e a sub-rede dedicada a instâncias gerenciadas. Essa implantação fornece:

- Um endereço IP privado seguro.
- A capacidade de conectar uma rede local a uma instância gerenciada.
- A capacidade de conectar uma instância gerenciada a um servidor vinculado ou a outro armazenamento de dados local.
- A capacidade de conectar uma instância gerenciada aos recursos do Azure.

## <a name="communication-overview"></a>Visão geral da comunicação

O diagrama a seguir mostra as entidades que se conectam a uma instância gerenciada. Ele também mostra os recursos que precisam se comunicar com a instância gerenciada. O processo de comunicação na parte inferior do diagrama representa aplicativos de cliente e ferramentas que se conectam à instância gerenciada como fontes de dados.  

![Entidades na arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Uma instância gerenciada é uma oferta de PaaS (plataforma como serviço). A Microsoft usa agentes automatizados (gerenciamento, implantação e manutenção) para gerenciar esse serviço com base em fluxos de dados de telemetria. Como a Microsoft é responsável pelo gerenciamento, os clientes não podem acessar as máquinas de cluster virtual de instância gerenciada por meio de protocolo RDP (RDP).

Algumas operações de SQL Server iniciadas pelos usuários finais ou aplicativos podem exigir que instâncias gerenciadas interajam com a plataforma. Um caso é a criação de um banco de dados de instância gerenciada. Esse recurso é exposto por meio do portal do Azure, do PowerShell, do CLI do Azure e da API REST.

As instâncias gerenciadas dependem de serviços do Azure, como o armazenamento do Azure para backups, hubs de eventos do Azure para telemetria, Azure Active Directory para autenticação, Azure Key Vault para Transparent Data Encryption (TDE) e alguns serviços da plataforma Azure que fornecem recursos de segurança e suporte. As instâncias gerenciadas fazem conexões com esses serviços.

Todas as comunicações são criptografadas e assinadas usando certificados. Para verificar a confiabilidade das partes de comunicação, as instâncias gerenciadas verificam constantemente esses certificados por meio de listas de certificados revogados. Se os certificados forem revogados, a instância gerenciada fechará as conexões para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Em um alto nível, uma instância gerenciada é um conjunto de componentes de serviço. Esses componentes são hospedados em um conjunto dedicado de máquinas virtuais isoladas que são executadas dentro da sub-rede da rede virtual do cliente. Esses computadores formam um cluster virtual.

Um cluster virtual pode hospedar várias instâncias gerenciadas. Se necessário, o cluster expande automaticamente ou contrai quando o cliente altera o número de instâncias provisionadas na sub-rede.

Os aplicativos do cliente podem se conectar a instâncias gerenciadas e podem consultar e atualizar bancos de dados dentro da rede virtual, rede virtual emparelhada ou rede conectada por VPN ou Azure ExpressRoute. Essa rede deve usar um ponto de extremidade e um endereço IP privado.  

![Diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Os serviços de implantação e gerenciamento da Microsoft são executados fora da rede virtual. Uma instância gerenciada e os serviços da Microsoft se conectam nos pontos de extremidade que têm endereços IP públicos. Quando uma instância gerenciada cria uma conexão de saída, o NAT (conversão de endereços de rede final) de recebimento faz com que a conexão pareça ser proveniente desse endereço IP público.

O tráfego de gerenciamento flui pela rede virtual do cliente. Isso significa que os elementos da infraestrutura da rede virtual podem prejudicar o tráfego de gerenciamento, fazendo com que a instância falhe e se torne indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual do Azure. A política pode afetar a forma como a instância gerenciada funciona. Esse mecanismo de plataforma comunica de forma transparente os requisitos de rede para os usuários. A principal meta da política é impedir a configuração incorreta da rede e garantir as operações normais de instância gerenciada. Quando você exclui uma instância gerenciada, a política de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do cluster virtual

Vamos aprofundar-se na arquitetura de conectividade para instâncias gerenciadas. O diagrama a seguir mostra o layout conceitual do cluster virtual.

![Arquitetura de conectividade do cluster virtual](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes se conectam a uma instância gerenciada usando um nome de host `<mi_name>.<dns_zone>.database.windows.net`que tem o formulário. Esse nome de host é resolvido para um endereço IP privado, embora esteja registrado em uma zona DNS (sistema de nomes de domínio) público e possa ser resolvido publicamente. O `zone-id` é gerado automaticamente quando você cria o cluster. Se um cluster recém-criado hospedar uma instância gerenciada secundária, ele compartilhará sua ID de zona com o cluster primário. Para obter mais informações, consulte [usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Esse endereço IP privado pertence ao balanceador de carga interno da instância gerenciada. O balanceador de carga direciona o tráfego para o gateway da instância gerenciada. Como várias instâncias gerenciadas podem ser executadas dentro do mesmo cluster, o gateway usa o nome de host da instância gerenciada para redirecionar o tráfego para o serviço de mecanismo do SQL correto.

Os serviços de gerenciamento e implantação se conectam a uma instância gerenciada usando um [ponto de extremidade de gerenciamento](#management-endpoint) que é mapeado para um balanceador de carga externo. O tráfego será roteado para os nós somente se ele for recebido em um conjunto predefinido de portas que apenas os componentes de gerenciamento da instância gerenciada usam. Um firewall interno nos nós é configurado para permitir o tráfego somente de intervalos de IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre os componentes de gerenciamento e o plano de gerenciamento.

## <a name="management-endpoint"></a>Ponto de extremidade de gerenciamento

A Microsoft gerencia a instância gerenciada usando um ponto de extremidade de gerenciamento. Esse ponto de extremidade está dentro do cluster virtual da instância. O ponto de extremidade de gerenciamento é protegido por um firewall interno no nível da rede. No nível do aplicativo, ele é protegido pela verificação de certificado mútuo. Para localizar o endereço IP do ponto de extremidade, consulte [determinar o endereço IP do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando as conexões são iniciadas dentro da instância gerenciada (como com backups e logs de auditoria), o tráfego parece iniciar a partir do endereço IP público do ponto de extremidade de gerenciamento. Você pode limitar o acesso a serviços públicos de uma instância gerenciada definindo regras de firewall para permitir apenas o endereço IP da instância gerenciada. Para obter mais informações, consulte [verificar o firewall interno da instância gerenciada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> O tráfego que vai para os serviços do Azure que estão dentro da região da instância gerenciada é otimizado e, por esse motivo, não é NATe para o endereço IP público do ponto de extremidade de gerenciamento de instância gerenciada. Por esse motivo, se você precisar usar regras de firewall baseadas em IP, mais comumente para armazenamento, o serviço precisa estar em uma região diferente da instância gerenciada.

## <a name="network-requirements"></a>Requisitos de rede

Implante uma instância gerenciada em uma sub-rede dedicada dentro da rede virtual. A sub-rede deve ter estas características:

- **Sub-rede dedicada:** A sub-rede da instância gerenciada não pode conter nenhum outro serviço de nuvem associado a ela e não pode ser uma sub-rede de gateway. A sub-rede não pode conter nenhum recurso, mas a instância gerenciada, e você não pode adicionar outros tipos de recursos na sub-rede posteriormente.
- **NSG (Grupo de Segurança de Rede):** Um NSG associado à rede virtual deve definir [regras de segurança de entrada](#mandatory-inbound-security-rules) e de [saída](#mandatory-outbound-security-rules) antes de qualquer outra regra. Você pode usar um NSG para controlar o acesso ao ponto de extremidade de dados da instância gerenciada filtrando o tráfego na porta 1433 e as portas 11000-11999 quando a instância gerenciada estiver configurada para conexões de redirecionamento.
- **Tabela de rota definida pelo usuário (UDR):** Uma tabela UDR associada à rede virtual deve incluir [entradas](#user-defined-routes)específicas.
- **Nenhum ponto de extremidade de serviço:** Nenhum ponto de extremidade de serviço deve ser associado à sub-rede da instância gerenciada. Verifique se a opção pontos de extremidade de serviço está desabilitada quando você cria a rede virtual.
- **Endereços IP suficientes:** A sub-rede da instância gerenciada deve ter pelo menos 16 endereços IP. O mínimo recomendado é 32 endereços IP. Para obter mais informações, consulte [determinar o tamanho da sub-rede para instâncias gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md). Você pode implantar instâncias gerenciadas na [rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de configurá-las para atender aos [requisitos de rede para instâncias gerenciadas](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Não é possível implantar uma nova instância gerenciada se a sub-rede de destino não tem essas características. Quando você cria uma instância gerenciada, uma política de intenção de rede é aplicada na sub-rede para evitar alterações não compatíveis na configuração de rede. Depois que a última instância for removida da sub-rede, a política de intenção de rede também será removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança de entrada obrigatórias

| NOME       |Porta                        |Protocol|Origem           |Destino|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|TCP     |Any              |SUB-REDE DA MI  |Allow |
|mi_subnet   |Any                         |Any     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|SUB-REDE DA MI  |Allow |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança de saída obrigatórias

| Nome       |Porta          |Protocol|Origem           |Destino|Action|
|------------|--------------|--------|-----------------|-----------|------|
|gerenciamento  |80, 443, 12000|TCP     |SUB-REDE DA MI        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |

> [!IMPORTANT]
> Verifique se há apenas uma regra de entrada para as portas 9000, 9003, 1438, 1440, 1452 e uma regra de saída para as portas 80, 443, 12000. Instância Gerenciada provisionamento por meio de implantações de Azure Resource Manager falhará se as regras de entrada e saída forem configuradas separadamente para cada porta. Se essas portas estiverem em regras separadas, a implantação falhará com o código de erro`VnetSubnetConflictWithIntendedPolicy`

\*A sub-rede MI refere-se ao intervalo de endereços IP para a sub-rede na forma 10. x. x/y. Você pode encontrar essas informações na portal do Azure, em Propriedades da sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada necessárias permitam o tráfego de _qualquer_ fonte nas portas 9000, 9003, 1438, 1440 e 1452, essas portas são protegidas por um firewall interno. Para obter mais informações, consulte [determinar o endereço do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Se você usar a replicação transacional em uma instância gerenciada e se usar qualquer banco de dados de instância como Publicador ou distribuidor, abra a porta 445 (TCP de saída) nas regras de segurança da sub-rede. Essa porta permitirá o acesso ao compartilhamento de arquivos do Azure.

### <a name="user-defined-routes"></a>rotas definidas pelo usuário

|Nome|Prefixo de endereço|Próximo salto|
|----|--------------|-------|
|subnet_to_vnetlocal|SUB-REDE DA MI|Rede virtual|
|Mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|Mi-13-96-13-nexthop-Internet|13.96.0.0/13|Internet|
|Mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|Mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|Mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|Mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|Mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|Mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|Mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|Mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|Mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|Mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|Mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|Mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|Mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|Mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|Mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|Mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|Mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|Mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|Mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
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
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|Mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|Mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|Mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|Mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
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
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
||||

Além disso, você pode adicionar entradas à tabela de rotas para rotear o tráfego que tem intervalos IP privados locais como um destino por meio do gateway de rede virtual ou NVA (dispositivo de rede virtual).

Se a rede virtual incluir um DNS personalizado, o servidor DNS personalizado deverá ser capaz de resolver os registros DNS públicos. O uso de recursos adicionais, como a autenticação do Azure AD, pode exigir a resolução de FQDNs adicionais. Para obter mais informações, consulte [configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, confira [segurança de dados avançada do SQL Database](sql-database-managed-instance.md).
- Saiba como [Configurar uma nova rede virtual do Azure](sql-database-managed-instance-create-vnet-subnet.md) ou uma [rede virtual do Azure existente](sql-database-managed-instance-configure-vnet-subnet.md) onde você pode implantar instâncias gerenciadas.
- [Calcule o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) em que você deseja implantar as instâncias gerenciadas.
- Saiba como criar uma instância gerenciada:
  - No [portal do Azure](sql-database-managed-instance-get-started.md).
  - Usando o [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Usando [um modelo de Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [um modelo de Azure Resource Manager (usando Jumpbox, com o SSMS incluído)](https://portal.azure.com/). 
