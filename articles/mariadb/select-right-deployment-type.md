---
title: Selecionando o tipo de implantação certo-banco de dados do Azure para MariaDB
description: Este artigo descreve quais fatores devem ser considerados antes de implantar o banco de dados do Azure para MariaDB como IaaS (infraestrutura como serviço) ou PaaS (plataforma como um serviço).
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d8885e374142b3d916803fc472ae18351ca6d470
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664511"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Escolha a opção de servidor MariaDB correta no Azure

Com o Azure, suas cargas de trabalho do servidor MariaDB podem ser executadas em uma infraestrutura de máquina virtual hospedada como um serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem várias opções de implantação e há camadas de serviço dentro de cada opção de implantação. Ao escolher entre IaaS e PaaS você precisa decidir se deseja gerenciar o banco de dados, aplicar patches e fazer backups ou se deseja delegar essas operações ao Azure.

Ao tomar sua decisão, considere as duas opções a seguir:

- **Banco de dados do Azure para MariaDB:** Essa opção é um mecanismo de banco de dados MariaDB totalmente gerenciado com base na versão estável do MariaDB Community Edition. Esse DBaaS (banco de dados relacional como serviço), hospedado na plataforma de nuvem do Azure, se enquadra na categoria do setor de PaaS.

  Com uma instância gerenciada do MariaDB no Azure, você pode usar recursos internos que, caso contrário, exigem uma configuração extensiva quando o servidor MariaDB está no local ou em uma VM do Azure.

  Ao usar o MariaDB como um serviço, você paga conforme o uso das opções para escalar verticalmente ou escalar horizontalmente para maior controle sem interrupção. E, diferentemente do MariaDB Server autônomo, o banco de dados do Azure para MariaDB tem recursos adicionais, como alta disponibilidade, inteligência e gerenciamento internos.

- **MariaDB em VMs do Azure:** Essa opção se encaixa na categoria do setor de IaaS. Com esse serviço, você pode executar o MariaDB Server dentro de uma máquina virtual totalmente gerenciada na plataforma de nuvem do Azure. Todas as versões e edições recentes do MariaDB podem ser instaladas em uma máquina virtual IaaS.

  Na diferença mais significativa do banco de dados do Azure para MariaDB, o MariaDB em VMs do Azure oferece controle sobre o mecanismo de banco de dados. No entanto, esse controle é acompanhado da responsabilidade de gerenciar as VMs e muitas tarefas de DBA (administração de banco de dados). Essas tarefas incluem manutenção e aplicação de patches de servidores de banco de dados, recuperação de banco de dados e design de alta disponibilidade.

As diferenças principais entre essas opções estão listadas na tabela a seguir:

