---
title: FAQ-serviço de migração de banco de dados do Azure
description: Perguntas frequentes sobre como usar o serviço de migração de banco de dados do Azure para executar migrações de banco de dados
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f4d65c97bfccd223453583b25ee0586c5bc0b1ec
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091449"
---
# <a name="faq-about-using-azure-database-migration-service"></a>FAQ sobre como usar o serviço de migração de banco de dados do Azure

Este artigo lista as perguntas frequentes sobre como usar o serviço de migração de banco de dados do Azure junto com respostas relacionadas.

## <a name="overview"></a>Visão geral

**Perguntas. O que é o serviço de migração de banco de dados do Azure?**
O serviço de migração de banco de dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações diretas de várias fontes de banco de dados para plataformas de data do Azure com tempo de inatividade No momento, o serviço está em disponibilidade geral, com esforços de desenvolvimento em andamento com foco em:

* Confiabilidade e desempenho.
* Adição iterativa de pares de fonte-destino.
* Investimento contínuo em migrações sem conflitos.

**Perguntas. Para quais pares de origem/destino o serviço de migração de banco de dados do Azure oferece suporte atualmente?**
O serviço atualmente dá suporte a uma variedade de pares de origem/destino ou cenários de migração. Para obter uma listagem completa do status de cada cenário de migração disponível, consulte o artigo [Status dos cenários de migração com suporte pelo Serviço de Migração de Banco de Dados do Azure](./resource-scenario-status.md).

