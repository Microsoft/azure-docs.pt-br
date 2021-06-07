---
title: Orquestrar a implementação do Azure DevTest Labs
description: Este artigo fornece diretrizes para implementação de orquestração do Azure DevTest Labs na organização.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330607"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orquestrar a implementação do Azure DevTest Labs
Este artigo fornece uma abordagem recomendada para implantação e implementação rápidas do Azure DevTest Labs. A imagem a seguir enfatiza o processo geral como diretrizes prescritivas, observando simultaneamente a flexibilidade para dar suporte a vários cenários e requisitos do setor.

![Etapas para implementar o Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Suposições
Este artigo pressupõe que você tenha os seguintes itens em vigor antes de implementar um piloto do DevTest Labs:

- **Assinatura do Azure**: A equipe-piloto tem acesso para implantar recursos em uma assinatura do Azure. Se as cargas de trabalho são apenas de desenvolvimento e teste, é recomendável selecionar a oferta Desenvolvimento/Teste Enterprise para imagens disponíveis adicionais e taxas mais baixas em máquinas virtuais do Windows.
- **Acesso local**: se necessário, o acesso local já foi configurado. O acesso local pode ser feito por meio de uma conexão VPN site a site ou por meio do ExpressRoute. A conectividade por ExpressRoute normalmente pode levar várias semanas para ser estabelecida, então é recomendável ter o ExpressRoute em vigor antes de iniciar o projeto.
- **Equipes-piloto**: as equipes de projeto de desenvolvimento inicial que usam o DevTest Labs foram identificadas com atividades de teste ou de desenvolvimento aplicáveis e foram estabelecidos requisitos/objetivos/metas para essas equipes.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Marco 1: estabelecer o design e a topologia de rede iniciais
A primeira área de foco ao implantar uma solução do Azure DevTest Labs é estabelecer a conectividade planejada para as máquinas virtuais. As etapas a seguir descrevem os procedimentos necessários:

1. Definir **intervalos de endereços IP iniciais** que são atribuídos à assinatura do DevTest Labs no Azure. Esta etapa requer a previsão do uso esperado em número de VMs, de modo que você possa fornecer um bloco suficientemente grande para expansão futura.
2. Identifique **métodos de acesso desejado** no DevTest Labs (por exemplo, acesso externo/interno). Um ponto importante nesta etapa é determinar se as máquinas virtuais têm endereços IP públicos (ou seja, acessíveis pela Internet diretamente).
3. Identifique e estabeleça **métodos de conectividade** localmente e com o restante do ambiente de nuvem do Azure. Se o roteamento forçado com o ExpressRoute estiver habilitado, provavelmente as máquinas virtuais precisarão de configurações de proxy apropriadas para atravessar o firewall corporativo.
4. Se as VMs forem **ingressar no domínio**, determine se o ingresso será feito em um domínio baseado em nuvem (por exemplo, Serviços de Diretório do AAD) ou um domínio local. No caso de ser local, determine em qual UO (unidade organizacional) dentro do Active Directory as máquinas virtuais ingressam. Além disso, confirme que os usuários tenham acesso para ingressar (ou estabeleça uma conta de serviço que tenha a capacidade de criar registros de computador no domínio)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Marco 2: Implantar o laboratório piloto
Depois que a topologia de rede está em vigor, o primeiro laboratório/laboratório piloto pode ser criado executando as seguintes etapas:

1. Crie um ambiente inicial do DevTest Labs.
2. Determine as imagens e os tamanhos de VM que serão permitidos para uso com o laboratório. Decida se imagens personalizadas podem ser carregadas no Azure para uso com o DevTest Labs.
3. Proteja o acesso ao laboratório criando o Azure RBAC (controle de acesso baseado em função) inicial do Azure para o laboratório (proprietários e usuários do laboratório). É recomendável que você use contas sincronizadas do Active Directory com o Azure Active Directory para identidade com o DevTest Labs.
4. Configure o DevTest Labs para usar políticas como agendas, fórmulas, VMs declaráveis, imagens personalizadas ou gerenciamento de custos.
5. Estabeleça um repositório online como Git/Azure Repos.
6. Decida sobre o uso de repositórios públicos ou privados, ou ainda uma combinação de ambos. Organize modelos de JSON para implantações e sustentabilidade a longo prazo.
7. Se necessário, crie artefatos personalizados. Esta etapa é opcional. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Marco 3: documentação, suporte, aprendizado e melhora
As equipes-piloto iniciais podem exigir suporte detalhado para que possam começar. Use as experiências deles para garantir que a documentação correta e o suporte estejam preparados para distribuição contínua do Azure DevTest Labs.

1. Apresentar as equipes-piloto a seus novos recursos do DevTest Labs (demonstrações, documentação)
2. Com base em experiências das equipes piloto, planeje e forneça documentação conforme necessário
3. Formalizar o processo de integração novas equipes (criação e configuração de laboratórios, fornecimento de acesso, etc.)
4. Com base na adoção inicial, verificar se a previsão original de espaço de endereços IP ainda é razoável e precisa
5. Assegurar que as análises de segurança e conformidade apropriadas foram concluídas

## <a name="next-steps"></a>Próximas etapas
Consulte o próximo artigo desta série: [Governança da infraestrutura do Azure DevTest Labs](devtest-lab-guidance-governance-resources.md)
