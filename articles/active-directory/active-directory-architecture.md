---
title: Compreender a arquitetura do Azure Active Directory | Microsoft Docs
description: "Explica o que é um locatário do Azure AD e como gerenciar o Azure por meio do Active Directory do Azure"
services: active-directory
documentationcenter: 
author: MarkusVi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/02/2017
ms.author: markvi
ms.openlocfilehash: 50dad848cfbdab7f5b1fff0fcec3b5f754e6ae74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-active-directory-architecture"></a>Compreender a arquitetura do Azure Active Directory
O Azure AD (Azure Active Directory) o habilita a gerenciar o acesso a recursos e serviços do Azure para os usuários. Está incluído no Azure AD um conjunto completo de recursos de gerenciamento de identidade. Para obter informações sobre os recursos do Azure AD, confira [O que é o Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis)

Com o Azure AD, você pode criar e gerenciar usuários e grupos e habilitar as permissões para permitir e negar acesso aos recursos da empresa. Para obter informações sobre o gerenciamento de identidade, confira [Os fundamentos do gerenciamento de identidades do Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity).

## <a name="azure-ad-architecture"></a>Arquitetura do Azure AD
A arquitetura distribuída geograficamente do Azure AD combina monitoramento abrangente, redirecionamento automatizado, failover e recursos de recuperação para habilitar o desempenho e a disponibilidade de nível corporativo para nossos clientes.

Os seguintes elementos de arquitetura são abordados neste artigo:
 *  Design de arquitetura de serviço
 *  Escalabilidade 
 *  Disponibilidade contínua
 *  Data centers

### <a name="service-architecture-design"></a>Design de arquitetura de serviço
A maneira mais comum de criar um sistema altamente disponível, escalonável e com dados avançados é por meio de blocos de construção independentes ou unidades de escala para a camada de dados do Azure AD. As unidades de escala são chamadas de *partições*. 

A camada de dados tem vários serviços de front-end que fornecem a capacidade de leitura/gravação. O diagrama a seguir mostra como os componentes de uma partição de diretório único são distribuídos geograficamente nos data centers. 

  ![Partições de Diretório Único](./media/active-directory-architecture/active-directory-architecture.png)

Os componentes da arquitetura do Azure AD incluem uma réplica principal e uma secundária.

**Réplica primária**

A *réplica principal* recebe todas as *gravações* para a partição à qual ela pertence. Qualquer operação de gravação é imediatamente replicada em uma réplica secundária em um datacenter diferente antes de retornar êxito para o chamador, garantindo assim a redundância geográfica com durabilidade das gravações.

**Réplicas secundárias**

Todas as *leituras* de diretório são realizadas por meio de *réplicas secundárias*, que estão em data centers fisicamente localizados em regiões diferentes. Há várias réplicas secundárias, pois os dados são replicados de forma assíncrona. Leituras de diretório, como solicitações de autenticação, são atendidas por meio de data centers que estão próximos a nossos clientes. As réplicas secundárias são responsáveis pela escalabilidade de leitura.

### <a name="scalability"></a>Escalabilidade

A escalabilidade é a capacidade de expandir um serviço para atender às necessidades crescentes de desempenho. A escalabilidade de gravação é obtida por meio do particionamento de dados. A escalabilidade de leitura é obtida com a replicação de dados de uma partição para várias réplicas secundárias distribuídas em todo o mundo.

Solicitações de diretório de aplicativos geralmente são roteadas para o datacenter que está fisicamente mais próximo. As gravações são redirecionadas de forma transparente para a réplica primária para fornecer consistência de leitura/gravação. Réplicas secundárias estendem significativamente a escalas de partições, porque os diretórios normalmente atendem a leituras na maioria das vezes.

Os aplicativos de diretório se conectam aos data centers mais próximos. Isso melhora o desempenho e, portanto, o dimensionamento é possível. Como uma partição de diretório pode ter várias réplicas secundárias, as réplicas secundárias podem ser colocadas mais perto dos clientes de diretório. Somente componentes de serviço internos que fazem uso intensivo de gravação direcionam a réplica principal ativa diretamente.

### <a name="continuous-availability"></a>Disponibilidade contínua

A disponibilidade (ou tempo de atividade) define a capacidade de um sistema de funcionar sem interrupções. A chave para alta disponibilidade do Azure AD é que nossos serviços podem mudar rapidamente o tráfego em vários datacenters distribuídos geograficamente. Cada data center é independente, o que habilita modos de falha não correlacionados.

O design de partição do Azure AD é simples em comparação com o design do AD empresarial, que é essencial para a expansão do sistema. Adotamos um design de mestre único que inclui um processo de failover de réplica primária cuidadosamente orquestrada e determinística.

**Tolerância a falhas**

Um sistema está mais disponível se é tolerante a falhas de hardware, rede e software. Para cada partição de diretório, existe uma réplica mestre altamente disponível: a réplica principal. Apenas as gravações na partição são executadas nessa réplica. Esta réplica está sendo monitorada continuamente e com atenção, e as gravações poderão ser transferidas imediatamente para outra réplica (que se torna o novo principal) se for detectada uma falha. Durante o failover, pode haver uma perda de disponibilidade de gravação normalmente de 1 a 2 minutos. A disponibilidade de leitura não é afetada durante esse tempo.

