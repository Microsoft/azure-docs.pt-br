---
title: Melhores práticas de implantação
description: Este artigo fornece as práticas recomendadas para a implantação do Azure alcance. O Azure Purview permite que qualquer usuário registre, descubra, entenda e consuma fontes de dados.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e02ad9187743603d46259d70965e49d6839ecd71
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949831"
---
# <a name="azure-purview-deployment-best-practices"></a>Práticas recomendadas de implantação do Azure alcance

Este artigo identifica tarefas comuns que podem ajudá-lo a implantar o alcance em produção. Essas tarefas podem ser concluídas em fases, no decorrer de um mês ou mais. Mesmo as organizações que já implantaram o alcance podem usar este guia para garantir que estejam obtendo o máximo de seus investimentos.

Uma implantação bem planejada de uma plataforma de governança de dados (como o Azure alcance) pode oferecer os seguintes benefícios:

- Melhor descoberta de dados
- Colaboração analítica aprimorada
- Retorno de investimento maximizado.

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a Microsoft Azure com uma assinatura de desenvolvimento ou de produção
* Capacidade de criar recursos do Azure, incluindo alcance
* Acesso a fontes de dados como Azure Data Lake Storage ou SQL do Azure em ambientes de teste, desenvolvimento ou produção
  * Por Data Lake Storage, a função necessária para verificar é a função leitor
  * Para SQL, a identidade deve ser capaz de consultar tabelas para amostragem de classificações
* Acesso à central de segurança do Azure ou à capacidade de colaborar com o administrador da central de segurança para rotular dados

## <a name="identify-objectives-and-goals"></a>Identificar objetivos e metas

Muitas organizações começaram sua jornada de governança de dados desenvolvendo soluções individuais que atendem a requisitos específicos de grupos isolados e domínios de dados em toda a organização. Embora as experiências possam variar dependendo do setor, do produto e da cultura, a maioria das organizações acha difícil manter controles e políticas consistentes para esses tipos de soluções.

Alguns dos objetivos comuns de governança de dados que você pode querer identificar nas fases iniciais incluem:

* Maximizando o valor comercial de seus dados
* Habilitando uma cultura de dados em que os consumidores de dados podem facilmente localizar, interpretar e confiar em dados
* Aumentando a colaboração entre várias unidades de negócios para fornecer uma experiência de dados consistente
* Promovendo a inovação acelerando a análise de dados para colher os benefícios da nuvem
* Reduzindo o tempo para descobrir dados por meio de opções de autoatendimento para vários grupos de habilidades
* Reduzindo o tempo de colocação no mercado para a entrega de soluções de análise que melhoram o serviço para seus clientes
* Reduzindo os riscos operacionais devido ao uso de ferramentas específicas de domínio e tecnologia sem suporte

A abordagem geral é dividir esses objetivos abrangentes em várias categorias e metas. Alguns exemplos incluem:

|Categoria|Goal|
|---------|---------|
|Descoberta|Os usuários administradores devem ser capazes de verificar fontes de dados do Azure e não Azure (incluindo fontes locais) para reunir informações sobre os ativos de dados automaticamente.|
|classificação|A plataforma deve classificar automaticamente os dados com base em uma amostragem dos dados e permitir a substituição manual usando classificações personalizadas.|
|Consumo|Os usuários empresariais devem ser capazes de encontrar informações sobre cada ativo para os metadados corporativos e técnicos.|
|Linhagem|Cada ativo deve mostrar uma exibição gráfica dos conjuntos de valores subjacentes para que os usuários compreendam as fontes originais e quais alterações foram feitas.|
|Colaboração|A plataforma deve permitir que os usuários colaborem fornecendo informações adicionais sobre cada ativo de dados.|
|Relatórios|Os usuários devem ser capazes de exibir relatórios sobre o espaço de dados, incluindo dados confidenciais e dados que precisam de enriquecimento adicional.|
|Governança de dados|A plataforma deve permitir que o administrador defina políticas para o controle de acesso e aplique automaticamente o acesso a dados com base em cada usuário.|
|Fluxo de trabalho|A plataforma deve ter a capacidade de criar e modificar o fluxo de trabalho para que seja fácil expandir e automatizar várias tarefas na plataforma.|
|Integração|Outras tecnologias de terceiros, como tíquete ou orquestração, devem ser capazes de se integrar à plataforma por meio de script ou APIs REST.|

