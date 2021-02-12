---
title: Política da empresa e conformidade no Azure DevTest Labs
description: Este artigo fornece diretrizes sobre governança da política da empresa e a conformidade para a infraestrutura do Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592340"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governança da infraestrutura do Azure DevTest Labs – política da empresa e conformidade
Este artigo fornece diretrizes sobre governança da política da empresa e a conformidade para a infraestrutura do Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Repositório de artefatos público vs privado

### <a name="question"></a>Pergunta
Quando uma organização deve usar um repositório de artefatos público ou usar um repositório de artefatos privado no DevTest Labs?

### <a name="answer"></a>Resposta
O [repositório de artefatos público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são mais comumente usados. Isso ajuda com implantação rápida, sem a necessidade de investir tempo para reproduzir as ferramentas para desenvolvedores e suplementos comuns. Você pode optar por implantar seu próprio repositório. Você pode usar um repositório público e um privado em paralelo. Você também pode optar por desabilitar o repositório público. Os critérios para implantar um repositório privado devem ser orientados pelas seguintes perguntas e considerações:

- A organização tem um requisito de ter software corporativo licenciado como parte de sua oferta do DevTest Labs? Se a resposta for sim, um repositório privado deverá ser criado.
- A organização desenvolve software personalizado que fornece uma operação específica, que é exigida como parte do processo geral de provisionamento? Se a resposta for sim, um repositório privado deverá ser implantado.
- Se a política de governança da organização exige isolamento e repositórios externos não estão sob gerenciamento de configuração direto pela organização, um repositório de artefatos privado deve ser implantado. Como parte desse processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Em seguida, o repositório público pode ser desabilitado para que ninguém na organização possa continuar acessando-o. Essa abordagem força todos os usuários dentro da organização a ter apenas um único repositório que é aprovado pela organização e minimizar os descompassos de configuração.

### <a name="single-repository-or-multiple-repositories"></a>Repositório único ou vários repositórios 

### <a name="question"></a>Pergunta
Uma organização deve planejar ter um único repositório ou permitir vários repositórios?

### <a name="answer"></a>Resposta
Como parte da estratégia de gerenciamento de configuração e de governança de geral da sua organização, é recomendável que você use um repositório centralizado. Quando você usa vários repositórios, eles podem se tornar silos de software não gerenciado ao longo do tempo. Com um repositório central, várias equipes podem consumir artefatos desse repositório para seus projetos. Ele impõe a padronização, a segurança, a facilidade de gerenciamento e elimina a duplicação de esforços. Como parte a centralização, as seguintes ações são práticas recomendadas para gerenciamento a longo prazo e de sustentabilidade:

- Associe o Azure Repos ao mesmo locatário do Azure Active Directory que a assinatura do Azure está usando para autenticação e autorização.
- Crie um grupo centralmente gerenciado chamado **Todos os desenvolvedores do DevTest Labs** no Azure Active Directory. Qualquer desenvolvedor que contribui para o desenvolvimento de artefatos deve ser colocado nesse grupo.
- O mesmo grupo do Azure Active Directory pode ser usado para fornecer acesso ao repositório do Azure Repos e ao laboratório.
- No Azure Repos, a ramificação ou bifurcação deve ser usada para um repositório em desenvolvimento separado do repositório de produção primário. O conteúdo é adicionado somente ao Branch principal com uma solicitação pull após uma revisão de código adequada. Depois que o revisor de código aprovar a alteração, um desenvolvedor líder, responsável pela manutenção da ramificação principal, mesclará o código atualizado. 

## <a name="corporate-security-policies"></a>Políticas de segurança corporativa

### <a name="question"></a>Pergunta
Como uma organização pode assegurar que as políticas de segurança corporativa estão em vigor?

### <a name="answer"></a>Resposta
Uma organização pode alcançar esse objetivo realizando as seguintes ações:

1. Desenvolver e publicar uma política de segurança abrangente. A política articula as regras de uso aceitável associadas ao uso de software e de ativos de nuvem. Ele também define o que claramente viola a política. 
2. Desenvolva uma imagem personalizada, artefatos personalizados e um processo de implantação que permite a orquestração no realm de segurança que é definido com o Active Directory. Essa abordagem impõe o limite corporativo e define um conjunto comum de controles ambientais. Esses controles determinam o ambiente que um desenvolvedor pode considerar conforme desenvolve e segue um ciclo de vida de desenvolvimento seguro como parte de seu processo geral. O objetivo é também fornecer um ambiente que não seja excessivamente restritivo a ponto de atrapalhar o desenvolvimento, mas sim um conjunto razoável de controles. As políticas de grupo na UO (unidade organizacional) que contém máquinas virtuais do laboratório pode ser um subconjunto das políticas de grupo totais que são encontrados na produção. Como alternativa, elas podem ser um conjunto adicional para mitigar adequadamente todos os riscos identificados.

## <a name="data-integrity"></a>Integridade dos dados

### <a name="question"></a>Pergunta
Como uma organização pode assegurar a integridade de dados para garantir que os desenvolvedores de comunicação remota não possam remover o código ou introduzir malware ou software não aprovado?

### <a name="answer"></a>Resposta
Há várias camadas de controle para atenuar a ameaça de consultores externos, prestadores de serviços ou funcionários que estão acessando remotamente para colaborar no DevTest Labs. 

Conforme mencionado anteriormente, a primeira etapa precisa ter uma política de uso aceitável esboçada e definida que descreva claramente as consequências de quando alguém viola a política. 

A primeira camada de controles de acesso remoto é aplicar uma política de acesso remoto por meio de uma conexão VPN que não está conectada diretamente ao laboratório. 

A segunda camada de controles é aplicar um conjunto de objetos de política de grupo que evita copiar e colar por meio da Área de Trabalho Remota. Uma política de rede pode ser implementada para não permitir a saída de serviços do ambiente, tais como serviços de protocolo RDP e FTP fora do ambiente. O roteamento definido pelo usuário pode forçar todo o tráfego de rede do Azure de volta para o local, mas o roteamento não foi capaz de contabilizar todas as URLs que podem permitir o upload de dados, a menos que controlado por meio de um proxy para verificar o conteúdo e as sessões. IPs públicos poderiam ser restritos dentro da rede virtual que dá suporte ao DevTest Labs para não permitir a passagem de um recurso de rede externo.

Por fim, restrições desse mesmo tipo precisam ser aplicadas em toda a organização, o que também teria que levar em conta todos os métodos possíveis de mídia removível ou URLs externas que pudessem aceitar uma postagem de conteúdo. Confira seu profissional de segurança para examinar e implementar uma política de segurança. Para obter mais recomendações, confira [Segurança cibernética da Microsoft](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Próximas etapas
Veja [Integração e migração de aplicativos](devtest-lab-guidance-governance-application-migration-integration.md).