Outros cenários de migração estão em versão prévia e exigem o envio de uma indicação por meio do site de visualização do DMS. Para obter uma lista completa dos cenários na visualização e para se inscrever para participar de uma dessas ofertas, consulte o [site de visualização do DMS](https://aka.ms/dms-preview/).

**Perguntas. Quais versões do SQL Server o serviço de migração de banco de dados do Azure oferece suporte como fonte?**
Ao migrar do SQL Server, as fontes com suporte para o serviço de migração de banco de dados do Azure são SQL Server 2005 por meio de SQL Server 2019.

**P: ao usar o serviço de migração de banco de dados do Azure, qual é a diferença entre uma migração offline e online?**
Você pode usar o serviço de migração de banco de dados do Azure para realizar migrações online e offline. Com uma migração *offline* , o tempo de inatividade do aplicativo começa quando a migração é iniciada. Com uma migração *online* , o tempo de inatividade é limitado ao período de redução no final da migração. Sugerimos que você teste uma migração offline para determinar se o tempo de inatividade é aceitável; caso contrário, faça uma migração online.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Para saber mais, confira a página de [preços](https://azure.microsoft.com/pricing/details/database-migration/) do Serviço de Migração de Banco de Dados do Azure.

**Perguntas. Como o serviço de migração de banco de dados do Azure se compara a outras ferramentas de migração de banco de dados da Microsoft, como o Assistente de Migração de dados (DMA) ou o Assistente de Migração do SQL Server (SSMA)?**
O serviço de migração de banco de dados do Azure é o método preferencial para a migração de banco de dados para Microsoft Azure em escala. Para obter mais detalhes sobre como o serviço de migração de banco de dados do Azure se compara a outras ferramentas de migração de banco de dados da Microsoft e recomendações sobre como usar o serviço para vários cenários, consulte a postagem de blog [diferenciando ferramentas e serviços de migração de banco de dados](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)

**Perguntas. Como o serviço de migração de banco de dados do Azure se compara à oferta de migrações para Azure?**
As migrações para Azure auxiliam na migração de máquinas virtuais locais para IaaS do Azure. O serviço avalia a adequação da migração e o dimensionamento com base no desempenho, e fornece estimativas de custo para a execução das máquinas virtuais locais no Azure. As Migrações para Azure são úteis para migrações de lift-and-shift de cargas de trabalho baseadas em VM local para VMs de IaaS do Azure. No entanto, ao contrário do serviço de migração de banco de dados do Azure, as migrações para Azure não são uma oferta de serviço de migração de banco de dados especializada para plataformas de banco de dados relacionais de PaaS do Azure, Instância Gerenciada como

**Perguntas. O serviço de migração de banco de dados armazena os dados do cliente?**
Não. O serviço de migração de banco de dados não armazena os clientes.

## <a name="setup"></a>Instalação

**Perguntas. Quais são os pré-requisitos para usar o serviço de migração de banco de dados do Azure?**
Há vários pré-requisitos necessários para garantir que o serviço de migração de banco de dados do Azure seja executado sem problemas ao executar migrações de banco de dados. Alguns dos pré-requisitos se aplicam em todos os cenários (pares de origem e destino) com suporte do serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Os pré-requisitos do Serviço de Migração de Banco de Dados do Azure que são comuns a todos os cenários de migração compatíveis incluem a necessidade de:

* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Certifique-se de que suas regras de grupo de segurança de rede virtual não bloqueiem a porta 443 para os objecttags do ServiceBus, armazenamento e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.

Para obter uma lista de todos os pré-requisitos necessários para competir com cenários de migração específicos usando o serviço de migração de banco de dados do Azure, consulte os tutoriais relacionados na [documentação](./dms-overview.md) do serviço de migração de banco de dados do azure em docs.Microsoft.com.

**Perguntas. Como fazer localizar o endereço IP do serviço de migração de banco de dados do Azure para que eu possa criar uma lista de permissões para as regras de firewall usadas para acessar meu banco de dados de origem para migração?**
Talvez seja necessário adicionar regras de firewall, permitindo que o serviço de migração de banco de dados do Azure acesse o banco de dados de origem para migração. O endereço IP para o serviço é dinâmico, mas se você estiver usando o ExpressRoute, esse endereço será atribuído de forma privada por sua rede corporativa. A maneira mais fácil de identificar o endereço IP apropriado é examinar o mesmo grupo de recursos que o recurso de serviço de migração de banco de dados do Azure provisionado para localizar a interface de rede associada. Normalmente, o nome do recurso de Adaptador de Rede começa com o prefixo de NIC e é seguido por um caractere único e sequência numérica, por exemplo NIC-jj6tnztnmarpsskr82rbndyp. Ao selecionar esse recurso de interface de rede, você pode ver o endereço IP que deve ser incluído na lista de permissões na página do portal do Azure para visão de geral de recursos.

Talvez você precise incluir a origem da porta que o SQL Server está escutando na lista de permissões. Por padrão, é a porta 1433, mas o SQL Server de origem também pode estar configurado para escutar outras portas. Nesse caso, você também precisa incluir as portas na lista de permissões. Você pode determinar a porta que o SQL Server está escutando usando uma consulta de modo de exibição de Gerenciamento Dinâmico:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Você também pode determinar a porta que o SQL Server está escutando consultando o log de erros do SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Perguntas. Como fazer configurar um Rede Virtual do Microsoft Azure?**
Embora vários tutoriais da Microsoft possam orientá-lo durante o processo de configuração de uma rede virtual, a documentação oficial aparece no artigo [rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Uso

**Perguntas. O que é um resumo das etapas necessárias para usar o serviço de migração de banco de dados do Azure para executar uma migração de banco de dados?**
Durante uma migração de banco de dados típica e simples, você:

1. Cria um banco de dados de destino.
2. Avalie seus bancos de dados de origem.
    * Para migrações homogêneas, avalie seus bancos de dados existentes usando [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Para migrações heterogêneas (de fontes de concorrentes), avalie seus bancos de dados existentes com o [SSMA](/sql/ssma/sql-server-migration-assistant). Você também usa o SSMA para converter objetos de banco de dados e migrar o esquema para sua plataforma de destino.
3. Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
4. Crie um projeto de migração especificando os bancos de dados de origem, os bancos de dados de destino e as tabelas a serem migradas.
5. Inicie a carga completa.
6. Escolhe a validação subsequente.
7. Execute uma mudança manual do seu ambiente de produção para o novo banco de dados baseado em nuvem.

## <a name="troubleshooting-and-optimization"></a>Solução de problemas e otimização

**Perguntas. Estou configurando um projeto de migração no DMS e estou tendo dificuldades para se conectar ao meu banco de dados de origem. O que devo fazer?**
Se você tiver problemas para se conectar ao sistema de banco de dados de origem enquanto trabalha na migração, crie uma máquina virtual na mesma sub-rede da rede virtual com a qual você configurou sua instância DMS. Na máquina virtual, você deve ser capaz de executar um teste do Connect, como usar um arquivo UDL para testar uma conexão com SQL Server ou baixar Robo 3T para testar conexões do MongoDB. Se o teste de conexão tiver sucesso, você não deverá ter um problema com a conexão com o banco de dados de origem. Se o teste de conexão não tiver sucesso, contate o administrador da rede.

**Perguntas. Por que meu serviço de migração de banco de dados do Azure não está disponível ou parou?**
Se o usuário parar explicitamente o serviço de migração de banco de dados do Azure (DMS) ou se o serviço estiver inativo por um período de 24 horas, o serviço estará em um estado interrompido ou automaticamente pausado. Em cada caso, o serviço estará indisponível e em status parado.  Para retomar as migrações ativas, reinicie o serviço.

**Perguntas. Há alguma recomendação para otimizar o desempenho do serviço de migração de banco de dados do Azure?**
Você pode fazer algumas coisas para acelerar a sua migração de banco de dados usando o serviço:

* Use os vários tipos de preços de uso geral da CPU ao criar sua instância de serviço para permitir que o serviço aproveite as várias vCPUs para paralelização e transferência de dados mais rápida.
* Temporariamente, escale verticalmente a sua instância de destino de banco de dados SQL do Azure para o SKU da camada Premium durante a operação de migração de dados para minimizar a limitação do banco de dados SQL do Azure que pode afetar as atividades de transferência de dados ao usar SKUs de nível inferior.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional, consulte o artigo [ O que é o Serviço de Migração de Banco de Dados do Azure ](dms-overview.md).
