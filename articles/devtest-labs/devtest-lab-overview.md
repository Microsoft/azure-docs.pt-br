---
title: Sobre os Laboratórios de Desenvolvimento/Teste | Microsoft Azure
description: Saiba como os Laboratórios de Desenvolvimento/Teste podem facilitar criar, gerenciar e monitorar as máquinas virtuais do Azure
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480194"
---
# <a name="about-azure-devtest-labs"></a>Sobre os Laboratórios de Desenvolvimento/Teste do Azure
Azure DevTest Labs permite que os desenvolvedores de equipes gerenciem automaticamente máquinas virtuais (VMs) e recursos de PaaS sem esperar por aprovações.

O DevTest Labs cria laboratórios que consistem em bases pré-configuradas ou modelos de Azure Resource Manager. Eles têm todas as ferramentas e os softwares necessários que você pode usar para criar ambientes. Você pode criar ambientes em alguns minutos, em vez de horas ou dias.

Usando o DevTest Labs, você pode testar as versões mais recentes de seus aplicativos executando as seguintes tarefas:

- Provisione rapidamente ambientes Windows e Linux usando artefatos e modelos reutilizáveis.
- Integre facilmente seu pipeline de implantação dos Laboratórios de Teste/Desenvolvimento para provisionar ambientes sob demanda.
- Escale verticalmente o teste de carga Provisionando vários agentes de teste e crie ambientes pré-configurados para treinamento e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funcionalidades
O DevTest Labs fornece os seguintes recursos para desenvolvedores que trabalham com VMs:

- Crie VMs rapidamente seguindo menos de cinco etapas simples.
- Escolha entre uma lista organizada de bases de VM configuradas, aprovadas e autorizadas pelo líder de equipe ou ti central.
- Crie VMs de imagens personalizadas criadas previamente que tenham todos os softwares e ferramentas já instalados. 
- Crie VMs a partir de fórmulas que são essencialmente imagens personalizadas combinadas com as compilações mais recentes do software que é instalado quando as VMs são criadas. 
- Instale os artefatos que são extensões implantadas em VMs depois que elas são provisionadas.
- Defina o desligamento automático e os agendamentos de início automático em VMs.
- Declare uma VM criada previamente sem passar pelo processo de criação.

O DevTest Labs fornece os seguintes recursos para desenvolvedores que trabalham com ambientes PaaS:

- Use o Gerenciador de recursos para criar rapidamente ambientes PaaS seguindo menos de três etapas simples.
- Escolha entre uma lista organizada de modelos do Resource Manager, que são configurados e autorizados pelo líder de equipe ou pela ti central.
- Crie um grupo de recursos vazio (Sandbox) usando um modelo do Resource Manager para explorar o Azure no contexto de um laboratório.

O DevTest Labs também permite que a ti central controle os resíduos, otimize os custos nos recursos e permaneça dentro dos Orçamentos fazendo as seguintes tarefas:  

- Definir o desligamento automático e agendamentos de início automático em VMs.
- Configuração de políticas no número de VMs que os usuários podem criar.
- Definir políticas em tamanhos de VMs e imagens da galeria que os usuários escolhem.
- Acompanhamento de custos e configuração de destinos em laboratórios.
- Ser notificado sobre altos custos projetados para laboratórios para que você possa tomar as medidas necessárias.

O DevTest Labs oferece os seguintes benefícios na criação, configuração e gerenciamento de ambientes na nuvem.

## <a name="cost-control-and-governance"></a>Controle de custo e governança
O DevTest Labs torna mais fácil controlar os custos, permitindo que você realize as seguintes tarefas:

- [Defina políticas em seus laboratórios](devtest-lab-set-lab-policy.md), como o número de VMs por usuário ou por laboratório. 
- Crie [políticas para desligar](devtest-lab-set-lab-policy.md) e iniciar VMs automaticamente.
- Acompanhe os custos em VMs e recursos de PaaS girados dentro de laboratórios para permanecer dentro [do seu orçamento](devtest-lab-configure-cost-management.md).
- Fique dentro do contexto de seus laboratórios para não criar recursos fora deles.

## <a name="quickly-get-to-ready-to-test"></a>Chegue rapidamente ao estado Pronto para teste
O DevTest Labs permite que você crie ambientes pré-configurados equipados com tudo o que sua equipe precisa para desenvolver e testar aplicativos. Basta [declarar os ambientes](devtest-lab-add-claimable-vm.md) em que a última compilação boa de seu aplicativo está instalada e começar a trabalhar. Ou use contêineres para criar um ambiente mais rápido e mais enxuto.

## <a name="create-once-use-everywhere"></a>Crie uma vez, use em qualquer lugar
Capture e compartilhe [modelos de ambiente](devtest-lab-create-environment-from-arm.md) de PaaS e [artefatos](add-artifact-repository.md) em sua equipe ou organização, tudo no controle do código-fonte, para criar facilmente ambientes de desenvolvimento e teste.

## <a name="worry-free-self-service"></a>Autoatendimento sem preocupações
O DevTest Labs permite que seus desenvolvedores e testadores [criem VMs IaaS](devtest-lab-add-vm.md) e [recursos de PaaS](devtest-lab-create-environment-from-arm.md) de forma rápida e fácil usando um conjunto de recursos pré-configurados.

## <a name="use-iaas-and-paas-resources"></a>Usar os recursos de IaaS e PaaS 
Os desenvolvedores também podem criar recursos de PaaS, como clusters de Service Fabric do Azure, o recurso de aplicativos Web do serviço Azure App e farms do SharePoint, usando modelos do Resource Manager. Para começar a usar o PaaS em laboratórios, use os modelos do [repositório de ambiente público](devtest-lab-configure-use-public-environments.md) ou [Conecte o laboratório a seu próprio repositório git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Você também pode controlar os custos desses recursos para permanecer dentro do seu orçamento.

## <a name="integrate-with-your-existing-toolchain"></a>Integre-se com seu ferramentas existente
Use plug-ins predefinidos ou a API para provisionar ambientes de desenvolvimento/teste diretamente da sua [ferramenta de CI (integração contínua)](devtest-lab-integrate-ci-cd.md), IDE (ambiente de desenvolvimento integrado) ou pipeline de liberação automatizado preferido. Você também pode usar a ferramenta de linha de comando abrangente.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- Para saber mais sobre o DevTest Labs, consulte [conceitos do DevTest Labs](devtest-lab-concepts.md).
- Para obter instruções passo a passo, consulte [tutorial: configurar um laboratório usando Azure DevTest Labs](tutorial-create-custom-lab.md).