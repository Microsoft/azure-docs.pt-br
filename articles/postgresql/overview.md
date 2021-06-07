---
title: O que é o Banco de Dados do Azure para PostgreSQL
description: Fornece uma visão geral do serviço de banco de dados relacional do Banco de Dados do Azure para PostgreSQL no contexto do servidor flexível.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92491318"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é o Banco de Dados do Azure para PostgreSQL?

O Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados relacional no Microsoft Cloud com base no mecanismo de banco de dados [PostgreSQL Community Edition](https://www.postgresql.org/) (disponível sob a licença GPLv2). O Banco de Dados do Azure para PostgreSQL fornece:

- Alta disponibilidade interna.
- Proteção de dados usando backups automáticos e restauração pontual por até 35 dias.
- Manutenção automatizada para hardware, sistema operacional e mecanismo de banco de dados subjacentes para manter o serviço seguro e atualizado.
- Desempenho previsível, com preços pré-pagos inclusivos.
- Dimensionamento elástico em segundos.
- Segurança de nível corporativo e conformidade líder do setor para proteger dados confidenciais em repouso e em movimento.
- Monitoramento e automação para simplificar o gerenciamento e o monitoramento para implantações em larga escala.
- Experiência de suporte líder do setor.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Banco de Dados do Azure para PostgreSQL":::

Esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Eles permitem que você se concentre no método RAD e em acelerar seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos ao gerenciamento de máquinas virtuais e de infraestrutura. Além disso, você pode continuar desenvolvendo seu aplicativo com a plataforma e as ferramentas de software livre de sua escolha e pode fornecê-lo com a velocidade e a eficiência que sua empresa exige, tudo isso sem precisar aprender novas habilidades.

## <a name="deployment-models"></a>Modelos de implantação

O Banco de Dados do Azure para PostgreSQL desenvolvido com o PostgreSQL community edition está disponível em três modos de implantação:

- Servidor único
- Servidor Flexível (versão prévia)
- Hiperescala (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Banco de Dados do Azure para PostgreSQL – Servidor único

O Banco de Dados do Azure para PostgreSQL Single Server é um serviço de banco de dados totalmente gerenciado com requisitos mínimos para personalizações do banco de dados. A plataforma de servidor único é projetada para administrar a maioria das funções de gerenciamento de banco de dados, como aplicação de patches, backups, alta disponibilidade, controle e segurança com mínima configuração do usuário. A arquitetura é otimizada para alta disponibilidade interna com disponibilidade de 99,99% em uma zona de disponibilidade única. Ela dá suporte à versão da comunidade do PostgreSQL 9.5, 9.6, 10 e 11. O serviço está em disponibilidade geral em diversas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/).

A opção de implantação Servidor Único oferece três tipos de preço: Básico, Uso Geral e Otimizado para Memória. Cada tipo oferece recursos diferentes para dar suporte a suas cargas de trabalho do banco de dados. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja [Tipos de preço](./concepts-pricing-tiers.md) para obter detalhes.

Servidores únicos são mais adequados para aplicativos nativos na nuvem projetados para lidar com aplicação de patch automatizada sem a necessidade de controle granular sobre o agendamento de aplicação de patch e definições de configuração de PostgreSQL personalizadas.

Para obter uma visão geral detalhada do modo de implantação de servidor único, confira [visão geral de servidor único](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Banco de Dados do Azure para PostgreSQL – Servidor Flexível (versão prévia)

O Servidor Flexível do Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados totalmente gerenciado projetado para fornecer controle e flexibilidade mais granulares nas funções de gerenciamento de banco de dados e definições de configuração. Em geral, o serviço fornece mais flexibilidade e personalizações conforme os requisitos do usuário. A arquitetura de servidor flexível permite que os usuários optem por alta disponibilidade dentro de uma zona de disponibilidade única e entre várias zonas de disponibilidade. O Servidor Flexível fornece controles de otimização de custo melhores com a capacidade de parar/iniciar o servidor e a camada de computação expansível, ideal para cargas de trabalho que não precisam de capacidade de computação completa continuamente. O serviço atualmente dá suporte à versão da comunidade do PostgreSQL 11 e 12 com planos para adicionar versões mais recentes em breve. O serviço está em versão prévia pública, disponível em diversas regiões do Azure.

Os servidores flexíveis são mais adequados para

- Desenvolvimentos de aplicativos que exigem controle e personalizações melhores.
- Controles de otimização de custos com capacidade de parar/iniciar o servidor.
- Alta disponibilidade com redundância de zona
- Janelas de manutenção gerenciadas
  
Para obter uma visão geral detalhada do modo de implantação de servidor flexível, confira [visão geral do servidor flexível](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)

A opção Hiperescala (Citus) escala horizontalmente as consultas em vários computadores usando a fragmentação. Seu mecanismo de consulta faz a correspondência entre consultas SQL recebidas nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados. Ele atende aplicativos que exigem maior escala e desempenho, geralmente cargas de trabalho que estão se aproximando, ou já excederam, 100 GB de dados.

A opção de implantação Hiperescala (Citus) oferece:

- Dimensionamento horizontal entre vários computadores usando a fragmentação
- Consultar a paralelização nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados
- Um excelente suporte para aplicativos multilocatário, análise operacional em tempo real e cargas de trabalho transacionais de alta taxa de transferência
  
Os aplicativos criados para o PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com [bibliotecas de conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os três modos de implantação para o Banco de Dados do Azure para PostgreSQL e escolha as opções corretas conforme suas necessidades.

- [Servidor único](./overview-single-server.md)
- [Servidor Flexível](./flexible-server/overview.md)
- Hiperescala (Citus)