---
title: Escolher a opção certa do servidor PostgreSQL no Azure
description: Fornece diretrizes para escolher a opção do servidor PostgreSQL adequada para as suas implantações.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797240"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Escolher a opção certa do servidor PostgreSQL no Azure

Com o Azure, as suas cargas de trabalho do Servidor PostgreSQL podem ser executadas como uma PaaS (plataforma como serviço) hospedada ou em uma IaaS (infraestrutura como serviço) de máquina virtual hospedada. A PaaS tem várias opções de implantação, cada uma com várias camadas de serviço. Ao escolher entre IaaS e PaaS você precisa decidir se deseja gerenciar o banco de dados, aplicar patches e fazer backups ou se deseja delegar essas operações ao Azure.

Ao tomar essa decisão, considere as três opções a seguir na PaaS ou, como alternativa, em execução nas VMs do Azure (IaaS)
- [Servidor Único do banco de dados do Azure para PostgreSQL](./overview-single-server.md)
- [Servidor Flexível do banco de dados do Azure para PostgreSQL](./flexible-server/overview.md)
- [Hiperescala (Citus) do banco de dados do Azure para PostgreSQL](hyperscale/index.yml)

A opção **PostgreSQL em VMs do Azure** se enquadra na categoria do setor de IaaS. Com esse serviço, você pode executar o Servidor PostgreSQL dentro de uma máquina virtual totalmente gerenciada na plataforma de nuvem do Azure. Todas as versões e edições recentes do PostgreSQL podem ser instaladas em uma máquina virtual IaaS. A diferença mais significativa do Banco de Dados do Azure para PostgreSQL é que o PostgreSQL em VMs do Azure oferece controle sobre o mecanismo de banco de dados. No entanto, esse controle é acompanhado da responsabilidade de gerenciar as VMs e muitas tarefas de DBA (administração de banco de dados). Essas tarefas incluem manutenção e aplicação de patches de servidores de banco de dados, recuperação de banco de dados e design de alta disponibilidade.

As diferenças principais entre essas opções estão listadas na tabela a seguir:

| **Atributo** | **Postgres em VMs do Azure** | **PostgreSQL como PaaS** |
| ----- | ----- | ----- |
| <B> SLA de Disponibilidade |– 99,99% com conjuntos de disponibilidade <br> – 99,95% com VMs únicas | – Servidor Único – 99,99% <br> – Servidor Flexível – Indisponível durante a Versão Prévia <br> – Hiperescala (Citus) – 99,95% (quando a alta disponibilidade está habilitada)|
| <B> Aplicação de patch no sistema operacional e no PostgreSQL | – Gerenciado pelo cliente | – Servidor Único – Automático <br> – Servidor Flexível – Automático com janela gerenciada opcional do cliente <br> – Hiperescala (Citus) – Automático |
| <B> Alta disponibilidade | – Os clientes arquitetam, implementam, testam e mantêm a alta disponibilidade. As funcionalidades podem incluir clustering, replicação etc. | – Servidor Único: interno <br> – Servidor Flexível: interno <br> – Hiperescala (Citus): criado com o modo em espera |
| <B> Redundância de Zona | – As VMs do Azure podem ser configuradas para serem executadas em diferentes zonas de disponibilidade. Para uma solução local, os clientes precisam criar, gerenciar e manter o próprio data center secundário. | – Servidor Único: Não <br> – Servidor Flexível: Sim <br> – Hiperescala (Citus): Não |
| <B> Cenário Híbrido | – Gerenciado pelo cliente |– Servidor Único: réplica de leitura <br> – Servidor Flexível: indisponível durante a Versão Prévia <br> – Hiperescala (Citus): Não |
| <B> Backup e Restauração | – Gerenciado pelo Cliente | – Servidor Único: interno com a configuração do usuário para área geográfica e local <br> -Servidor Flexível: interno com a configuração do usuário no armazenamento com redundância de zona <br> – Hiperescala (Citus): interno |
| <B> Monitoramento de Operações de Banco de Dados | – Gerenciado pelo Cliente | – Servidor Único, Servidor Flexível e Hiperescala (Citus): todos oferecem aos clientes a capacidade de definir alertas na operação de banco de dados e agir quando os limites estiverem sendo alcançados. |
| <B> Proteção Avançada contra Ameaças | – Os clientes precisam criar essa proteção para eles mesmos. |– Servidor Único: Sim <br> – Servidor Flexível: indisponível durante a Versão Prévia <br> – Hiperescala (Citus): Não |
| <B> Recuperação de Desastre | – Gerenciado pelo Cliente | – Servidor Único: backup com redundância geográfica e réplica com leitura geográfica <br> – Servidor Flexível: indisponível durante a Versão Prévia <br> – Hiperescala (Citus): Não |
| <B> Desempenho Inteligente | – Gerenciado pelo Cliente | – Servidor Único: Sim <br> – Servidor Flexível: indisponível durante a Versão Prévia <br> – Hiperescala (Citus): Não |

## <a name="total-cost-of-ownership-tco"></a>TCO (custo total de propriedade)

