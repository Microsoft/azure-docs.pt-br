---
title: Políticas de suporte do serviço de Kubernetes do Azure (AKS)
description: Saiba mais sobre as políticas de suporte do serviço de Kubernetes do Azure (AKS), responsabilidade compartilhada, recursos de visualização/Alpha/Beta.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032461"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Políticas de suporte do serviço de Kubernetes do Azure (AKS)

Este artigo fornece detalhes em torno das políticas de suporte técnico do AKS, limitações, e os detalhes, incluindo gerenciamento de nós de trabalho, gerenciado componentes de plano, componentes de software livre de terceiros e segurança de controle / gerenciamento de patches.

## <a name="service-updates--releases"></a>Versões e atualizações de serviço

* Para obter informações de versão, consulte o [notas de versão do AKS][1]
* Para os recursos em visualização pública, consulte [recursos de visualização do AKS e projetos relacionados][2]

## <a name="what-is-managed"></a>O que é 'gerenciado'

Ao contrário de componentes básicos do IaaS nuvem como computação ou rede, que expõem os controles de nível baixo e opções de personalização para os usuários aproveitem, o serviço AKS fornece uma implantação de Kubernetes turnkey que representa o conjunto comum de configurações e recursos necessários para o Kubernetes. Os clientes que usam esse serviço tem um número limitado de personalizações, implantação e outras opções. Isso também significa que os clientes não precisam se preocupar ou gerenciar o cluster do Kubernetes (s) diretamente.

Com o AKS, o cliente recebe um totalmente gerenciado **plano de controle** – em que o plano de controle contém todos os componentes e serviços necessários para operar e fornecer a clusters de Kubernetes para os usuários finais. Todos os componentes de Kubernetes são mantidos e operados pela Microsoft.

Com o gerenciado **plano de controle** os seguintes componentes são gerenciados e monitorados pela Microsoft:

* Kubelet / servidor (es) de API do Kubernetes
* Etcd ou um repositório de chave/valor compatível – incluindo qualidade de serviço, escalabilidade e tempo de execução
* Serviços DNS (por exemplo, kube-dns / CoreDNS)
* Proxy de Kubernetes/rede

