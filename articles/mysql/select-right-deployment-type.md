---
title: Selecionando o tipo de implantação certo-banco de dados do Azure para MySQL
description: Este artigo descreve quais fatores devem ser considerados antes de implantar o banco de dados do Azure para MySQL como IaaS (infraestrutura como serviço) ou PaaS (plataforma como um serviço).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 125431e6630ccfdd9e0e5d6b2a4ec5fa9b9e58fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736178"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Escolha a opção de servidor MySQL correta no Azure

Com o Azure, suas cargas de trabalho de servidor MySQL podem ser executadas em uma infraestrutura de máquina virtual hospedada como um serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem várias opções de implantação e há camadas de serviço dentro de cada opção de implantação. Ao escolher entre IaaS e PaaS você precisa decidir se deseja gerenciar o banco de dados, aplicar patches e fazer backups ou se deseja delegar essas operações ao Azure.

Ao tomar sua decisão, considere as duas opções a seguir:

- **Banco de dados do Azure para MySQL**. Essa opção é um mecanismo de banco de dados MySQL totalmente gerenciado com base na versão estável do MySQL Community Edition. Esse DBaaS (banco de dados relacional como serviço), hospedado na plataforma de nuvem do Azure, se enquadra na categoria do setor de PaaS.

  Com uma instância gerenciada do MySQL no Azure, você pode usar recursos internos aula sobre visualização aplicação de patch automatizada, alta disponibilidade, backups automatizados, dimensionamento elástico, segurança de nível empresarial, conformidade e governança, monitoramento e alertas que, caso contrário, exigem uma configuração extensiva quando o servidor MySQL está no local ou em uma VM do Azure. Ao usar o MySQL como um serviço, você paga conforme o uso, com opções para escalar verticalmente ou escalar horizontalmente para maior controle sem interrupção.
  
  O [banco de dados do Azure para MySQL](overview.md), da plataforma MySQL Community Edition, está disponível em dois modos de implantação:

  - O [servidor único](single-server-overview.md) é um serviço de banco de dados totalmente gerenciado com requisitos mínimos para personalizações do banco de dados. A plataforma de servidor único é projetada para administrar a maioria das funções de gerenciamento de banco de dados, como aplicação de patches, backups, alta disponibilidade, controle e segurança com mínima configuração do usuário. A arquitetura é otimizada para fornecer 99,99% de disponibilidade em uma única zona de disponibilidade. Servidores únicos são mais adequados para aplicativos nativos na nuvem projetados para lidar com aplicação de patch automatizada sem a necessidade de controle granular sobre o agendamento de aplicação de patch e definições de configuração de MySQL personalizadas.

  - O [servidor flexível (visualização)](flexible-server/overview.md) é um serviço de banco de dados totalmente gerenciado projetado para fornecer controle e flexibilidade mais granulares sobre as funções de gerenciamento de banco de dados e definições de configuração. Em geral, o serviço fornece mais flexibilidade e personalizações de configuração do servidor em comparação com a implantação de servidor único com base nos requisitos do usuário. A arquitetura de servidor flexível permite que os usuários optem por alta disponibilidade em uma única zona de disponibilidade e em várias zonas de disponibilidade. Os servidores flexíveis também fornecem melhores controles de otimização de custos com a capacidade de iniciar/parar o servidor e SKUs expansíveis, ideal para cargas de trabalho que não precisam de capacidade de computação completa continuamente.

  Os servidores flexíveis são mais adequados para:

  - Desenvolvimento de aplicativos que exigem melhor controle e personalizações do mecanismo MySQL.
  - Alta disponibilidade com redundância de zona
  - Janelas de manutenção gerenciadas

- **MySQL em VMs do Azure**. Essa opção se encaixa na categoria do setor de IaaS. Com esse serviço, você pode executar o MySQL Server dentro de uma máquina virtual gerenciada na plataforma de nuvem do Azure. Todas as versões e edições recentes do MySQL podem ser instaladas na máquina virtual.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Comparando as opções de implantação do MySQL no Azure

As diferenças principais entre essas opções estão listadas na tabela a seguir:

