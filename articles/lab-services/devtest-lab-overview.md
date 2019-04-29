---
title: Sobre os Laboratórios de Desenvolvimento/Teste | Microsoft Azure
description: Saiba como os Laboratórios de Desenvolvimento/Teste podem facilitar criar, gerenciar e monitorar as máquinas virtuais do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561448"
---
# <a name="about-azure-devtest-labs"></a>Sobre os Laboratórios de Desenvolvimento/Teste do Azure
O Azure DevTest Labs permite que os desenvolvedores em equipes Self gerenciar com eficiência os recursos de PaaS e máquinas virtuais (VMs) sem esperar que as aprovações.

O DevTest Labs cria laboratórios consistindo em bases pré-configurado ou modelos do Azure Resource Manager. Elas têm todas as ferramentas necessárias e software que você pode usar para criar ambientes. Você pode criar ambientes em poucos minutos, em vez de horas ou dias.

Usando o DevTest Labs, você pode testar as versões mais recentes de seus aplicativos, fazendo as seguintes tarefas:

- Provisione rapidamente ambientes Windows e Linux usando modelos e artefatos reutilizáveis.
- Integre facilmente seu pipeline de implantação dos Laboratórios de Teste/Desenvolvimento para provisionar ambientes sob demanda.
- Escalar verticalmente seu teste Provisionando vários agentes de teste de carga e crie ambientes pré-provisionados para treinamentos e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funcionalidades
Laboratórios de desenvolvimento/teste fornece os seguintes recursos para desenvolvedores que trabalham com máquinas virtuais:

- Crie VMs rapidamente executando menos de cinco etapas simples.
- Escolha entre uma lista estruturada de bases VM que são configuradas, aprovado e autorizado pelo líder de equipe ou central de IT.
- Crie VMs de imagens personalizadas criadas previamente que têm todos os softwares e ferramentas já instaladas. 
- Crie VMs de fórmulas que são combinadas com os builds mais recentes do software que é instalado quando as VMs são criadas de imagens essencialmente personalizadas. 
- Instale os artefatos que são as extensões implantadas em VMs depois que eles são provisionados.
- Definir o desligamento automático e início automático agendas em máquinas virtuais.
- Declaração de uma VM criada previamente sem passar pelo processo de criação.

Laboratórios de desenvolvimento/teste fornece os seguintes recursos para desenvolvedores que trabalham com os ambientes de PaaS:

- Use o Gerenciador de recursos para criar rapidamente ambientes PaaS seguindo menos de três etapas simples.
- Escolha entre uma lista estruturada de modelos do Resource Manager, que são configuradas e autorizado pelo líder de equipe ou central IT.
- Crie um grupo de recursos vazio (área restrita) usando um modelo do Resource Manager para explorar o Azure dentro do contexto de um laboratório.

DevTest Labs também permite central que a TI controlar desperdiça, otimizar os custos de recursos e permanecer dentro orçamentos, fazendo as seguintes tarefas:  

- Definição de agendas de desligamento automático e início automático em máquinas virtuais.
- Configuração de políticas no número de VMs que os usuários podem criar.
- Definindo políticas em tamanhos de VMs e imagens da Galeria que os usuários escolher.
- Controlando os custos e definir metas de laboratórios práticos.
- Ser notificado em altos custos projetados para laboratórios para que você possa aproveitar as ações necessárias.

Laboratórios de desenvolvimento/teste fornece os seguintes benefícios na criação, configuração e gerenciamento de ambientes na nuvem.

## <a name="cost-control-and-governance"></a>Governança e controle de custos
Laboratórios de desenvolvimento/teste torna mais fácil controlar os custos, permitindo que você faça as seguintes tarefas:

- [Definir políticas nos seus laboratórios](devtest-lab-get-started-with-lab-policies.md), como o número de VMs por usuário ou por laboratório. 
- Crie [políticas para desligar automaticamente](devtest-lab-set-lab-policy.md) e iniciar VMs.
- Controlar os custos de recursos de VMs e PaaS funcionando dentro de laboratórios para permanecer dentro [seu orçamento](devtest-lab-configure-cost-management.md).
- Mantenha-se dentro do contexto de seus laboratórios para que você não perca os recursos fora dele.

## <a name="quickly-get-to-ready-to-test"></a>Chegue rapidamente ao estado Pronto para teste
DevTest Labs permite que você crie ambientes pré-provisionados equipados com tudo o que sua equipe precisa para desenvolver e testar aplicativos. Basta [declarar os ambientes](devtest-lab-add-claimable-vm.md) onde a última compilação boa do seu aplicativo está instalada e funcionando de início. Ou use contêineres para a criação do ambiente ainda mais rápida e descomplicada.

## <a name="create-once-use-everywhere"></a>Crie uma vez, use em qualquer lugar
Capturar e compartilhar PaaS [modelos de ambiente](devtest-lab-create-environment-from-arm.md) e [artefatos](add-artifact-repository.md) dentro de sua equipe ou organização — tudo no controle de origem — para facilmente criar desenvolvedor e ambientes de teste.

## <a name="worry-free-self-service"></a>Autoatendimento sem preocupações
DevTest Labs permite que seus desenvolvedores e testadores a rapidamente e facilmente [criar VMs de IaaS](devtest-lab-add-vm.md) e [recursos de PaaS](devtest-lab-create-environment-from-arm.md) usando um conjunto de recursos pré-configurado.

## <a name="use-iaas-and-paas-resources"></a>Use os recursos de IaaS e PaaS 
Os desenvolvedores podem também acelera os recursos de PaaS, como clusters do Service Fabric, o recurso de aplicativos Web do serviço de aplicativo do Azure e farms do SharePoint, usando modelos do Resource Manager. Para começar no PaaS nos laboratórios, use os modelos a partir o [repositório de ambiente público](devtest-lab-configure-use-public-environments.md) ou [conectar-se o laboratório em seu próprio repositório Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Você também pode controlar os custos desses recursos para permanecer dentro do orçamento.

## <a name="integrate-with-your-existing-toolchain"></a>Integre-se com a sua cadeia de ferramentas existente
Use previamente plug-ins ou a API para provisionar ambientes de desenvolvimento/teste diretamente de sua preferência [ferramenta de CI (integração contínua)](devtest-lab-integrate-ci-cd-vsts.md), ambiente desenvolvimento integrado (IDE) ou pipeline de liberação automatizado. Você também pode usar a ferramenta de linha de comando abrangente.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- Para saber mais sobre o DevTest Labs, consulte [conceitos do DevTest Labs](devtest-lab-concepts.md).
- Para obter uma explicação com instruções passo a passo, consulte [Tutorial: Configurar um laboratório usando o Azure DevTest Labs](tutorial-create-custom-lab.md).