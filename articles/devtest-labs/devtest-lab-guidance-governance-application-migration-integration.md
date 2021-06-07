---
title: Migração e integração de aplicativos no Azure DevTest Labs
description: Este artigo fornece diretrizes para governança de Azure DevTest Labs infraestrutura no contexto de migração e integração de aplicativos.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8d86728eefc46c74b49ac610e2207ce5e7ae6a9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289353"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governança da infraestrutura do Azure DevTest Labs – Integração e migração de aplicativos
Depois que seu ambiente de laboratório de desenvolvimento/teste tiver sido estabelecido, você precisará pensar sobre as perguntas a seguir:

- Como você utiliza o ambiente dentro da sua equipe de projeto?
- Como você garante que segue as políticas organizacionais necessárias e mantém a agilidade para adicionar valor ao seu aplicativo?

## <a name="azure-marketplace-images-vs-custom-images"></a>Imagens do Microsoft Azure Marketplace versus imagens personalizadas

### <a name="question"></a>Pergunta
Quando devo usar uma imagem do Microsoft Azure Marketplace versus minha própria imagem organizacional personalizada?

### <a name="answer"></a>Resposta
O Microsoft Azure Marketplace deve ser usado por padrão, a menos que você tenha preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem:

- Configuração de softwares complexos que requerem um aplicativo para serem incluídos como parte da imagem base.
- A instalação e configuração de um aplicativo pode levar várias horas, o que não é um uso eficiente de tempo de computação a ser adicionado em uma imagem do Azure Marketplace.
- Desenvolvedores e testadores necessitam de acesso rápido a uma máquina virtual e querem minimizar o tempo de configuração de uma nova máquina virtual.
- Condições de conformidade ou regulamentares (por exemplo, políticas de segurança) que devem estar em vigor para todas as máquinas.

O uso de imagens personalizadas não deve ser considerado superficialmente. Elas introduzem complexidade extra porque agora você tem que gerenciar arquivos VHD para essas imagens base subjacentes. Também será preciso aplicar patches frequentemente nas imagens base com atualizações de software. Essas atualizações incluem novas atualizações do sistema operacional (SO) e todas as alterações de atualizações ou configurações necessárias para o pacote de software.

## <a name="formula-vs-custom-image"></a>Fórmula versus imagem personalizada

### <a name="question"></a>Pergunta
Quando devo usar uma fórmula versus a imagem personalizada?

### <a name="answer"></a>Resposta
Normalmente, o fator decisivo neste cenário é o custo e a reutilização.

Se tiver um cenário em que muitos usuários/laboratórios requerem uma imagem com uso intensivo de software sobre a imagem base, você poderá reduzir o custo com a criação de uma imagem personalizada. Isso significa que a imagem é criada uma vez. Ela reduz o tempo de configuração da máquina virtual e o custo incorrido uma vez que a máquina virtual encontra-se em execução durante a instalação.

No entanto, um fator adicional a observar é a frequência das alterações ao seu pacote de software. Se você executa compilações diariamente e necessita que o software esteja nas máquinas virtuais dos seus usuários, considere usar uma fórmula em vez de uma imagem personalizada.

## <a name="use-custom-organizational-images"></a>Usar imagens organizacionais personalizadas

### <a name="question"></a>Pergunta
Como posso configurar um processo facilmente repetível para trazer minhas imagens organizacionais personalizadas para um ambiente do DevTest Labs?

