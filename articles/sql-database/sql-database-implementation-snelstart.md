---
title: Estudo de caso sobre o Banco de Dados SQL do Azure - SnelStart | Microsoft Docs
description: "Saiba mais sobre como a SnelStart usa o Banco de Dados SQL para expandir rapidamente seus serviços comerciais a uma taxa de 1.000 novos Bancos de Dados SQL do Azure por mês"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: fab506b2-439d-4f1a-bdc5-d1d25c80d267
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 3861814974b95f0c65879158cb02ec727a25c99f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Com o Azure, a SnelStart expandiu rapidamente seus serviços comerciais a uma taxa de 1.000 novos Bancos de Dados SQL do Azure por mês
![Logotipo da SnelStart](./media/sql-database-implementation-snelstart/snelstartlogo.png)

A SnelStart torna populares programas de software de gerenciamento financeiro e comercial para SMBs (pequenas e médias empresas) nos Países Baixos. Seus 55.000 clientes são atendidos por uma equipe de 110 funcionários, incluindo uma equipe de TI de 35 pessoas. Ao passar do software de área de trabalho para uma oferta de SaaS (software como serviço) criada no Azure, a SnelStart aproveita ao máximo os serviços internos, automatizando o gerenciamento usando um ambiente familiar em C# e otimizando o desempenho e a escalabilidade sem provisionamento em excesso ou escasso às empresas usando pools elásticos. Usando o Azure, a SnelStart tem a fluidez de mover os clientes entre o local e a nuvem.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Por que a SnelStart estendeu os serviços do desktop para a nuvem
> "Trabalhar com o Azure significa que podemos fornecer software mais rapidamente, reagir rapidamente às demandas do cliente e dimensionar soluções quando as demandas aumentam."
> 
> — Henry Been, arquiteto de software
> 
> 

A SnelStart comandou uma empresa de software bem-sucedida durante anos usando um modelo tradicional de desenvolvimento: código, pacote, envio e repetição. Com o tempo, o ritmo da mudança aumentou cada vez mais depressa. As normas eram alteradas com frequência, e os clientes precisavam de meios mais fáceis de processar registros financeiros e colaborar com seus contadores e o governo a fim de acompanhar essas mudanças.

"O envio de software aos clientes era oneroso e limitado", de acordo com Henry Been, arquiteto de software. "Os custos de produção, embalagem e envio limitavam a frequência de lançamento de software. Tínhamos que empacotar atualizações para envios periódicos, mas isso dificultou atender às necessidades em constante mudança dos nossos clientes. Nunca podíamos garantir que nossos clientes atualizassem para a última versão do produto. Portanto, tínhamos que dar suporte a várias versões, o que tornava o trabalho de suporte bastante difícil de ser cumprido".

Been acrescenta, "Queríamos uma maneira de programar e lançar atualizações em um ritmo acelerado, para que pudéssemos inovar rapidamente e criar novos serviços para nossos clientes. Também queríamos uma forma de automatizar mais processos a fim de simplificar as necessidades de administração de negócios dos nossos clientes".

Para a SnelStart, a solução era estender seus serviços tornando-se um provedor de SaaS com base na nuvem. A plataforma de Banco de Dados SQL do Azure ajudou a SnelStart a chegar lá sem incorrer em uma grande sobrecarga de TI que uma solução IaaS (infraestrutura como serviço) teria exigido.

O modelo de nuvem também permite que a SnelStart corrija bugs e forneça novos recursos rapidamente, sem que os clientes precisem baixar e atualizar o software. De acordo com Been, "Usar os serviços de nuvem do Azure nos permite agir rapidamente mediante mudanças de requisitos de terceiros. Em vez de ter que enviar uma nova versão para milhares de clientes, podemos adaptar as informações enviadas do nosso aplicativo de desktop para novos formatos exigidos por terceiros".

## <a name="a-modern-company-with-traditional-roots"></a>Uma empresa moderna com raízes tradicionais
A SnelStart é uma empresa moderna, ágil e de alta tecnologia com raízes humildes que data de 1964, quando os fundadores a iniciaram como uma fabricante de peças de instrumentos musicais. O coração da empresa de software SnelStart realmente começou a bater na década de 80, durante a proliferação do computador pessoal. A empresa precisava de uma alternativa melhor ao software de contabilidade disponível no momento, de modo que ela resolveu por a mão na massa. Em 1982, a empresa criou a base do que futuramente veio a se tornar o software de contabilidade SnelStart. Desde que surgiu, o software foi elogiado por sua simplicidade e agilidade — tanto que a empresa finalmente mudou o foco e se reinventou, transformando-se em uma empresa de software.