O AKS não é um 100% gerenciado **cluster** solução. Alguns componentes (como nós de trabalho) tem certas **responsabilidades compartilhadas** casos em que ações para manter o cluster do AKS exigem interação do usuário. Por exemplo, nó de trabalho aplicação de patches de segurança do sistema operacional.

 **Gerenciado**, significa que a Microsoft e a equipe do AKS é implantado, opera e é responsável pela disponibilidade e a funcionalidade desses serviços. **Os clientes não é possível alterar esses componentes**. Personalização é limitada para garantir uma experiência de usuário consistente e escalável. Para uma solução totalmente personalizável, consulte [AKS-mecanismo](https://github.com/Azure/aks-engine).

> [!NOTE]
> É importante entender que nós de trabalho do serviço Kubernetes do Azure são exibidos no Portal do Azure como recursos de IaaS do Azure regular, embora essas máquinas virtuais são implantadas em um grupo de recursos do Azure personalizado (prefixados com MC\\*). Um usuário pode alterá-los, SSH-los assim como máquinas de virtuais normais (você não pode, no entanto, alterar a imagem base do sistema operacional, e as alterações não podem persistir por meio de uma atualização ou reinicialização), e você pode anexar outros recursos do Azure-los ou modificá-los de outra forma. **No entanto, fazer isso de fora da banda e a personalização significa que o cluster do AKS em si pode se tornar sem suporte. Evite a alteração de nó de trabalho, a menos que indicado pelo Microsoft Support.**

## <a name="what-is-shared-responsibility"></a>O que é de responsabilidade compartilhada

No momento da criação de cluster, o AKS cria um número de nós de trabalho de Kubernetes definidos pelo cliente. Esses nós, conforme observado são onde as cargas de trabalho do cliente são executadas. Os clientes possuem e podem exibir ou modificar esses nós de trabalho.

Como esses nós estão em execução de código privado e armazenam dados confidenciais, o suporte da Microsoft tem **acesso limitado** ao cliente todos os nós de cluster. O suporte da Microsoft não pode fazer logon no, executar comandos ou exibir os logs para esses nós sem permissão expressa do cliente e/ou assistência para executar comandos de depuração, conforme indicado pela equipe de suporte.

Devido à natureza confidencial desses nós de trabalho, a Microsoft leva muito cuidadoso a fim de limitar o gerenciamento de 'nos bastidores' de nós. Mesmo se o Kubernetes mestre nós, etcd e outras falhas de componentes (gerenciada pela Microsoft) continuará sua carga de trabalho ser executado em muitos casos. Se nós de trabalho são modificados sem cuidado, ele pode resultar em perda de dados e/ou a carga de trabalho e renderizar o cluster sem suporte.

## <a name="azure-kubernetes-service-support-coverage"></a>Cobertura de suporte do serviço Kubernetes do Azure

**A Microsoft fornece suporte técnico para o seguinte:**

* Conectividade com todos os componentes de Kubernetes fornecida e suportada pelo serviço do Kubernetes (como o servidor de API)
* Gerenciamento, tempo de atividade, QoS e operações do Kubernetes controlam os serviços de plano (etcd de nós, o servidor de API, Kubernetes mestre, kube-dns, por exemplo.
* Suporte de Etcd inclui backups automatizados, transparentes de todos os dados etcd a cada 30 minutos para restauração de estado de planejamento e o cluster de desastre. Esses backups não estão disponíveis diretamente aos clientes/usuários e são usados para garantir a consistência e confiabilidade de dados
* Quaisquer pontos de integração no driver do provedor de nuvem do Azure para Kubernetes como integrações de outros Azure serviços, como balanceadores de carga, Volumes persistentes, de rede (Kubernetes e CNI do Azure)
* Dúvidas ou problemas em torno de personalização do controle do plano de componentes, como o servidor de API do Kubernetes, etcd e kube-dns.
* Problemas sobre tópicos de rede, como o CNI do Azure, Kubenet ou outra rede problemas de acesso e funcionalidade (DNS resolução, perda de pacotes, roteamento e assim por diante).
  * Cenários de rede com suporte incluem Kubenet (Basic) e o Advanced Networking (CNI do Azure) dentro do cluster e componentes associados, conectividade com outros aplicativos e serviços do Azure. Além disso, os controladores de entrada e configuração de Balanceador de carga/entrada, o desempenho da rede e latência são suportados pela Microsoft.

**Microsoft não oferece suporte técnico para o seguinte:**

* Consultoria / uso "Como" Kubernetes perguntas, por exemplo de como criar controladores de entrada personalizado, perguntas de carga de trabalho do aplicativo, e pacotes de terceiros/OSS de terceiro ou ferramentas que estão fora do escopo.
  * Comunicado de tíquetes para AKS cluster funcionalidade, personalização, ajuste – operações do Kubernetes, por exemplo, problemas/how-tos estiverem dentro do escopo.
* Projetos de código aberto de terceiros não são fornecidos como parte do Kubernetes plano de controle ou implantados com clusters AKS, como Istio, Helm, Envoy e outros.
  * Para projetos de software livre de terceiros, como Helm e Kured, melhor suporte é fornecido para obter exemplos e aplicativos fornecidos na documentação da Microsoft e em que essa ferramenta de software livre de terceiros se integra com o provedor de nuvem do Kubernetes Azure ou outros bugs específicos do AKS.
* Software de código-fonte fechado de terceiros – isso pode incluir ferramentas, software ou dispositivos de rede de verificação de segurança.
* Não há suporte para questões sobre saídas de compilação "a nuvem" ou de vários fornecedores, por exemplo, executar uma solução de fornecedor de nuvem pública do federados multi não é suportado.
* Personalizações de rede específico, que não sejam aquelas documentadas oficial [documentação do AKS][3].
  > [!NOTE]
  > Há suporte para problemas e bugs em torno de grupos de segurança de rede. Por exemplo, o suporte pode responder a perguntas sobre os NSGs não atualizar ou comportamento inesperado de NSG ou balanceador de carga.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Cobertura de suporte do serviço Kubernetes do Azure (nós de trabalho)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilidades da Microsoft para nós de trabalho do serviço Kubernetes do Azure

Conforme observado no `shared responsibility` seção, Kubernetes, nós de trabalho se enquadram em um modelo de responsabilidade de junção, onde:

* Fornece a imagem do sistema operacional de base com adições necessárias (por exemplo, monitoramento e agentes de rede)
* Entrega automática de patches do sistema operacional para os nós de trabalho
* Correção automática de problemas com o Kubernetes controlar os componentes de plano em execução em nós de trabalho, tais como:
  * kube-proxy
  * componentes de mestre de túneis de rede que fornecem os caminhos de comunicação para o Kubernetes
  * kubelet
  * daemon do docker/moby

> [!NOTE]
> Se um componente do plano de controle é não-operacional em um nó de trabalho, a equipe AKS talvez seja necessário reinicializar o nó de trabalho inteira para resolver o problema. Isso é feito em nome do usuário e não é executado, a menos que um escalonamento do cliente é feito (devido ao acesso à carga de trabalho ativa de clientes e dados). Sempre que possível pessoal AKS funcionará para tornar qualquer obrigatório reinicialize afetando fora do aplicativo.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilidades do cliente para nós de trabalho do serviço Kubernetes do Azure

**Microsoft não:**

- Automaticamente patches para efeito de nível de nós de trabalho de reinicialização de sistema operacional **(atualmente, veja abaixo)**

Patches do sistema operacional são entregues a nós de trabalho, no entanto, é o **responsabilidade do cliente** reinicializar os nós de trabalho para que as alterações entrem em vigor. Essa aplicação de patch automática inclui bibliotecas compartilhadas, daemons, como SSHD e outros componentes de nível de sistema/sistema operacional.

Para atualizações de Kubernetes **os clientes são responsáveis pela execução da atualização** por meio do painel de controle do Azure ou a CLI do Azure. Isso se aplica a atualizações que contém a segurança ou melhorias de funcionalidade no Kubernetes.

> [!NOTE]
> Como o AKS é um `managed service` nossas metas de término do serviço incluem a remoção de responsabilidade de patches, atualizações, coleta de log e muito mais para torná-lo um serviço completamente gerenciado e participação ativa mais. Esses itens (reinicialização do nó, aplicação de patch automática) podem ser removidos em versões futuras, à medida que aumentam de nossos recursos de ponta a ponta de gerenciamento.

### <a name="security-issues-and-patching"></a>Problemas de segurança e aplicação de patch

Em alguns casos (por exemplo, CVEs), uma falha de segurança pode ser encontrada em um ou mais dos componentes do serviço AKS. Nesses cenários, o AKS corrigir todos os clusters afetados para atenuar o problema se possível ou fornecer orientações sobre a atualização para os usuários.

Para nós de trabalho afetados por tal uma brecha de segurança, se um patch de tempo de inatividade zero do problema estiver disponível, a equipe AKS aplicar essa correção e notificar os usuários da alteração.

Se um patch de segurança requer reinicialização do nó de trabalho, a Microsoft notificará os clientes desse requisito e o cliente é responsável por executar a reinicialização ou atualizar para obter o patch para seu cluster. Se os usuários não aplicar os patches de acordo com as diretrizes do AKS, seu cluster continuará vulnerável aos problemas.

### <a name="node-maintenance-and-access"></a>Acesso e a manutenção de nó

Como nós de trabalho são uma responsabilidade compartilhada e sob a propriedade dos clientes, os clientes podem fazer logon nesses trabalhadores e realizar alterações potencialmente prejudiciais, como atualizações de kernel, remoção de pacotes e instalar novos pacotes.

Se os clientes executam ações destrutivas ou ações que disparam os componentes do plano de controle para ficar offline ou caso contrário, ficar inoperante, o serviço AKS detectará essa falha e executar a correção automática para restaurar o nó de trabalho para o trabalho anterior estado.

Embora os clientes podem entrar e alterar nós de trabalho, é *desencorajado* porque isso pode fazer com que o cluster sem suporte.

## <a name="network-ports-access-and-network-security-groups"></a>Portas de rede, acesso e os grupos de segurança de rede

Como um serviço gerenciado, o AKS tem requisitos específicos de rede e de conectividade. Esses requisitos são menos flexíveis que componentes normais de IaaS. Ao contrário de outros componentes de IaaS, determinadas operações (como a personalização de regras do grupo de segurança de rede, bloqueio de porta específica, lista de permissões de URL e assim por diante) podem renderizar seu cluster sem suporte (por exemplo, regras de firewall bloqueando a porta de saída 443).

> [!NOTE]
> Bloquear completamente a saída (por exemplo, lista de permissões explícitas de domínio/porta) do seu cluster não é um cenário AKS com suporte no momento. A lista de URLs e portas está sujeitas a alterações sem aviso e pode ser fornecida pelo suporte do Azure por meio de um tíquete. A lista fornecida é somente para clientes que estão dispostos a aceitar que *a disponibilidade do seu cluster pode ser afetada a qualquer momento.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Recursos de Kubernetes Alpha/Beta (sem suporte)

AKS oferece suporte apenas à estáveis recursos dentro do projeto Kubernetes upstream. Não há suporte para recursos de alfa/Beta disponíveis no Kubernetes upstream, a menos que o contrário comunicadas e documentadas.

Há dois casos em que os recursos de alfa ou Beta podem ser revertidos antes do GA:

* Os clientes foram atendidos com o produto do AKS, suporte ou equipes de engenharia e foi solicitados explicitamente para experimentar esses novos recursos.
* Esses recursos foram [habilitados por meio de um sinalizador de recursos] [ 2] (explícita opt-in)

## <a name="preview-features--feature-flags"></a>Recursos de visualização / sinalizadores de recurso

Para obter recursos e funcionalidades que exigem a comunidade de teste, estendida e comentários do usuário, a Microsoft lançará novos recursos de visualização ou recursos por trás de um sinalizador de recursos. Esses recursos devem ser considerados pré-lançamento / Beta e são expostas para dar aos usuários a oportunidade de experimentar esses novos recursos.

No entanto, esses visualizar / sinalizador de recurso recursos não se destinam para uso em produção – APIs, alteração de comportamento, correções de bug e outras alterações podem ser feitas que pode resultar em tempo de inatividade e clusters instáveis. Suporte para esses recursos é limitado aos relatórios de bug/problema. Não habilite esses recursos em sistemas de produção ou assinaturas.

> [!NOTE]
> Habilitar recursos de visualização entra em vigor no Azure **assinatura** nível. Não instale recursos de visualização na assinatura de produção como ele pode alterar o comportamento de API padrão que afetam as operações normais.

## <a name="upstream-bugs-and-issues"></a>Problemas e erros de upstream

Dada a velocidade de desenvolvimento do projeto Kubernetes upstream, invariavelmente há bugs que não podem ser corrigidos ou trabalharam alternativa dentro do sistema de AKS e em vez disso, exigem maiores patches aos projetos upstream (como Kubernetes, sistemas operacionais de nó/de trabalho e Kernels). Para componentes que sabemos (por exemplo, o provedor de nuvem do Azure), pessoal do AKS e do Azure é confirmadas para corrigir o problema upstream na comunidade.

Para casos em que um problema de suporte técnico é causados por raiz para um ou mais bugs de upstream, o suporte do AKS e engenharia fará os seguintes itens:

* Identifique e vincular os bugs upstream com quaisquer detalhes de suporte sobre por que isso afeta o seu cluster e/ou a carga de trabalho. Os clientes serão fornecidos com links para os repositórios/problemas necessários assistir os problemas e ver quando uma nova versão de Kubernetes/outros incluirá nas correções
* Possíveis soluções alternativas ou atenuações: Em alguns casos, talvez seja possível resolver o problema – nesse caso, um "[problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)" serão arquivados no repositório de AKS explica:
  * O problema e o link a bugs de upstream
  * Os detalhes em torno de atualização/persistência da solução e a solução alternativa
  * Linhas do tempo aproximadas para inclusão com base na cadência de lançamento de upstream

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
