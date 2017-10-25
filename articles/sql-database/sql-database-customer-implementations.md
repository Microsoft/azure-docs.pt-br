---
title: "Aspectos técnicos de implementações do cliente do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais detalhes técnicos sobre implementações de cliente do Banco de Dados SQL do Azure para solucionar problemas de negócios"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/03/2017
ms.author: carlrab
ms.openlocfilehash: 74b04cb7737011423556cbd956ecef770ac82ad4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Aspectos técnicos de implementações do cliente do Banco de Dados SQL do Azure

- [Daxko](sql-database-implementation-daxko.md): a Daxko/CSI Software enfrentou um desafio: sua base de clientes dos centros de bem-estar e recreação foi aumentando rapidamente, graças ao sucesso de sua abrangente solução de software corporativo, mas manter as necessidade de infraestrutura de TI para esse aumento da base de clientes foi uma prova para a equipe de TI da empresa. A empresa era cada vez mais restringida pela sobrecarga de operações crescentes, especialmente pelo gerenciamento dos bancos de dados em expansão. Pior ainda, essa sobrecarga de operações foi minando os recursos de desenvolvimento para novas iniciativas, como novos recursos de mobilidade para o software da empresa.

- [GEP](sql-database-implementation-gep.md): a GEP fornece software e serviços que permitem aos líderes de aquisição ao redor do mundo maximizar seu impacto nas operações, estratégias e desempenhos financeiros das empresas. Além dos serviços de consultoria e gerenciados, a empresa oferece o SMART by GEP®, uma abrangente plataforma de software de aquisição baseada em nuvem. No entanto, a GEP deparou-se com limitações ao tentar dar suporte a soluções como o SMART by GEP, com seus próprios datacenters locais: os investimentos necessários eram excessivos e os requisitos normativos em outros países teriam tornado os investimentos necessários ainda mais assustadores. Ao migrar para a nuvem, a GEP liberou recursos de TI, permitindo que ela se preocupasse menos com as operações de TI e se concentrasse mais no desenvolvimento de novas fontes de valor para seus clientes no mundo todo.

- [SnelStart](sql-database-implementation-snelstart.md): a SnelStart torna populares programas de software de gerenciamento financeiro e comercial para SMBs (pequenas e médias empresas) nos Países Baixos. Seus 55.000 clientes são atendidos por uma equipe de 110 funcionários, incluindo uma equipe de TI de 35 pessoas. Ao passar do software de área de trabalho para uma oferta de SaaS (software como serviço) criada no Azure, a SnelStart aproveita ao máximo os serviços internos, automatizando o gerenciamento usando um ambiente familiar em C# e otimizando o desempenho e a escalabilidade sem provisionamento em excesso ou escasso às empresas usando pools elásticos. Usando o Azure, a SnelStart tem a fluidez de mover os clientes entre o local e a nuvem.

- [Umbraco](sql-database-implementation-umbraco.md): para simplificar as implantações do cliente, a Umbraco adicionou a UaaS (Umbraco como serviço): uma oferta de SaaS (software como serviço) que elimina a necessidade de implantações locais, fornece dimensionamento interno e elimina a sobrecarga de gerenciamento permitindo que os desenvolvedores se concentrem na inovação de produtos em vez de no gerenciamento de soluções. A Umbraco é capaz de fornecer todos esses benefícios confiando no modelo flexível de PaaS (plataforma como serviço) oferecido pelo Microsoft Azure.

- [Quorum](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database): o Quorum dobra a principal carga de trabalho do banco de dados, enquanto reduz a DTU em 70% com o Banco de Dados SQL.

- [Quest](https://customers.microsoft.com/en-US/story/quest): o Quest oferece o Spotlight no serviço SQL Server Enterprise com um objetivo em mente: fornecer as melhores ferramentas disponíveis aos profissionais de banco de dados para proteção de dados, movimentação dos dados e monitoramento de operações de bancos de dados. Com o Spotlight, usando o Microsoft Azure e Banco de Dados SQL do Azure, os administradores de Banco de Dados do SQL Server podem monitorar, detectar, diagnosticar e fornecer uma maneira de resolver problemas de desempenho no SQL Server, seja de suas mesas de trabalho ou em casa.

- [Microsoft Dynamics](https://customers.microsoft.com/story/dynamics365operationsproductteam): esse breve estudo de caso realça as práticas recomendadas e lições aprendidas com a experiência da equipe do produto Dynamics 365 for Operations em migrar para o Banco de Dados SQL do Azure a fim de fornecer aos clientes uma oferta de SaaS (software como serviço) totalmente gerenciado. Usando o Banco de Dados SQL do Azure, a equipe do Dynamics 365 for Operations foi capaz de gerenciar e operar o serviço com menos pessoal e dimensionar com facilidade usando recursos de gerenciamento prontos para uso, como backups automáticos de banco de dados, retenção de backup do banco de dados, alta disponibilidade e recuperação de desastres. Isso, junto com a capacidade de provisionar bancos de dados com a automação trivial, permitiu que o Banco de Dados SQL se tornasse uma excelente plataforma para manter os serviços em larga escala.

- [Microsoft OneDrive e SharePoint Online](https://customers.microsoft.com/story/microsoft-azure-sql-database-dicrete-manufacturing-united-states): esse breve estudo de caso conta a história por trás da decisão do Microsoft OneDrive e do SharePoint Online de migrar para o Banco de Dados SQL do Azure e descreve como essa migração proporcionou um gerenciamento de capacidade elástica quase ilimitadas, reduzindo também de forma significativa os custos operacionais e a sobrecarga de infraestrutura.
