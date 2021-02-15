---
title: Integração de Azure DevTest Labs e DevOps | Microsoft Docs
description: Aprenda a usar os laboratórios de Azure DevTest Labs dentro de um ambiente de integração contínua (CI)/entrega contínua (CD) em ambientes corporativos.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481061"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integração do Azure DevTest Labs e do Azure DevOps
O DevOps é uma metodologia de desenvolvimento de software que integra o desenvolvimento de software (dev) com operações (OPS) para um sistema. Esse sistema pode fornecer novos recursos, atualizações e correções em alinhamento com as metas de negócios. Essa metodologia abrange tudo, desde a criação de novos recursos com base em metas, padrões de uso e comentários de clientes; para corrigir, recuperar e proteger o sistema quando ocorrerem problemas. Um componente facilmente identificado dessa metodologia é o pipeline de integração contínua (CI)/entrega contínua (CD). Um pipeline de CI/CD Obtém informações, código e recursos de uma confirmação por meio de uma série de etapas que incluem criação, teste e implantação, para produzir o sistema. Este artigo se concentra em diferentes maneiras de usar efetivamente os laboratórios em um pipeline em um ambiente corporativo. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Benefícios do uso de laboratórios no fluxo de trabalho do DevOps 

### <a name="focused-access"></a>Acesso prioritário 
Usar um laboratório como um componente permite que um ecossistema específico seja associado a um grupo limitado de pessoas. Normalmente, uma equipe ou grupo que está trabalhando em uma área comum ou um recurso específico terá um laboratório atribuído a eles.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replicação de infraestrutura na nuvem 
Um desenvolvedor pode configurar rapidamente um ecossistema de desenvolvimento que inclua uma caixa de desenvolvimento com código-fonte e ferramentas. O desenvolvedor também pode criar um ambiente praticamente idêntico à configuração de produção. Ele ajuda com o desenvolvimento de loop interno mais rápido. 

### <a name="pre-production"></a>Pré-produção 
Ter um laboratório no pipeline de CI/CD torna mais fácil ter vários ambientes de pré-produção diferentes ou máquinas em execução ao mesmo tempo para testes assíncronos. Diferentes infraestruturas de suporte, como agentes de compilação, podem ser implantadas e gerenciadas em um laboratório. 

## <a name="devops-with-devtest-labs"></a>DevOps com DevTest Labs 

### <a name="development--operation"></a>Desenvolvimento/operação 
Um laboratório deve ser focado em uma equipe que esteja funcionando em uma área de recursos. Esse foco comum permite o compartilhamento de recursos específicos de áreas, como ferramentas, scripts ou modelos do Resource Manager. Ele permite alterações mais rápidas, limitando os efeitos negativos a um grupo menor. Esses recursos compartilhados permitem que o desenvolvedor crie VMs de desenvolvimento com todo o código, as ferramentas e a configuração necessários. Eles podem ser criados dinamicamente ou ter um sistema que cria imagens base com as personalizações. O desenvolvedor não só pode criar VMs, mas também pode criar ambientes do DevTest Labs com base nos modelos necessários para criar os recursos apropriados do Azure no laboratório. Qualquer alteração ou trabalho destrutivo pode ser feito no ambiente de laboratório sem afetar outras pessoas. Considere o cenário em que o produto é um sistema autônomo instalado na máquina do cliente. Nesse cenário, o DevTest Labs melhorou a criação de VM que inclui a instalação de software adicional usando artefatos e configurações do cliente de pré-compilação para um teste de loop interno mais rápido do código. 
  
## <a name="cicd-pipeline"></a>Pipeline de CI/CD 
O pipeline de CI/CD é um dos componentes críticos no DevOps que movem o código de uma solicitação de pull do desenvolvedor, integra-o com o código existente e o implanta no ecossistema de produção. Todos os recursos não precisam estar em um laboratório. Por exemplo, um host Jenkins poderia ser configurado fora do laboratório como um recurso mais persistente. Aqui estão alguns exemplos específicos de integração de laboratórios no pipeline. 

### <a name="build"></a>Build 
O pipeline de compilação se concentra na criação de um pacote de componentes que serão testados em conjunto para serem entregues ao pipeline de lançamento. Os laboratórios podem fazer parte do pipeline de compilação como o local para agentes de compilação e outros recursos de suporte. Ter a capacidade de criar dinamicamente a infraestrutura permite maior controle. Com a capacidade de ter vários ambientes em um laboratório, cada compilação pode ser executada de forma assíncrona ao usar a ID de compilação como parte das informações de ambiente para identificar exclusivamente os recursos para a compilação específica.   

Para agentes de compilação, a capacidade do laboratório de restringir o acesso aumenta a segurança e reduz a possibilidade de danos acidentais.  

### <a name="test"></a>Teste 
O DevTest Labs permite que um pipeline de CI/CD Automatize a criação de recursos do Azure (VMs, ambientes) que podem ser usados para teste automatizado e manual. As VMs seriam criadas usando artefatos ou fórmulas que usam informações do processo de compilação para criar as diferentes configurações personalizadas necessárias para o teste.   

### <a name="release"></a>Versão 
O DevTest Labs é normalmente usado para verificação na seção versão antes da implantação do código. É semelhante ao teste na seção Build. Os recursos de produção não devem ser implantados no DevTest Labs. 

### <a name="customization"></a>Personalização 
No Azure DevOps, há tarefas existentes que permitem a manipulação de VMs e ambientes dentro de laboratórios específicos. Embora Azure DevOps Services sejam uma maneira de gerenciar o pipeline de CI/CD, você pode integrar o laboratório em qualquer sistema que ofereça suporte à capacidade de chamar APIs REST, executar scripts do PowerShell ou usar CLI do Azure. 

Embora alguns gerenciadores de pipeline de CI/CD tenham plug-ins de software livre existentes que podem gerenciar recursos no Azure e no DevTest Labs. Talvez seja necessário usar alguns scripts personalizados para atender às necessidades específicas do pipeline.  Com isso em mente, ao executar uma tarefa, uma entidade de serviço é usada com a função apropriada para obter acesso ao laboratório. A entidade de serviço geralmente precisa do acesso à função colaborador para o laboratório. Para obter mais informações, consulte [integrar o Azure DevTests Labs em seu pipeline de integração e entrega contínua do DevOps do Azure](devtest-lab-integrate-ci-cd.md). 
 