---
title: Conceitos principais e visão geral do Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos principais do Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 85fa79cdfc7036be5b0ab20e49986a1d075152c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86254649"
---
# <a name="about-api-management"></a>Sobre o Gerenciamento de API

O Gerenciamento de API (APIM) é uma maneira de criar gateways de API consistentes e modernos para serviços de back-end existentes.

O Gerenciamento de API ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de desbloquear o potencial de seus dados e serviços. Empresas em todos os lugares estão procurando estender suas operações para uma plataforma digital, criando novos canais, encontrando novos clientes e estimulando uma interação mais profunda com os clientes já existentes. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O Gerenciamento de API do Azure permite usar qualquer back-end e lançar um programa completo de API com base nele.

Este artigo fornece uma visão geral dos cenários comuns que envolvem APIM.  Ele também fornece uma visão geral dos componentes principais do sistema APIM. Dessa forma, o artigo oferece uma visão geral mais detalhada de cada componente.

## <a name="overview"></a>Visão geral

Para usar o Gerenciamento de API, os administradores criaram as APIs. Cada API consiste em uma ou mais operações e cada uma pode ser adicionada a um ou mais produtos. Para usar uma API, os desenvolvedores assinam um produto que contém essa API e, em seguida, eles podem chamar a operação da API, estando sujeitos a quaisquer políticas que possam estar em vigor. Cenários comuns incluem:

* **Proteção de infraestrutura móvel** com a retenção de acesso às chaves de API, impedindo ataques DOS ao usar limitação ou políticas avançadas de segurança como validação de token JWT.
* **Habilitação de ecossistemas de parceiro ISV** oferecendo integração rápida de parceiro através do portal do desenvolvedor e criando uma fachada de API para desassociação de implementações internas não prontas para consumo de parceiro.
* **Execução de um programa de API interno** oferecendo um local centralizado para a organização se comunicar sobre a disponibilidade e as alterações mais recentes de APIs, retenção de acesso com base em contas organizacionais, tudo baseado em um canal protegido entre o gateway de API e o back-end.

O sistema é composto pelos seguintes componentes:

* O **gateway de API** é o ponto de extremidade que:
  
  * Aceita chamadas de API e as direciona para seu back-ends.
  * Verifica chaves de API, tokens JWT, certificados e outras credenciais.
  * Impõe cotas de uso e limites de taxa.
  * Transforma sua API em tempo real, sem modificações de código.
  * Armazena em cache respostas de back-end, quando houver respostas desse tipo configuradas.
  * Registra logs de metadados de chamadas para fins de análise.
* O **Portal do Azure** é a interface administrativa na qual você configura seu programa de API. Use-o para:
  
  * Definir ou importar esquemas de API.
  * Empacotar APIs em produtos.
  * Políticas de configuração como cotas ou transformações em APIs.
  * Obter insights por meio de análises.
  * Gerenciar usuários.
* O **Portal do desenvolvedor** serve como a presença da web principal para desenvolvedores, na qual eles podem:
  
  * Ler a documentação da API.
  * Experimente uma API por meio do console interativo.
  * Crie uma conta e assine para obter chaves de API.
  * Acessar a análise do seu próprio uso.