## <a name="top-questions-to-ask"></a>Principais perguntas a serem feitas

Depois que sua organização concordar com os objetivos e metas de alto nível, haverá muitas perguntas de vários grupos. É crucial reunir essas perguntas a fim de criar um plano para resolver todas as preocupações. Alguns exemplos de perguntas que você pode encontrar durante a fase inicial:

1. Quais são os principais sistemas de dados e fontes de dados da organização?
2. Para fontes de dados que ainda não são suportadas por alcance, quais são minhas opções?
3. Quantas instâncias de alcance precisamos?
4. Quem são os usuários?
5. Quem pode verificar novas fontes de dados?
6. Quem pode modificar o conteúdo dentro do alcance?
7. Qual processo posso usar para melhorar a qualidade de dados no alcance?
8. Como inicializar a plataforma com ativos críticos, termos de glossário e contatos existentes?
9. Como integrar com sistemas existentes?
10. Como reunir comentários e criar um processo sustentável?

Embora você talvez não tenha a resposta para a maioria dessas perguntas imediatamente, ela pode ajudar sua organização a estruturar esse projeto e garantir que todos os requisitos de "necessidade" possam ser atendidos.

## <a name="include-the-right-stakeholders"></a>Incluir os participantes certos

Para garantir o sucesso da implementação de alcance para toda a empresa, é importante envolver os participantes certos. Apenas algumas pessoas estão envolvidas na fase inicial. No entanto, à medida que o escopo se expande, você precisará de outras pessoas para contribuir com o projeto e fornecer comentários.

Algumas das principais partes interessadas que você talvez queira incluir:

|Persona|Funções|
|---------|---------|
|**Diretor de dados**|O CDO supervisiona uma variedade de funções que podem incluir gerenciamento de dados, qualidade de dados, gerenciamento de dados mestre, ciência de dados, business intelligence e criação de estratégia de dados. Eles podem ser o patrocinador do projeto de implementação alcance.|
|**Proprietário de domínio/empresa**|Uma pessoa de negócios que influencia o uso de ferramentas e tem controle de orçamento|
|**Analista de Dados**|Capaz de estruturar um problema de negócios e analisar dados para ajudar os líderes a tomar decisões de negócios|
|**Arquiteto de dados**|Crie bancos de dados para aplicativos de linha de negócios de missão crítica, juntamente com o design e a implementação da segurança do dado|
|**Engenheiro de dados**|Operar e manter a pilha de dados, extrair dados de fontes diferentes, integrar e preparar dados, configurar pipelines de dados|
|**Cientista de Dados**|Crie modelos analíticos e configure os produtos de dados a serem acessados por APIs|
|**Administrador do BD**|Possui, rastreia e resolve incidentes e solicitações relacionados ao banco de dados dentro de SLAs (contratos de nível de serviço); Pode configurar pipelines de dados|
|**DevOps**|Desenvolvimento e implementação de aplicativos de linha de negócios; pode incluir a gravação de scripts e recursos de orquestração|
|**Especialista em segurança de dados**|Avalie a segurança geral da rede e dos dados, o que envolve a entrada e a saída de dados de alcance|

## <a name="identify-key-scenarios"></a>Identificar os principais cenários

O alcance pode ser usado para gerenciar centralmente a governança de dados em todo o espaço de dados de uma organização, abrangendo ambientes locais e de nuvem. Para ter uma implementação bem-sucedida, você deve identificar os principais cenários que são essenciais para os negócios. Esses cenários podem atravessar limites de unidade de negócios ou impactar várias pessoas de usuário para upstream ou downstream.

Esses cenários podem ser gravados de várias maneiras, mas você deve incluir pelo menos estas cinco dimensões:

1. Pessoa – quem são os usuários?
2. Sistema de origem – quais são as fontes de dados, como o Azure Data Lake Storage Gen2 ou o Azure SQL Database?
3. Área de impacto – qual é a categoria deste cenário?
4. Cenários de detalhes – como os usuários usam o alcance para resolver problemas?
5. Resultado esperado – quais são os critérios de sucesso?

Os cenários devem ser específicos, acionáveis e executáveis com resultados mensuráveis. Alguns cenários de exemplo que você pode usar:

