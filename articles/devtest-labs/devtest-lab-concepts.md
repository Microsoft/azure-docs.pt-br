---
title: Conceitos do DevTest Labs | Microsoft Docs
description: Aprenda os conceitos básicos do DevTest Labs e como ele pode facilitar a criação, o gerenciamento e o monitoramento de máquinas virtuais do Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b184f835ee99d47ddb843e5b02136299c26bfcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327851"
---
# <a name="devtest-labs-concepts"></a>Conceitos dos Laboratórios de Desenvolvimento/Teste
## <a name="overview"></a>Visão geral
A lista a seguir contém as principais definições e conceitos dos Laboratórios de Desenvolvimento/Teste:

## <a name="labs"></a>Laboratórios
Um laboratório é a infraestrutura que abrange um grupo de recursos, como VMs (máquinas virtuais), que permite melhor gerenciar esses recursos especificando limites e cotas.

## <a name="virtual-machine"></a>Máquina virtual
Uma VM do Azure é um dos vários tipos de [recursos de computação sob demanda escalonáveis](/azure/architecture/guide/technology-choices/compute-decision-tree) oferecidos pelo Azure. As VMs do Azure lhe oferecem a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que a executa, embora você ainda precise manter a VM executando determinadas tarefas como configurar, corrigir e instalar o software que será executado nela.

[Visão geral das máquinas virtuais do Windows no Azure](../virtual-machines/windows/overview.md) fornece informações sobre o que você deve considerar antes de criar uma VM, como criá-la e como gerenciá-la.

## <a name="claimable-vm"></a>VM Declarável
Uma VM Declarável do Azure é uma máquina virtual que está disponível para uso por qualquer usuário do laboratório com permissões. Um administrador de laboratório pode preparar VMs com artefatos e imagens base específicas e salvá-los em um pool compartilhado. Em seguida, um usuário de laboratório poderá declarar uma VM de trabalho do pool quando precisar de uma com essa configuração específica.

Uma VM que não é declarável não é atribuída a nenhum usuário específico inicialmente, mas será mostrada na lista de todos os usuários em “Máquinas virtuais declaráveis”. Depois de uma VM é declarada por um usuário, ela é movida para cima em sua área “Minhas máquinas virtuais” e não é mais declarável por nenhum outro usuário.

## <a name="environment"></a>Ambiente
No DevTest Labs, um ambiente refere-se a uma coleção de recursos do Azure em um laboratório. [Esta postagem de blog](./devtest-lab-faq.md#blog-post) discute como criar ambientes de várias VMs dos modelos do Azure Resource Manager.

## <a name="base-images"></a>Imagens base
Imagens base são imagens de VM com todas as ferramentas e configurações pré-instaladas e configuradas para criar uma VM rapidamente. Você pode provisionar uma VM escolhendo uma base existente e adicionando um artefato para instalar o agente de teste. Você pode salvar a VM provisionada como base para que a base possa ser usada sem precisar reinstalar o agente de teste para cada provisionamento da VM.

## <a name="artifacts"></a>Artifacts
Artefatos são usados para implantar e configurar seu aplicativo após o provisionamento de uma VM. Os artefatos podem ser:

* Ferramentas que você deseja instalar na VM, por exemplo, agentes, Fiddler e Visual Studio.
* Ações que você deseja executar na VM - como clonar um repositório.
* Aplicativos que você deseja testar.

Os artefatos são arquivos JSON do [Azure Resource Manager](../azure-resource-manager/management/overview.md) que contêm instruções para realizar a implantação e aplicar a configuração.

## <a name="artifact-repositories"></a>Repositórios de artefatos
Repositórios de artefatos são repositórios git nos quais os artefatos são verificados. Os repositórios de artefato podem ser adicionados a vários laboratórios da sua organização, permitindo a reutilização e compartilhamento.

## <a name="formulas"></a>Fórmulas
Além de fornecerem as imagens base, as fórmulas também fornecem um mecanismo para provisionamento rápido de VMs. Uma fórmula em Laboratórios de Desenvolvimento/Teste é uma lista de valores de propriedade padrão usados para criar uma VM de laboratório.
Com fórmulas, VMs com o mesmo conjunto de propriedades - como a imagem de base, tamanho de máquina virtual, rede virtual e artefatos - podem ser criadas sem a necessidade de especificar essas propriedades a cada vez. Ao criar uma máquina virtual a partir de uma fórmula, os valores padrão poderão ser usados como são ou modificados.

## <a name="policies"></a>Políticas
Políticas ajudam no controle de custos em seu laboratório. Por exemplo, você pode criar uma política para desligar automaticamente máquinas virtuais com base em uma agenda definida.

## <a name="caps"></a>Bonés
Limites são um mecanismo usado para minimizar o desperdício em seu laboratório. Por exemplo, você pode definir um limite para restringir o número de VMs que podem ser criados por usuário ou em um laboratório.

## <a name="security-levels"></a>Níveis de segurança
O acesso de segurança é determinado pelo RBAC (controle de acesso baseado em função) do Azure. Para entender como funciona o acesso, é importante reconhecer as diferenças entre um escopo, uma permissão e uma função, conforme definido pelo RBAC do Azure.

* Permissão – uma permissão é um acesso definido para uma ação específica (por exemplo, acesso de leitura para todas as máquinas virtuais).
* Função – uma função é um conjunto de permissões que podem ser agrupadas e atribuídas a um usuário. Por exemplo, a função *proprietário da assinatura* tem acesso a todos os recursos dentro de uma assinatura.
* Escopo – um escopo é um nível na hierarquia de um recurso do Azure, como um grupo de recursos, um único laboratório ou toda a assinatura.

Dentro do escopo do DevTest Labs, há dois tipos de funções para definir permissões de usuário: usuário de laboratório e proprietário de laboratório.

* Proprietário de laboratório – um proprietário de laboratório tem acesso a qualquer recurso no laboratório. Portanto, um proprietário de laboratório pode modificar políticas, ler e gravar quaisquer máquinas virtuais, alterar a rede virtual e assim por diante.
* Usuário de laboratório – um usuário de laboratório pode exibir todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas não pode modificar políticas ou as VMs criadas por outros usuários.

Para ver como criar funções personalizadas no DevTest Labs, e você pode aprender como fazer isso neste artigo, [Grant user permissions to specific lab policies (Conceder permissões de usuário para políticas específicas do laboratório)](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Como os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, ele recebe essas permissões automaticamente em cada escopo de nível inferior que está englobado. Por exemplo, se um usuário recebe a função de proprietário da assinatura, ele tem acesso a todos os recursos em uma assinatura, o que inclui todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Portanto, um proprietário de assinatura herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdadeiro. O proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de assinatura. Portanto, um proprietário de laboratório não pode ver máquinas virtuais, redes virtuais ou recursos que estão fora do laboratório.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Todos os conceitos discutidos neste artigo podem ser configurados usando modelos do Azure Resource Manager, que lhe permitem definir a infraestrutura/configuração de sua solução do Azure e implantá-la repetidamente em um estado consistente.

[Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md#template-format) descreve a estrutura de um modelo do Azure Resource Manager e as propriedades que estão disponíveis nas diferentes seções de um modelo.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
[Criar um laboratório no DevTest Labs](devtest-lab-create-lab.md)
