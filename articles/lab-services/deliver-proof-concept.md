---
title: Fornecer uma prova de conceito-Azure DevTest Labs | Microsoft Docs
description: Saiba como fornecer uma prova de conceito para que Azure DevTest Labs possa ser incorporada com êxito a um ambiente corporativo.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501540"
---
# <a name="delivering-a-proof-of-concept"></a>Fornecendo uma prova de conceito 

Um dos principais cenários para o Azure DevTest Labs é habilitar ambientes de desenvolvimento e teste na nuvem. Os exemplos incluem:

* criando áreas de trabalho de desenvolvedor na nuvem,
* Configurando ambientes para teste,
* Habilitando o acesso a máquinas virtuais e a outros recursos do Azure,
* Configurando uma área de área restrita para os desenvolvedores aprenderem e experimentarem.

O DevTest Labs também fornece os guardrails de política e os controles de custo para capacitar a empresa a fornecer "Autoatendimento do Azure" a desenvolvedores que aderem às políticas de segurança, regulamentação e conformidade corporativas. 

Toda empresa tem requisitos diferentes de como Azure DevTest Labs pode ser incorporada com êxito em seu ambiente. Este artigo descreve as etapas mais comuns que as empresas normalmente precisam concluir para garantir uma prova de conceito bem-sucedida, que é a primeira etapa para uma implantação bem-sucedida de ponta a ponta. 

## <a name="getting-started"></a>Guia de Introdução 

Para começar a fornecer uma prova de conceito. É importante dedicar algum tempo para aprender sobre o Azure e o DevTest Labs.  Aqui estão alguns recursos iniciais: 