|Cenário|Detalhe|Persona|
|---------|---------|---------|
|Catalogar ativos críticos para os negócios|Preciso ter informações sobre cada conjunto de dados para ter uma boa compreensão do que ele é. Esse cenário inclui dados de metadados comerciais e técnicos sobre o conjunto de dados no catálogo. As fontes de dados incluem Azure Data Lake Storage Gen2, Azure Synapse DW e/ou Power BI. Esse cenário também inclui recursos locais, como SQL Server.|Analista de negócios, cientista de dados, engenheiro de dados|
|Descubra ativos críticos para os negócios|Preciso ter um mecanismo de pesquisa que possa Pesquisar todos os metadados no catálogo. Eu deveria ser capaz de Pesquisar usando o termo técnico, termo comercial com pesquisa simples ou complexa usando curinga.|Analista de negócios, cientista de dados, engenheiro de dados, administrador de dados|
|Acompanhe os dados para entender sua origem e solucionar problemas de dados|Preciso ter a linhagem de dados para rastrear dados em relatórios, previsões ou modelos de volta para sua fonte original e entender as alterações e onde os dados residiram por meio do ciclo de vida dos dados. Esse cenário precisa dar suporte a pipelines de dados priorizados Azure Data Factory e databricks.|Engenheiro de dados, cientista de dados|
|Enriquecer metadados em ativos de dados críticos|Preciso enriquecer o conjunto de dados no catálogo com metadados técnicos que são gerados automaticamente. Classificação e rotulagem são alguns exemplos.|Engenheiro de dados, proprietário de domínio/empresa|
|Administre os ativos de dados com uma experiência de usuário amigável|Preciso ter um glossário de negócios para metadados específicos de negócios. Os usuários empresariais podem usar o alcance para cenários de autoatendimento para anotar seus dados e permitir que os dados sejam descobertos facilmente por meio de pesquisa.|Proprietário de domínio/empresa, analista de negócios, cientista de dados, engenheiro de dados|

## <a name="deployment-models"></a>Modelos de implantação

Se você tiver apenas um pequeno grupo usando alcance com casos de uso de consumo básico, a abordagem poderá ser tão simples quanto ter uma instância alcance para atender a todo o grupo. No entanto, você também pode se perguntar se sua organização precisa de mais de uma instância de alcance. E se estiver usando várias instâncias alcance, como os funcionários podem promover os ativos de um estágio para outro.

### <a name="determine-the-number-of-purview-instances"></a>Determinar o número de instâncias de alcance

Na maioria dos casos, deve haver apenas uma conta alcance para toda a organização. Essa abordagem tira o máximo proveito dos "efeitos de rede", em que o valor da plataforma aumenta exponencialmente como uma função dos dados que residem dentro da plataforma.

No entanto, há exceções a esse padrão:

1. **Testando novas configurações** – as organizações podem querer criar várias instâncias para testar as configurações de verificação ou as classificações em ambientes isolados. Embora haja um recurso de "controle de versão" em algumas áreas da plataforma, como Glossário, seria mais fácil ter uma instância "descartável" para teste livremente.
2. **Separação de teste, pré-produção e produção** – as organizações desejam criar diferentes plataformas para diferentes tipos de dados armazenados em ambientes diferentes. Não é recomendável que esses tipos de dados sejam tipos de conteúdo diferentes. Você pode usar o termo do glossário no nível de hierarquia superior ou categoria para separar os tipos de conteúdo.
3. **Conglomerados e modelo federado** – o conglomerados geralmente tem muitas unidades de negócios (barramento) que operam separadamente e, em alguns casos, eles nem compartilham a cobrança entre si. Nesses casos, a organização acabará criando uma instância alcance para cada BU. Esse modelo não é o ideal, mas pode ser necessário, especialmente porque o barramento geralmente não está disposto a compartilhar cobrança.
4. **Conformidade** – há alguns regimes de conformidade estritos, que tratam até mesmo metadados como confidenciais e exigem que eles estejam em uma geografia específica. Se uma empresa tiver várias regiões geográficas, a única solução será ter várias instâncias de alcance, uma para cada geografia.

### <a name="create-a-process-to-move-to-production"></a>Criar um processo para mover para a produção

Algumas organizações podem optar por manter as coisas simples trabalhando com uma única versão de produção do alcance. Eles provavelmente não precisam ir além dos cenários de descoberta, pesquisa e procura. Se alguns ativos tiverem termos de Glossário incorretos, será bastante tolerante a permitir que as pessoas se corrijam. No entanto, a maioria das organizações que desejam implantar alcance em várias unidades de negócios desejará ter alguma forma de processo e controle.

