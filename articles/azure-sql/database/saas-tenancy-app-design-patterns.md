---
title: Padrões de SaaS multilocatário
description: Aprenda sobre os requisitos e os padrões da arquitetura de dados comuns de aplicativos de banco de dados SaaS (software como serviço) multilocatários executados no ambiente de nuvem do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 8a13c641d50a68d9661b4aa6caf8effb82d53dd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793221"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Padrões de locatário de banco de dados de SaaS multilocatários
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve os vários modelos de locação disponíveis para um aplicativo SaaS multilocatário.

Ao criar um aplicativo de SaaS multilocatário, você deve escolher cuidadosamente o modelo de aluguel que melhor atenda às necessidades do seu aplicativo.  Um modelo de locação determina como os dados de cada locatário são mapeados para o armazenamento.  A escolha do modelo de aluguel afeta o design de aplicativo e gerenciamento.  Alternar para um modelo diferente mais tarde, às vezes, é caro.

## <a name="a-saas-concepts-and-terminology"></a>a. Conceitos e terminologia de SaaS

No Software como um modelo de serviço (SaaS), sua empresa não vende *licenças* com o seu software. Em vez disso, cada cliente faz alugar pagamentos à sua empresa, tornando a cada cliente um *locatário* da sua empresa.

Em troca de pagamento de aluguel, cada locatário recebe acesso aos componentes do seu aplicativo SaaS, e tem seus dados armazenados no sistema de SaaS.

O termo *modelo de locação* refere-se a como os dados armazenados dos locatários são organizados:

- *Locação única:* &nbsp; Cada Database armazena dados de apenas um locatário.
- *Multilocação:* &nbsp; Cada Database armazena dados de vários locatários separados (com mecanismos para proteger a privacidade de dados).
- Modelos de aluguel híbridos também estão disponíveis.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Como escolher o modelo apropriado de aluguel

Em geral, o modelo de aluguel não afeta a função de um aplicativo, mas provavelmente afeta outros aspectos da solução geral.  Os critérios a seguir são usados para avaliar cada um dos modelos:

- **Escalabilidade**
    - Número de locatários.
    - Armazenamento por locatário.
    - Armazenamento em agregação.
    - Carga de trabalho.

- **Isolamento de locatário:** &nbsp; Isolamento de dados e desempenho (se a carga de trabalho de um locatário impacta outras pessoas).

- **Custo por locatário:** &nbsp; Custos do banco de dados.

- **Complexidade de desenvolvimento:**
    - Alterações para esquema.
    - Alterações em consultas (exigido pelo padrão).

- **Complexidade operacional:**
    - Monitoramento e ajuste de desempenho.
    - Gerenciamento do Esquema.
    - Restaurando um locatário.
    - Recuperação de desastre.

- **Personalização:** &nbsp; Facilidade de suporte a personalizações de esquema que são específicas de locatário ou específicos de classe de locatário.

A discussão de aluguel enfoca na camada de *dados*.  Mas considere por um momento a camada de *aplicativo*.  A camada de aplicativo é tratada como uma entidade monolítica.  Se você dividir o aplicativo em vários componentes pequenos, a escolha do modelo de aluguel pode ser alterada.  Você pode tratar alguns componentes diferentemente de outras pessoas sobre tanto aluguel e a tecnologia de armazenamento quanto a plataforma usada.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Aplicativo de único locatário autônomo com o banco de dados único locatário

#### <a name="application-level-isolation"></a>Isolamento do nível de aplicativo

Nesse modelo, todo o aplicativo é instalado várias vezes, uma vez para cada locatário.  Cada instância do aplicativo é uma instância autônoma, para que ele nunca interaja com qualquer outra instância autônoma.  Cada instância do aplicativo tem apenas um locatário e, portanto, precisa de apenas um banco de dados.  O locatário tem o banco de dados todo para si mesmo.

![Design de aplicativo autônomo com exatamente um banco de dados de único locatário.][image-standalone-app-st-db-111a]

Cada instância de aplicativo é instalada em um grupo de recursos do Azure separado.  O grupo de recursos pode pertencer a uma assinatura que pertence ou ao fornecedor do software ou ao locatário.  Em ambos os casos, o fornecedor pode gerenciar o software para o locatário.  Cada instância do aplicativo está configurada para se conectar ao seu banco de dados correspondente.