* [Noções básicas sobre o portal do Azure](https://azure.microsoft.com/features/azure-portal/)
* [Noções básicas do DevTest Labs](devtest-lab-overview.md)
* [Cenários com suporte do DevTest Labs](devtest-lab-guidance-get-started.md)
* [Documentação do DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Introdução à rede do Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir com êxito um piloto ou uma prova de conceito com o DevTest Labs, há alguns pré-requisitos necessários: 

* **Assinatura do Azure**: As empresas geralmente têm um [Enterprise Agreement](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) existente em vigor que permite o acesso ao Azure e uma assinatura nova ou existente pode ser usada para DevTest Labs. Como alternativa, uma [assinatura do Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) pode ser usada durante o piloto (aproveitando os créditos gratuitos do Azure). Se nenhuma dessas opções estiver disponível, uma [conta gratuita do Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) poderá ser criada e usada. Se houver um Enterprise Agreement em vigor, usar uma [assinatura de desenvolvimento/teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) é uma ótima opção para obter acesso a sistemas operacionais cliente do Windows 10/Windows 8.1 e tarifas com desconto para cargas de trabalho de desenvolvimento e teste. 
* **Azure Active Directory locatário**:  Para habilitar o gerenciamento de usuários (por exemplo, adição de usuários ou adição de proprietários de laboratório), esses usuários devem fazer parte do [locatário de Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usado na assinatura do Azure para o piloto. Muitas vezes, as empresas configurarão [identidade híbrida](../active-directory/hybrid/whatis-hybrid-identity.md) para permitir que os usuários aproveitem sua identidade local na nuvem, mas isso não é necessário para o piloto do DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Escopo do piloto 

É muito importante planejar um piloto adequado antes de iniciar a implementação. Sabendo antecipadamente que os recursos não ficarão indefinidamente, o define as expectativas apropriadas para os usuários do piloto. 

> [!IMPORTANT]
> Não podemos enfatizar o suficiente da importância de definir com nitidez o piloto e configurar a expectativa antecipadamente.

Há perguntas importantes a serem respondidas antes do Iniciar do piloto: 

* O que você deseja saber e o que o sucesso parece para o piloto? 
* Quais cargas de trabalho ou cenários serão abordados no piloto? É importante definir apenas um pequeno conjunto para garantir que o piloto possa ser definido com escopo e concluído rapidamente. 
* Quais recursos devem estar disponíveis no laboratório? Por exemplo: imagens personalizadas, imagens do Marketplace, políticas, topologia de rede, etc. 
* Quem são os usuários finais/equipes que estarão envolvidos no piloto para verificar a experiência?  
* Qual é a duração do piloto? Escolha um período que se alinhe bem ao escopo planejado, como duas semanas ou um mês. 
* Quando o piloto for concluído, o que acontecerá com os recursos alocados que foram usados durante o piloto? Você planeja descartar os recursos do piloto?
   
   Se pudermos nos planejar descartar as máquinas virtuais e os laboratórios no final do piloto, podemos simplesmente configurar uma única assinatura para o piloto e fazer todo nosso trabalho enquanto resolvemos as perguntas de distribuição de escala em paralelo. 

Há uma tendência geral de tornar o piloto "perfeito" para que ele represente de forma idêntica qual será o estado final quando o serviço for distribuído na empresa. Isso é um falso pressuposto. Quanto mais próximo você chegar ao "perfeito", mais precisará concluir *antes* de começar a usar o piloto. A finalidade do piloto é tomar as decisões certas sobre como escalar verticalmente e distribuir o serviço final. 

O foco do piloto deve estar na escolha das cargas de trabalho e dependências mínimas necessárias para responder à pergunta se Azure DevTest Labs é o serviço certo para sua empresa. É aconselhável escolher a carga de trabalho mais simples com as dependências mínimas para garantir um sucesso mais rápido e limpo. Se isso não for possível, a melhor opção é escolher uma carga de trabalho mais representativa que exponha possíveis complexidades para que o sucesso na fase piloto possa ser replicado na fase de expansão. 

O exemplo a seguir demonstra uma prova de conceito bem no escopo. 

## <a name="example-proof-of-concept-plan"></a>Exemplo: plano de prova de conceito 

Esta seção mostra um exemplo a ser usado para definir o escopo de uma prova de conceito do piloto para DevTest Labs. 

> [!TIP]
> Para minimizar a possibilidade de configurar seu projeto para uma falha, é altamente recomendável que você não ignore o exemplo descrito nesta seção. 

### <a name="overview"></a>Visão geral 

Estamos planejando desenvolver um novo ambiente no Azure com base no DevTest Labs para que os fornecedores usem como um ambiente isolado da rede corporativa. Para determinar se a solução atenderá aos requisitos, desenvolveremos uma prova de conceito para validar a solução de ponta a ponta e incluirei vários fornecedores para experimentar e verificar a experiência. 

### <a name="outcomes"></a>Resultados 

Ao criar uma prova de conceito, nos concentramos primeiro nos resultados (o que estamos tentando alcançar) – aqueles listados aqui.  Ao final da prova de conceito, esperamos: 

* Uma solução funcional de ponta a ponta para fornecedores aproveitarem contas de convidado no Azure Active Directory (Azure AD) para acessar um ambiente isolado no Azure que tem os recursos necessários para que eles sejam produtivos. 
* Quaisquer problemas potenciais de bloqueio que afetem o uso e a adoção de escala mais ampla são enumerados e compreendidos.
* Os indivíduos envolvidos no desenvolvimento da prova de conceito têm uma boa compreensão de todo o código. Eles também entendem o material de apoio envolvido e estão confiantes em uma adoção mais ampla.

### <a name="open-questions--prerequisites"></a>Abrir perguntas & pré-requisitos 

* Temos uma assinatura criada que poderíamos usar para este projeto? 
* Temos um locatário do Azure AD e um administrador global do Azure AD identificou quem pode fornecer ajuda & diretrizes para perguntas relacionadas ao Azure AD? 
* Um local para colaborar para os indivíduos que trabalham no projeto: 

   * Código-fonte e scripts (como Azure Repos) 
   * Documentos (como equipes ou SharePoint)  
   * Conversas (como o Microsoft Teams) 
   * Itens de trabalho (como Azure Boards) 
* Quais são os recursos necessários para os fornecedores? Isso inclui ambos localmente nas máquinas virtuais e outros servidores necessários, aplicativos disponíveis na rede. 
* As máquinas virtuais serão Unidas a um domínio no Azure? Nesse caso, isso será Azure AD Domain Services ou algo mais? 
* Temos a equipe/fornecedores identificados que serão o alvo da prova de conceito? Quem serão os clientes do ambiente?
* Qual região do Azure será usada para a prova de conceito? 
* Temos uma lista de serviços que os fornecedores podem usar por meio do DevTest Labs além de IaaS (VMs)? 
* Como planejamos o treinamento de fornecedores/usuários sobre o uso do laboratório? 

### <a name="proof-of-concept-solution-components"></a>Componentes da solução de prova de conceito 

Estamos esperando que a solução tenha os seguintes componentes: 

* Um conjunto de DevTest Labs no Azure para várias equipes de fornecedores.
* Os laboratórios estão conectados a uma infraestrutura de rede, que oferece suporte aos requisitos.
* Os fornecedores têm acesso aos laboratórios por meio do Azure AD e concedendo atribuições de função ao laboratório.
* Uma maneira para os fornecedores se conectarem com êxito aos seus recursos. Especificamente, uma VPN site a site para habilitar o acesso a máquinas virtuais diretamente sem endereços IP públicos.
* Um conjunto de artefatos que abrangem o software necessário exigido pelos fornecedores nas máquinas virtuais.

## <a name="additional-planning-and-design-decisions"></a>Decisões de planejamento e design adicionais 

Antes de lançar uma solução completa do DevTest Labs, há algumas decisões importantes de planejamento e design a serem feitas. A experiência de trabalhar em uma prova de conceito pode ajudá-lo a tomar essas decisões. A consideração adicional inclui: 

* **Topologia de assinatura**: Os requisitos de nível empresarial para recursos no Azure podem se estender além das [cotas disponíveis em uma única assinatura](https://docs.microsoft.com/azure/azure-subscription-service-limits), o que exige várias assinaturas do Azure e/ou solicitações de serviço para aumentar os limites iniciais da assinatura. É importante decidir como distribuir recursos em assinaturas, um recurso valioso é o [Guia de decisão da assinatura](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) , pois é difícil mover recursos para outra assinatura mais tarde. Por exemplo, um laboratório de DevTest não pode ser movido para outra assinatura após sua criação.  
* **Topologia de rede**: A [infraestrutura de rede padrão](../app-service/networking-features.md) criada automaticamente pelo DevTest Labs pode não ser suficiente para atender aos requisitos e às restrições dos usuários empresariais. É comum ver o [ExpressRoute conectado VNets](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), [Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para conectividade entre assinaturas e até mesmo o [Roteamento forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) , garantindo somente a conectividade local. O DevTest Labs permite que as redes virtuais existentes sejam conectadas ao laboratório para permitir seu uso durante a criação de novas máquinas virtuais no laboratório. 
* **Acesso remoto de máquinas virtuais**: Há muitas opções para acessar remotamente as máquinas virtuais localizadas no DevTest Labs. O mais fácil é usar IPs públicos ou IPs públicos compartilhados, que são [configurações disponíveis no laboratório](devtest-lab-shared-ip.md). Se essas opções não forem suficientes, o uso de um gateway de acesso remoto também será uma opção, conforme mostrado na [arquitetura de referência do DevTest Labs Enterprise](devtest-lab-reference-architecture.md) e descrito mais detalhadamente na [documentação do gateway de área de trabalho remota do DevTest Labs](configure-lab-remote-desktop-gateway.md). As empresas também podem usar a rota expressa ou uma VPN site a site para conectar seus DevTest Labs à rede local. Isso permite conexões diretas de área de trabalho remota ou SSH para as máquinas virtuais com base em seu endereço IP privado sem exposição à Internet. 
* **Manipulando permissões**: As duas permissões de chave normalmente usadas no DevTest Labs são ["proprietário" e "usuário de laboratório"](devtest-lab-add-devtest-user.md). É importante decidir antes de distribuir o DevTest Labs de forma ampla que será confiável para cada nível de acesso no laboratório. Um modelo comum é o proprietário do orçamento (líder de equipe, por exemplo) como o proprietário do laboratório e os membros da equipe como usuários do laboratório. Isso permite que a pessoa (líder de equipe) responsável pelo orçamento ajuste as configurações de política para manter a equipe dentro do orçamento.  

## <a name="completing-the-proof-of-concept"></a>Concluindo a prova de conceito 

Depois que os aprendizados esperados foram cobertos, é hora de terminar e concluir o piloto. Esse é o momento de coletar comentários dos usuários, determinar se o piloto foi bem-sucedido e decidir se a organização se movimentará em uma escala de expansão dos laboratórios de DevTest na empresa. Também é um ótimo momento para se considerar automatizar a implantação do DevTest Labs e os recursos associados para garantir a consistência em toda a escala horizontal. 

## <a name="next-steps"></a>Próximas etapas 

* [Documentação do DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Arquitetura de referência para uma empresa](devtest-lab-reference-architecture.md)
* [Escalar verticalmente sua implantação do DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Orquestrar a implementação do Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
