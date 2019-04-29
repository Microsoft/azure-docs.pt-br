---
title: Cenários comuns de Catálogo de Dados do Azure
description: Uma visão geral dos cenários comuns para o Catálogo de Dados do Azure, incluindo o registro e a descoberta de fontes de dados de alto valor, a habilitação de business intelligence de autoatendimento e a captura de conhecimento existente sobre fontes de dados e processos.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: e95cc64b9086a6fb4c5e2d42521a5fd3f44244ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003949"
---
# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns de Catálogo de Dados do Azure
Este artigo apresenta cenários comuns nos quais o Catálogo de Dados do Azure pode ajudar a sua organização a obter mais valor de suas fontes de dados existentes.

## <a name="scenario-1-registration-of-central-data-sources"></a>Cenário 1: Registro de fontes de dados central
As organizações costumam têm várias fontes de dados de alto valor. Essas fontes de dados incluem linha de negócios, sistemas OLTP (processamento de transações online), data warehouses e bancos de dados de business intelligence/análise. O número de sistemas e a sobreposição entre eles geralmente cresce com o tempo conforme as necessidades das empresas evoluem e a empresa em si evolui por meio de, por exemplo, fusões e aquisições.

Pode ser difícil para os membros da organização saber em que ponto localizar os dados dentro dessas fontes de dados. Perguntas como as seguintes são muito comuns:

* Dos três sistemas de RH usados dentro da empresa, qual devo usar para criar esse tipo de relatório?
* Onde posso ir para obter os números de vendas oficiais para o ano fiscal que acabou de se encerrar?
* Quem devo consultar ou qual é processo devo usar para obter acesso ao data warehouse?
* Não sei se esses números estão corretos. A quem posso pedir mais informações sobre como esses dados devem ser usados para compartilhar esse painel com minha equipe?

Para essas e outras perguntas, O Catálogo de Dados do Azure pode fornecer as respostas. As fontes de dados centrais de alto valor gerenciadas por TI usadas em todas as organizações costumam ser o ponto de partida lógico para preencher o catálogo. Embora qualquer usuário possa registrar uma fonte de dados, iniciar o catálogo com as fontes de dados que têm mais probabilidade de fornecer valor para o maior número de usuários ajudará a promover a adoção e o uso do sistema. 

Se você estiver começando a usar o Catálogo de Dados do Azure, identificar e registrar as principais fontes de dados usadas por muitas equipes diferentes de consumidores de dados pode ser seu primeiro passo para o sucesso.

Este cenário também apresenta uma oportunidade para anotar as fontes de dados de alto valor para torná-las mais fáceis de entender e de acessar. Um aspecto fundamental desse esforço é incluir informações sobre como os usuários podem solicitar acesso à fonte de dados. Com o Catálogo de Dados do Azure, você pode fornecer o endereço de email do usuário ou da equipe responsável pelo controle do acesso à fonte de dados, links para ferramentas ou documentação existentes ou texto livre que descreve o processo de solicitação de acesso. Essas informações ajudam os membros que descobrem fontes de dados registradas, mas ainda não têm permissões para acessar os dados a facilmente solicitar acesso usando os processos definidos e controlados pelos proprietários da fonte de dados.

## <a name="scenario-2-self-service-business-intelligence"></a>Cenário 2: Autoatendimento de business intelligence do
Embora as soluções corporativas tradicionais de business intelligence continuem sendo uma parte valiosa das paisagens de dados de muitas organizações, a mudança no ritmo dos negócios torna a BI de autoatendimento cada vez mais importante. Usando BI de autoatendimento, operadores e analistas de informações podem criar seus próprios relatórios, pastas de trabalho e painéis sem depender de uma equipe de TI central nem ficar restrito pela programação e pela disponibilidade dessa equipe de TI.

Em cenários de BI de autoatendimento, é comum os usuários combinarem dados de várias fontes, muitas das quais podem não ter sido usadas anteriormente para BI e análise. Embora algumas dessas fontes de dados possam já ser conhecidas, pode ser um desafio descobrir o que fazer para localizar e avaliar as possíveis fontes de dados para uma determinada tarefa.

Tradicionalmente, esse processo de descoberta é manual: os analistas usam suas conexões de rede de computador par para identificar outros que trabalham com os dados que eles estão buscando. Depois que uma fonte de dados é encontrada e usada, o processo se repetirá novamente para cada esforço de BI de autoatendimento subsequente, com vários usuários executando um processo manual redundante de descoberta.

Com o Catálogo de Dados do Azure, sua organização pode quebrar esse ciclo de esforço. Depois de descobrir uma fonte de dados pela via tradicional, um analista pode registrá-lo para tornar sua descoberta mais fácil para outros usuários no futuro. Embora o analista possa adicionar mais valor anotando os ativos de dados registrados, essa anotação não precisa acontecer ao mesmo tempo que o registro. Os usuários podem contribuir ao longo do tempo, conforme permitido pelas suas agendas, agregando valor gradualmente para as fontes de dados registradas no catálogo.

Esse crescimento orgânico do conteúdo do catálogo é um complemento natural para o registro inicial de fontes de dados central. Pré-popular o catálogo com dados que muitos usuários precisarão pode ser uma motivação para o uso e descoberta iniciais. Habilitar usuários a registrarem-se e anotarem fontes adicionais pode ser uma maneira manter tanto eles quanto outros membros da organização envolvidos.

Vale observar que, embora esse cenário se concentre especificamente em BI de autoatendimento, os mesmos padrões e desafios aplicam-se também a projetos de BI corporativos em grande escala. Usando o Catálogo de Dados, sua organização pode melhorar qualquer esforço que envolva um processo manual de descoberta de fonte de dados.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Cenário 3: Capturando o conhecimento tribal
Como saber quais dados você precisa para fazer seu trabalho e onde encontrá-los?

Se você estiver na sua função por algum tempo, você provavelmente já sabe. Você passou por um processo de aprendizado gradual e aprendeu ao longo do tempo sobre as fontes de dados essenciais para o seu trabalho diário.

Quando um novo funcionário entra na sua equipe, como essa pessoa sabe quais dados são necessários para o trabalho e onde encontrá-los?

É provável que a nova pessoa aborde-o com essas perguntas.

Essa transferência contínua de conhecimento do grupo faz parte do processo de descoberta de fonte de dados em organizações grandes e pequenas. Membros da equipe mais antigos e experientes acumularam conhecimento ao longo dos anos e os membros mais novos da equipe aprendem a perguntar quando surgem dúvidas. As informações mais importantes geralmente existem apenas na cabeça de algumas pessoas importantes e quando elas estão de férias ou deixam a equipe, a organização sofre.

Os especialistas de dados costumam fazer um esforço para documentar seu conhecimento, compartilhando-o por email ou em documentos do Word em um site do SharePoint da equipe. Embora essa abordagem possa ser útil, ela introduz um novo problema de descoberta: como as pessoas sabem qual documentação existe e onde encontrá-la?

No Catálogo de Dados do Azure, sua organização tem um único local central para armazenar e compartilhar esse conhecimento de grupo e tornar fácil descobri-lo. No Catálogo de Dados, seus especialistas de dados podem anotar os ativos de dados diretamente e fornecer links para documentação existente. Quando os membros da organização usam o catálogo para descobrir uma fonte de dados, eles encontram não apenas a fonte em si, mas também o conhecimento que existia anteriormente apenas na cabeça dos especialistas da sua organização.
