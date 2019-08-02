---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: f903828285b0d4fdc8fbd932fa7c85056e937481
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172479"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>(PRETERIDO) Perguntas frequentes sobre o Serviço de Contêiner

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

## <a name="orchestrators"></a>Orquestradores

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Quais orquestradores de contêiner têm suporte no Serviço de Contêiner do Azure? 

Há suporte para o DC/OS de software livre, Docker Swarm e Kubernetes. Para obter mais informações, confira a [Visão geral](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Há suporte para o modo Docker Swarm? 

Atualmente, não há suporte para o modo Swarm, mas isso está no roteiro de serviço. 

### <a name="does-azure-container-service-support-windows-containers"></a>O Serviço de Contêiner do Azure dá suporte a contêineres do Windows?  

Atualmente há suporte para contêineres do Linux com todos os orquestradores. O suporte para contêineres do Windows com o Kubernetes está em versão prévia.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>É recomendado um orquestrador específico no Serviço de Contêiner do Azure? 
Geralmente, não recomendamos um orquestrador específico. Se tiver experiência com um dos orquestradores com suporte, você poderá aplicar essa experiência ao Serviço de Contêiner do Azure. No entanto, as tendências de dados sugerem que DC/OS é comprovado em produção para Big Data e cargas de trabalho de IoT, Kubernetes é adequado para cargas de trabalho nativas de nuvem e Docker Swarm é conhecido por sua integração a ferramentas Docker e sua fácil curva de aprendizado.

Dependendo do cenário, você também pode criar e gerenciar soluções de contêiner personalizadas com outros serviços do Azure. Esses serviços incluem [Máquinas Virtuais](../articles/virtual-machines/linux/overview.md), [Service Fabric](../articles/service-fabric/service-fabric-overview.md), [Aplicativos Web](../articles/app-service/overview.md) e [Lote](../articles/batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Qual é a diferença entre o Serviço de Contêiner do Azure e o Mecanismo ACS? 
Serviço de Contêiner do Azure é um serviço do Azure com suporte de SLA com recursos no portal do Azure, ferramentas de linha de comando do Azure e APIs do Azure. O serviço o habilita a implementar rapidamente e gerenciar clusters que executam ferramentas de orquestração do contêiner padrão com um número relativamente pequeno de opções de configuração. 

O [Mecanismo ACS](http://github.com/Azure/acs-engine) é um projeto de software livre que habilita usuários avançados a personalizar a configuração de cluster em todos os níveis. Essa capacidade de alterar a configuração de infraestrutura e software significa que não oferecemos um SLA para o Mecanismo ACS. O suporte é tratado por meio do projeto de software livre no GitHub, em vez de canais oficiais da Microsoft. 

Para obter mais detalhes, consulte nossa [política de suporte para contêineres](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers).

## <a name="cluster-management"></a>Gerenciamento de clusters

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Como criar chaves SSH para o cluster?

Você pode usar as ferramentas padrão no sistema operacional para criar um RSA SSH público e um par de chaves privadas para autenticação em relação a máquinas virtuais Linux para o cluster. Para obter as etapas, confira as diretrizes de [OS X e Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../articles/virtual-machines/linux/ssh-from-windows.md). 

Se você usar [comandos da CLI do Azure](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) para implantar um cluster do serviço de contêiner, as chaves SSH poderão ser geradas automaticamente para seu cluster.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Como criar uma entidade de serviço para o cluster Kubernetes?

Uma ID de entidade e senha de serviço do Azure Active Directory também são necessárias para criar um cluster Kubernetes no Serviço de Contêiner do Azure. Para obter mais informações, confira [Sobre a entidade de serviço para um cluster Kubernetes](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md).

Se você usar [comandos da CLI do Azure](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) para implantar um cluster Kubernetes, as credenciais de entidade de serviço poderão ser geradas automaticamente para o seu cluster.

### <a name="how-large-a-cluster-can-i-create"></a>Posso criar um cluster de qual tamanho?
Você pode criar um cluster com 1, 3 ou 5 nós mestres. Você pode escolher até 100 nós de agente.

> [!IMPORTANT]
> Para clusters maiores e dependendo do tamanho VM que você escolher para seus nós, talvez seja necessário aumentar a cota de núcleos em sua assinatura. Para solicitar um aumento de cota, abra uma [solicitação de atendimento ao cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitamente. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Como aumentar o número de mestres depois de criar um cluster? 
Depois que o cluster é criado, o número de mestres é fixo e não pode ser alterado. Durante a criação do cluster, o ideal é selecionar vários mestres para uma alta disponibilidade.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Como aumentar o número de agentes depois de criar um cluster? 
Você pode dimensionar o número de agentes no cluster usando o portal do Azure ou as ferramentas de linha de comando. Confira [Dimensionar um cluster do Serviço de Contêiner do Azure](../articles/container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Quais são as URLs dos mestres e agentes? 
As URLs de recursos de cluster no Serviço de Contêiner do Azure baseiam-se no prefixo de nome DNS que você fornece e no nome da região do Azure que escolheu para a implantação. Por exemplo, o FQDN (nome de domínio totalmente qualificado) do nó mestre está neste formato:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Você pode encontrar as URLs comumente usadas para o cluster no portal do Azure, no Gerenciador de Recursos do Azure ou em outras ferramentas do Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Como saber a qual versão de orquestrador está em execução no meu cluster?

* DC/SO: veja a [documentação do Mesosphere](https://docs.mesosphere.com/1.7/usage/cli/command-reference/)
* Docker Swarm: Execute `docker version`
* Kubernetes: Execute `kubectl version`

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Como atualizar o orquestrador após a implantação?

Atualmente, o Serviço de Contêiner do Azure não fornece ferramentas para atualizar a versão do orquestrador implantado no cluster. Se o Serviço de Contêiner oferecer suporte a uma versão posterior, você pode implantar um novo cluster. Outra opção é usar ferramentas específicas do orquestrador, se disponíveis, para atualizar um cluster no local. Por exemplo, consulte [Atualização de DC/OS](http://docs.mesosphere.com/1.12/installing/production/upgrading).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Onde encontrar a cadeia de conexão SSH para o cluster?

Você pode encontrar a cadeia de conexão no portal do Azure ou usando as ferramentas de linha de comando do Azure. 

1. No portal, navegue até o grupo de recursos da implantação de cluster.  

2. Clique em **Visão geral** e clique no link para **Implantações** em **Noções básicas**. 

3. Na folha **Histórico de implantação**, clique na implantação que tem um nome começando com **microsoft-acs** seguido por uma data de implantação. Exemplo: microsoft-acs-201701310000.  

4. Na página **Resumo**, em **Saídas**, são fornecidos vários links de cluster. **SSHMaster0** fornece uma cadeia de conexão SSH para o primeiro mestre no cluster do serviço de contêiner. 

Como mencionado anteriormente, você também pode usar ferramentas do Azure para localizar o FQDN do mestre. Fazer uma conexão SSH com o mestre usando o FQDN do mestre e o nome de usuário que você especificou ao criar o cluster. Por exemplo:

```bash
ssh userName@masterFQDN –A –p 22 
```

Para obter mais informações, confira [Conectar-se a um cluster do Serviço de Contêiner do Azure](../articles/container-service/kubernetes/container-service-connect.md).

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>Minha resolução de nome DNS não está funcionando no Windows. O que devo fazer?

Há alguns problemas conhecidos do DNS no Windows cujas correções ainda estão sendo descontinuadas. Verifique se você está usando o mecanismo de acs e a versão do Windows mais atualizados (com [KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) e [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848) instalados) para que seu ambiente possa aproveitá-los. Caso contrário, consulte a tabela abaixo para conhecer as etapas de mitigação:

| Sintoma de DNS | Solução alternativa  |
|-------------|-------------|
|Quando o contêiner de carga de trabalho estiver instável e falhar, o namespace de rede será limpo | Reimplantar todos os serviços afetados |
| Acesso ao serviço VIP interrompido | Configure um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) para sempre manter um pod normal (sem privilégios) em execução |
|Quando o nó no qual o contêiner está sendo executado ficar indisponível, as consultas DNS poderão falhar, resultando em uma "entrada de cache negativo" | Execute o seguinte dentro dos contêineres afetados: <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> Se isso ainda não resolver o problema, tente desabilitar completamente o cache de DNS: <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais](../articles/container-service/kubernetes/container-service-intro-kubernetes.md) sobre o Serviço de Contêiner do Azure.
* Implantar um cluster de serviço de contêiner usando o [portal](../articles/container-service/dcos-swarm/container-service-deployment.md) ou a [CLI do Azure](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md).