| Atributo          | Banco de Dados do Azure para MariaDB | MariaDB em VMs do Azure    |
|:-------------------|:-----------------------------|:--------------------|
| SLA (Contrato de Nível de Serviço)                | Oferece SLA de 99,99% de disponibilidade| Até 99,95% de disponibilidade com duas ou mais instâncias no mesmo conjunto de disponibilidade.<br/><br/>99,9% de disponibilidade com uma única VM de instância usando o armazenamento Premium.<br/><br/>99,99% usando Zonas de Disponibilidade com várias instâncias em vários conjuntos de disponibilidade.<br/><br/>Consulte o [SLA de máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Aplicação de patch do sistema operacional        | Automática  | Gerenciado por clientes |
| Aplicação de patch MariaDB     | Automática  | Gerenciado por clientes |
| Alta disponibilidade | O modelo de alta disponibilidade (HA) é baseado em mecanismos de failover internos para quando ocorre uma interrupção no nível do nó. Nesses casos, o serviço cria automaticamente uma nova instância e anexa o armazenamento a essa instância. | Os clientes arquitetam, implementam, testam e mantêm a alta disponibilidade. Os recursos podem incluir clustering de failover Always on, replicação de grupo Always on, envio de logs ou replicação transacional.|
| Redundância de zona | Não há suporte no momento | As VMs do Azure podem ser configuradas para serem executadas em diferentes zonas de disponibilidade. Para uma solução local, os clientes precisam criar, gerenciar e manter o próprio data center secundário.|
| Cenários híbridos | Com [replicação de dados](concepts-data-in-replication.md), você pode sincronizar dados de um servidor MariaDB externo no banco de dados do Azure para o serviço MariaDB. O servidor externo pode ser local, em máquinas virtuais ou um serviço de banco de dados hospedado por outros provedores de nuvem.<br/><br/> Com o recurso de [réplica de leitura](concepts-read-replicas.md) , você pode replicar dados de um servidor de origem do Azure para MariaDB para até cinco servidores de réplica somente leitura. As réplicas estão dentro da mesma região do Azure ou entre regiões. Réplicas somente leitura são atualizadas de forma assíncrona usando a tecnologia de replicação binlog.<br/><br/>A replicação de leitura entre regiões está atualmente em visualização pública.| Gerenciado por clientes
| Backup e restauração | O cria automaticamente [backups de servidor](concepts-backup.md#backups) e os armazena em um armazenamento configurado pelo usuário que seja localmente redundante ou com redundância geográfica. O serviço usa backups completos, diferenciais e de log de transações | Gerenciado por clientes |
| Operações de banco de dados de monitoramento | Oferece aos clientes a capacidade de [definir alertas](concepts-monitoring.md) na operação de banco de dados e agir sobre o alcance de limites. | Gerenciado por clientes |
| Proteção Avançada contra Ameaças | Fornece [proteção avançada contra ameaças](howto-database-threat-protection-portal.md). Essa proteção detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.<br/><br/>A proteção avançada contra ameaças está atualmente em visualização pública.| Os clientes devem criar essa proteção para si mesmos.
| Recuperação de desastre | Armazena backups automatizados em armazenamento com redundância [local ou](howto-restore-server-portal.md)com redundância geográfica configurado pelo usuário. Os backups também podem restaurar um servidor para um ponto no tempo. O período de retenção é de 7 a 35 dias. A restauração é realizada usando o portal do Azure. | Totalmente gerenciado pelos clientes. As responsabilidades incluem, mas não se limitam a agendamento, teste, arquivamento, armazenamento e retenção. Uma opção adicional é usar um cofre dos serviços de recuperação do Azure para fazer backup de VMs e bancos de dados do Azure em VMs. Esta opção está em visualização. |
| Recomendações de desempenho | Fornece aos clientes [recomendações de desempenho](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) com base em arquivos de log de uso gerados pelo sistema. As recomendações ajudam a otimizar as cargas de trabalho.<br/><br/>As recomendações de desempenho estão atualmente em visualização pública. | Gerenciado por clientes |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações de negócios para escolher PaaS ou IaaS

Há vários fatores que podem influenciar sua decisão de escolher PaaS ou IaaS para hospedar seus bancos de dados do MariaDB.

### <a name="cost"></a>Cost

O financiamento limitado geralmente é a principal consideração que determina a melhor solução para hospedar seus bancos de dados. Isso é verdadeiro tanto se você for uma startup com pouco dinheiro como uma equipe em uma empresa estabelecida que opere sob restrições de orçamento rígidas. Esta seção descreve as noções básicas de cobrança e licenciamento no Azure, pois elas se aplicam ao banco de dados do Azure para MariaDB e MariaDB em VMs do Azure.

#### <a name="billing"></a>Cobrança

O banco de dados do Azure para MariaDB está disponível atualmente como um serviço em várias camadas com preços diferentes para os recursos. Todos os recursos são cobrados por hora a uma taxa fixa. Para obter as informações mais recentes sobre as camadas de serviço, tamanhos de computação e valores de armazenamento com suporte no momento, consulte [modelo de compra baseado em vCore](concepts-pricing-tiers.md). Você pode ajustar dinamicamente as camadas de serviço e os tamanhos de computação para corresponder às necessidades de taxa de transferência variadas do seu aplicativo. Você será cobrado pelo tráfego de Internet de saída em [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares.

Com o banco de dados do Azure para MariaDB, a Microsoft configura, corrige e atualiza automaticamente o software de banco de dados. Essas ações automatizadas reduzem os custos de administração. Além disso, o banco de dados do Azure para MariaDB tem recursos de [backup internos](concepts-backup.md) . Essas funcionalidades ajudam você a obter economia significativa, principalmente quando você tem um grande número de bancos de dados. Por outro lado, com o MariaDB nas VMs do Azure, você pode escolher e executar qualquer versão do MariaDB. Não importa qual versão do MariaDB você usa, você paga pela VM provisionada e os custos para o tipo de licença MariaDB específico usado.

O banco de dados do Azure para MariaDB fornece alta disponibilidade interna para qualquer tipo de interrupção no nível de nó e, ao mesmo tempo, mantém a garantia de 99,99% de SLA para o serviço. No entanto, para alta disponibilidade de banco de dados nas VMs, os clientes devem usar as opções de alta disponibilidade como [replicação MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/) que estão disponíveis em um banco de dados MariaDB. Usar uma opção de alta disponibilidade com suporte não fornece um SLA adicional. Mas permite que você obtenha mais de 99,99% de disponibilidade de banco de dados com custo adicional e sobrecarga administrativa.

Para obter mais informações sobre preços, confira os seguintes artigos:
* [Preço do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Preço da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a redução da complexidade de administração é tão importante na decisão de fazer a transição para um serviço de nuvem quanto o custo. Com IaaS e PaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Replica automaticamente todos os dados para fornecer recuperação de desastre.
- Configura e atualiza o software de banco de dados.
- Gerencia o balanceamento de carga.
- O failover transparente se houver uma falha no servidor.

A lista a seguir descreve as considerações administrativas para cada opção:

* Com o banco de dados do Azure para MariaDB, você pode continuar a administrar seu banco de dados. Mas você não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional ou o hardware. Exemplos de itens que você pode continuar a administrar incluem:

  - Bancos de dados
  - Conexão
  - Ajuste do índice
  - Ajuste de consulta
  - Auditoria
  - Segurança

  Além disso, a configuração da alta disponibilidade para outro data center requer pouca ou nenhuma administração e configuração.

* Com o MariaDB em VMs do Azure, você tem controle total sobre o sistema operacional e a configuração da instância do servidor MariaDB. Com uma VM, você decide quando atualizar ou atualizar o sistema operacional e o software de banco de dados. Você também decide quando instalar qualquer software adicional, como um aplicativo antivírus. Alguns recursos automatizados são fornecidos para simplificar muito a aplicação de patches, backup e alta disponibilidade. Você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos. Para obter mais informações, confira [Tamanhos da máquina virtual e do serviço de nuvem para o Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Tempo para mover para o Azure

* O banco de dados do Azure para MariaDB é a solução certa para aplicativos projetados para a nuvem quando a produtividade do desenvolvedor e o rápido tempo de colocação no mercado para novas soluções são essenciais. Com a funcionalidade de programação como DBA, o serviço é adequado para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes.

* Quando você quiser evitar o tempo e as despesas de adquirir um novo hardware local, o MariaDB nas VMs do Azure é a solução certa para aplicativos que exigem um banco de dados MariaDB ou acesso aos recursos do MariaDB no Windows ou no Linux. Essa solução também é adequada para migrar os aplicativos locais e bancos de dados existentes para o Azure intactos, para casos em que o banco de dados do Azure para MariaDB é uma boa opção.

  Como não há necessidade de alterar as camadas de apresentação, aplicativo e dados, você economiza tempo e orçamento na rearquitetura de sua solução existente. Em vez disso, você pode se concentrar em migrar todas as suas soluções para o Azure e abordar algumas otimizações de desempenho que a plataforma do Azure pode exigir.

## <a name="next-steps"></a>Próximas etapas

* Consulte o [banco de dados do Azure para obter o preço do MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/).
* Comece com a [criação do seu primeiro servidor](quickstart-create-mariadb-server-database-using-azure-portal.md).