### <a name="answer"></a>Resposta
Consulte [este vídeo no padrão de fábrica de imagens](./devtest-lab-faq.md#blog-post). Esse cenário é um cenário avançado e os scripts fornecidos são apenas scripts de exemplo. Caso alguma alteração seja necessária, você deve gerenciar e manter os scripts usados em seu ambiente.

Usando DevTest Labs para criar um pipeline de imagem personalizado em Azure Pipelines:

- [Introdução: prepare as VMs em minutos configurando uma fábrica de imagem no Azure DevTest Labs](./devtest-lab-faq.md#blog-post)
- [Fábrica de imagens – parte 2! Instalação Azure Pipelines e laboratório de fábrica para criar VMs](./devtest-lab-faq.md#blog-post)
- [Fábrica de imagem – Parte 3: salvar imagens personalizadas e distribuí-las para vários laboratórios](./devtest-lab-faq.md#blog-post)
- [Vídeo: fábrica de imagem personalizada com o Azure DevTest Labs](./devtest-lab-faq.md#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>Padrões para definir a configuração de rede

### <a name="question"></a>Pergunta
Como faço para garantir que máquinas virtuais de desenvolvimento e teste não tenham acesso à internet pública? Há padrões recomendados para definir a configuração de rede?

### <a name="answer"></a>Resposta
Sim. Há dois aspectos a considerar: tráfego de entrada e de saída.

**Tráfego de entrada** – Se a máquina virtual não tem um endereço IP público, ela não pode ser acessada pela internet. Uma abordagem comum é garantir que uma política de nível de assinatura seja definida, de modo que nenhum usuário seja capaz de criar um endereço IP público.

**Tráfego de saída** – Se quiser impedir máquinas virtuais de irem diretamente para a internet pública e forçar o tráfego com um firewall corporativo, então você pode rotear o tráfego local por meio da rota expressa ou VPN, usando o roteamento forçado.

> [!NOTE]
> Se você tiver um servidor proxy que bloqueia o tráfego sem as configurações de proxy, não se esqueça de adicionar exceções à conta de armazenamento de artefato do laboratório.

Você também pode usar grupos de segurança de rede para máquinas virtuais ou sub-redes. Esta etapa adiciona uma camada adicional de proteção para permitir/bloquear o tráfego.

## <a name="new-vs-existing-virtual-network"></a>Rede virtual nova versus rede virtual existente

### <a name="question"></a>Pergunta
Quando devo criar uma nova rede virtual para meu ambiente de Laboratórios de Desenvolvimento/Teste versus o uso de uma rede virtual existente?

### <a name="answer"></a>Resposta
Se suas VMs precisam interagir com a infraestrutura existente, você deve considerar usar uma rede virtual existente dentro de seu ambiente de Laboratórios de Desenvolvimento/Teste. Além disso, se você usar o ExpressRoute, é possível minimizar a quantidade de VNets/sub-redes, de modo que você não fragmente seu espaço de endereço IP que fica atribuído para uso nas assinaturas. Você também deve considerar usar o padrão de VNet Peering aqui (modelo de Hub-Spoke). Essa abordagem permite a comunicação de vnet/sub-rede entre assinaturas dentro de uma determinada região, embora o emparelhamento entre regiões seja um recurso ainda por vir na rede do Azure.

Caso contrário, cada ambiente de Laboratórios de Desenvolvimento/Teste pode ter sua própria rede virtual. No entanto, observe que há [limites](../azure-resource-manager/management/azure-subscription-service-limits.md) no número de redes virtuais por assinatura. O valor padrão é 50, embora esse limite possa ser aumentado para 100.

## <a name="shared-public-or-private-ip"></a>IP compartilhado, público ou privado

### <a name="question"></a>Pergunta
Quando devo usar um IP compartilhado, um IP público ou um IP privado?

### <a name="answer"></a>Resposta
Se você usar uma VPN site a site ou Rota Expressa, considere o uso de IPs privados para que suas máquinas sejam acessíveis por meio de sua rede interna e inacessíveis pela internet pública.

> [!NOTE]
> Os proprietários de laboratórios podem alterar essa política de sub-rede para garantir que ninguém acidentalmente crie endereços IP públicos para suas VMs. O proprietário da assinatura deve criar uma política de assinatura impedindo a criação de IPs públicos.

Ao usar IPs públicos compartilhados, as máquinas virtuais em um laboratório compartilham um endereço IP público. Essa abordagem pode ser útil quando você precisa evitar que violem os limites em endereços IP públicos para uma determinada assinatura.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limites do número de máquinas virtuais por usuário ou laboratório

### <a name="question"></a>Pergunta
Há uma regra com relação a quantas máquinas virtuais devo definir por usuário ou por laboratório?

### <a name="answer"></a>Resposta
Ao considerar o número de máquinas virtuais por usuário ou por laboratório, há três principais preocupações:

- O **custo geral** que a equipe pode gastar em recursos do laboratório. É fácil acelerar várias máquinas. Para controlar os custos, um mecanismo serve para limitar o número de máquinas virtuais por usuário e/ou por laboratório
- O número total de máquinas virtuais em um laboratório é afetado pelas [cotas de nível de assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md) disponíveis. Um dos limites superiores é 800 grupos de recursos por assinatura. Os Laboratórios de Desenvolvimento/Teste criam um novo grupo de recursos para cada máquina virtual (a menos que sejam usados IPs públicos compartilhados). Se houver 10 laboratórios em uma assinatura, os laboratórios poderão se ajustar a aproximadamente 79 máquinas virtuais em cada laboratório (800 limite superior – 10 grupos de recursos para os 10 laboratórios em si) = 79 máquinas virtuais por laboratório.
- Se o laboratório é conectado ao local por meio da Rota Expressa (por exemplo), há **espaços de endereço IP definidos disponíveis** para a VNet/sub-rede. Para garantir que as máquinas virtuais no laboratório não falhem ao serem criadas (erro: não é possível obter o endereço IP), os proprietários de laboratório podem especificar o máximo de máquinas virtuais por laboratório alinhado com o espaço de endereço IP disponível.

## <a name="use-resource-manager-templates"></a>Use modelos do Gerenciador de Recursos

### <a name="question"></a>Pergunta
Como posso usar modelos do Resource Manager no meu ambiente de Laboratórios de Desenvolvimento/Teste?

### <a name="answer"></a>Resposta
Você implanta seus modelos do Resource Manager em um ambiente do DevTest Labs usando as etapas mencionadas no artigo [ambientes do DevTest Labs](devtest-lab-test-env.md) . Basicamente, você verifica seus modelos do Gerenciador de Recursos em um Repositório Git (Azure Repos ou GitHub) e adiciona um [repositório privado para seus modelos](devtest-lab-test-env.md) ao laboratório.

Esse cenário pode não ser útil se você estiver usando os Laboratórios de Desenvolvimento/Teste para hospedar de desenvolvimento, mas pode ser útil se você estiver criando um ambiente de preparo, que é representativo da produção.

Ainda vale a pena observar que o número de máquinas virtuais por laboratório ou por usuário só limita o número de máquinas criadas nativamente no laboratório em si e não por quaisquer ambientes (modelos do Resource Manager).

## <a name="next-steps"></a>Próximas etapas
Confira [Usar ambientes nos Laboratórios de Desenvolvimento/Teste](devtest-lab-test-env.md).
