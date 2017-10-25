---
title: Estudo de caso sobre o Banco de Dados SQL do Azure - GEP | Microsoft Docs
description: "Saiba como a GEP usa o Banco de Dados SQL para atingir mais clientes globais e obter mais eficiência"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ae8bcb10-c251-4bac-b666-10a253918583
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 64dec0bb789fe6e07975b1d403b6a37b5c2c2ed8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>O Azure oferece à GEP alcance global e maior eficiência
![Logotipo da GEP](./media/sql-database-implementation-gep/geplogo.png)

A GEP fornece software e serviços que permitem aos líderes de aquisição ao redor do mundo maximizar seu impacto nas operações, estratégias e desempenhos financeiros das empresas. Além dos serviços de consultoria e gerenciados, a empresa oferece o SMART by GEP®, uma abrangente plataforma de software de aquisição baseada em nuvem. No entanto, a GEP deparou-se com limitações ao tentar dar suporte a soluções como o SMART by GEP, com seus próprios datacenters locais: os investimentos necessários eram excessivos e os requisitos normativos em outros países teriam tornado os investimentos necessários ainda mais assustadores. Ao migrar para a nuvem, a GEP liberou recursos de TI, permitindo que ela se preocupasse menos com as operações de TI e se concentrasse mais no desenvolvimento de novas fontes de valor para seus clientes no mundo todo.

## <a name="expanding-services-and-growth-by-using-azure"></a>Expandindo serviços e o crescimento usando o Azure
Os clientes do SMART by GEP adoram os recursos e a facilidade de uso da plataforma; os clientes podem gerenciar seus processos de qualquer lugar, a qualquer momento, de qualquer dispositivo — laptop, tablet ou telefone. Ao passar para o Microsoft Azure, a GEP foi capaz de acomodar seu rápido crescimento e potencial para expandir em novos mercados. De acordo com o vice-presidente de tecnologia da GEP, Dhananjay Nagalkar, "O Microsoft Azure teve um papel importante no sucesso da GEP ao permitir que dimensionássemos rapidamente os serviços e fornecêssemos datacenters regionais que nos ajudam a atender às necessidades normativas de nossos clientes globais".

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>As limitações de um datacenter do tipo faça você mesmo
Em 2013, os líderes da GEP reconheceram que precisavam de uma maneira de garantir escalabilidade e desempenho, uma vez que a base de clientes aumentou. Nagalkar explicou, "Para atender à demanda com nossos datacenters existentes, teríamos que expandir nossa infraestrutura e os recursos de TI consideravelmente. O investimento e o tempo para isso teria sido gigantesco". As máquinas virtuais e físicas locais exigem configuração, gerenciamento, dimensionamento, backup e aplicação de patch amplos a uma taxa que teria sido impossível para a GEP. As soluções de nuvem, por outro lado, oferecem simplicidade e conveniência que permitiram à GEP se concentrar mais no desenvolvimento em vez no gerenciamento de grandes (e crescentes) operações de TI. Nagalkar sabia que a GEP poderia reduzir a sobrecarga de compra, configuração e gerenciamento da infraestrutura migrando para a nuvem.

A GEP também precisou de uma maneira de superar as barreiras normativas que a mantinham fora de alguns mercados globais. Para muitos clientes potenciais da GEP na Europa, a conformidade normativa exigia que os dados fossem armazenados nas respectivas regiões geográficas locais. Mas não seria prático para a GEP criar vários datacenters. "A expansão dos investimentos em infraestrutura e os custos de mão de obra de TI teriam um impacto significativo nas margens", de acordo com Nagalkar. "Consequentemente, fomos forçados a rejeitar os possíveis clientes que exigiam dados armazenados localmente".

Nagalkar sabia que uma solução de nuvem poderia ser a resposta para esse dilema. Se a GEP mudasse para um provedor de nuvem com uma presença global, poderia ser mais fácil atender às necessidades de desempenho e normativas de seus clientes armazenando dados mais próximo dos locais físicos dos clientes.