Em 1995, a SnelStart lançou seu primeiro aplicativo de contadoria para Windows. O aplicativo, criado nos bancos de dados do Microsoft Visual Basic 1.0 e Microsoft Access, ajudou a aumentar a base de clientes para mais de 5.000 usuários.

Hoje, a SnelStart é um dos principais provedores de um aplicativo de administração de negócios e LOB (linha de negócios) destinado às SMBs e aos empreendedores autônomos da Holanda. Segundo Carlo Kuip, arquiteto de TI, "Nossa meta é fornecer automação total dos serviços de administração de negócios aos nossos clientes".

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Otimizando desempenho e custo com pools elásticos
A SnelStart foi uma pioneira de grande escala na adoção dos pools elásticos. Os pools elásticos ajudam a empresa a limitar custos e a gerenciar requisitos de desempenho com mais eficiência. De acordo com Been, "Usando pools elásticos, podemos otimizar o desempenho com base nas necessidades de nossos clientes, sem provisionamento excessivo. Se tivéssemos que provisionar com base na carga de pico, seria muito caro. Em vez disso, a opção de compartilhar recursos entre vários bancos de dados de pouco uso nos permite criar uma solução que funciona bem e é econômica".

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Bancos de Dados SQL do Azure ajudam a colocar dados em contêiner para isolamento e segurança
O Banco de Dados SQL do Azure permite que a SnelStart mova com facilidade e transparência dados locais de administração de negócios dos clientes para o Azure. O Banco de Dados SQL do Azure é um contêiner conveniente que proporciona isolamento, um limite de autenticação, autorização e recursos descomplicados de backup e restauração. Os bancos de dados fornecem um modelo conceitual bastante adequado para a administração dos negócios. De acordo com Carlo Kuip, arquiteto de TI, "Itens dentro do limite desse contêiner contêm dados confidenciais que são essenciais a uma empresa, e armazenar esses itens em um banco de dados isolado os mantêm bem protegidos. Podemos gerenciar a autorização no nível de banco de dados e, até mesmo, automatizar o gerenciamento e a escala horizontal desses bancos de dados sem precisar de DBAs (administradores de banco de dados) na equipe".

O SQL Data Warehouse do Azure também desempenha um papel importante na história de segurança e gerenciamento da SnelStart, ajudando a empresa a coletar dados de telemetria, como detecção de intrusão, registro em log das atividades do usuário e conectividade.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>O Azure remove a sobrecarga para que os desenvolvedores possam passar mais tempo agregando valor
O modelo de plataforma do Azure removeu a sobrecarga da infraestrutura e permitiu à SnelStart automatizar implantações usando bibliotecas de gerenciamento de C#. Conforme declarado por Kuip, "Pudemos aumentar nossas operações atuais com uma equipe bem pequena, ao mesmo tempo que aumentamos a escalabilidade, a agilidade e as opções de recuperação de desastre para nossos clientes. A mudança para o desenvolvimento de serviços liberou a equipe para se concentrar em novos serviços e recursos, e não apenas atualizar o código existente a fim de manter a empresa atualizada com as novas regulamentações ou os códigos de imposto". Ele acrescenta, "Ao automatizar o gerenciamento e usar a oferta de SaaS, pudemos fornecer mais valor aos nossos clientes sem precisar fazer grandes investimentos na equipe operacional". Por exemplo, usando o Azure e os pools elásticos, a SnelStart foi capaz de adicionar uma variedade de novos recursos, incluindo uma integração mais robusta de dados dos clientes aos bancos, novos serviços de cobrança, verificações em segundo plano para pequenas empresas e serviços de email.

> "Nos primeiros meses de 2016, expandimos nossas implantações do Banco de Dados SQL do Azure de aproximadamente 5.500 para mais de 12.000 e, atualmente, estamos incluindo cerca de 1.000 bancos de dados por mês".
> 
> — Henry Been, arquiteto de software
> 
> 