Cada banco de dados de locatário é implantado como um banco de dados individual.  Esse modelo fornece o maior isolamento de banco de dados.  Mas o isolamento requer que os recursos suficientes ser alocados para cada banco de dados para lidar com suas cargas de pico.  Aqui é mais importante que os pools Elásticos não podem ser usados para bancos de dados implantados em diferentes grupos de recursos ou a assinaturas diferentes.  Essa limitação torna esse aplicativo de único locatário autônomo a solução mais cara da perspectiva de custo geral do banco de dados de modelo.

#### <a name="vendor-management"></a>Gerenciamento de fornecedor

O fornecedor pode acessar todos os bancos de dados em todas as instâncias de aplicativo autônomo, mesmo se as instâncias do aplicativo estão instaladas em assinaturas de locatários diferentes.  O acesso é obtido por meio de conexões do SQL.  Esse acesso entre instâncias pode habilitar o fornecedor centralizar o gerenciamento de esquema e consulta de bancos de dados para fins de relatório ou análise.  Se esse tipo de gerenciamento centralizado é desejado, um catálogo deve ser mapeado identificadores de locatário para URIs de banco de dados implantado.  O banco de dados SQL do Azure fornece uma biblioteca de fragmentação que é usada em conjunto para fornecer um catálogo.  A biblioteca de fragmentação é chamada formalmente o [biblioteca de cliente de Banco de Dados Elástico][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Aplicativo de multilocatário com o banco de dados por locatário

Esse padrão próximo usa um aplicativo multilocatário com muitos bancos de dados, todos os que estão sendo bancos de dados de único locatário.  Um novo banco de dados é provisionado para cada novo locatário.  A camada de aplicativo é dimensionada *backup* verticalmente, adicionando mais recursos por nó.  Ou o aplicativo é dimensionado *out* horizontalmente adicionando mais nós.  O dimensionamento é com base na carga de trabalho e é independente do número ou a escala dos bancos de dados individuais.

![Aplicativo de multilocatário com o banco de dados por locatário.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Personalizar para um locatário

Como o padrão de aplicativo autônomo, o uso de bancos de dados de único locatário fornece isolamento de locatários forte.  Em qualquer aplicativo cujo modelo especifica apenas os bancos de dados único locatário, o esquema para um determinado banco de dados pode ser personalizado e otimizado para seu locatário.  Essa personalização não afetar outros locatários no aplicativo. Talvez um locatário poderá precisar de dados além os campos de dados básico que precisam de todos os locatários.  Além disso, o campo de dados adicional pode ser necessário um índice.

Personalizando o esquema para um ou mais locatários individuais é simples atingir com o banco de dados por locatário.  O fornecedor do aplicativo deve projetar procedimentos para gerenciar cuidadosamente as personalizações de esquema em escala.

#### <a name="elastic-pools"></a>Pools elásticos

Quando os bancos de dados são implantados no mesmo grupo de recursos, eles podem ser agrupados em pools elásticos.  Os grupos oferecem uma maneira econômica de compartilhamento de recursos em vários bancos de dados.  Essa opção de pool é mais barata do que a necessidade de cada banco de dados seja grande o suficiente para acomodar os picos de uso que ele passa por.  Mesmo que os bancos de dados em pool compartilham acesso aos recursos que ele ainda pode conseguir um alto grau de isolamento de desempenho.

![Aplicativo de multilocatário com o banco de dados por locatário.][image-mt-app-db-per-tenant-pool-153p]

Banco de dados SQL do Azure fornece as ferramentas necessárias para configurar, monitorar e gerenciar o compartilhamento.  As métricas de desempenho no nível do pool e no nível de banco de dados estão disponíveis no portal do Azure e por meio de logs de Azure Monitor.  As métricas podem fornecer excelentes ideias sobre desempenho de agregação e específicos de locatário.  Bancos de dados individuais podem ser movidos entre grupos para fornecer recursos reservados para um locatário específico.  Essas ferramentas permitem garantir o bom desempenho de maneira econômica.

#### <a name="operations-scale-for-database-per-tenant"></a>Escala de operações para o banco de dados por locatário

O banco de dados SQL do Azure tem muitos recursos de gerenciamento projetados para gerenciar grandes números de bancos de dados em escala, como, por exemplo, mais de 100.000 banco de dados.  Esses recursos tornam o padrão de banco de dados por locatário plausível.

Por exemplo, suponha que um sistema tem um banco de dados de 1000 locatário como apenas um banco de dados.  O banco de dados pode ter 20 índices.  Se o sistema converter para ter bancos de dados de único locatário 1000, a quantidade de índices aumentará para 20.000.  No banco de dados SQL do Azure como parte do [ajuste automático][docu-sql-db-automatic-tuning-771a], os recursos de indexação automática são habilitados por padrão.  A indexação automática gerencia para você, todos os índices de 20.000 e seus otimizações create e drop em andamento.  Essas ações automatizadas ocorrerem dentro de um banco de dados individual, e eles não são coordenados ou restrito a ações semelhantes em outros bancos de dados.  A indexação automática trata índices de forma diferente em um banco de dados ocupado que em um banco de dados menos ocupado.  Esse tipo de personalização de gerenciamento de índice seria impraticável em escala a banco de dados por locatário se essa tarefa de gerenciamento de grande tinha que ser feita manualmente.

Outros recursos de gerenciamento que podem ser expandidos também incluem o seguinte:

- Somente de backups internos.
- Alta disponibilidade.
- Criptografia de Disco.
- Exibir desempenho de telemetria.

#### <a name="automation"></a>Automação

As operações de gerenciamento podem ser incluídos no script e oferecidas por meio de um [devops][http-visual-studio-devops-485m] modelo.  As operações ainda podem ser automatizadas e expostas no aplicativo.

Por exemplo, você pode automatizar a recuperação de um único locatário em um ponto anterior no tempo.  A recuperação só precisa restaurar um único locatário banco de dados que armazena o locatário.  Essa restauração não tem nenhum impacto em outros locatários, que confirma que as operações de gerenciamento estão no nível granular eficiente de cada locatário individual.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Aplicativo de multilocatário com o banco de dados por locatário

É outro padrão disponível para armazenar vários locatários em um banco de dados de vários locatários.  A instância do aplicativo pode ter qualquer número de bancos de dados de vários locatários.  O esquema de banco de dados multilocatário deve ter uma ou mais colunas de identificador de locatário para que os dados de qualquer determinado locatário podem ser recuperados seletivamente.  Além disso, o esquema pode exigir algumas tabelas ou colunas que são usadas por apenas um subconjunto de locatários.  No entanto, os dados de referência e de código estáticos são armazenados somente uma vez e são compartilhados por todos os locatários.

#### <a name="tenant-isolation-is-sacrificed"></a>No entanto, o isolamento de locatários é limitado

*Dados:* &nbsp; Um banco de dados multilocatário necessariamente sacrifica o isolamento de locatário.  Os dados de vários locatários são armazenados juntos em um banco de dados.  Durante o desenvolvimento, certifique-se de que consultas nunca exponham dados de mais de um locatário.  Banco de dados SQL dá suporte a [segurança em nível de linha][docu-sql-svr-db-row-level-security-947w], que pode garantir que os dados retornados por uma consulta escopo para um único locatário.

*Processamento:* &nbsp; Um banco de dados multilocatário compartilha recursos de computação e armazenamento em todos os seus locatários.  O banco de dados como um todo pode ser monitorado para garantir desempenho aceitável.  No entanto, o sistema do Azure não tem interno como monitorar ou gerenciar o uso desses recursos por um locatário individual.  Portanto, o banco de dados multilocatário traz um risco maior de encontrar vizinhos ruídos, onde a carga de trabalho de um locatário overactive afeta a experiência de desempenho de outros locatários no mesmo banco de dados.  Monitoramento adicional de nível de aplicativo pode monitorar o desempenho do nível de locatário.

#### <a name="lower-cost"></a>Menor custo

Em geral, bancos de dados multilocatários tem o mais baixo por locatário de custo.  Os custos de recurso para um banco de dados individual são menores do que para um pool elástico do mesmo tamanho.  Além disso, para cenários em que os locatários precisam apenas armazenamento limitada, potencialmente milhões de locatários podem ser armazenadas em um único banco de dados.  Nenhum pool Elástico pode conter milhões de bancos de dados.  No entanto, uma solução que contém os bancos de dados de 1000 por pool, com os pools de 1000, é possível alcançar a escala de milhões com o risco de se tornar difíceis de gerenciar.

Duas variações de um modelo de banco de dados multilocatário são discutidas os itens a seguir, com o modelo de multilocatário fragmentado, sendo a mais flexível e escalonável.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplicativo de multilocatário com o banco de dados por locatário

O padrão de banco de dados multilocatário mais simples usa um banco de dados individual para hospedar dados para todos os locatários.  À medida que mais locatários são adicionados, o banco de dados é dimensionado com mais recursos de computação e armazenamento.  Essa expansão pode ser tudo o que é necessário, embora sempre há um limite de escala principal.  No entanto, longa antes que o limite é atingido o banco de dados torna-se difíceis de gerenciar.

Operações de gerenciamento que concentram-se em locatários individuais são mais complexas para implementar em um banco de dados de vários locatários.  E, em grande escala essas operações podem se tornar muito lentas.  Um exemplo é uma restauração point-in-time de dados para somente um locatário.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplicativo de multilocatário com o banco de dados por locatário

A maioria dos aplicativos SaaS acessar os dados de locatário somente um por vez.  O padrão de acesso permite que os dados de locatário seja distribuída por vários bancos de dados ou fragmentos, onde todos os dados para um locatário está contido em um fragmento.  Um modelo fragmentado combinado com um padrão de banco de dados multilocatário, permite que a escala praticamente ilimitada.

![ de multilocatário com o banco de dados por locatário.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Gerenciar fragmentos

Fragmentação adiciona complexidade tanto para o design e gerenciamento operacional.  Um catálogo é necessária para manter o mapeamento entre locatários e bancos de dados.  Além disso, os procedimentos de gerenciamento são necessárias para gerenciar os fragmentos e a população de locatário.  Por exemplo, os procedimentos devem ser criados para adicionar e remover fragmentos e para mover dados de locatário entre fragmentos.  Uma maneira de dimensionar é adicionando um novo fragmento e populá-lo com novos locatários.  Em outras vezes, você pode dividir um fragmento densamente populado em dois fragmentos de menos densamente populados.  Depois de vários locatários foram movidos ou interrompidos, você pode mesclar fragmentos modo disperso preenchidos.  A mesclagem pode resultar em utilização de mais eficiente de recursos.  Os locatários também podem ser movidos entre os fragmentos para distribuir cargas de trabalho.

Banco de dados SQL fornece uma ferramenta de divisão/mesclagem funciona em conjunto com a biblioteca de fragmentação e o banco de dados do catálogo.  O aplicativo fornecido pode dividir e fragmentos de mesclagem e ele podem mover dados de locatário entre fragmentos.  O aplicativo também mantém o catálogo durante essas operações, marcar afetados locatários como offline antes de movê-los.  Após a movimentação, o aplicativo atualiza o catálogo novamente com o novo mapeamento e marcar o locatário como online novamente.

#### <a name="smaller-databases-more-easily-managed"></a>Bancos de dados menores mais facilmente gerenciados

Distribuindo locatários entre vários bancos de dados, a solução de vários locatários fragmentada resulta em bancos de dados menores que são gerenciados mais facilmente.  Por exemplo, a restauração de um locatário específico em um ponto anterior no tempo envolve a restauração de um único banco de dados menor de um backup, em vez de um banco de dados maior que contém todos os locatários. O tamanho do banco de dados e o número de locatários por banco de dados, podem ser escolhidos para equilibrar a carga de trabalho e os esforços de gerenciamento.

#### <a name="tenant-identifier-in-the-schema"></a>Identificador do locatário no esquema

Dependendo da abordagem de fragmentação usada, as restrições adicionais podem ser impostas sobre o esquema de banco de dados.  O aplicativo de divisão/mesclagem do banco de dados SQL requer que o esquema inclui a chave de fragmentação, que geralmente é o identificador do locatário.  O identificador do locatário é o elemento à esquerda na chave primária de todas as tabelas fragmentadas.  O identificador do locatário permite que o aplicativo de divisão/mesclagem localizar rapidamente e mover os dados associados a um locatário específico.

#### <a name="elastic-pool-for-shards"></a>Pool Elástico de fragmentos

Bancos de dados multilocatários fragmentados podem ser colocados em pools elásticos.  Em geral, com muitos bancos de dados de único locatário em um pool é mais econômica como tendo vários locatários em alguns bancos de dados de vários locatários.  Bancos de dados de vários locatários são vantajosos quando há um grande número de locatários relativamente inativos.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Modelo de bancos de dados multilocatários compartilhados, o híbrido

No modelo híbrido, todos os bancos de dados tem o identificador do locatário em seu esquema.  Os bancos de dados são todos capazes de armazenar mais de um locatário e os bancos de dados podem ser fragmentados.  Portanto, no sentido de esquema, eles são todos os bancos de dados de vários locatários.  Ainda na prática, alguns desses bancos de dados contém apenas um locatário.  Independente disso, a quantidade de locatários armazenados em um determinado banco de dados não tem efeito sobre o esquema de banco de dados.

#### <a name="move-tenants-around"></a>Mover os locatários

A qualquer momento, você pode mover um locatário específico para seu próprio banco de dados de vários locatários.  E a qualquer momento, você pode mudar de ideia e mover o locatário de volta para um banco de dados que contém vários locatários.  Você também pode atribuir um locatário para o novo banco de dados de único locatário ao provisionar o novo banco de dados.

O modelo híbrido brilha quando há grandes diferenças entre as necessidades de recursos de grupos de identificação de locatários.  Por exemplo, suponha que locatários participando de uma avaliação gratuita não têm garantia de mesmo nível alto de desempenho que são de assinatura de locatários.  A política pode ser para locatários na fase de avaliação gratuita para ser armazenado em um banco de dados multilocatário é compartilhado entre todos os locatários de avaliação gratuitos.  Quando um locatário de avaliação gratuita assina a camada de serviço Básica, o locatário pode ser movido para outro banco de dados de vários locatários com menos de locatários.  Um assinante que paga pela camada de serviço Premium pôde ser movido para seu próprio novo banco de dados de locatário único.

#### <a name="pools"></a>Pools

Neste modelo híbrido, os bancos de dados de único locatário para locatários do assinante podem ser colocados em pools de recursos para reduzir os custos de banco de dados por locatário.  Isso também é feito no modelo de banco de dados por locatário.

## <a name="i-tenancy-models-compared"></a>I. Modelos de aluguel em comparação comparados

A tabela a seguir resume as diferenças entre o Functions e o WebJobs.

| Medida | Aplicativo independente | Banco de dados por locatário | Multilocatário fragmentado |
| :---------- | :------------- | :------------------ | :------------------- |
| Escala | Médio<br />1-100s | Muito alta<br />1-100.000s | Ilimitado<br />1-1.000.000s |
| Isolamento de locatário | Muito alta | Alta | Baixo, exceto para qualquer locatário único (ou seja, sozinho em um banco de dados de MT). |
| Banco de dados por locatário | Alta; é dimensionado para picos. | Baixo; pools usados. | Mais baixo, para locatários pequenos em MT bancos de dados. |
| Monitoramento e gerenciamento de desempenho | Por locatário somente | Agregação + por locatário | Agregação, embora seja por locatário somente para únicos. |
| Complexidade de desenvolvimento | Baixo | Baixo | Médio; devido à fragmentação. |
| Complexidade operacional | Alta-baixa. Individualmente simples e complexas em escala. | Média-baixa. Padrões de endereço complexidade em escala. | Alta-baixa. Gerenciamento de locatário individual é complexo. |
| &nbsp; ||||

## <a name="next-steps"></a>Próximas etapas

- [Implantar e explorar um aplicativo SaaS multilocatário que usa o Banco de Dados SQL do Azure][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Bem-vindo ao aplicativo de aluguel de Banco de Dados SQL do Azure do SaaS de exemplo do Wingtip Tickets][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: /sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Design de aplicativo autônomo com exatamente um banco de dados de único locatário."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Aplicativo de multilocatário com o banco de dados por locatário."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Aplicativo de multilocatário com o banco de dados por locatário."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png " de multilocatário com o banco de dados por locatário."