Operações de leitura (que ultrapassam o número de gravações por várias ordens de magnitude) só vão para réplicas secundárias. Como as réplicas secundárias são idempotentes, a perda de qualquer réplica em determinada partição é compensada facilmente direcionando as leituras para outra réplica, normalmente no mesmo datacenter.

**Durabilidade dos dados**

Uma gravação é confirmada de forma durável para pelo menos dois data centers antes de ser reconhecida. Isso ocorre primeiro com a confirmação da gravação no principal e, depois, com a replicação imediata da gravação para pelo menos um outro data center. Isso garante que uma perda catastrófica potencial do data center que hospeda o principal não resulte em perda de dados.

O Azure AD mantém um [RTO (Objetivo de Tempo de Recuperação)](https://en.wikipedia.org/wiki/Recovery_time_objective) zero para a emissão de token e as leituras de diretório e um RTO de minutos (aproximadamente cinco minutos) para gravações de diretório. Também mantemos um [RPO (Objetivo de Ponto de Recuperação)](https://en.wikipedia.org/wiki/Recovery_point_objective) zero e não perderemos dados em failovers.

### <a name="data-centers"></a>Data centers

As réplicas do Azure AD são armazenadas em data centers no mundo todo. Para obter mais informações, confira [Datacenters do Azure](https://azure.microsoft.com/en-us/overview/datacenters).

O Azure AD opera em data centers com as seguintes características:

 * Autenticação, Graph e outros serviços do AD residem por trás do serviço de Gateway. O Gateway gerencia o balanceamento de carga desses serviços. O failover ocorrerá automaticamente se todos os servidores não íntegros forem detectados usando testes de integridade transacional. Com base nesses testes de integridade, o Gateway roteia dinamicamente o tráfego para os data centers íntegros.
 * Para *leituras*, o diretório tem réplicas secundárias e serviços de front-end correspondentes em uma configuração ativo-ativo operando em vários data centers. Em caso de falha de um data center inteiro, o tráfego será roteado para um data center diferente automaticamente.
 *  Para *gravações*, o diretório realizará o failover da réplica principal (mestre) em data centers por meio de procedimentos de failover planejados (o novo principal é sincronizado com o principal antigo) ou de emergência. A durabilidade dos dados é obtida por meio da replicação de qualquer confirmação para pelo menos dois data centers.

**Consistência de dados**

O modelo do diretório é uma consistência eventual. Um problema comum com sistemas distribuídos de replicação assíncrona é que os dados retornados de uma réplica "particular" podem não estar atualizados. 

O Azure AD oferece consistência de leitura e gravação para aplicativos destinados a uma réplica secundária roteando suas gravações para a réplica principal e incluindo as gravações de forma síncrona novamente na réplica secundária.

As gravações de aplicativo usando a API do Graph do Azure AD são abstraídas da afinidade de manutenção para uma réplica do diretório para consistência de leitura/gravação. O serviço Azure AD Graph mantém uma sessão lógica, que tem afinidade para uma réplica secundária usada para leituras; a afinidade é capturada em um "token de réplica" que o serviço do graph armazena em cache usando um cache distribuído. Esse token é usado para operações subsequentes na mesma sessão lógica. 

 >[!NOTE]
 >As gravações são replicadas imediatamente para a réplica secundária para a qual foram emitidas leituras lógicas da sessão.
 >

**Proteção de backup**

O diretório implementa exclusões a quente, em vez de exclusões irreversíveis, para usuários e locatários, para fácil recuperação no caso de exclusões acidentais por um cliente. Se o administrador de locatários acidentalmente excluir usuários, poderá facilmente desfazer e restaurar os usuários excluídos. 

O Azure AD implementa backups diários de todos os dados e, assim, pode restaurar os dados em caso de quaisquer exclusões lógicas ou corrupção. Nossa camada de dados utiliza códigos de correção de erros para que possa verificar se há erros e corrigir automaticamente os tipos específicos de erros de disco.

**Métricas e monitores**

Executar um serviço de alta disponibilidade requer métricas de classe mundial e recursos de monitoramento. O Azure AD analisa e relata continuamente critérios de sucesso e métricas de integridade de serviço principais para cada um de seus serviços. Desenvolvemos e ajustamos continuamente as métricas, monitorando e alertando para cada cenário, em cada serviço do Azure AD e em todos os serviços.

Se qualquer serviço do Azure AD não estiver funcionando conforme o esperado, agiremos imediatamente para restaurar a funcionalidade o mais rápido possível. A métrica mais importante que o Azure AD acompanha é a rapidez com que podemos detectar e reduzir um problema ativo do site ou do cliente. Investimos muito no monitoramento e nos alertas para minimizar o tempo para detectar (Alvo de TTD: <5 minutos) e a preparação operacional para minimizar o tempo para mitigar (Alvo de TTM: <30 minutos).

**Operações seguras**

Adotamos controles operacionais como MFA (autenticação multifator) para qualquer operação, bem como a auditoria de todas as operações. Além disso, usamos um sistema de elevação just-in-time para conceder acesso temporário necessário para qualquer tarefa operacional sob demanda de forma contínua. Para obter mais informações, confira [A nuvem confiável](https://azure.microsoft.com/en-us/support/trust-center).

## <a name="next-steps"></a>Próximas etapas
[Guia do desenvolvedor do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