O gerenciamento de banco de dados ainda pode ser mais automatizado usando o recurso de trabalhos elásticos. Conforme afirmado por Kuip, "Adoramos a descoberta automática de bancos de dados em uma instância [servidor] do Banco de Dados SQL". Isso permite que a SnelStart execute operações de gerenciamento em sua base de clientes, que aumenta dinamicamente, sem sobrecarga adicional.

A SnelStart também está desenvolvendo uma API que atua como um agente entre os dados dos clientes e os aplicativos criados por parceiros de software terceirizados. Como afirma Kuip, "Essa API permitirá que outros fornecedores adicionem funcionalidade ao nosso software, como eliminação de entrada de dados para faturas e outros documentos". Os clientes não precisarão digitar manualmente as faturas em seu software de contabilidade para pequenas empresas; o software da SnelStart fará a troca de informações necessárias diretamente. Isso permite aos clientes unir as tarefas de administração de negócios com o ecossistema de informações que está emergindo da transformação digital no setor.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Como os serviços do Azure habilitam o SaaS para a SnelStart
Usando o Azure, a SnelStart pode atender aos seus clientes e contadores mais adequadamente na nuvem. Por exemplo, clientes e contadores podem compartilhar informações diretamente acessando a API do cliente SnelStart, hospedado no Azure. Kuip afirma, "Esses serviços reutilizáveis estão disponíveis para nossos aplicativos Web voltados para o cliente e fornecem infraestrutura e funções comuns para permitir acesso à administração dos negócios aos clientes e ao software de terceiros usado por nossos clientes. Exemplos incluem o fornecimento de recursos de configuração de produto, gerenciamento de regras de firewall e gerenciamento de processos de execução longa, como backups".

> "Nossa meta é fornecer automação total dos serviços de administração de negócios aos nossos clientes". 
> 
> — Carlo Kuip, arquiteto de TI
> 
> 

Além disso, os serviços Web da SnelStart permitem que clientes e contadores acessem facilmente os dados em pools elásticos do Banco de Dados SQL do Azure. Esse modelo de SaaS, aliado à elasticidade do banco de dados e ao Azure Resource Manager, fornece a SnelStart recursos de escalabilidade que complementam cada implantação do Azure. A implementação é totalmente automatizada usando bibliotecas de gerenciamento do C#.

![Arquitetura da SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figura 1. Desde junho de 2016, a SnelStart tem mais de 11.000 bancos de dados e mais de 50 pools elásticos

## <a name="simplicity-from-the-cloud"></a>Simplicidade da nuvem
Desde a mudança para uma solução baseada em nuvem do Azure, a SnelStart tem sido capaz de dar suporte ao rápido aumento de clientes, oferecendo recursos e serviços inovadores. De acordo com Been, "Com o Azure, podemos fornecer atualizações quase contínuas aos nossos clientes, sem expandir nossa equipe de operações. Além de obtermos todos os outros excelentes recursos do Azure — como escalabilidade e recuperação de desastre — tudo em um único pacote".

> "Quando estávamos em Redmond... recebi uma ligação de um desenvolvedor na Holanda, falando de um problema específico. Em 48 horas, conseguimos com a Microsoft uma mudança no ambiente de produção dela para resolver nosso problema".
> 
> — Henry Been, arquiteto de software
> 
> 

A SnelStart também está satisfeita com a forte parceria desenvolvida com a equipe de Banco de Dados SQL do Microsoft Azure. Conforme declaração de Kuip, "Realizamos debates sobre recursos e como usar a tecnologia, que é algo apreciado por ambos os lados".
A meta imediata da SnelStart é continuar aumentando sua base de clientes satisfeitos. Como Been afirma, "Sem as limitações técnicas e de recursos que tínhamos como um ISV, não há limites para nosso crescimento".

## <a name="more-information"></a>Mais informações
* Para saber mais sobre os pools elásticos do Azure, consulte [pools elásticos](sql-database-elastic-pool.md).
* Para saber mais sobre funções web e funções de trabalho, confira [funções de trabalho](../fundamentals-introduction-to-azure.md#compute).    
* Para saber mais sobre o SQL Data Warehouse, confira [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Para saber mais sobre a SnelStart, confira [SnelStart](http://www.snelstart.nl).