Outro aspecto importante a ser incluído no processo de produção é como as classificações e os rótulos podem ser migrados. Alcance tem mais de 90 classificadores de sistema. Você pode aplicar classificações personalizadas ou do sistema em ativos de arquivo, tabela ou coluna. As classificações são como marcas de assunto e são usadas para marcar e identificar o conteúdo de um tipo específico encontrado no seu estado de dados durante a verificação. Rótulos de sensibilidade são usados para identificar as categorias de tipos de classificação dentro de seus dados organizacionais e, em seguida, agrupar as políticas que você deseja aplicar a cada categoria. Ele usa os mesmos tipos de informações confidenciais que Microsoft 365, permitindo que você amplie suas políticas de segurança existentes e a proteção em todo o conteúdo e os dados de sua totalidade. Ele pode verificar e classificar documentos automaticamente. Por exemplo, se você tiver um arquivo chamado multiple.docx e ele tiver um número de ID nacional em seu conteúdo, o alcance adicionará classificação como o número de identificação nacional da UE na página de detalhes do ativo.

No alcance, há várias áreas em que os administradores de catálogo precisam garantir a consistência e a manutenção das práticas recomendadas ao longo de seu ciclo de vida:

* **Ativos de dados** – as fontes de dados precisarão ser examinadas novamente entre ambientes. Não é recomendável verificar apenas em desenvolvimento e, em seguida, gerá-los novamente usando APIs em produção. O principal motivo é que os scanners alcance fazem muito mais "fiação" nos bastidores nos ativos de dados, o que poderia ser complexo para movê-los para uma instância alcance diferente. É muito mais fácil apenas adicionar a mesma fonte de dados na produção e verificar as fontes novamente. A prática recomendada geral é ter a documentação de todas as verificações, conexões e mecanismos de autenticação que estão sendo usados.
* **Verificar conjuntos de regras** – esta é a coleção de regras atribuídas a uma verificação específica, como tipo de arquivo e classificações a serem detectadas. Se você não tiver muitos conjuntos de regras de verificação, é possível recriá-los manualmente novamente por meio da produção. Isso exigirá um processo interno e uma boa documentação. No entanto, se os conjuntos de regras forem alterados na base diária ou semanal, isso poderá ser resolvido explorando a rota da API REST.
* **Classificações personalizadas** – suas classificações também podem não ser alteradas regularmente. Durante a fase inicial de implantação, pode levar algum tempo para entender vários requisitos para surgir com classificações personalizadas. No entanto, uma vez liquidada, isso exigirá pouca alteração. Portanto, a recomendação aqui é migrar manualmente quaisquer classificações personalizadas ou usar a API REST.
* **Glossário** – é possível exportar e importar os termos do glossário por meio do UX. Para cenários de automação, você também pode usar a API REST.
* **Políticas de padrão do conjunto de recursos** – essa funcionalidade é muito avançada para que as organizações típicas sejam aplicadas. Em alguns casos, sua Azure Data Lake Storage tem convenções de nomenclatura de pastas e estrutura específica que podem causar problemas para alcance gerar o conjunto de recursos. Sua unidade de negócios também pode querer alterar a construção do conjunto de recursos com personalizações adicionais para atender às necessidades dos negócios. Para esse cenário, é melhor manter o controle de todas as alterações por meio da API REST e documentar as alterações por meio da plataforma de controle de versão externa.
* **Atribuição de função** – é aqui que você controla quem tem acesso ao alcance e quais permissões eles têm. O alcance também tem a API REST para dar suporte à exportação e importação de usuários e funções, mas isso não é compatível com a API do Atlas. A recomendação é atribuir um grupo de segurança do Azure e gerenciar a associação de grupo em vez disso.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Planejar e implementar diferentes pontos de integração com o alcance

É provável que uma organização madura já tenha um catálogo de dados existente. A principal questão é se deseja continuar a usar a tecnologia existente e a sincronização com alcance ou não. Para lidar com a sincronização com os produtos existentes em uma organização, o alcance fornece as APIs REST do Atlas. As APIs do Atlas fornecem um mecanismo poderoso e flexível que manipula cenários de push e pull. As informações podem ser publicadas no alcance usando as APIs do Atlas para inicializar ou enviar por push as atualizações mais recentes de outro sistema para o alcance. As informações disponíveis no alcance também podem ser lidas usando as APIs do Atlas e, em seguida, sincronizadas de volta para os produtos existentes. 