Frequentemente, o TCO é principal consideração que determina a melhor solução para hospedar os seus bancos de dados. Isso é verdadeiro tanto se você for uma startup com pouco dinheiro como uma equipe em uma empresa estabelecida que opere sob restrições de orçamento rígidas. Esta seção descreve as noções básicas de cobrança e licenciamento no Azure, pois elas se aplicam ao Banco de Dados do Azure para PostgreSQL e ao PostgreSQL em VMs do Azure.

## <a name="billing"></a>Cobrança

O Banco de Dados do Azure para PostgreSQL está disponível atualmente como um serviço em vários níveis com preços diferentes para recursos. Todos os recursos são cobrados por hora a uma taxa fixa. Para obter as informações mais recentes sobre as camadas de serviço, tamanhos de computação e valores de armazenamento com suporte no momento, confira a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/server/). Você pode ajustar dinamicamente as camadas de serviço e os tamanhos de computação para corresponder às diversas necessidades de taxa de transferência do seu aplicativo. Você será cobrado pelo tráfego de Internet de saída em [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares.

Com o Banco de Dados do Azure para PostgreSQL, a Microsoft configura automaticamente, aplica patches e atualiza o software de banco de dados. Essas ações automatizadas reduzem os custos de administração. Além disso, o Banco de Dados do Azure para PostgreSQL tem funcionalidades de [link de backup automatizado](). Essas funcionalidades ajudam você a obter economia significativa, principalmente quando você tem um grande número de bancos de dados. Por outro lado, com o PostgreSQL em VMs do Azure, você pode escolher e executar qualquer versão do PostgreSQL. No entanto, você precisa pagar pela VM provisionada, pelo custo de armazenamento associado aos dados, backup, monitoramento de dados e armazenamento de logs, bem como os custos do tipo de licença do PostgreSQL específico usado (se houver).

O Banco de Dados do Azure para PostgreSQL fornece alta disponibilidade interna para qualquer tipo de interrupção no nível de nó e, ao mesmo tempo, mantém a garantia de 99,99% de SLA para o serviço. No entanto, para alta disponibilidade do banco de dados nas VMs, use as opções de alta disponibilidade como [Replicação de Streaming](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) que estão disponíveis em um banco de dados PostgreSQL. Usar uma opção de alta disponibilidade com suporte não fornece um SLA adicional. Mas permite que você obtenha mais de 99,99% de disponibilidade de banco de dados com custo adicional e sobrecarga administrativa.

Para obter mais informações sobre preços, confira os seguintes artigos:
- [Preço do Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Preço da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administração

Para muitas empresas, a redução da complexidade de administração é tão importante na decisão de fazer a transição para um serviço de nuvem quanto o custo.

Com a IaaS, a Microsoft:

- Administra a infraestrutura subjacente.
- Fornece aplicação de patch automatizada para o hardware e o sistema operacional subjacentes

Com a PaaS, a Microsoft:

- Administra a infraestrutura subjacente.
- Fornece a aplicação automatizada de patch do hardware, do sistema operacional e do mecanismo de banco de dados subjacentes.
- Gerencia a alta disponibilidade do banco de dados.
- Executa backups automaticamente e replica todos os dados para fornecer recuperação de desastre.
- Criptografa os dados em repouso e em movimento por padrão.
- Monitora o servidor e fornece recursos para insights de desempenho de consulta e recomendações de desempenho.

Com o Banco de Dados do Azure para PostgreSQL, você pode continuar a administrar o seu banco de dados. Mas você não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional ou o hardware. Exemplos de itens que você pode continuar a administrar incluem:

- Bancos de dados
- Conexão
- Ajuste do índice
- Ajuste de consulta
- Auditoria
- Segurança

Além disso, a configuração da alta disponibilidade para outro data center requer pouca ou nenhuma administração e configuração.

- Com o PostgreSQL nas VMs do Azure, você tem controle total sobre o sistema operacional e a configuração da instância do servidor PostgreSQL. Com uma VM, você decide quando atualizar ou fazer upgrade do sistema operacional e do software de banco de dados e quais patches aplicar. Você também decide quando instalar qualquer software adicional, como um aplicativo antivírus. Alguns recursos automatizados são fornecidos para simplificar muito a aplicação de patches, backup e alta disponibilidade. Você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos. Para obter mais informações, confira [Tamanhos da máquina virtual e do serviço de nuvem para o Azure](../virtual-machines/sizes.md).

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Tempo para mover para o Serviço PostgreSQL do Azure (PaaS)

- O Banco de Dados do Azure para PostgreSQL é a solução adequada para aplicativos projetados em nuvem quando a produtividade do desenvolvedor e um tempo de entrega rápido para novas soluções são fatores críticos. Com a funcionalidade de programação como DBA, o serviço é adequado para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes.

- Quando você quiser economizar tempo e as despesas de adquirir um novo hardware local, o PostgreSQL em VMs do Azure será a solução adequada para aplicativos que exigem um controle granular e cuja personalização do mecanismo PostgreSQL não tem suporte no serviço ou exige acesso do sistema operacional subjacente.

## <a name="next-steps"></a>Próximas etapas

- Confira o [Preço do Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).
- Comece com a criação do seu primeiro servidor.