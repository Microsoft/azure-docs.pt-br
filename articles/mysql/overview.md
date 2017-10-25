---
title: "Visão geral do serviço de banco de dados relacional Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Visão geral do serviço de banco de dados relacional Banco de Dados do Azure para MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.custom: mvc
ms.openlocfilehash: 3eff97a9e7568566cd01a4eb3c7ee66806eac4cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-database-for-mysql-service-introduction"></a>O que é o Banco de Dados do Azure para MySQL? Introdução ao serviço
O Banco de Dados do Azure para MySQL é um serviço de banco de dados relacional no Microsoft Cloud, baseado no mecanismo de banco de dados [MySQL Community Edition](https://www.mysql.com/products/community/).  O Banco de Dados do Azure para MySQL fornece:

- Desempenho previsível em vários níveis de serviço.
- Escalabilidade dinâmica sem tempo de inatividade do aplicativo.
- Alta disponibilidade interna.
- Proteção de dados.

Esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e em acelerar seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos ao gerenciamento de máquinas virtuais e de infraestrutura. Além disso, você pode continuar desenvolvendo seu aplicativo com a plataforma e as ferramentas de software livre de sua escolha e pode fornecê-lo com a velocidade e a eficiência que sua empresa exige, tudo isso sem precisar aprender novas habilidades.

![Diagrama conceitual do Banco de Dados do Azure para MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Este artigo é uma introdução aos principais conceitos e recursos do Banco de Dados do Azure para MySQL relacionados a desempenho, escalabilidade e gerenciabilidade, com links para explorar os detalhes. Consulte estes inícios rápidos para começar:
- [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar um servidor de Banco de Dados do Azure para MySQL usando a CLI do Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Para ver diversos exemplos da CLI do Azure, consulte:
- [Exemplos da CLI do Azure para o Banco de Dados do Azure para MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste de desempenho e dimensionamento sem tempo de inatividade
O serviço de Banco de Dados do Azure para MySQL oferece duas camadas de serviço: Básico e Standard. Cada camada oferece diferentes níveis de desempenho e recursos para dar suporte a cargas de trabalho de banco de dados leves e pesadas. Você pode compilar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e, em seguida, alterar a camada de serviço e dimensioná-la de acordo com as necessidades da solução, sem tempo de inatividade. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Como você sabe o momento certo de parar ao fazer o ajuste? Use os recursos internos de alerta e monitoramento de desempenho, em conjunto com as classificações de desempenho baseadas na Unidade de computação. Usando essas ferramentas, é possível avaliar rapidamente o impacto da expansão ou redução da escala com base nas suas necessidades de desempenho atuais ou previstas. Consulte [Conceitos: camadas de serviço](concepts-service-tiers.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios
O SLA (Contrato de Nível de Serviço) de disponibilidade de 99,99% do Azure, que é líder do setor e é alimentado por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução de forma ininterrupta. Com cada servidor do Banco de Dados do Azure para MySQL, você tira proveito dos recursos internos de segurança, tolerância a falhas e proteção de dados que, em outras situações, seria necessário comprar ou projetar, criar e gerenciar. Com o Banco de Dados do Azure para MySQL, você pode usar a restauração pontual para recuperar um servidor para um estado anterior, com alcance de até 35 dias.

## <a name="secure-your-data"></a>Proteja seus dados
Os serviços de banco de dados do Azure têm uma tradição de segurança de dados que o Banco de Dados do Azure para MySQL mantém, com recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam a monitorar atividades. Visite a [Central de Confiabilidade do Azure](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) para obter informações sobre a segurança da plataforma do Azure.

O serviço Banco de Dados do Azure para MySQL usa a criptografia de armazenamento para dados em repouso. Os dados incluindo backups são criptografados no disco (com exceção dos arquivos temporários que são criados pelo mecanismo durante a execução de consultas). O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

Por padrão, o serviço Banco de Dados do Azure para MySQL está configurado para exigir a [segurança da conexão SSL](./concepts-ssl-connection-security.md) para dados em movimento em toda a rede. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.  Como opção, você pode desabilitar a exigência de SSL para conectar ao seu serviço de banco de dados, se seu aplicativo cliente não oferecer suporte à conectividade SSL.

## <a name="next-steps"></a>Próximas etapas
Agora que leu uma introdução ao Banco de Dados do Azure para MySQL e respondeu à pergunta "O que é o Banco de Dados do Azure para MySQL?", você está pronto para:
- Consultar a página de preços para ver comparações de custo e calculadoras. [Preços](https://azure.microsoft.com/pricing/details/mysql/)
- Comece com a criação do seu primeiro servidor. [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Crie seu primeiro aplicativo no Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conectividade usadas para se conectar ao Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)
