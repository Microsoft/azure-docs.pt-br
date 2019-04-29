---
title: Abordagem e processo de adoção do Catálogo de Dados do Azure
description: Este artigo apresenta uma abordagem e o processo para as organizações que consideram a adoção do Catálogo de Dados do Azure, inclusive a definição de uma visão, a identificação dos casos de uso comerciais importantes e a escolha um projeto piloto.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 62eb51e48ab688abcf39ba3c8d57aaccf6f47cb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61002389"
---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Abordagem e processo de adoção do Catálogo de Dados do Azure

Este artigo o ajuda a começar a adotar o **Catálogo de Dados do Azure** em sua organização. Para adotar com êxito o **Catálogo de Dados do Azure**, convém concentrar-se em três itens principais: definir sua visão, identificar casos de uso de negócios em sua organização e escolher um projeto-piloto.

## <a name="introducing-the-azure-data-catalog"></a>Apresentação do Catálogo de Dados do Azure

No mundo do trabalho, as expectativas das pessoas sobre como elas devem ser capazes de localizar informações especializadas sobre ativos de dados mudaram. Hoje, com o uso difundido no local de trabalho de ferramentas de mídia social, como o Yammer, as pessoas esperam poder obter rapidamente assistência e consultoria sobre uma ampla variedade de tópicos. O **Catálogo de Dados do Azure** ajuda as empresas e equipes a consolidar as informações sobre ativos de dados da empresa em um repositório central. Os consumidores de dados podem descobrir esses ativos de dados e aprender com a contribuição de especialistas no assunto.

Este artigo apresenta uma abordagem para começar a usar o **Catálogo de Dados do Azure**. O artigo descreve um plano de adoção típico do Catálogo de Dados para a empresa fictícia chamada Adventure Works.

**O que é o Catálogo de Dados do Azure?**