Para outros cenários de integração, como tíquete, interface do usuário personalizada e orquestração, você pode usar as APIs do Atlas e os pontos de extremidade do Kafka. Em geral, há quatro pontos de integração com alcance:

* **Ativo de dados** – isso permite que o alcance examine os ativos de um repositório para enumerar o que são esses ativos e coletar quaisquer metadados disponíveis imediatamente sobre eles. Portanto, para o SQL, isso pode ser uma lista de bancos de dados, tabelas, procedimentos armazenados, exibições de modos de exibição e de configuração sobre eles mantidos em locais como `sys.tables` . Para algo como Azure Data Factory (ADF), isso pode estar enumerando todos os pipelines e obtendo dados quando eles foram criados, última execução, estado atual.
* **Linhagem** – isso permite que o alcance colete informações de um sistema de mutação de dados/análise sobre como os dados são movidos. Para algo como o Spark, isso pode coletar informações da execução de um bloco de anotações para ver quais dados o notebook ingeriu, como ele o transformou e onde o emitiu. Para algo como o SQL, ele pode estar analisando logs de consulta para fazer engenharia reversa de quais operações de mutação foram executadas e o que eles fizeram. Damos suporte à linhagem baseada em push e pull, dependendo das necessidades.
* **Classificação** – isso permite que o alcance Pegue amostras físicas de fontes de dados e execute-as por meio de nosso sistema de classificação. O sistema de classificação descobre a semântica de uma parte dos dados. Por exemplo, poderemos saber que um arquivo é um arquivo parquet e tem três colunas e a terceira é uma cadeia de caracteres. Mas os classificadores que executamos nos exemplos nos indicarão que a cadeia de caracteres é um nome, endereço ou número de telefone. Acender esse ponto de integração significa que definimos como o alcance pode abrir objetos como notebooks, pipelines, arquivos parquet, tabelas e contêineres.
* **Experiência incorporada** – os produtos que têm uma experiência semelhante a "estúdio" (como ADF, Synapse, SQL Studio, PBI e Dynamics) geralmente desejam permitir que os usuários descubram os dados com os quais desejam interagir e também encontrem locais para os dados de saída. O catálogo do alcance pode ajudar a acelerar essas experiências fornecendo uma experiência de incorporação. Essa experiência pode ocorrer na API ou no nível de UX na opção do parceiro. Ao inserir uma chamada para alcance, a organização pode aproveitar o mapa do alcance do espaço de dados para encontrar ativos de dados, confira linhagem, verificar esquemas, examinar classificações, contatos etc.

## <a name="phase-1-pilot"></a>Fase 1: piloto

Nesta fase, alcance deve ser criado e configurado para um conjunto muito pequeno de usuários. Normalmente, é apenas um grupo de 2-3 pessoas trabalhando juntas para executar cenários de ponta a ponta. Eles são considerados defensores de alcance em sua organização. O objetivo principal dessa fase é garantir que as principais funcionalidades possam ser atendidas e que os participantes certos estejam cientes do projeto.

### <a name="tasks-to-complete"></a>Tarefas a concluir

|Tarefa|Detalhe|Duration|
|---------|---------|---------|
|Reúna & concorde com os requisitos|Discussão com todos os participantes para reunir um conjunto completo de requisitos. As pessoas diferentes devem participar para concordar sobre um subconjunto de requisitos a serem concluídos para cada fase do projeto.|1 semana|
|Configurar o kit do iniciante|Acesse [alcance início rápido](create-catalog-portal.md) e configure o [Kit do iniciante do alcance](tutorial-scan-data.md) para demonstrar alcance para todos os participantes.|1 dia|
|Navegando alcance|Entenda como usar o alcance da home page.|1 dia|
|Configurar o ADF para linhagem|Identificar pipelines-chave e ativos de dados. Reúna todas as informações necessárias para se conectar a uma conta interna do ADF.|1 dia|
|Verificar uma fonte de dados, como Azure Data Lake Storage|Adicione a fonte de dados e configure uma verificação. Verifique se a verificação detecta com êxito todos os ativos.|2 dias|
|Pesquisar e procurar|Permitir que os usuários finais acessem o alcance e executem cenários de pesquisa e navegação de ponta a ponta.|1 dia|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* A conta alcance foi criada com êxito na assinatura da organização no locatário da organização.
* Um pequeno grupo de usuários com várias funções pode acessar o alcance.
* Alcance está configurado para verificar pelo menos uma fonte de dados.
* Os usuários devem ser capazes de extrair valores de chave de alcance, como:
  * Pesquisar e procurar
  * Linhagem
