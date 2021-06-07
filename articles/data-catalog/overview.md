---
title: Introdução ao Catálogo de Dados do Azure
description: Este artigo fornece uma visão geral do Catálogo de Dados do Microsoft Azure, incluindo seus recursos e problemas que ele aborda. O Catálogo de Dados permite que qualquer usuário registre, descubra, entenda e consuma fontes de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: e128e9f7515e572fc0be4b92ef03d8d98529ac66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674896"
---
# <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

O Catálogo de Dados do Azure é um serviço de nuvem totalmente gerenciado. Ele permite que os usuários descubram os dados de origens de que precisam e entendam as fontes de dados que ele encontra. Ao mesmo tempo, o Catálogo de Dados ajuda as organizações a obter mais valor sobre os investimentos existentes.

Com o Catálogo de Dados, qualquer usuário (analista, cientista de dados ou desenvolvedor) pode descobrir, entender e consumir fontes de dados. O Catálogo de Dados inclui um modelo de crowdsourcing de metadados e anotações. Ele é um local único e central para que todos os usuários da organização possam contribuir com seus conhecimentos e criar uma comunidade e uma cultura de dados.

## <a name="discovery-challenges-for-data-consumers"></a>Desafios de descoberta para consumidores de dados

Tradicionalmente, a descoberta de fontes de dados da empresa tem sido um processo orgânico com base no conhecimento tribal. Para as empresas que desejam obter o valor máximo de seus ativos de informações, essa abordagem apresenta vários desafios:

* Os usuários poderão não saber que existe uma fonte de dados, a menos que entrem em contato com ela como parte de outro processo. Não há nenhum local central onde as fontes de dados são registradas.
* A menos que os usuários saibam o local de uma fonte de dados, eles não podem se conectar aos dados por meio de um aplicativo cliente. As experiências de consumo de dados exigem que os usuários conheçam a cadeia de conexão ou o caminho.
* A menos que os usuários saibam o local da documentação de uma fonte de dados, eles não conseguem entender o uso dos dados pretendido. A documentação e as fontes de dados podem residir em uma variedade de locais e ser consumidas por meio de uma variedade de experiências.
* Se os usuários tiverem perguntas sobre um ativo de informação, eles devem localizar o especialista ou a equipe responsável pelos dados e entrar em contato com eles offline. Não há nenhuma conexão explícita entre os dados e os especialistas com perspectivas sobre seu uso.
* A menos que os usuários compreendam o processo de solicitação de acesso à fonte de dados, a descoberta da fonte de dados e de sua documentação não será suficiente para ajudá-los a acessar os dados.

## <a name="discovery-challenges-for-data-producers"></a>Desafios de descoberta para produtores de dados

Embora os consumidores de dados enfrentem os desafios mencionados, os usuários responsáveis por produzir e manter informações também têm seus próprios obstáculos:

* A anotação de fontes de dados com metadados descritivos geralmente é um esforço à toa. Os aplicativos clientes geralmente ignoram descrições armazenadas na fonte de dados.
* A criação de documentação para fontes de dados geralmente é um esforço à toa. Manter a documentação em sincronia com fontes de dados é uma responsabilidade contínua. Os usuários talvez não confiem em documentos que pareçam estar desatualizados.
* Criar e manter documentação para fontes de dados é um processo complexo e demorado. Disponibilizar essa documentação prontamente para qualquer pessoa que usa a fonte de dados geralmente pode ser ainda mais.
* Restringir o acesso às fontes de dados e garantir que os consumidores de dados saibam como solicitar o acesso são um desafio contínuo.

Quando esses desafios são combinados, eles apresentam uma barreira significativa para as empresas que desejam encorajar e promover o uso e a compreensão dos dados da empresa.

## <a name="azure-data-catalog-can-help"></a>O Catálogo de Dados do Azure pode ajudar

O Catálogo de Dados foi projetado para resolver esses problemas e ajudar as empresas a aproveitar ao máximo seus ativos de informações existentes. O Catálogo de Dados torna fontes de dados facilmente identificáveis e compreensíveis para os usuários que gerenciam os dados.

O Catálogo de Dados fornece um serviço baseado em nuvem no qual uma fonte de dados pode ser registrada. Os dados permanecem no local existente, mas uma cópia de seus metadados é adicionada ao Catálogo de Dados, juntamente com uma referência ao local da fonte de dados. Os metadados também são indexados para tornar cada fonte de dados fácil de descobrir por meio de pesquisa e compreensível para os usuários que os descobrirem.

Depois de uma fonte de dados ter sido registrada, seus metadados poderão ser aprimorados. Os metadados podem ser adicionados pelo usuário que os registrou ou por outros usuários na empresa. Qualquer usuário pode anotar uma fonte de dados, fornecendo descrições, marcas ou outros metadados, como documentação, e processos para a solicitação de acesso à fonte de dados. Esses metadados descritivos complementam os metadados estruturais (como nomes de colunas e tipos de dados) registrados da fonte de dados.

Descobrir e entender fontes de dados e seu uso é o principal objetivo de registrar as fontes. Usuários corporativos podem precisar de dados de business intelligence, desenvolvimento de aplicativos, ciência de dados ou outra tarefa em que os dados certos são necessários. Eles podem usar a experiência de descoberta do Catálogo de Dados para localizar dados que correspondam às suas necessidades rapidamente, compreender os dados para avaliar sua adequação à finalidade e consumir os dados ao abrir a fonte de dados na sua ferramenta de escolha. 

Ao mesmo tempo, os usuários podem contribuir para o catálogo marcando, documentando e anotando as fontes de dados que já foram registradas. Eles também podem registrar novas fontes de dados, que podem então ser descobertas, entendidas e consumidas pela comunidade de usuários do catálogo.

![Recursos do Catálogo de Dados](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="learn-more-about-data-catalog"></a>Saiba mais sobre o Catálogo de Dados

Para saber mais sobre os recursos do Catálogo de Dados, confira:

* [Como registrar fontes de dados](data-catalog-how-to-register.md)
* [Como descobrir fontes de dados](data-catalog-how-to-discover.md)
* [Como anotar fontes de dados](data-catalog-how-to-annotate.md)
* [Como documentar fontes de dados](data-catalog-how-to-documentation.md)
* [Como se conectar a fontes de dados](data-catalog-how-to-connect.md)
* [Como trabalhar com Big Data](data-catalog-how-to-big-data.md)
* [Como gerenciar ativos de dados](data-catalog-how-to-manage.md)
* [Como configurar o Glossário de Negócios](data-catalog-how-to-business-glossary.md)
* [Perguntas frequentes](data-catalog-frequently-asked-questions.md)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Catálogo de Dados:

* [Início Rápido: Criar um Catálogo de Dados do Azure](data-catalog-get-started.md)
* [Abra seu Catálogo de Dados do Azure](https://www.azuredatacatalog.com)