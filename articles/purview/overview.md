---
title: Introdução ao Azure Purview (versão prévia)
description: Este artigo fornece uma visão geral do Azure Purview, incluindo seus recursos e problemas que ele aborda. O Azure Purview permite que qualquer usuário registre, descubra, entenda e consuma fontes de dados.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 3fadfa9d4467209ddbe997afabba27a3752f685d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419925"
---
# <a name="what-is-azure-purview"></a>O que é o Azure Purview?

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

O Azure Purview é um serviço de governança de dados unificado que ajuda você a gerenciar e controlar seus dados locais, multinuvem e de SaaS (software como serviço). Crie facilmente um mapa holístico e atualizado de seu cenário de dados com a descoberta automatizada de dados, a classificação de dados confidenciais e a linhagem de dados de ponta a ponta. Capacite consumidores de dados a encontrar dados confiáveis e valiosos.

O Mapa de Dados do Azure Purview fornece a base para a descoberta de dados e a governança de dados efetiva. O Mapa de Dados do Purview é um serviço de PaaS nativo de nuvem que captura metadados sobre os dados corporativos presentes em sistemas de operações e de análise locais e na nuvem. O Mapa de Dados do Purview é atualizado automaticamente com um sistema de classificação e verificação automatizada interno. Usuários empresariais podem configurar e usar o Mapa de Dados do Purview por meio de uma interface do usuário intuitiva, e desenvolvedores podem interagir programaticamente com o Mapa de Dados usando as APIs do Apache Atlas 2.0 de software livre.

O Mapa de Dados do Azure Purview alimenta o Catálogo de Dados do Purview e os insights de dados do Purview como experiências unificadas no Purview Studio.
 
Com o Catálogo de Dados do Purview, usuários técnicos e empresariais podem encontrar, de maneira fácil e rápida, dados relevantes usando uma experiência de pesquisa com filtros baseados em várias lentes, como termos de glossário, classificações, rótulos de confidencialidade e muito mais. Para especialistas no assunto, administradores de dados e gerentes, o Catálogo de Dados do Purview fornece recursos de curadoria de dados como gerenciamento de glossário de negócios e a capacidade de automatizar a marcação de ativos de dados com termos do glossário. Produtores e consumidores de dados também podem rastrear visualmente a linhagem de ativos de dados dos sistemas operacionais locais por meio do movimento, da transformação e do enriquecimento com vários sistemas de processamento e de armazenamento de dados na nuvem para consumo em um sistema de análise como o Power BI.

Com os insights de dados do Purview, gerentes de dados e de segurança podem ter uma vista panorâmica e entender quais dados são verificados ativamente, onde os dados confidenciais se encontram e como se movem.

## <a name="discovery-challenges-for-data-consumers"></a>Desafios de descoberta para consumidores de dados

Tradicionalmente, a descoberta de fontes de dados da empresa tem sido um processo orgânico com base no conhecimento comunitário. Para empresas que desejam obter o valor máximo de seus ativos de informações, essa abordagem apresenta vários desafios:

* Como não há uma localização central onde as fontes de dados são registradas, os usuários podem não estar cientes de uma fonte de dados, a menos que entrem em contato com ela como parte de outro processo.
* A menos que os usuários saibam a localização de uma fonte de dados, eles não podem se conectar aos dados por meio de um aplicativo cliente. As experiências de consumo de dados exigem que os usuários conheçam a cadeia de conexão ou o caminho.
* O uso pretendido dos dados fica oculto para os usuários, a menos que eles saibam a localização da documentação de uma fonte de dados. A documentação e as fontes de dados podem residir em diversos locais e ser consumidas por meio de diferentes tipos de experiências.
* Se os usuários tiverem perguntas sobre um ativo de informação, eles devem localizar o especialista ou a equipe responsável pelos dados e entrar em contato com eles offline. Não há nenhuma conexão explícita entre os dados e os especialistas que têm perspectivas sobre seu uso.
* A menos que os usuários compreendam o processo de solicitação de acesso à fonte de dados, a descoberta da fonte de dados e de sua documentação não os ajudará a acessar os dados.

## <a name="discovery-challenges-for-data-producers"></a>Desafios de descoberta para produtores de dados

Embora os consumidores de dados enfrentem os desafios mencionados, os usuários responsáveis por produzir e manter informações também têm seus próprios obstáculos:

* A anotação de fontes de dados com metadados descritivos geralmente é um esforço à toa. Os aplicativos clientes geralmente ignoram descrições armazenadas na fonte de dados.
* A criação da documentação para fontes de dados pode ser difícil e é uma responsabilidade contínua manter a documentação sincronizada com as fontes de dados. Os usuários podem não confiar em documentos que pareçam estar desatualizados.
* Criar e manter documentação para fontes de dados é um processo complexo e demorado. Disponibilizar essa documentação prontamente para qualquer pessoa que usa a fonte de dados geralmente pode ser ainda mais.
* Restringir o acesso às fontes de dados e garantir que os consumidores de dados saibam como solicitar o acesso são um desafio contínuo.

Quando esses desafios são combinados, eles apresentam uma barreira significativa para empresas que desejam encorajar e promover o uso e a compreensão dos dados da empresa.

## <a name="discovery-challenges-for-security-administrators"></a>Desafios de descoberta para administradores da segurança

Os usuários responsáveis por garantir a segurança dos dados da organização podem se deparar com qualquer um dos desafios listados acima como produtores e consumidores de dados, bem como os seguintes desafios adicionais:

* Os dados de uma organização crescem constantemente, sendo armazenados e compartilhados em novas direções. A tarefa de descobrir, proteger e controlar dados confidenciais nunca termina. Você quer ter certeza de que o conteúdo da organização seja compartilhado com as pessoas e os aplicativos corretos e com as permissões corretas.
* Entender os níveis de risco nos dados da organização requer aprofundar-se em seu conteúdo, procurando palavras-chave, padrões de RegEx e/ou tipos de dados confidenciais. Os tipos de dados confidenciais podem incluir números de cartão de crédito, números de seguro social ou números de contas bancárias, para citar alguns. Você precisa monitorar constantemente todas as fontes de dados para detectar conteúdo confidencial, pois até mesmo a menor perda de dados pode ser crítica para sua organização.
* Garantir que a organização continue cumprindo as políticas de segurança corporativa é uma tarefa desafiadora conforme o conteúdo cresce e muda e conforme esses requisitos e políticas são atualizados de acordo com as mudanças que ocorrem na realidade digital. Os administradores da segurança muitas vezes têm a tarefa de garantir a segurança de dados o mais rapidamente possível.

## <a name="azure-purview-advantages"></a>Vantagens do Azure Purview

O Azure Purview foi projetado para resolver os problemas mencionados nas seções anteriores e ajudar as empresas a aproveitar ao máximo seus ativos de informações existentes. O catálogo torna as fontes de dados facilmente detectáveis e reconhecíveis para os usuários que gerenciam os dados.

O Azure Purview fornece um serviço baseado em nuvem no qual você pode registrar fontes de dados. Durante o registro, os dados permanecem na localização existente, mas uma cópia de seus metadados é adicionada ao Azure Purview, em conjunto com uma referência ao local da fonte de dados. Os metadados também são indexados para tornar cada fonte de dados fácil de descobrir por meio de pesquisa e compreensível para os usuários que os descobrirem.

Após registrar uma fonte de dados, você pode enriquecer seus metadados. O usuário que registrou a fonte de dados ou outro usuário na empresa adiciona os metadados. Qualquer usuário pode anotar uma fonte de dados, fornecendo descrições, marcas ou outros metadados para solicitação de acesso à fonte de dados. Esses metadados descritivos complementam os metadados estruturais, como nomes de colunas e tipos de dados, registrados da fonte de dados.

Descobrir e entender fontes de dados e seu uso é o principal objetivo de registrar as fontes. Usuários corporativos podem precisar de dados de business intelligence, desenvolvimento de aplicativos, ciência de dados ou outra tarefa em que os dados certos são necessários. Eles usam a experiência de descoberta do catálogo de dados para localizar rapidamente dados que correspondam às suas necessidades, compreender os dados para avaliar sua adequação à finalidade e consumir os dados ao abrir a fonte de dados na sua ferramenta de escolha.

Ao mesmo tempo, os usuários podem contribuir para o catálogo marcando, documentando e anotando as fontes de dados que já foram registradas. Eles também podem registrar novas fontes de dados, que são descobertas, entendidas e consumidas pela comunidade de usuários do catálogo.

## <a name="in-region-data-residency"></a>Residência de dados na região
O Azure Purview não transfere nem armazena dados do cliente fora da região em que está implantado.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure Purview, confira [Criar uma conta do Azure Purview](create-catalog-portal.md).
