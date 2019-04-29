---
title: Contoso realiza migração em escala para o Azure | Microsoft Docs
description: Saiba como a Contoso lida com uma migração em escala para o Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: 9253051d907a811ffedad3a714112c9b25543a35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60667377"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso – escala uma migração para o Azure

Neste artigo, a Contoso executa uma migração em grande escala para o Azure. Ela considera como planejar e executar uma migração de mais de 3 mil cargas de trabalho, 8 mil bancos de dados e mais de 10 mil VMs.


Este artigo é um de uma série de artigos que documenta como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações de planejamento e contexto e cenários de implantação que ilustram como configurar uma infraestrutura de migração, avaliar a adequação dos recursos locais para migração e executar diferentes tipos de migrações. Cenários aumentam em complexidade. Vamos adicionar artigos à série ao longo do tempo.


**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível.
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados. | Disponível
[Artigo 4: Hospedar novamente um aplicativo em uma VM do Azure e uma Instância Gerenciada do Banco de Dados SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. A Contoso migra a VM front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra as VMs de aplicativos SmartHotel360 para VMs do Azure usando o serviço do Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo usando o Site Recovery para migrar as VMs do aplicativo e o Database Migration Service para migrar o banco de dados do aplicativo para um cluster do SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para VMs do Azure usando o serviço Site Recovery. | Disponível
[Artigo 8: Hospedar novamente um aplicativo do Linux em VMs do Azure e no Banco de Dados do Azure para MySQL](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery. Ele migra o banco de dados do aplicativo para Banco de Dados do Azure para MySQL, usando MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em um aplicativo Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra seu aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativos para uma instância do Azure SQL Server com o Assistente de Migração de Banco de Dados. | Disponível    
[Artigo 10: Refatorar um aplicativo do Linux em um aplicativo Web do Azure e no Banco de Dados do Azure para MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para um aplicativo Web do Azure em vários sites. O aplicativo Web está integrado ao GitHub para entrega contínua. Ele migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível
[Artigo 11: Refatorar o Team Foundation Server no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo em contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados do aplicativo com o Banco de Dados SQL do Azure. | Disponível    
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel usando diversas funcionalidades e serviços do Azure, incluindo o Serviço de Aplicativo do Azure, o AKS (Serviço de Kubernetes do Azure), o Azure Functions, os Serviços Cognitivos do Azure e o Azure Cosmos DB. | Disponível 
Artigo 14: Dimensionar uma migração para o Azure | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Este artigo

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com parceiros comerciais para entender o que eles desejam obter com essa migração:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo, causando pressão sobre a infraestrutura e os sistemas locais.
- **Aumentar a eficiência**: A Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários. Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**: A TI da Contoso precisa atender melhor às necessidades empresariais. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global. Ele não deve obter da forma ou se tornar um bloqueador de negócios.
- **Escala**: Na medida em que a empresa cresce com sucesso, a equipe de TI da Contoso deve disponibilizar sistemas capazes de crescer no mesmo ritmo.
- **Melhorar os modelos de custo**: A Contoso deseja diminuir os requisitos de capital no orçamento de TI.  A Contoso deseja usar os recursos de nuvem para dimensionar e reduzir a necessidade de hardware caro.
- **Reduzir os custos de licenciamento**: A Contoso deseja minimizar os custos com a nuvem.


## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas foram usadas para determinar o melhor método de migração.

**Requisitos** | **Detalhes**
--- | --- 
**Mover rapidamente para o Azure** | A Contoso deseja começar a mover VMs e aplicativos para o Azure assim que possível.
**Compilar um inventário completo** | A Contoso deseja um inventário completo de todos os aplicativos, bancos de dados e VMs na organização.
**Avaliar e classificar aplicativos** | A Contoso deseja aproveitar totalmente a nuvem. Por padrão, a Contoso pressupõe que todos os serviços serão executados como PaaS. IaaS será usada quando PaaS não for apropriada. 
**Treinar e mover para DevOps** | A Contoso deseja mudar para um modelo de DevOps. A Contoso oferecerá treinamento em Azure e DevOps e reorganizará as equipes conforme necessário. 


Depois de fixar metas e requisitos, a Contoso examina o volume de TI e identifica o processo de migração.

## <a name="current-deployment"></a>Implantação atual

Depois de planejar e configurar uma [Infraestrutura do Azure](contoso-migration-infrastructure.md) e experimentar diferentes combinações de migração de POC (prova de conceito) conforme detalhado na tabela acima, a Contoso está pronta para embarcar em uma migração completa para o Azure em grande escala. Isto é o que a Contoso deseja migrar.

**Item** | **Volume** | **Detalhes**
--- | --- | ---
**Cargas de trabalho** | Mais de três mil aplicativos | Os aplicativos são executados em VMs.<br/><br/>  Os aplicativos são Windows, com base em SQL e OSS LAMP.
**Bancos de dados** | Em torno de 8.500 | Os bancos de dados incluem SQL Server, MySQL, PostgreSQL.
**VMs** | Mais de 10 mil | As VMs executadas em hosts do VMware e gerenciadas pelo vCenter Servers.


## <a name="migration-process"></a>Processo de migração

Agora que a Contoso fixou os fatores empresariais e as metas de migração, ela determina uma abordagem de quatro vias para o processo de migração:

- **Fase 1 – Avaliar**: Descubra os ativos atuais e saiba se eles são adequados para a migração para o Azure.
- **Fase 2 – Migrar**: Mova os ativos para o Azure. Como ela move os aplicativos e os objetos para o Azure dependerá do aplicativo e do objetivo que ela deseja atingir.
- **Fase 3– Otimizar**: Depois de mover recursos para o Azure, a Contoso precisa melhorá-los e simplificá-los para eficiência e desempenho máximos.
- **Fase 4 – Proteger e gerenciar**: Com tudo em vigor, a Contoso agora usa os serviços e os recursos de gerenciamento e segurança do Azure para controlar, proteger e monitorar seus aplicativos na nuvem do Azure.


Essas fases não são seriais em toda a organização. Cada parte do projeto de migração da Contoso estará em um estágio diferente do processo de avaliação e de migração. Otimização, segurança e gerenciamento serão contínuos ao longo do tempo.


## <a name="phase-1-assess"></a>Fase 1: Avaliar

A Contoso inicia o processo descobrindo e avaliando a infraestrutura, os dados e os aplicativos locais. A Contoso fará o seguinte:

- A Contoso precisa descobrir aplicativos, mapear dependências entre aplicativos e decidir a prioridade e a ordem de migração.
- Conforme a Contoso avaliar, ela compilará um inventário abrangente de aplicativos e recursos. Juntamente com o novo inventário, a Contoso vai usar e atualizar o CMDB (Banco de Dados de Gerenciamento de Configuração) existente e o catálogo de serviços.
    - O CMDB contém configurações técnicas para aplicativos da Contoso.
    - O catálogo de serviços documenta os detalhes operacionais de aplicativos, inclusive parceiros de negócios associados e contratos de nível de serviço (SLAs)

### <a name="discover-apps"></a>Descobrir aplicativos

A Contoso executa milhares de aplicativos em uma variedade de servidores. Além do CMDB e Catálogo de Serviços, a Contoso precisa de ferramentas de descoberta e avaliação. 

- As ferramentas devem oferecer um mecanismo que possa fornecer dados de avaliação para o processo de migração.
- Ferramentas de avaliação devem fornecer dados que ajudem a criar um inventário inteligente dos recursos físicos e virtuais da Contoso. Os dados devem incluir informações de perfil e métricas de desempenho.
- Quando a descoberta estiver concluída, a Contoso deverá ter um inventário completo dos ativos e dos metadados associados a eles. Esse inventário será usado para definir o plano de migração.

### <a name="identify-classifications"></a>Identificar as classificações

A Contoso identifica algumas categorias comuns para classificar os ativos no inventário. Essas classificações são importantes para decisões da Contoso para a migração. A lista de classificação ajuda a estabelecer prioridades de migração e identificar problemas complexos.

**Categoria** | **Valor Atribuído** | **Detalhes**
--- | --- | ---
Grupo de negócios | Lista de nomes de grupo de negócios | Que grupo é responsável para o item de estoque?
Candidato de POC | S/N | O aplicativo pode ser usado como POC ou um dos primeiros a adotar migração na nuvem?
Débito técnico | Nenhum/Alguns/Grave | O item de estoque em execução ou usando um produto, uma plataforma ou um sistema operacional sem suporte?
Implicações de firewall | S/N | O aplicativo se comunica com a Internet/tráfego externo?  Ele se integra com um firewall?
Problemas de segurança | S/N | Há problemas conhecidos de segurança com o aplicativo?  O aplicativo usa dados não criptografados ou plataformas desatualizadas?


### <a name="discover-app-dependencies"></a>Descobrir dependências do aplicativo

Como parte do processo de avaliação, a Contoso precisa identificar quais aplicativos estão em execução, e descobrir as dependências e as conexões entre os servidores de aplicativo. A Contoso mapeia o ambiente em etapas.

1. Como uma primeira etapa, a Contoso descobre como os servidores e máquinas são mapeados para aplicativos individuais, locais de rede e grupos.
2. Com essas informações, a Contoso pode identificar claramente os aplicativos que têm algumas dependências e, portanto, são adequados para uma migração rápida.
3. A Contoso pode usar o mapeamento para ajudá-los a identificar as dependências e as comunicações mais complexas entre servidores de aplicativos. A Contoso pode agrupar esses servidores logicamente para representar aplicativos e planejar uma estratégia de migração com base nesses grupos.


Com o mapeamento concluído, a Contoso pode garantir que todos os componentes do aplicativo sejam identificados e considerados ao criar o plano de migração. 

![Mapeamento de dependências](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>Avaliar aplicativos

Como a última etapa no processo de descoberta e avaliação, a Contoso pode analisar os resultados de avaliação e de mapeamento para descobrir como migrar cada aplicativo no Catálogo de Serviços. 

Para capturar esse processo de avaliação, ela adiciona duas classificações extras ao inventário.

**Categoria** | **Valor Atribuído** | **Detalhes**
--- | --- | ---
Grupo de negócios | Lista de nomes de grupo de negócios | Que grupo é responsável para o item de estoque?
Candidato de POC | S/N | O aplicativo pode ser usado como POC ou um dos primeiros a adotar migração na nuvem?
Débito técnico | Nenhum/Alguns/Grave | O item de estoque em execução ou usando um produto, uma plataforma ou um sistema operacional sem suporte?
Implicações de firewall | S/N | O aplicativo se comunica com a Internet/tráfego externo?  Ele se integra com um firewall?
Problemas de segurança | S/N | Há problemas conhecidos de segurança com o aplicativo?  O aplicativo usa dados não criptografados ou plataformas desatualizadas?
Estratégia de migração | Re-hospedar/Refatorar/Rearquitetar/Recompilar | Que tipo de migração é necessário para o aplicativo? Como o aplicativo será implantado no Azure? [Saiba mais](contoso-migration-overview.md#migration-strategies).
Complexidade técnica | 1-5 | Quão complexa é a migração? Esse valor deve ser definido pela Contoso DevOps e parceiros relevantes.
Nível de importância de negócios | 1-5 | Quão importante é que o aplicativo para os negócios? Por exemplo, um pequeno grupo de trabalho aplicativo pode receber uma pontuação de um, enquanto um aplicativo crítico usado em toda a organização pode receber a uma pontuação de cinco. Essa pontuação terá impacto sobre o nível de prioridade da migração.
Prioridade de migração | 1/2/3 | Qual é a prioridade de migração para o aplicativo?
Risco de migração  | 1-5 | Qual é o nível de risco para migrar o aplicativo? Esse valor deve ser acordado pela Contoso DevOps e parceiros relevantes.




### <a name="figure-out-costs"></a>Determinar custos

Para descobrir custos e a economia potencial da migração para o Azure, a Contoso pode usar o [calculadora de TCO (Custo Total de Propriedade)](https://azure.microsoft.com/pricing/tco/calculator/) para calcular e comparar o TCO para o Azure com uma implantação local equivalente.

### <a name="identify-assessment-tools"></a>Identificar as ferramentas de avaliação

A Contoso decide qual ferramenta usar para descoberta, avaliação e compilação do inventário. A Contoso identifica uma combinação de ferramentas e serviços do Azure, scripts e ferramentas de aplicativo nativo e ferramentas de parceiros. Em particular, a Contoso está interessada em como o recurso Migrações para Azure pode ser usado para avaliar em escala.


#### <a name="azure-migrate"></a>Migrações para Azure


O serviço Migrações para Azure ajuda você a descobrir e avaliar as VMs do VMware locais em preparação para migração para o Azure. É isto que Migrações para Azure faz:

1. Descobrir: Descubra VMs locais do VMware.
    - O recurso Migrações para Azure dá suporte à descoberta de diversos Servidores vCenter (em série) e pode executar descobertas em projetos separados de Migrações para Azure.
    - O recurso Migrações para Azure executa a descoberta usando uma VM VMware em execução no Coletor de Migrações. O mesmo coletor pode descobrir VMs em diferentes servidores vCenter e enviar dados para diferentes projetos.
1. Avaliar a preparação: Avalie se os computadores locais são adequados para execução no Azure. A avaliação inclui:
    - Recomendações sobre tamanho: Obtenha recomendações sobre tamanho para VMs do Azure, com base no histórico de desempenho das VMs locais.
    - Custos mensais estimados: obtenha custos estimados para execução de computadores locais no Azure.
2. Identificar dependências:  Visualize as dependências de computadores locais para criar grupos de computadores ideais para avaliação e migração.


![Migrações para Azure](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>Migrar em escala

A Contoso precisa usar Migrações para Azure para dar a escala correta dessa migração. 

- A Contoso fará uma avaliação de aplicativo por aplicativo com Migrações para Azure. Isso garante que Migrações para Azure retorne dados em tempo hábil para o portal do Azure.
- Os administradores da Contoso leem sobre [como implantar Migrações para Azure em escala](how-to-scale-assessment.md)
- A Contoso observa os limites de Migrações para Azure resumidos na tabela a seguir.


**Ação** | **Limite**
--- | ---
Criar um projeto de Migrações para Azure | 1.500 VMs
Descoberta | 1.500 VMs
Avaliação | 1.500 VMs

A Contoso usará Migrações para Azure da seguinte maneira:

- No vCenter, a Contoso organizará VMs em pastas. Isso tornará mais fácil para a Contoso se concentrar em como ela executa uma avaliação em relação a VMs em uma pasta específica.
- Migrações para Azure usa o Mapa do Serviço do Azure para avaliar as dependências entre computadores. Isso requer a instalação de agentes em VMs a serem avaliadas. 
    - A Contoso usará scripts automatizados para instalar os agentes Windows ou Linux necessários.
    - Com script, a Contoso pode efetuar push da instalação para as VMs em uma pasta do vCenter.


#### <a name="database-tools"></a>Ferramentas de Banco de Dados

Além de Migrações para Azure, a Contoso focará em usar ferramentas especificamente para avaliação do banco de dados. Ferramentas como o [Assistente de Migração do Banco de Dados](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) ajudarão a avaliar os bancos de dados do SQL Server para a migração.

O AMD (Assistente de Migração de Dados) pode ajudar a Contoso a descobrir se os bancos de dados local são compatíveis com uma variedade de soluções de banco de dados do Azure, como o Banco de Dados SQL do Azure, o SQL Server em execução em uma VM IaaS do Azure e Instância Gerenciada SQL do Azure. 

Além de DMS, a Contoso usa outros scripts para descobrir e documentar os bancos de dados do SQL Server. Eles estão localizados no repositório do GitHub.

#### <a name="partner-tools"></a>Ferramentas de parceiros

Várias outras ferramentas de parceiros podem ajudar a Contoso na avaliação do ambiente local para a migração para o Azure. [Saiba mais](https://azure.microsoft.com/migration/partners/) sobre os parceiros de Migração para Azure.  

## <a name="phase-2-migrate"></a>Fase 2: Migrar

Com a avaliação completa, a Contoso precisa identificar as ferramentas para mover aplicativos, dados e infraestrutura para o Azure. 




### <a name="migration-strategies"></a>Estratégias de migração

Há quatro estratégias de migração amplas que a Contoso pode considerar. 

**Estratégia** | **Detalhes** | **Uso**
--- | --- | ---
**Hospedar novamente**  | Isso geralmente é conhecido como migração "lift-and-shift", uma opção sem código para migrar os aplicativos existentes para o Azure rapidamente.<br/><br/> Um aplicativo é migrado no estado em que se encontra, com os benefícios da nuvem, sem os riscos nem os custos associados com as alterações de código. | A Contoso pode hospedar novamente aplicativos menos estratégico sem necessidade de qualquer alteração de código.
**Refatorar** |  Também conhecida como "reempacotamento", essa estratégia exige alterações mínimas de código ou configuração de aplicativo para conectar o aplicativo à PaaS do Azure e aproveitar ao máximo as funcionalidades da nuvem. | A Contoso pode refatorar aplicativos estratégicos para manter a mesma funcionalidade básica, mas movê-los para execução em uma plataforma do Azure, como Serviços de Aplicativos do Azure.<br/><br/> Isso requer alterações mínimas de código.<br/><br/> Por outro lado, a Contoso precisará manter uma plataforma de VM, pois isso não será gerenciado pela Microsoft.
**Recriação de arquitetura** | Essa estratégia modifica ou estende uma base de código de aplicativo para otimizar a arquitetura de aplicativo para funcionalidades de nuvem e escala.<br/><br/> Ela moderniza seu aplicativo em uma arquitetura resiliente, altamente escalonável e que pode ser implantada de maneira independente.<br/><br/> Os serviços do Azure podem acelerar o processo, dimensionar aplicativos com confiança e gerenciar aplicativos com facilidade.
**Recompilar** | Essa estratégia recria um aplicativo do zero usando tecnologias nativas da nuvem.<br/><br/> A PaaS (plataforma como serviço) do Azure fornece um ambiente de desenvolvimento e implantação completo na nuvem. Ela elimina alguns gastos e a complexidade de licenças de software e a necessidade de uma infraestrutura de aplicativo subjacente, middleware e outros recursos. | A Contoso pode reescrever aplicativos críticos desde o princípio para aproveitar tecnologias de nuvem como o computador sem servidor ou microsserviços.<br/><br/> A Contoso vai gerenciar o aplicativo e os serviços que ele desenvolve e o Azure gerenciará todo o resto.


Os dados também devem ser considerados, especialmente com o volume de bancos de dados que a Contoso tem. A abordagem padrão da Contoso é usar os serviços de PaaS como o Banco de Dados SQL do Azure para aproveitar ao máximo os recursos de nuvem. Ao passar para um serviço de PaaS para bancos de dados, a Contoso somente precisará manter os dados, deixando a plataforma subjacente para a Microsoft.

### <a name="evaluate-migration-tools"></a>Avaliar as ferramentas de migração

A Contoso está usando principalmente algumas ferramentas e serviços do Azure para a migração:

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): Orquestra a recuperação de desastre e migra VMs locais para o Azure.
- [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview): Migra bancos de dados locais, como SQL Server, MySQL e Oracle, para o Azure.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

O Azure Site Recovery é o principal serviço do Azure para orquestrar a recuperação de desastres e a migração de dentro do Azure e de sites locais para o Azure.

1. O Site Recovery habilita e orquestra a replicação de seus sites locais para o Azure.
2. Quando a replicação está configurada e em execução, é possível realizar failover de máquinas locais para o Azure, concluindo a migração.

A Contoso já [concluiu uma POC](contoso-migration-rehost-vm.md) para ver como o Site Recovery pode ajudá-las a migrar para a nuvem.

##### <a name="using-site-recovery-at-scale"></a>Como usar o Site Recovery em escala

A Contoso planeja executar várias migrações lift-and-shift. Para garantir que isso funcione, o Site Recovery replicará lotes de cerca de 100 VMs por vez. Para descobrir como isso vai funcionar, a Contoso precisa executar o planejamento de capacidade para a migração do Site Recovery proposta.

- A Contoso precisa coletar informações sobre os volumes de tráfego. Especificamente:
    - A Contoso precisa determinar a taxa de alteração para VMs que deseja replicar.
    - A Contoso também precisa considerar a conectividade de rede do site local para o Azure.
- Em resposta aos requisitos de capacidade e volume, a Contoso precisa alocar largura de banda suficiente com base na taxa de alteração diária de dados para as VMs necessárias para atender a seu RPO (objetivo de ponto de recuperação).
- Por fim, ela precisa descobrir quantos servidores são necessários para executar componentes do Site Recovery necessários para a implantação.

###### <a name="gather-on-premises-information"></a>Coletar informações locais
A Contoso pode usar a ferramenta [Planejador de Implantação do Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) para concluir estas etapas:

- A Contoso pode usar a ferramenta para criar remotamente o perfil de VMs sem causar impacto ao ambiente de produção. Isso ajuda a identificar os requisitos de armazenamento e de largura de banda para replicação e failover.
- A Contoso pode executar a ferramenta sem instalar componentes do Site Recovery localmente.
- Você coletará informações sobre VMs compatíveis e incompatíveis, discos por VM e variação de dados por disco. Também identifica os requisitos de largura de banda de rede, bem como a infraestrutura do Azure necessária para replicação e failover bem-sucedidos.
- A Contoso precisa garantir que então execute a ferramenta do planejador em computadores Windows Server que cumpram os requisitos mínimos para o servidor de configuração do Site Recovery. O servidor de configuração é um computador do Site Recovery de que você precisa para replicar VMs do VMware locais.


###### <a name="identify-site-recovery-requirements"></a>Identificar requisitos do Site Recovery

Além das VMs que estão sendo replicadas, o Site Recovery requer vários componente para a migração do VMware.

**Componente** | **Detalhes**
--- | ---
**Servidor de configuração** | Normalmente, uma VM do VMware configurada usando o modelo OVF.<br/><br/> O componente de servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
**Servidor de processo** | Instalado por padrão no servidor de configuração.<br/><br/> O componente de servidor de processo recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.<br/><br/> O servidor de processo também instala o Serviço de Mobilidade do Azure Site Recovery nas VMs que você deseja replicar e executa a descoberta automática de máquinas locais.<br/><br/> Implantações em escala precisam de servidores de processo independentes adicionais para lidar com grandes volumes de tráfego de replicação.
**Serviço de mobilidade** | O agente de Serviço de Mobilidade é instalado em cada VM do VMware que será migrada com o Site Recovery.  

A Contoso precisa descobrir como implantar esses componentes com base em considerações de capacidade.

**Componente** | **Requisitos de capacidade**
--- | ---
**Taxa de alteração diária máxima** | Um único servidor de processo pode lidar com uma taxa de alteração diária de até 2 TB. Uma vez que uma VM só pode usar um servidor de processo, a taxa de alteração de dados diária máxima com suporte para uma VM replicada é de 2 TB.
**Taxa de transferência máxima** | Uma conta de armazenamento padrão do Azure pode processar um máximo de 20 mil solicitações por segundo, e as operações de IOPS (entrada/saída por segundo) em uma VM de replicação devem estar dentro desse limite. Por exemplo, se uma VM tiver 5 discos e cada disco gerar 120 IOPS (8 mil de tamanho) na VM, isso estará dentro do limite de IOPS por disco do Azure de 500.<br/><br/> Observe que o número de contas de armazenamento necessário é igual ao total de IOPS do computador de origem dividido por 20 mil. Um computador replicado pode pertencer somente a uma única conta de armazenamento no Azure.
**Servidor de configuração** | Com base na estimativa da Contoso de replicar 100=200 VMs juntas e os [requisitos de dimensionamento do servidor de configuração](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server), a Contoso estima que precisará de um computador de servidor de configuração assim:<br/><br/> CPU: 16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz)<br/><br/> Memória: 32 GB<br/><br/> Disco de cache: 1 TB<br/><br/> Taxa de alteração de dados: 1 TB a 2 TB.<br/><br/> Além dos requisitos de dimensionamento, a Contoso precisará garantir que o servidor de configuração esteja localizado no lugar ideal, na mesma rede e segmento de LAN que as VMs que serão migradas.
**Servidor de processo** | A Contoso implantará um servidor de processo dedicado autônomo com capacidade para replicar de 100 a 200 VMs:<br/><br/> CPU: 16 vCPUs (2 soquetes * 8 núcleos @ 2,5 GHz)<br/><br/> Memória: 32 GB<br/><br/> Disco de cache: 1 TB<br/><br/> Taxa de alteração de dados: 1 TB a 2 TB.<br/><br/> O servidor de processo vai trabalhar duro, assim, deve estar localizado em um host ESXi que possa lidar com a E/S de disco, o tráfego de rede e a CPU necessários para a replicação. A Contoso considerará um host dedicado para essa finalidade. 
**Rede** | A Contoso examinou a atual infraestrutura de VPN site a site e decidiu implementar o Azure ExpressRoute. A implementação é crítica porque reduzirá a latência e melhorará a largura de banda para a região Leste dos EUA 2 do Azure primária da Contoso.<br/><br/> **Monitoramento**: A Contoso precisará monitorar cuidadosamente os dados que fluem do servidor de processo. Se os dados sobrecarregarem a largura de banda de rede, a Contoso considerará a [limitação da largura de banda do servidor de processo](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
**Armazenamento do Azure** | Para a migração, a Contoso deve identificar o tipo e o número certos de contas de armazenamento do Azure de destino.  O Site Recovery replica dados de VM para o armazenamento do Azure.<br/><br/> O Site Recovery pode replicar contas de armazenamento standard ou premium (SSD).<br/><br/> Para decidir sobre o armazenamento, a Contoso deve examinar os [limites de armazenamento](../virtual-machines/windows/disks-types.md), além do fator de crescimento esperado e do aumento no uso ao longo do tempo. Dada a velocidade e a prioridade das migrações, a Contoso decidiu usar SSDs premium<br/><br/>

A Contoso tomou a decisão de usar Managed Disks para todas as VMs implantadas no Azure.  O IOPS necessário determinará se os discos serão Standard HDD, Standard SSD ou Premium (SSD).<br/><br/>

#### <a name="data-migration-service"></a>Serviço de Migração de Dados

O DMS (Serviço de Migração de Banco de Dados do Azure) é um serviço totalmente gerenciado que habilita migrações contínuas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo.

- DMS integra a funcionalidade de serviços e ferramentas existentes. Ele usa o AMD (Assistente de Migração de Dados) para gerar relatórios de avaliação para identificar recomendações sobre compatibilidade do banco de dados e quaisquer modificações necessárias.
- O DMS usa um processo de migração simples e autoguiado com avaliação inteligente que ajuda a abordar possíveis problemas antes da migração.
- O DMS pode migrar em escala de várias fontes para o banco de dados do Azure de destino.
- O DMS oferece o suporte do SQL Server 2005 ao SQL Server 2017.

O DMS não é a única ferramenta de migração de banco de dados da Microsoft. Obtenha uma [comparação de ferramentas e serviços](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

###### <a name="using-dms-at-scale"></a>Como usar DMS em escala

A Contoso usará DMS ao migrar do SQL Server.  

- Ao provisionar o DMS, a Contoso precisa garantir que ele seja dimensionado corretamente e esteja definido para otimizar o desempenho para migrações de dados. A Contoso selecionará a opção "camada comercialmente crítica com 4 vCores", permitindo que o serviço aproveite as várias vCPUs para paralelização e transferência de dados mais rápida.

    ![Dimensionamento do DMS](./media/contoso-migration-scale/dms.png)

- Outra tática de dimensionamento para a Contoso é aumentar temporariamente a instância de destino do SQL do Azure ou do Banco de Dados MySQL para a SKU da camada Premium durante a migração de dados. Isso minimiza a limitação do banco de dados que pode afetar as atividades de transferência de dados ao usar SKUs de nível inferior.



##### <a name="using-other-tools"></a>Como usar outras ferramentas

Além do DMS, a Contoso pode usar outras ferramentas e serviços para identificar as informações da VM.

- Há scripts para ajudar com as migrações manuais. Eles estão disponíveis no repositório do GitHub.
- Diversas [ferramentas de parceiros](https://azure.microsoft.com/migration/partners/) também podem ser usadas para a migração.


## <a name="phase-3-optimize"></a>Fase 3: Otimizar

Depois de a Contoso mover recursos para o Azure, ela precisa simplificá-los para melhorar o desempenho e maximizar o ROI com ferramentas de Gerenciamento de Custos. Considerando que o Azure é um serviço de pagamento por uso, é essencial para a Contoso entender como os sistemas estão sendo executados e garantir que eles sejam dimensionados corretamente.


### <a name="azure-cost-management"></a>Gerenciamento de Custos do Azure

Para aproveitar ao máximo seu investimento na nuvem, a Contoso aproveitará a ferramenta de Gerenciamento de Custos do Azure gratuita.

- Essa solução licenciada criada pelo Cloudyn, uma subsidiária da Microsoft, permite que a Contoso gerencie gastos em nuvem com transparência e precisão.  Ela fornece ferramentas para monitorar, alocar e cortar os custos de nuvem.
- O Gerenciamento de Custos do Azure fornece relatórios de painel simples para ajudar com alocação de custos, showbacks e estornos.
- O Gerenciamento de Custos pode otimizar os gastos na nuvem identificando os recursos subutilizados que, posteriormente, a Contoso pode gerenciar e ajustar.
- [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure.

    
![Gerenciamento de custo](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>Ferramentas nativas

A Contoso também usará scripts para localizar os recursos não utilizados.

- Durante grandes migrações, geralmente há dados restantes, como VHDs (discos rígidos virtuais), que incorrem em cobranças, mas não fornecem nenhum valor para a empresa. Scripts estão disponíveis no repositório do GitHub.
- A Contoso aproveitará o trabalho feito pelo departamento de TI da Microsoft e considerará implementar o Kit de Ferramentas ARO (Otimização de Recursos do Azure).
- A Contoso pode implantar uma conta de Automação do Azure com runbooks e agendas pré-configurados à sua assinatura e começar a economizar dinheiro. A otimização de recursos do Azure ocorre automaticamente em uma assinatura depois de uma agenda ter sido habilitada ou criada, incluindo otimização de novos recursos.
- Isso fornece funcionalidades de automação descentralizada para reduzir os custos. Os recursos incluem:
    - Adiamento automático de VMs do Azure com base em CPU baixa.
    - Agendar VMs do Azure para adiamento e cancelamento de adiamento.
    - Agendar VMs do Azure para adiamento ou cancelar adiamento em ordens crescente e decrescente usando marcas do Azure.
    - Exclusão em massa de grupos de recursos sob demanda.
- Introdução ao Kit de ferramentas ARO neste [repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit).

### <a name="partner-tools"></a>Ferramentas de parceiros

Ferramentas de parceiros, como [Hanu](https://hanu.com/insight/) e [Scalr]( https://www.scalr.com/cost-optimization/), podem ser aproveitadas.


## <a name="phase-4-secure--manage"></a>Fase 4: Proteger e gerenciar

Nesta fase, a Contoso usa recursos de gerenciamento e segurança do Azure para controlar, proteger e monitorar aplicativos de nuvem no Azure. Esses recursos ajudam a executar um ambiente seguro e bem gerenciado, ao mesmo tempo que usam os produtos disponíveis no portal do Azure. A Contoso começa a usar esses serviços durante a migração e, com o suporte híbrido do Azure, continua usando várias dessas soluções para adquirir uma experiência consistente na nuvem híbrida.


### <a name="security"></a>Segurança
A Contoso contará com a Central de Segurança do Azure para proteção avançada contra ameaças e gerenciamento de segurança unificados em cargas de trabalho de nuvem híbrida.

- A Central de Segurança fornece visibilidade completa e controle sobre a segurança dos aplicativos de nuvem no Azure.
- A Contoso pode rapidamente detectar e realizar uma ação em resposta a ameaças e reduzir a exposição de segurança habilitando proteção contra ameaças adaptável.

[Saiba mais](https://azure.microsoft.com/services/security-center/) sobre a Central de Segurança. 


### <a name="monitoring"></a>Monitoramento

A Contoso precisa de visibilidade sobre a integridade e o desempenho de aplicativos, infraestrutura e dados migrados recentemente agora em execução no Azure. A Contoso aproveitará internos nuvem do Azure, ferramentas como o Azure Monitor, espaço de trabalho do Log Analytics e Application Insights de monitoramento.
 
- Usando essas ferramentas, a Contoso pode coletar dados de fontes e facilmente obter insights avançados. Por exemplo, a Contoso pode calibrar a utilização de memória e disco da CPU para suas VMs, exibir aplicativos e dependências de rede em diversas VMs e acompanhar o desempenho do aplicativo.
- A Contoso usará essas ferramentas de monitoramento de nuvem para agir e integrar soluções de serviço.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) sobre o monitoramento do Azure.

### <a name="bcdr"></a>BCDR 

A Contoso precisará de uma estratégia BCDR (continuidade dos negócios e recuperação de desastres) para seus recursos do Azure.

- O Azure fornece [recursos internos de BCDR](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) para manter os dados seguros e aplicativos e serviços em funcionamento. 
- Além dos recursos internos, a Contoso deseja garantir que ela possa se recuperar de falhas, evitar interrupções dos negócios onerosas, cumprir metas de conformidade e proteger dados contra ransomware e erros humanos. Para fazer isto
    - A Contoso implantará o Backup do Azure como uma solução econômica para o backup de recursos do Azure. Por ser interna, a Contoso pode configurar backups na nuvem em poucas etapas simples.
    - A Contoso vai configurar a recuperação de desastres para VMs do Azure usando o Azure Site Recovery para replicação, failover e failback entre regiões do Azure que ela especifica.  Isso garante que aplicativos em execução em VMs do Azure permaneçam disponíveis em uma região secundária de escolha da Contoso no caso de uma interrupção na região primária. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

 
## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso planejou uma migração do Azure em escala.  O processo de migração é dividido em quatro estágios. Da avaliação e da migração até a otimização, a segurança e o gerenciamento após a migração ser concluída. Basicamente, é importante planejar um projeto de migração como um processo inteiro, mas migrar sistemas dentro de uma organização dividindo conjuntos em classificações e números que façam sentido para os negócios. Ao avaliar dados e aplicar classificações, e o projeto pode ser dividido em uma série de migrações menores, que podem ser executadas rapidamente e com segurança.  A soma dessas migrações menores rapidamente se transforma em uma migração grande bem-sucedida para o Azure.