## <a name="finding-smooth-skies-in-the-cloud"></a>Localizando um céu limpo na nuvem
Nagalkar e sua equipe exploraram várias opções de nuvem, mas a maioria eram soluções baseadas em IaaS (infraestrutura como serviço) que teriam exigido que a GEP investisse pesadamente em recursos de TI para configurar e gerenciar o serviço. A solução PaaS (plataforma como serviço) do Azure apresentou-se como a melhor opção.

"Com o Azure, a GEP não precisa lidar com o gerenciamento de banco de dados, a configuração de máquina virtual, a aplicação de patch ou outras tarefas de gerenciamento de infraestrutura", declarou Nagalkar. "Em vez disso, podemos concentrar nossos recursos naquilo que fazemos melhor: aproveitar nosso conhecimento em aquisição para escrever software que realmente forneça resultados aos nossos clientes". 

Na verdade, a mudança para o Azure permitiu que a GEP reduzisse sua equipe de operações de TI, dando simultaneamente mais funcionalidade para os clientes.

Aproveitando os datacenters do Azure no mundo todo, a GEP pode estender facilmente seu alcance pela Europa e Ásia. Esses datacenters globais permitem que a GEP dimensione com agilidade e atenda às necessidades dos clientes por dados armazenados localmente que reduza a latência e atenda aos requisitos normativos.

## <a name="smart-by-gep-architecture"></a>Arquitetura do SMART by GEP
A GEP criou o SMART by GEP desde o início no Azure. Uma motivação importante para a GEP foi a maior escalabilidade, menos tempo de inatividade e custos reduzidos de manutenção que a GEP poderia experimentar com o Banco de Dados SQL do Azure em comparação como o que a GEP poderia alcançar no local. No entanto, assim que passou para a nuvem, a GEP descobriu novas oportunidades de desenvolvimento na nuvem, como a criação rápida de protótipo e engenharia de tendência para melhor responder às necessidades dos clientes. O desenvolvimento no Azure permitiu que a GEP eliminasse as preocupações com licenciamento de software que seus desenvolvedores poderiam ter no local. O núcleo do SMART by GEP é o Banco de Dados SQL do Azure, embora a GEP use muitos outros serviços do Azure para continuar melhorado de modo rápido e fácil o SMART by GEP.

![Arquitetura do SMART by GEP](./media/sql-database-implementation-gep/figure1.png) Figura 1. Arquitetura do SMART by GEP

## <a name="structured-data"></a>Dados estruturados
No coração do aplicativo SMART by GEP estão as instâncias do Banco de Dados SQL do Azure que movimentam a solução corporativa de gerenciamento de aquisição. Quando a GEP projetou o SMART by GEP, ela viu o Banco de Dados SQL do Azure como um ajuste perfeito para sua arquitetura, um que permitiria que a empresa atingisse o grau mais alto de proteção de dados e atendesse aos requisitos normativos. A GEP usa várias camadas de proteção de dados oferecidas pelo Banco de Dados SQL do Azure, incluindo:

* Criptografia de dados em repouso por meio de criptografia de dados transparente.
* Proteção da autenticação integrando o Banco de Dados SQL do Azure ao Azure Active Directory.
* Limitação do acesso a um subconjunto apropriado de dados com Segurança em Nível de Linha.
* Mascaramento de dados em tempo real por meio de políticas.
* Rastreamento de eventos de banco de dados por meio de Auditoria do Banco de Dados SQL do Azure.

> "Podemos usar todas essas opções sem fazer nenhuma mudança grande no código e com mínimo impacto no desempenho", afirmou Nagalkar.
> 
> 