Para obter mais informações, consulte o white-paper em PDF [Gerenciamento de API baseado em nuvem: utilizando a energia das APIs](https://j.mp/ms-apim-whitepaper) . Este white paper introdutório sobre o Gerenciamento de API, feito pelo CITO Research, abrange: 
 
 * Desafios e requisitos comuns de API
 * Dissociação de APIs e apresentação de fachadas
 * Como ajudar os desenvolvedores a trabalhar de modo rápido e fácil
 * Proteção de acesso
 * Métricas e análise
 * Obtenção de controle e ideias com uma plataforma de Gerenciamento de API
 * Usar a nuvem vs. soluções locais
 * Gerenciamento de API do Azure
 
## <a name="apis-and-operations"></a><a name="apis"> </a>APIs e operações
As APIs são a fundação de uma instância de serviço de Gerenciamento de API. Cada API representa um conjunto de operações disponíveis para desenvolvedores. Cada API contém uma referência para serviço back-end que implementa a API, e suas operações são mapeadas para as operações implementadas pelo serviço de back-end. As operações no Gerenciamento de API são altamente configuráveis, com controle sobre o mapeamento de URL, parâmetros de consulta e caminho, conteúdo de solicitação e resposta e caching de resposta de operação. As políticas de limite de taxa, cotas e restrições de IP podem também ser implementadas no nível de operação individual ou da API.

Para obter mais informações, confira [Como criar APIs][How to create APIs] e [Como adicionar operações a uma API][How to add operations to an API].

## <a name="products"></a><a name="products"> </a> Produtos
Os produtos são como as APIs são exibidas para os desenvolvedores. Os produtos no Gerenciamento de API têm uma ou mais APIs e são configurados com título, descrição e termos de uso. Produtos podem ser **Abertos** ou **Protegidos**. Produtos protegidos devem ser assinados antes que possam ser usados, enquanto produtos abertos podem ser usados sem uma assinatura. Quando um produto fica pronto para uso pelo desenvolvedor ele pode ser publicado. Após ele ser publicado, pode ser visualizado (e em caso de produtos protegidos, assinado) pelos desenvolvedores. A aprovação de assinatura é configurada no nível do produto e pode requere a aprovação do administrador ou ser aprovada automaticamente.

Os grupos são usados para gerenciar a visibilidade dos produtos para os desenvolvedores. Os produtos dão visibilidade aos grupos e os desenvolvedores podem exibir e assinar os produtos visíveis para os grupos aos quais pertencem. 

## <a name="groups"></a><a name="groups"> </a> Grupos
Os grupos são usados para gerenciar a visibilidade dos produtos para os desenvolvedores. O Gerenciamento de API tem os grupos de sistema imutáveis a seguir:

* **Administradores** – Os administradores de assinatura do Azure são membros desse grupo. Os administradores gerenciam instâncias de serviço de Gerenciamento de API, criando as APIs, operações e produtos que são usados pelos desenvolvedores.
* **Desenvolvedores** – Os usuários autenticados no portal do desenvolvedor se enquadram nesse grupo. Os desenvolvedores são clientes que compilam aplicativos usando suas APIs. Os desenvolvedores têm acesso ao portal do desenvolvedor e criam aplicativos que chamam as operações de uma API.
* **Convidados** - os usuários não autenticados no portal do desenvolvedor, tais como potenciais clientes visitando o portal do desenvolvedor de uma instância de Gerenciamento de API, pertencem a esse grupo. Eles podem receber certos acessos somente leitura, como a capacidade de exibir APIs, mas não de chamá-las.

Além desses grupos de sistema, os administradores podem criar grupos personalizados ou [aproveitar grupos externos em locatários do Active Directory do Azure](api-management-howto-aad.md). Grupos personalizados e externos podem ser usados juntamente com grupos de sistema oferecendo visibilidade aos desenvolvedores e acesso a produtos de API. Por exemplo, você poderia criar um grupo personalizado para os desenvolvedores associados a uma organização parceira específica e conceder acesso às APIs de um produto que contém apenas as APIs relevantes. Um usuário pode ser um membro de mais de um grupo.

Para obter mais informações, consulte [Como criar e utilizar grupos][How to create and use groups].

## <a name="developers"></a><a name="developers"> </a> Desenvolvedores
Os desenvolvedores representam as contas de usuários em uma instância de serviço de Gerenciamento de API. Os desenvolvedores podem ser criados ou convidados para se juntar aos administradores ou podem fazer a inscrição no [Portal do desenvolvedor][Developer portal]. Cada desenvolvedor é um membro de um ou mais grupos, e pode assinar produtos que dão visibilidade a esses grupos.

Quando os desenvolvedores assinam um produto, recebem as chaves principal e secundária para esse produto. Essa chave é usada quando eles fazem chamadas às APIs dos produtos.

Para obter mais informações, confira [Como criar ou convidar desenvolvedores][How to create or invite developers] e [Como associar grupos aos desenvolvedores][How to associate groups with developers].

## <a name="policies"></a><a name="policies"> </a> Políticas
As políticas são um poderoso recurso de Gerenciamento de API que permite ao Portal do Azure alterar o comportamento da API através da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. As instruções populares incluem a conversão do formato de XML para JSON e limite de taxa de chamada para restringir a quantidade de chamadas recebidas de um desenvolvedor, além de várias outras políticas disponíveis.

Expressões de política podem ser usadas como valores de atributo ou texto em qualquer uma das políticas de Gerenciamento de API, a menos que a política especifique o contrário. Algumas políticas, como [Controlar fluxo](./api-management-advanced-policies.md#choose) e [Definir variável](./api-management-advanced-policies.md#set-variable) se baseiam em expressões de políticas. Para obter mais informações, confira [Políticas avançadas](./api-management-advanced-policies.md#AdvancedPolicies) e [Expressões de política](./api-management-policy-expressions.md).


Para obter uma lista completa de políticas de Gerenciamento de API, consulte [Referência de política][Policy reference]. Para obter mais informações sobre como usar e configurar políticas, consulte [Políticas de Gerenciamento de API][API Management policies]. Para obter um tutorial sobre como criar um produto com políticas de cota e limite de taxa, confira [Como criar e definir configurações avançadas de produto][How create and configure advanced product settings].


## <a name="developer-portal"></a><a name="developer-portal"> </a> Portal do desenvolvedor
O portal do desenvolvedor é onde os desenvolvedores podem aprender sobre suas operações de APIs, visualização e atendimento e assinar produtos. Clientes potenciais podem visitar o portal do desenvolvedor, exibir APIs e operações e fazer inscrição. A URL para o seu portal do desenvolvedor está localizada no painel no portal do Azure da sua instância de serviço de Gerenciamento de API.

Você pode personalizar a aparência do portal do desenvolvedor adicionando conteúdo personalizado, personalização de estilo e adicionando sua marca.

## <a name="api-management-and-the-api-economy"></a>Gerenciamento de API e a economia de API

Para saber mais sobre o Gerenciamento de API, assista à apresentação a seguir da conferência Microsoft Ignite 2017.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Próximas etapas

Conclua o seguinte guia de início rápido e comece a usar o Gerenciamento de API do Azure:

> [!div class="nextstepaction"]
> [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: ./import-and-publish.md
[How to add operations to an API]: ./mock-api-responses.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: ./api-management-policies.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md