* Os usuários devem ser capazes de atribuir propriedade de ativo na página ativo.
* Apresentação e demonstração para aumentar a conscientização das principais partes interessadas.
* Compre de gerenciamento para aprovar recursos adicionais para a fase MVP.

## <a name="phase-2-minimum-viable-product"></a>Fase 2: produto viável mínimo

Depois que você tiver os requisitos acordados e as unidades de negócios participantes para carregar o alcance, a próxima etapa será trabalhar em uma versão mínima do MVP (produto viável). Nesta fase, você expandirá o uso de alcance para mais usuários que terão necessidades adicionais na horizontal e na vertical. Haverá cenários importantes que devem ser atendidos horizontalmente para todos os usuários, como termos do glossário, pesquisa e navegação. Também haverá requisitos detalhados verticalmente para cada unidade ou grupo de negócios para cobrir cenários de ponta a ponta específicos, como a linhagem de Azure Data Lake Storage ao Azure Synapse DW para Power BI.

### <a name="tasks-to-complete"></a>Tarefas a concluir

|Tarefa|Detalhe|Duration|
|---------|---------|---------|
|[Examinar o Azure Synapse Analytics](register-scan-azure-synapse-analytics.md)|Comece a carregar suas fontes de banco de dados e verifique-as para preencher os principais ativos|2 dias|
|[Criar classificações e regras personalizadas](create-a-custom-classification-and-classification-rule.md)|Depois que seus ativos são verificados, os usuários podem perceber que há casos de uso adicionais para mais classificação ao lado das classificações padrão de alcance.|2-4 semanas|
|[Verificar Power BI](register-scan-power-bi-tenant.md)|Se sua organização usa Power BI, você pode digitalizar Power BI para reunir todos os ativos de dados que estão sendo usados por cientistas de dados ou analistas de dados que têm requisitos para incluir a linhagem da camada de armazenamento.|1-2 semanas|
|[Importar os termos do glossário](how-to-create-import-export-glossary.md)|Na maioria dos casos, sua organização pode já desenvolver uma coleção de termos do glossário e atribuição de termos para ativos. Isso exigirá um processo de importação em alcance por meio do arquivo. csv.|1 semana|
|Adicionar contatos aos ativos|Para os principais ativos, talvez você queira estabelecer um processo para permitir que outras pessoas possam atribuir contatos ou importar por meio de APIs REST.|1 semana|
|Adicionar rótulos confidenciais e examinar|Isso pode ser opcional para algumas organizações, dependendo do uso de rótulos de M365.|1-2 semanas|
|Obtenha informações confidenciais e de classificação|Para relatórios e informações sobre o alcance, você pode acessar essa funcionalidade para obter vários relatórios e fornecer apresentação ao gerenciamento.|1 dia|
|Integrar adição de usuários usando alcance usuários gerenciados|Esta etapa exigirá que o administrador do alcance trabalhe com o administrador do Azure Active Directory para estabelecer novos grupos de segurança para conceder acesso ao alcance.|1 semana|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* Integração de um grupo maior de usuários com êxito ao alcance (50 +)
* Verificar fontes de dados críticas de negócios
* Importar e atribuir todos os termos de Glossário críticos
* Teste de rotulação importante em ativos de chave com êxito
* Cenários mínimos atendidos com êxito para os usuários das unidades de negócios participos

## <a name="phase-3-pre-production"></a>Fase 3: pré-produção

Depois que a fase MVP tiver passado, é hora de planejar a etapa de pré-produção. Sua organização pode decidir ter uma instância separada de alcance para pré-produção e produção, ou manter a mesma instância, mas restringir o acesso. Também nesta fase, talvez você queira incluir a verificação em fontes de dados locais, como SQL Server. Se houver qualquer lacuna nas fontes de dados sem suporte do alcance, é hora de explorar a API do Atlas para entender as opções adicionais.

### <a name="tasks-to-complete"></a>Tarefas a concluir

