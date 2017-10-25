---
title: "Visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Fornece uma visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 08/01/2017
ms.openlocfilehash: 0dba4db0ae62a4f7e8440e80e830e5f64f5b49e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é o Banco de Dados do Azure para PostgreSQL?

O Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados relacional no Microsoft Cloud, projetado para desenvolvedores com base na versão de comunidade do mecanismo de banco de dados [PostgreSQL](https://www.postgresql.org/) de software livre. Esse serviço está na fase de visualização pública. O Banco de Dados do Azure para PostgreSQL fornece:
- Desempenho previsível em vários níveis de serviço
- Escalabilidade dinâmica sem tempo de inatividade do aplicativo
- Alta disponibilidade interna
- Proteção de dados

Todos esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Esses recursos permitem que você se concentre no método RAD e acelere seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos para gerenciamento de máquinas virtuais e infraestrutura. Além disso, você pode continuar desenvolvendo seu aplicativo com a plataforma e as ferramentas de software livre de sua escolha e pode fornecê-lo com a velocidade e a eficiência que sua empresa exige, sem precisar aprender novas habilidades. 

Este artigo é uma introdução aos principais conceitos e recursos do Banco de Dados do Azure para PostgreSQL relacionados a desempenho, escalabilidade e gerenciabilidade. Consulte estes inícios rápidos para começar:

- [Criar um servidor de Banco de Dados do Azure para PostgreSQL usando o portal do Azure](quickstart-create-server-database-portal.md)
- [Criar um servidor de Banco de Dados do Azure para PostgreSQL usando a CLI Azure](quickstart-create-server-database-azure-cli.md)

Para ver vários exemplos da CLI do Azure e do PowerShell, consulte:

- [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste de desempenho e dimensionamento sem tempo de inatividade

O serviço de Banco de Dados do Azure para PostgreSQL atualmente oferece duas camadas de serviço: Básico e Standard. Cada camada de serviço oferece [diferentes níveis de desempenho, garantias de IOPS e recursos](concepts-service-tiers.md) para dar suporte a cargas de trabalho leves e pesadas de banco de dados. Você pode criar seu primeiro aplicativo em um servidor pequeno por alguns dólares por mês e, em seguida, [alterar o nível de desempenho](scripts/sample-scale-server-up-or-down.md) na camada de serviço manualmente ou por meio de programação a qualquer momento para atender às necessidades de sua solução. Você pode fazer isso sem tempo de inatividade para seu aplicativo ou seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Como você decide quando aumentar e reduzir? Use os recursos internos de alerta e monitoramento de desempenho, em conjunto com as classificações de desempenho baseadas na Unidade de computação. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução das Unidades de computação com base nas suas necessidades de desempenho atuais ou previstas. Para obter detalhes, consulte [Opções e desempenho do Banco de Dados do Azure para PostgreSQL: compreender o que está disponível em cada camada de serviço](./concepts-service-tiers.md).

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios
O SLA (Contrato de Nível de Serviço) de disponibilidade de 99,99% do Azure (indisponível na versão prévia), que é líder do setor e é alimentado por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução de forma ininterrupta. Com cada servidor do Banco de Dados do Azure para PostgreSQL, você tira proveito dos recursos internos de segurança, tolerância a falhas e proteção de dados que, em outras situações, seria necessário comprar ou projetar, criar e gerenciar. Com o Banco de Dados do Azure para PostgreSQL, cada camada de serviço oferece um conjunto abrangente de opções e recursos de continuidade de negócios que você pode usar para voltar à execução e permanecer assim. Você pode usar a [restauração para um ponto específico](howto-restore-server-portal.md) para retornar um banco de dados para um estado anterior, até 35 dias. Além disso, se o datacenter que hospeda seus bancos de dados sofrer uma interrupção, você poderá restaurar os bancos de dados de cópias de backups recentes com redundância geográfica.

## <a name="secure-your-data"></a>Proteja seus dados
Os serviços de banco de dados do Azure têm uma tradição de segurança de dados que o Banco de Dados do Azure para PostgreSQL mantém, com recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam a monitorar atividades. Visite a [Central de Confiabilidade do Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx) para obter informações sobre a segurança da plataforma do Azure.

O serviço Banco de Dados do Azure para PostgreSQL usa a criptografia de armazenamento para dados em repouso. Os dados incluindo backups são criptografados no disco (com exceção dos arquivos temporários criados pelo mecanismo durante a execução de consultas). O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

Por padrão, o serviço Banco de Dados do Azure para PostgreSQL está configurado para exigir a [segurança da conexão SSL](./concepts-ssl-connection-security.md) para dados em movimento em toda a rede. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.  Como opção, você pode desabilitar a exigência de SSL para conectar ao seu serviço de banco de dados, se seu aplicativo cliente não oferecer suporte à conectividade SSL.

## <a name="next-steps"></a>Próximas etapas
- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparações de custo e calculadoras.
- Comece pela [criação do seu primeiro Banco de Dados do Azure para PostgreSQL](./quickstart-create-server-database-portal.md).
- Crie seu primeiro aplicativo no Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conexão](./concepts-connection-libraries.md)