| Atributo          | Banco de Dados do Azure para MySQL<br/>Servidor único |Banco de Dados do Azure para MySQL<br/>Servidor Flexível  |MySQL em VMs do Azure                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| Suporte à versão do MySQL | 5,6, 5,7 & 8,0| 5,7 & 8,0 | Qualquer versão|
| Dimensionamento de computação | Com suporte (não há suporte para dimensionar de e para a camada básica)| Com suporte | Com suporte|
| Tamanho de armazenamento | 5 GiB a 16 TiB| 5 GiB a 16 TiB | 32 GiB a 32.767 GiB|
| Dimensionamento de armazenamento online | Com suporte| Com suporte| Sem suporte|
| Dimensionamento automático do armazenamento | Com suporte| Sem suporte na visualização| Sem suporte|
| Escala de IOPs adicional | Sem suporte| Com suporte| Sem suporte|
| Conectividade de rede | -Pontos de extremidade públicos com o Firewall do servidor.<br/> -Acesso privado com suporte a link privado.|-Pontos de extremidade públicos com o Firewall do servidor.<br/> -Acesso privado com integração de rede virtual.| -Pontos de extremidade públicos com o Firewall do servidor.<br/> -Acesso privado com suporte a link privado.|
| SLA (Contrato de Nível de Serviço) | SLA de disponibilidade de 99,99% |Nenhum SLA na versão prévia| 99,99% usando Zonas de Disponibilidade|
| Aplicação de patch do sistema operacional| Automática  | Automático com controle de janela de manutenção personalizada | Gerenciado por usuários finais |
| Aplicação de patch do MySQL     | Automática  | Automático com controle de janela de manutenção personalizada | Gerenciado por usuários finais |
| Alta disponibilidade | HA interna dentro da zona de disponibilidade única| HA interna dentro e entre zonas de disponibilidade | Gerenciados personalizados usando clustering, replicação, etc.|
| Redundância de zona | Sem suporte | Com suporte | Com suporte|
| Posicionamento da zona | Sem suporte | Com suporte | Com suporte|
| Cenários híbridos | Com suporte com [replicação de dados](./concepts-data-in-replication.md)| Não disponível no modo visualização | Gerenciado por usuários finais |
| Réplicas de leitura | Com suporte (até 5 réplicas)| Com suporte (até 10 réplicas)| Gerenciado por usuários finais |
| Backup | Automatizado com retenção de 7-35 dias | Automatizado com retenção de 1-35 dias | Gerenciado por usuários finais |
| Operações de banco de dados de monitoramento | Com suporte | Com suporte | Gerenciado por usuários finais |
| Recuperação de desastre | Com suporte com armazenamento de backup com redundância geográfica e réplicas de leitura entre regiões | Sem suporte na visualização| Gerenciado personalizado com tecnologias de replicação |
| Insights de Desempenho de Consulta | Com suporte | Não disponível no modo visualização| Gerenciado por usuários finais |
| Preço de Instância Reservada | Com suporte | Não disponível no modo visualização | Com suporte |
| Autenticação do Azure AD | Com suporte | Não disponível no modo visualização | Sem suporte|
| Criptografia de dados em repouso | Com suporte com chaves gerenciadas pelo cliente | Com suporte com chaves gerenciadas pelo serviço | Sem suporte|
| SSL/TLS | Habilitado por padrão com suporte para TLS v 1.2, 1,1 e 1,0 | Imposto com TLS v 1.2 | Com suporte com TLS v 1.2, 1,1 e 1,0 |
| Gerenciamento de frota | Com suporte com CLI do Azure, PowerShell, REST e Azure Resource Manager | Com suporte com CLI do Azure, PowerShell, REST e Azure Resource Manager  | Com suporte para VMs com CLI do Azure, PowerShell, REST e Azure Resource Manager |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações de negócios para escolher PaaS ou IaaS

Há vários fatores que podem influenciar sua decisão de escolher PaaS ou IaaS para hospedar seus bancos de dados MySQL.

### <a name="cost"></a>Cost

A redução de custos geralmente é a principal consideração que determina a melhor solução para hospedar seus bancos de dados. Isso é verdadeiro tanto se você for uma startup com pouco dinheiro como uma equipe em uma empresa estabelecida que opere sob restrições de orçamento rígidas. Esta seção descreve as noções básicas de cobrança e licenciamento no Azure, pois elas se aplicam ao banco de dados do Azure para MySQL e MySQL em VMs do Azure.

#### <a name="billing"></a>Cobrança