|Tarefa|Detalhe|Duration|
|---------|---------|---------|
|Refinar a digitalização usando o conjunto de regras de verificação|Sua organização terá muitas fontes de dados para pré-produção. É importante definir previamente os critérios de chave para verificação para que as classificações e a extensão do arquivo possam ser aplicadas consistentemente no quadro.|1-2 dias|
|Avaliar a disponibilidade da região para verificação|Dependendo da região das fontes de dados e dos requisitos organizacionais de conformidade e segurança, talvez você queira considerar quais regiões devem estar disponíveis para verificação.|1 dia|
|Entender o conceito de firewall ao verificar|Esta etapa requer alguma exploração de como a organização configura seu firewall e como o alcance pode se autenticar para acessar as fontes de dados para verificação.|1 dia|
|Entender o conceito de link privado ao verificar|Se sua organização usa o link privado, você deve dispor a base da segurança de rede para incluir o link privado como parte dos requisitos.|1 dia|
|[Verificar SQL Server local](register-scan-on-premises-sql-server.md)|Isso é opcional se você tiver SQL Server local. A verificação exigirá a configuração de [Integration Runtime auto-hospedados](manage-integration-runtimes.md) e a adição de SQL Server como uma fonte de dados.|1-2 semanas|
|Usar a API REST do amalcance para cenários de integração|Se você tiver requisitos para integrar o alcance a outras tecnologias de terceiros, como orquestração ou sistema de tíquetes, talvez queira explorar a área da API REST.|1-4 semanas|
|Entender os preços do alcance|Esta etapa fornecerá a você informações financeiras importantes da organização para tomar decisões.|1-5 dias|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* Integração bem-sucedida de pelo menos uma unidade de negócios com todos os usuários
* Verificar fonte de dados local, como SQL Server
* Pelo menos um cenário de integração usando a API REST
* Concluir um plano para ir para a produção que deve incluir áreas-chave de infraestrutura e segurança

## <a name="phase-4-production"></a>Fase 4: produção

As fases acima devem ser seguidas para criar um controle de informações eficaz, que é a base para melhores programas de governança. A governança de dados ajudará sua organização a se preparar para as crescentes tendências, como ia, Hadoop, IoT e blockchain. É apenas o início para muitas coisas de dados e análises, e há muito mais que pode ser discutido. O resultado dessa solução forneceria:

* **Centrado nos negócios** – uma solução que está alinhada aos requisitos e cenários de negócios em relação aos requisitos técnicos.
* **Pronto para o futuro** – uma solução maximizará os recursos padrão da plataforma e usará práticas padronizadas do setor para configuração ou atividades de script para dar suporte aos avanços/evolução da plataforma.

### <a name="tasks-to-complete"></a>Tarefas a concluir

|Tarefa|Detalhe|Duration|
|---------|---------|---------|
|Verificar fontes de dados de produção com o firewall habilitado|Se isso for opcional quando o firewall estiver em vigor, mas for importante explorar as opções para proteger sua infraestrutura.|1-5 dias|
|Habilitar o Link Privado|Se for opcional, quando o link privado for usado. Caso contrário, você pode ignorá-lo, pois ele é um critério de que deve ter quando privado estiver habilitado.|1-5 dias|
|Criar fluxo de trabalho automatizado|O fluxo de trabalho é importante para automatizar processos como aprovação, escalonamento, revisão e gerenciamento de problemas.|2-3 semanas|
|Documentação da operação|A governança de dados não é um projeto de uso único. É um programa contínuo para alimentar a tomada de decisões voltada para dados e para criar oportunidades de negócios. É fundamental para documentar os principais procedimentos e os padrões de negócios.|1 semana|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* Integração de todas as unidades de negócios e seus usuários com êxito
* Atende aos requisitos de segurança e infraestrutura para produção com êxito
* Êxito ao atender a todos os casos de uso exigidos pelos usuários

## <a name="platform-hardening"></a>Proteção de plataforma

Etapas adicionais de proteção podem ser executadas:

* Aumente a postura de segurança habilitando a verificação nos recursos de firewall ou usando o link privado
* Ajuste a verificação de escopo para melhorar o desempenho da verificação
* Usar APIs REST para exportar metadados e propriedades críticas para backup e recuperação
* Use o fluxo de trabalho para automatizar a emissão de tíquetes e eventos para evitar erros humanos

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: executar o kit de início e verificar dados](tutorial-scan-data.md)
- [Tutorial: navegar na home page e pesquisar um ativo](tutorial-asset-search.md)