Ao usar o Banco de Dados SQL do Azure, a GEP tem automaticamente melhores recursos de recuperação de desastre do que poderia ter economicamente projetado no local devido aos recursos de tolerância a falhas criados no Banco de Dados SQL do Azure. A GEP usa a funcionalidade de replicação geográfica ativa no Banco de dados SQL do Azure, em conjunto com várias réplicas secundárias ativas, legíveis e online (Grupos de disponibilidade AlwaysOn) em diferentes regiões geográficas para formar pares de alta disponibilidade. A replicação dos dados do SMART by GEP entre regiões significa que, no caso de um desastre em toda a região, a GEP pode recuperar facilmente dados dos clientes com um RPO (objetivo de ponto de recuperação) e RTO (objetivo de tempo de recuperação) mínimos.

Cada cliente do SMART by GEP tem duas instâncias do Banco de Dados SQL do Azure: uma para OLTP (processamento de transação online) e uma para análise (como gastos do cliente e análise de relatório). Os pools elásticos do Banco de Dados SQL do Azure permitem que a GEP gerencie facilmente milhares de bancos de dados globalmente para lidar com demandas imprevisíveis de recursos de banco de dados. Os pools elásticos fornecem à GEP um meio de garantir que os bancos de dados de clientes possam ser dimensionados conforme a necessidade, sem provisionamento em excesso ou escasso, ao mesmo tempo que permitem que a GEP controle os custos. Além disso, como esse é um serviço PaaS, a GEP obtém todos os novos recursos do Banco de Dados SQL do Azure com atualizações automáticas.

## <a name="unstructured-and-semi-structured-data"></a>Dados não estruturados e semiestruturados
No entanto, alguns dados de clientes do SMART by GEP precisam ser armazenados de maneira menos rígida. Para esse tipo de dados, a GEP utiliza o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure e o Cache Redis do Azure. O Armazenamento de Blobs do Azure hospeda todos os anexos que os usuários do SMART by GEP carregam no aplicativo. Também é onde o SMART by GEP armazena conteúdo estático, como arquivos CSS (folhas de estilos em cascata) e JavaScript.

A GEP armazena dados voltados para não clientes, como os dados de log do SMART by GEP, no Armazenamento de Tabelas do Azure, que fornece à GEP armazenamento econômico efetivamente ilimitado e tempos rápidos de recuperação sem precisar se preocupar com a configuração de um esquema para os dados. A GEP usa o Cache Redis do Azure para um cache mestre.

## <a name="authentication-and-routing"></a>Autenticação e roteamento
O ACS (Serviço de Controle de Acesso) do Azure fornece aos usuários do SMART by GEP uma ampla variedade de opções para entrar no software. O ACS do Azure pode ser federado com qualquer provedor de identidade que troque os dados de autenticação usando SAML (Security Assertion Markup Language), como os Active Directory Domain Services, Ping Identity, OneLogin ou SiteMinder. Isso ajuda a GEP a implementar o SSO (logon único) para clientes sem se preocupar em armazenar as credenciais do usuário e manter políticas de senha para clientes.

Depois de conectados, os clientes têm acesso aos recursos corporativos corretos no SMART by GEP. A GEP usa o Gerenciador de Tráfego do Azure para redirecionar e balancear a carga de solicitações originadas de dispositivos móveis e sessões do navegador dos clientes.

## <a name="other-azure-services"></a>Outros serviços do Azure
A GEP utiliza vários outros serviços do Azure para tornar o SMART by GEP responsivo às necessidades dos clientes. A GEP usa serviços de nuvem do Azure (funções web e de trabalho) para hospedar apresentação do aplicativo e os serviços lógicos corporativos protegidos. Os serviços de nuvem permitem aos desenvolvedores gerenciar a IAC (infraestrutura como código) e implantar novos aplicativos SMART by GEP em uma fração do tempo que é exigido com datacenters locais — tudo sem qualquer envolvimento da TI. Os desenvolvedores da GEP podem usar o ambiente de preparo dos serviços de nuvem para testar novas versões sem afetar a implantação atual da produção. Depois de testados, a GEP usa os recursos de troca VIP dos serviços de nuvem do Azure para mover o código de preparo para o slot de produção em um minuto, reduzindo assim o tempo de inatividade da implantação.