**Catálogo de Dados do Azure** é um serviço totalmente gerenciado no Azure e um catálogo de informações (metadados) para toda a empresa que habilita a descoberta de fontes de dados de autoatendimento. Com o Catálogo de Dados, você registra, descobre, anota e se conecta a ativos de dados. O Catálogo de Dados é criado para gerenciar ativos de informações diferentes a fim de facilitar a localização de ativos de dados, compreendê-los e conectá-los. Ele reduz o tempo de aprofundamento nos dados disponíveis e aumenta o valor para organizações. Para saber mais, confira [Catálogo de Dados do Microsoft Azure](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Plano de adoção do Catálogo de Dados do Azure

Um plano adoção do **Catálogo de Dados do Azure** descreve como os benefícios de usar o serviço serão comunicados aos participantes e aos usuários, além do tipo de treinamento que você fornecerá aos usuários. Um fator-chave de sucesso para adotar o Catálogo de Dados é a eficácia com que você comunica o valor do serviço aos usuários e participantes. O público principal em um plano de adoção inicial é composto pelos usuários do serviço. Não importa quanto apoio você obtenha dos participantes, se os usuários ou clientes de sua oferta de Catálogo de Dados não o incorporarem ao uso deles, a adoção não será bem-sucedida. Portanto, este artigo pressupõe que você tenha o apoio dos participantes e se concentra na criação de um plano para a adoção dos usuários do Catálogo de Dados.
Um plano de adoção eficaz envolve as pessoas com êxito no que é possível com o Catálogo de Dados e fornece a elas as informações e a orientação para obtê-lo. Os usuários precisam entender o valor que o Catálogo de Dados oferece para ajudá-los a ter êxito em seus trabalhos. Quando as pessoas veem como o Catálogo de Dados pode ajudá-las a obter mais resultados com os dados, o valor de adotar o Catálogo de Dados torna-se claro. A alteração é difícil; portanto, um plano eficaz precisa levar em conta os desafios da alteração.

Um plano de adoção o ajuda a comunicar o que é crucial para que as pessoas tenham êxito e atinjam suas metas. Um plano típico explica como o Catálogo de Dados facilitará as vidas dos usuários e inclui as seguintes partes:

* **Declaração de visão** - isso o ajudará a discutir de forma concisa o plano de adoção com os usuários e os participantes. É o argumento de reforço.
* **Equipe-piloto e influenciadores** - aprender com uma equipe-piloto e com os influenciadores o ajudará a refinar a maneira de apresentar o Catálogo de Dados às equipes e aos usuários. Os influenciadores podem treinar outros usuários. Isso também o ajuda a identificar bloqueadores e impulsionadores para a adoção.
* **Plano de comunicações e repercussão** - isso ajuda os usuários a entender como o Catálogo de Dados pode ajudá-los, pode promover a adoção orgânica em equipes e, em última análise, na organização inteira.
* **Plano de treinamento** - o treinamento abrangente geralmente leva ao sucesso da adoção e a resultados favoráveis.

Aqui estão algumas dicas para definir um plano de adoção do **Catálogo de Dados do Azure** .

## <a name="define-your-data-catalog-project-vision"></a>Defina sua visão do projeto do Catálogo de Dados

A primeira etapa para definir um plano de adoção do **Catálogo de Dados do Azure** é escrever uma descrição de aspiração do que você está tentando realizar. É melhor manter a declaração de visão bastante abrangente, mas suficientemente concisa para definir metas específicas de curto e de longo prazo.

Aqui estão algumas dicas para ajudá-lo a definir sua visão:

* **Identifique o principal impulsionador de implantação** - pense nas necessidades de gerenciamento de fonte de dados específicas da empresa que podem ser abordadas com o Catálogo de Dados. Isso também ajuda a definir as principais vantagens de usar o Catálogo de Dados. Por exemplo, pode haver fontes de dados comuns que todos os novos funcionários precisam aprender e usar ou fontes de dados importantes e complexas que apenas algumas pessoas-chave entendem profundamente. **Catálogo de Dados do Azure** pode ajudar a tornar essas fontes de dados fáceis de descobrir e entender, para que esses pontos problemáticos conhecidos possam ser abordados de forma direta e antecipada na adoção do serviço.
* **Seja nítido e claro** - uma clara compreensão da visão colocará todos em sincronia sobre o valor que o Catálogo de Dados proporciona à organização e como a visão dá suporte a metas organizacionais.
* **Inspire as pessoas a usar o Catálogo de Dados** - sua visão e o plano de comunicação devem inspirar as pessoas a reconhecer que o Catálogo de Dados pode beneficiá-las para localizar e se conectar a fontes de dados e fazer mais com os dados.
* **Indique as metas específicas e a linha do tempo** - isso faz com que seu plano de adoção possa ter resultados específicos e alcançáveis. Uma linha do tempo mantém todos concentrados e possibilita pontos de verificação para medir o sucesso.

Aqui está uma declaração de visão de exemplo para um plano de adoção do Catálogo de Dados para a empresa fictícia chamada Adventure Works:

**O catálogo de dados do Azure** capacita a equipe de finanças do Adventure Works para colaborar em fontes de dados de chave, para que cada membro da equipe possa facilmente localizar e usar os dados que eles precisam e compartilham seus conhecimentos com a equipe como um todo.

Depois que tiver uma declaração de visão clara, você deverá identificar um projeto-piloto adequado para o Catálogo de Dados. Geralmente, há vários cenários para o Catálogo de Dados. A próxima seção fornece algumas dicas para identificar casos de uso relevantes.

## <a name="identify-data-catalog-business-use-cases"></a>Identificar casos de uso de negócios do Catálogo de Dados

Para identificar casos de uso que são relevantes para o Catálogo de Dados, entre em contato com especialistas de várias unidades de negócios para identificar casos de uso relevantes e problemas de negócios para resolver. Examine os desafios existentes que as pessoas enfrentam para identificar e compreender ativos de dados. Por exemplo, as equipes tomam conhecimento dos ativos de dados somente depois de perguntar a várias pessoas na organização quem tem as fontes de dados relevantes?

É melhor escolher casos de uso que oferecem o melhor retorno: casos que são importantes, mas terão uma alta probabilidade de sucesso se forem resolvidos com o Catálogo de Dados.

Aqui estão algumas dicas para identificar casos de uso:

* **Defina as metas da equipe** - como a equipe atinge suas metas? Não se concentre ainda no Catálogo de Dados, pois você deseja ser objetivo nessa etapa. Lembre-se: o foco são os resultados de negócios, não a tecnologia.
* **Defina o problema de negócios** - quais são os problemas enfrentados pela equipe ao localizar e aprender sobre os ativos de dados? Por exemplo, informações sobre fontes de dados importantes podem ser localizadas em pastas de trabalho do Excel em uma pasta de rede, e a equipe pode gastar muito tempo localizando as pastas de trabalho.
* **Entenda a cultura da equipe relacionada às alterações** - muitos desafios de adoção estão relacionados com a resistência às alterações, em vez da implementação de uma nova ferramenta. A maneira como uma equipe reage às alterações é importante ao identificar os casos de uso, pois o processo existente pode estar em vigor porque "é assim que sempre fizemos" ou "se não está quebrado, para que consertar?". A adoção de qualquer nova ferramenta ou processo sempre é mais fácil quando as pessoas afetadas entendem o valor a ser obtido com a alteração e apreciam a importância dos problemas a serem resolvidos.
* **Mantenha o foco relacionado a ativos de dados** – ao abordar os problemas empresariais enfrentados por uma equipe, você precisa "eliminar o supérfluo" e se concentrar no que é relevante para aproveitar os ativos de dados corporativos de forma mais eficaz.

Aqui estão alguns casos de uso de exemplo relacionados ao Catálogo de Dados:

### <a name="example-use-cases"></a>Casos de uso de exemplo

* **Registrar fontes de dados de alto valor central** - a TI gerencia as fontes de dados usadas em toda a organização. A TI pode começar a usar o Catálogo de Dados registrando e anotando as fontes de dados corporativas comuns.
* **Registrar fontes de dados baseadas em equipe** - equipes diferentes têm fontes de dados úteis de linha de negócios. Comece a usar o **Catálogo de Dados do Azure** identificando e registrando as principais fontes de dados usadas por muitas equipes diferentes e capture o conhecimento global da equipe em anotações no **Catálogo de Dados do Azure**.
* **Business intelligence de autoatendimento** - as equipes gastam muito tempo para combinar dados de várias fontes. Registre e anote as fontes de dados em um local central para eliminar um processo manual de descoberta de fontes de dados.

Para saber mais sobre cenários do Catálogo de Dados, confira [Cenários comuns do Catálogo de Dados do Azure](data-catalog-common-scenarios.md).

Depois que você identificar alguns casos de uso para o Catálogo de Dados, deverão surgir cenários comuns. A próxima seção descreve como identificar seu primeiro projeto-piloto com base em um caso de uso.

## <a name="choose-a-data-catalog-pilot-project"></a>Escolha um projeto-piloto do Catálogo de Dados

Um fator de sucesso importante é simplificar e começar aos poucos. Um piloto bem definido com um escopo restrito ajuda a manter o andamento do projeto sem a sobrecarga de um projeto muito complexo ou com um número excessivo de participantes. Porém, também é importante incluir uma variedade de usuários, desde usuários pioneiros até céticos. Os usuários que adotam a solução o ajudam a refinar seu plano de comunicação e repercussão futuros. Os céticos o ajudam a identificar e abordar problemas de bloqueio. À medida que os céticos se tornarem defensores, você poderá usar seus comentários para identificar impulsionadores de sucesso.

O plano-piloto deve ter etapas para metas de negócios que você deseja alcançar com o Catálogo de Dados. Ao aprender com o piloto inicial, você poderá expandir sua base de usuários. Um piloto fechado inicial é adequado para estabelecer o êxito mensurável, mas a meta final é o crescimento orgânico ou viral. Com o crescimento orgânico do Catálogo de Dados, os usuários estão no controle de seu próprio uso de dados e podem influenciar e incentivar outras pessoas a adotar e contribuir para o catálogo.

### <a name="target-the-right-team"></a>Direcione a equipe certa

Ao escolher o projeto-piloto, selecione uma equipe com os cenários mais atraentes que resolva um problema de negócios existente. Por exemplo, uma analista de negócios cria relatórios de um banco de dados do SQL Server. O problema é que eles se tornou cientes da fonte de dados somente após conversar com vários colegas. Finalmente, após perder tempo tentando encontrar fontes de dados para usar, eles descobriram uma pasta de trabalho do Excel, que contém uma descrição de cada fonte de dados. Embora a pasta de trabalho do Excel descreva adequadamente as tabelas que eles precisam, eles teria localizado rapidamente essas fontes de dados se elas estivessem registradas e anotadas no **catálogo de dados do Azure**.

### <a name="identify-data-heroes"></a>Identifique os heróis de dados

Seu primeiro projeto-piloto deve ter alguns indivíduos que produzem e consomem dados, de forma que a equipe tenha uma representação equilibrada.

**Produtores de dados** são pessoas com especialização em fontes de dados. Por exemplo, Pedro, em outra equipe, trabalhou amplamente com as principais fontes de dados da Adventure Works. Antes da adoção do **Catálogo de Dados do Azure**, Pedro criou uma pasta de trabalho do Excel para capturar informações sobre as fontes de dados da Adventure Works.

**Consumidores de dados** são pessoas com especialização no uso dos dados para solucionar problemas de negócios. Por exemplo, Clara é uma analista de negócios que usa fontes de dados do SQL Server da Adventure Works para analisar dados.

Um dos problemas de negócios que o **Catálogo de Dados do Azure** aborda é conectar os **Produtores de dados** aos **Consumidores de dados**. Ele faz isso atuando como um repositório central de informações sobre fontes de dados da empresa. Usando o Catálogo de Dados, Pedro registra as fontes de dados da Adventure Works e do SQL Server. Usando crowdsourcing qualquer usuário que descobre essa fonte de dados pode compartilhar suas opiniões sobre os dados, além de usar os dados que eles descobriram. Por exemplo, Clara descobre as fontes de dados pesquisando o catálogo e compartilha seu conhecimento especializado sobre os dados.  Agora, outras pessoas na organização se beneficiam do conhecimento compartilhado pesquisando o catálogo de dados.

* Para saber mais sobre como registrar fontes de dados, confira [Registrar fontes de dados](data-catalog-get-started.md).
* Para saber mais sobre a descoberta de fontes de dados, consulte [Pesquisar fontes de dados](data-catalog-get-started.md).

### <a name="start-small-and-focused"></a>Comece aos poucos e mantenha o foco

Na maioria dos projetos-piloto da empresa, você deve propagar o catálogo com fontes de dados de alto valor para que os usuários de negócios possam ver rapidamente o valor do Catálogo de Dados. A TI é um bom lugar para começar a identificar fontes de dados comuns que seriam interessantes para sua equipe-piloto. Para fontes de dados com suporte, como o SQL Server, é recomendável usar a ferramenta de registro de fontes de dados do **Catálogo de Dados do Azure** . Com a ferramenta de registro de fontes de dados, você pode registrar uma ampla variedade de fontes de dados, incluindo bancos de dados do SQL Server e Oracle e relatórios do SQL Server Reporting Services. Para obter uma lista completa de fontes de dados atuais, confira [Fontes de dados com suporte do Catálogo de Dados do Azure](data-catalog-dsr.md).

Depois de identificar e registrar as principais fontes de dados, também é possível importar descrições de fonte de dados armazenadas em outros locais. A API do Catálogo de Dados permite aos desenvolvedores carregar descrições e anotações de outro local, como a Pasta de Trabalho do Excel que Pedro criou e mantém.

A próxima seção descreve um projeto de exemplo da empresa Adventure Works.

### <a name="an-example-project"></a>Um projeto de exemplo

Neste exemplo, a analista de negócios Clara cria relatórios para sua equipe usando dados de um banco de dados do SQL Server. O problema é que eles se tornou cientes da fonte de dados somente após conversar com vários colegas. Eles teria localizado rapidamente essas fontes de dados se elas estivessem registradas e anotadas em um local central, como **catálogo de dados do Azure**.

Para ilustrar a facilidade com que Clara e sua equipe podem localizar dados de alto valor, você pode usar a ferramenta de registro de fonte de dados para popular o Catálogo com informações (metadados) sobre as fontes de dados. Dessa forma, as informações sobre o banco de dados estão disponíveis para a equipe e a empresa, não apenas para alguns indivíduos. Depois que as fontes de dados são registradas no Catálogo de Dados, Clara e sua equipe podem usá-las facilmente. O resultado é um catálogo de dados mais abrangente e relevante para sua equipe e a empresa. À medida que mais equipes adotam o Catálogo de Dados, as fontes de dados corporativos se tornam mais fáceis de localizar e usar, habilitando uma cultura mais centrada nos dados para que seja possível fazer mais com os dados.

Para saber mais sobre a ferramenta de registro de fonte de dados, confira [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md).

Como parte do projeto-piloto, a equipe de Clara também usa fontes de dados que são descritas em uma pasta de trabalho do Excel que Pedro e seus colegas mantêm. Como outras equipes da empresa também usam pastas de trabalho do Excel para descrever fontes de dados, a equipe de TI decide criar uma ferramenta para migrar a pasta de trabalho do Excel para o Catálogo de Dados. Usando a API REST do Catálogo de Dados para importar anotações existentes, a equipe de projeto-piloto pode ter um catálogo de dados completo que consiste em metadados extraídos de fontes de dados usando a ferramenta de registro de fonte de dados, com informações documentadas anteriormente por produtores e consumidores de dados, sem a necessidade de reinserção manual. À medida que o catálogo de dados corporativo cresce, a organização pode usar a ferramenta de registro de fonte de dados para fontes de dados comuns, e a API do Catálogo de Dados para fontes personalizadas e cenários incomuns.

> [!NOTE]
> Criamos uma ferramenta de exemplo que usa a API do **Catálogo de Dados do Azure** para migrar uma pasta de trabalho do Excel para o Catálogo de Dados. Para saber mais sobre a API do Catálogo de Dados e a ferramenta de exemplo, [baixe o exemplo de código da pasta de trabalho Ad Hoc](https://azure.microsoft.com/documentation/samples/data-catalog-dotnet-excel-register-data-assets/) e conferir a documentação da [API REST de Catálogo de Dados do Azure](/rest/api/datacatalog/).

Depois que o projeto-piloto estiver em vigor, será a hora de executar o plano de adoção do Catálogo de Dados.

### <a name="execute"></a>Executar

Nesse ponto, você identificou casos de uso para o Catálogo de Dados e identificou seu primeiro projeto. Além disso, você registrou as principais fontes de dados da Adventure Works e adicionou informações da pasta de trabalho do Excel existente usando a ferramenta que a TI criou. Agora é hora de trabalhar com a equipe-piloto para iniciar o processo de adoção do Catálogo de Dados.

Aqui estão algumas dicas para começar:

* **Crie expectativa** - os usuários de negócios ficarão empolgados se acreditarem que o **Catálogo de Dados do Azure** facilitará suas vidas. Tente guiar a conversa para a solução e seus benefícios, não a tecnologia.
* **Possibilite a alteração** - comece aos poucos e comunique o plano aos usuários de negócios. Para ter êxito, é crucial envolver os usuários desde o início para que eles influenciem o resultado e desenvolvam um senso de propriedade sobre a solução.
* **Treine os pioneiros** - os pioneiros são usuários de negócios que são apaixonados pelo que fazem e estão empolgados em apregoar os benefícios do **Catálogo de Dados do Azure** para seus colegas.
* **Direcione o treinamento** - os usuários de negócios não precisam saber tudo sobre o Catálogo de Dados. Portanto, direcione o treinamento para abordar metas específicas da equipe. Concentre-se no que os usuários fazem e como algumas de suas tarefas podem ser alteradas, para incorporar o **Catálogo de Dados do Azure** à sua rotina diária.
* **Esteja disposto a falhar** - se o piloto não estiver obtendo os resultados desejados, reavalie e identifique áreas a serem alteradas. Corrija os problemas do piloto antes de passar para um escopo mais amplo.

Antes que a equipe-piloto comece a usar o Catálogo de Dados, agende uma reunião inicial para discutir as expectativas para o projeto-piloto e fornecer treinamento inicial.

### <a name="set-expectations"></a>Defina as expectativas

Definir expectativas e metas ajuda os usuários de negócios a se concentrar em resultados finais específicos. Para manter o projeto nos trilhos, atribua tarefas regulares (por exemplo, diárias ou semanais, com base no escopo e na duração do piloto). Um dos recursos mais valiosos do Catálogo de Dados é o crowdsourcing de ativos de dados para que os usuários de negócios possam se beneficiar de conhecimentos dos dados corporativos. Uma ótima tarefa para todos os membros da equipe-piloto é registrar ou anotar pelo menos uma fonte de dados que eles usaram. Confira [Registrar uma fonte de dados](data-catalog-get-started.md) e [Como anotar fontes de dados](data-catalog-get-started.md).

Reúna-se com a equipe regularmente para examinar algumas das anotações. Boas anotações sobre fontes de dados são a essência de uma adoção bem-sucedida do Catálogo de Dados, pois fornecem percepções significativas de fontes de dados em um local central. Sem boas anotações, os conhecimentos sobre as fontes de dados permanecem espalhados por toda a empresa. Confira [Como anotar fontes de dados](data-catalog-get-started.md).

E o teste final do projeto é se os usuários podem descobrir e entender as fontes de dados que precisam usar. Os usuários do piloto devem testar regularmente o catálogo para garantir que as fontes de dados que eles usam para seu trabalho diário são relevantes. Quando uma fonte de dados necessária está ausente ou não foi anotada corretamente, isso deve servir como um lembrete para registrar fontes de dados adicionais ou para fornecer anotações adicionais. Essa prática não apenas adiciona valor à iniciativa do piloto, mas também cria hábitos eficazes que são transferidos para outras equipes após a conclusão do piloto.

### <a name="provide-training"></a>Forneça treinamento

O treinamento deve ser suficiente para que os usuários iniciem o trabalho e deve ser adaptado às metas específicas e ao nível de experiência dos membros da equipe-piloto. Para começar com o treinamento, você pode seguir as etapas do artigo [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md) . Além disso, você pode baixar a [Apresentação de treinamento de projeto-piloto do Catálogo de Dados do Azure](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true). Essa apresentação do PowerPoint deve ajudá-lo a apresentar o Catálogo de Dados aos membros da equipe-piloto.

## <a name="conclusion"></a>Conclusão

Depois que a equipe-piloto estiver trabalhando bem e você tiver atingido suas metas iniciais, expanda a adoção do Catálogo de Dados para mais equipes. Aplique e refine o que você aprendeu com o projeto-piloto para expandir o Catálogo de Dados por toda a organização.

Os pioneiros que participaram do piloto podem ser úteis para apregoar os benefícios da adoção do Catálogo de Dados. Eles podem compartilhar com outras equipes como o Catálogo de Dados ajudou a sua equipe a solucionar problemas de negócios, descobrir fontes de dados mais facilmente e compartilhar informações sobre as fontes de dados que eles usam. Por exemplo, os pioneiros da equipe-piloto da Adventure Works podem mostrar aos outros como é fácil encontrar informações sobre os ativos de dados da Adventure Works que, antes, eram difíceis de localizar e compreender.

Este artigo foi uma introdução ao **Catálogo de Dados do Azure** em sua organização. Esperamos que você tenha conseguido iniciar um projeto-piloto do Catálogo de Dados e expandir o Catálogo de Dados por toda a organização.

## <a name="next-steps"></a>Próximas etapas

[Criar um catálogo de dados do Azure](data-catalog-get-started.md)