O banco de dados do Azure para MySQL está disponível atualmente como um serviço em várias camadas com preços diferentes para os recursos. Todos os recursos são cobrados por hora a uma taxa fixa. Para obter as informações mais recentes sobre as camadas de serviço, tamanhos de computação e valores de armazenamento com suporte no momento, consulte a [página de preços](https://azure.microsoft.com/pricing/details/mysql/). Você pode ajustar dinamicamente as camadas de serviço e os tamanhos de computação para corresponder às necessidades de taxa de transferência variadas do seu aplicativo. Você será cobrado pelo tráfego de Internet de saída em [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares.

Com o banco de dados do Azure para MySQL, a Microsoft configura, corrige e atualiza automaticamente o software de banco de dados. Essas ações automatizadas reduzem os custos de administração. Além disso, o banco de dados do Azure para MySQL tem recursos de [backups automatizados](./concepts-backup.md) . Essas funcionalidades ajudam você a obter economia significativa, principalmente quando você tem um grande número de bancos de dados. Por outro lado, com o MySQL em VMs do Azure, você pode escolher e executar qualquer versão do MySQL. Independentemente da versão do MySQL que você usa, você paga pela VM provisionada, pelo custo de armazenamento associado aos dados, backup, monitoramento de dados e armazenamento de log e os custos para o tipo de licença MySQL específico usado (se houver).

O banco de dados do Azure para MySQL fornece alta disponibilidade interna para qualquer tipo de interrupção no nível de nó e, ao mesmo tempo, mantém a garantia de 99,99% de SLA para o serviço. No entanto, para alta disponibilidade do banco de dados nas VMs, você usa as opções de alta disponibilidade, como [replicação do MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) que estão disponíveis em um banco de dados MySQL. Usar uma opção de alta disponibilidade com suporte não fornece um SLA adicional. Mas permite que você obtenha mais de 99,99% de disponibilidade de banco de dados com custo adicional e sobrecarga administrativa.

Para obter mais informações sobre preços, confira os seguintes artigos:

- [Preço do banco de dados do Azure para MySQL](https://azure.microsoft.com/pricing/details/mysql/)
- [Preço da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a redução da complexidade de administração é tão importante na decisão de fazer a transição para um serviço de nuvem quanto o custo.

Com a IaaS, a Microsoft:

- Administra a infraestrutura subjacente.
- Fornece aplicação de patch automatizada para o hardware e o sistema operacional subjacentes.
  
Com a PaaS, a Microsoft:

- Administra a infraestrutura subjacente.
- Fornece aplicação de patch automatizada para o hardware, o sistema operacional e o mecanismo de banco de dados subjacentes.
- Gerencia a alta disponibilidade do banco de dados.
- Executa backups automaticamente e replica todos os dados para fornecer recuperação de desastre.
- Criptografa os dados em repouso e em movimento por padrão.
- Monitora o servidor e fornece recursos para informações de desempenho de consulta e recomendações de desempenho

A lista a seguir descreve as considerações administrativas para cada opção:

- Com o banco de dados do Azure para MySQL, você pode continuar a administrar seu banco de dados. Mas você não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional ou o hardware. Exemplos de itens que você pode continuar a administrar incluem:

  - Bancos de dados
  - Conexão
  - Ajuste do índice
  - Ajuste de consulta
  - Auditoria
  - Segurança

  Além disso, a configuração da alta disponibilidade para outro data center requer pouca ou nenhuma administração e configuração.

- Com o MySQL em VMs do Azure, você tem controle total sobre o sistema operacional e a configuração da instância do MySQL Server. Com uma VM, você decide quando atualizar ou fazer upgrade do sistema operacional e do software de banco de dados e quais patches aplicar. Você também decide quando instalar qualquer software adicional, como um aplicativo antivírus. Alguns recursos automatizados são fornecidos para simplificar muito a aplicação de patches, backup e alta disponibilidade. Você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos. Para obter mais informações, confira [Tamanhos da máquina virtual e do serviço de nuvem para o Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Tempo para mover para o Azure

- O banco de dados do Azure para MySQL é a solução certa para aplicativos projetados para a nuvem quando a produtividade do desenvolvedor e o rápido tempo de colocação no mercado para novas soluções são essenciais. Com a funcionalidade de programação como DBA, o serviço é adequado para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes.

- Quando você quiser evitar o tempo e as despesas de adquirir um novo hardware local, o MySQL em VMs do Azure é a solução certa para aplicativos que exigem um controle granular e a personalização do mecanismo do MySQL sem suporte do serviço nem a exigência do acesso do sistema operacional subjacente. Essa solução também é adequada para migrar os aplicativos locais e bancos de dados existentes para o Azure intactos, para casos em que o banco de dados do Azure para MySQL é uma boa opção.

Como não há necessidade de alterar as camadas de apresentação, aplicativo e dados, você economiza tempo e orçamento na rearquitetura de sua solução existente. Em vez disso, você pode se concentrar em migrar todas as suas soluções para o Azure e abordar algumas otimizações de desempenho que a plataforma do Azure pode exigir.

## <a name="next-steps"></a>Próximas etapas

- Consulte [preços do banco de dados do Azure para MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
- Comece com a [criação do seu primeiro servidor](./quickstart-create-mysql-server-database-using-azure-portal.md).