Para reduzir a latência do aplicativo, a GEP usa a CDN (Rede de Distribuição de Conteúdo) do Azure para colocar conteúdo estático armazenado no Armazenamento de Blobs do Azure (como arquivos CSS e JavaScript) em servidores de borda próximos dos usuários. A GEP usa Barramento de Serviço do Azure para dar suporte aos padrões de arquitetura do aplicativo que vão de assinatura de publicação à CQRS (Segregação Responsiva de Consulta de Comando) e arquitetura em camadas com acoplamento impreciso e comunicação assíncrona. A GEP usa os Serviços de Mídia do Azure para melhorar o serviço de suporte ao cliente. A GEP descobriu que poderia postar com facilidade vídeos de suporte para o usuário nos Serviços de Mídia do Azure. Esses vídeos respondem a perguntas comuns dos usuários, o que ajuda a aprimorar a satisfação do usuário com o SMART by GEP, ao mesmo tempo que retira um pouco da carga de suporte da equipe de suporte ao cliente da GEP.

Para enviar os milhares de emails transacionais gerados diariamente pelo SMART by GEP, a firma usa a API .NET para SendGrid para integração com o Azure. Para os desenvolvedores da GEP, essa tarefa é fácil — o complemento SendGrid para Azure está disponível diretamente no Azure Marketplace. Os desenvolvedores da GEP podem configurar o SMART by GEP usando o pacote NuGet do SendGrid diretamente no Microsoft Visual Studio; a TI da GEP pode monitorar o tráfego de email do SendGrid do software diretamente no Azure.

Por fim, o SMART by GEP usa Máquinas Virtuais do Azure (o serviço IaaS do Azure) para hospedar aplicativos e serviços que não faziam sentido, no momento da engenharia, de modo a substituir por soluções SaaS (software como serviço) ou PaaS. Por exemplo, a GEP hospeda serviços de API de integração em máquinas virtuais para integração B2B (entre empresas) com sistemas ERP (planejamento de recursos empresariais) locais dos clientes, como SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP e Lawson, e com soluções SaaS dos clientes para trocar com eficiência documentos de aquisição, como faturas.

> "A criação do SMART by GEP na nuvem do Microsoft Azure eliminou completamente a necessidade de TI local, não apenas para a GEP, mas também para as operações de aquisição de nossos clientes." 
> 
> — Dhananjay Nagalkar, vice-presidente de soluções tecnológicas
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Aumentar a satisfação dos clientes sem expandir a TI
Desde a migração dos datacenters locais para o Azure e a criação do SMART by GEP do zero na plataforma do Azure, a GEP aumentou a escalabilidade e a flexibilidade sem precisar expandir a infraestrutura ou a equipe de TI. Na verdade, a empresa não acrescentou recursos de TI por mais de quatro anos. O modelo conveniente de PaaS do Azure permitiu que a GEP reduzisse os gastos com suporte ao fornecedor e gerenciamento de operações. Como resultado, a GEP foi capaz de se concentrar em recursos no desenvolvimento de software; e desenvolver na nuvem permite que os desenvolvedores da GEP testem rapidamente novas ideias sem precisar perder tempo coordenando com a TI ou se preocupando com os requisitos de licenciamento local. O Banco de Dados SQL do Azure ajuda a GEP a garantir melhor que seus clientes sempre tenham serviço e desempenho excepcionais.

## <a name="more-information"></a>Mais informações
* Home page da GEP: [GEP](http://www.gep.com)
* Smart by GEP: [Smart by GEP](http://www.smartbygep.com)

## <a name="gep-contributors"></a>Colaboradores da GEP
* Huzaifa Matawala, diretor associado — arquiteto da GEP
* Sathyan Narasingh, gerente de engenharia da GEP
* Deepa Velukutty, arquiteto de banco de dados da GEP

