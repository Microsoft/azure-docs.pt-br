---
title: Considerações de segurança de instâncias de contêiner do Azure
description: Recomendações para proteger imagens e segredos para instâncias de contêiner do Azure e considerações de segurança geral para qualquer plataforma de contêiner
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 618d3a901698e46760d970f6d4fbc4157c5d2ea3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325915"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerações de segurança para instâncias de contêiner do Azure

Este artigo apresenta considerações de segurança para usar instâncias de contêiner do Azure para executar aplicativos de contêiner. Os tópicos incluem:

> [!div class="checklist"]
> * **Recomendações de segurança** para o gerenciamento de imagens e segredos para instâncias de contêiner do Azure
> * **Considerações para o ecossistema de contêiner** ao longo do ciclo de vida do contêiner, para qualquer plataforma de contêiner

## <a name="security-recommendations-for-azure-container-instances"></a>Recomendações de segurança para instâncias de contêiner do Azure

### <a name="use-a-private-registry"></a>Usar um registro privado

Os contêineres são criados a partir de imagens que são armazenadas em um ou mais repositórios. Esses repositórios podem pertencer a um registro público, como o [Hub](https://hub.docker.com)do Docker ou a um registro privado. Um exemplo de um registro privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), que pode ser instalado localmente ou em uma nuvem privada virtual. Você também pode usar os serviços de registro de contêiner privado baseados em nuvem, incluindo o [registro de contêiner do Azure](../container-registry/container-registry-intro.md). 

Uma imagem de contêiner disponível publicamente não garante a segurança. As imagens de contêiner consistem em várias camadas de software e cada camada de software pode ter vulnerabilidades. Para ajudar a reduzir a ameaça de ataques, você deve armazenar e recuperar imagens de um registro privado, como o registro de contêiner do Azure ou o registro confiável do Docker. Além de fornecer um registro privado gerenciado, o registro de contêiner do Azure dá suporte à [autenticação baseada na entidade de serviço](../container-registry/container-registry-authentication.md) por meio de Azure Active Directory para fluxos de autenticação básica. Essa autenticação inclui acesso baseado em função para permissões somente leitura (pull), gravação (push) e proprietário.

### <a name="monitor-and-scan-container-images"></a>Monitorar e verificar imagens de contêiner

Soluções de monitoramento e verificação de segurança, como [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [segurança de água](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) , estão disponíveis por meio do Azure Marketplace. Você pode usá-los para verificar as imagens de contêiner em um registro privado e identificar possíveis vulnerabilidades. É importante entender a profundidade da verificação que as diferentes soluções fornecem. 

### <a name="protect-credentials"></a>Proteger credenciais

Os contêineres podem se espalhar por vários clusters e regiões do Azure. Portanto, você deve proteger as credenciais necessárias para logons ou acesso à API, como senhas ou tokens. Certifique-se de que somente usuários privilegiados possam acessar esses contêineres em trânsito e em repouso. Inventariar todos os segredos de credencial e, em seguida, exigir que os desenvolvedores usem ferramentas de gerenciamento de segredos emergentes projetadas para plataformas de contêiner.  Certifique-se de que sua solução inclui bancos de dados criptografados, criptografia de TLS para segredos em trânsito e [controle de acesso baseado em função](../role-based-access-control/overview.md)de privilégio mínimo. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) é um serviço de nuvem que protege as chaves de criptografia e os segredos (como certificados, cadeias de conexão e senhas) para aplicativos em contêineres. Como esses dados são confidenciais e críticos para os negócios, proteja o acesso a seus cofres de chaves para que somente aplicativos e usuários autorizados possam acessá-los.

## <a name="considerations-for-the-container-ecosystem"></a>Considerações para o ecossistema do contêiner

As seguintes medidas de segurança, implementadas bem e gerenciadas efetivamente, podem ajudá-lo a proteger e proteger seu ecossistema de contêiner. Essas medidas se aplicam ao longo do ciclo de vida do contêiner, desde o desenvolvimento até a implantação de produção e a uma variedade de orquestradores de contêiner, hosts e plataformas. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Use o gerenciamento de vulnerabilidades como parte do seu ciclo de vida de desenvolvimento de contêiner 

Ao usar o gerenciamento de vulnerabilidades eficaz em todo o ciclo de vida de desenvolvimento do contêiner, você aumenta as chances de identificar e resolver questões de segurança antes que elas se tornem um problema mais sério. 

### <a name="scan-for-vulnerabilities"></a>Verificar se há vulnerabilidades 

Novas vulnerabilidades são descobertas o tempo todo, portanto, a verificação e a identificação de vulnerabilidades é um processo contínuo. Incorpore a verificação de vulnerabilidade em todo o ciclo de vida do contêiner:

* Como uma verificação final em seu pipeline de desenvolvimento, você deve executar uma verificação de vulnerabilidade em contêineres antes de enviar as imagens por push para um registro público ou privado. 
* Continue a verificar as imagens de contêiner no registro para identificar as falhas que estavam de alguma forma perdidas durante o desenvolvimento e para resolver quaisquer vulnerabilidades recém-descobertas que possam existir no código usado nas imagens de contêiner.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapear vulnerabilidades de imagem para executar contêineres 

Você precisa ter um meio de mapear vulnerabilidades identificadas em imagens de contêiner para executar contêineres, para que os problemas de segurança possam ser mitigados ou resolvidos.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Garantir que apenas as imagens aprovadas sejam usadas em seu ambiente 

Há uma alteração e volatilidade suficientes em um ecossistema de contêineres sem permitir contêineres desconhecidos também. Permitir apenas imagens de contêiner aprovadas. Ter ferramentas e processos em vigor para monitorar e impedir o uso de imagens de contêiner não aprovadas. 

Uma maneira eficaz de reduzir a superfície de ataque e impedir que os desenvolvedores façam erros de segurança críticos é controlar o fluxo de imagens de contêiner em seu ambiente de desenvolvimento. Por exemplo, você pode aprovar uma única distribuição do Linux como uma imagem de base, preferencialmente uma que seja Lean (Alpine ou CoreOS em vez do Ubuntu), para minimizar a superfície para possíveis ataques. 

Assinatura de imagem ou impressão digital pode fornecer uma cadeia de custódia que permite verificar a integridade dos contêineres. Por exemplo, o registro de contêiner do Azure dá suporte ao modelo de [confiança de conteúdo](https://docs.docker.com/engine/security/trust/content_trust) do Docker, que permite que os editores de imagens assinem imagens enviadas por push a um registro e que os consumidores de imagem recebam apenas imagens assinadas.

### <a name="permit-only-approved-registries"></a>Permitir somente registros aprovados 

Uma extensão para garantir que seu ambiente Use apenas imagens aprovadas é permitir apenas o uso de registros de contêiner aprovados. Exigir o uso de registros de contêiner aprovados reduz sua exposição ao risco, limitando o potencial para a introdução de vulnerabilidades desconhecidas ou problemas de segurança. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantir a integridade das imagens durante todo o ciclo de vida 

Parte do gerenciamento de segurança em todo o ciclo de vida do contêiner é garantir a integridade das imagens de contêiner no registro e conforme elas são alteradas ou implantadas na produção. 

* Imagens com vulnerabilidades, mesmo secundárias, não devem ter permissão para serem executadas em um ambiente de produção. O ideal é que todas as imagens implantadas na produção sejam salvas em um registro privado acessível a uma seleção. Mantenha o número de imagens de produção pequenas para garantir que elas possam ser gerenciadas com eficiência.

* Porque é difícil identificar a origem do software de uma imagem de contêiner disponível publicamente, criar imagens da origem para garantir o conhecimento da origem da camada. Quando surge uma vulnerabilidade em uma imagem de contêiner criada automaticamente, os clientes podem encontrar um caminho mais rápido para uma resolução. Com uma imagem pública, os clientes precisariam encontrar a raiz de uma imagem pública para corrigi-la ou obter outra imagem segura do Publicador. 

* Não há garantia de que uma imagem digitalizada completamente implantada em produção esteja atualizada durante o tempo de vida do aplicativo. As vulnerabilidades de segurança podem ser reportadas para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implantação em produção. 

  Auditar periodicamente as imagens implantadas em produção para identificar imagens desatualizadas ou que não foram atualizadas há algum tempo. Você pode usar metodologias de implantação verde-azulado e mecanismos de atualização sem interrupção para atualizar imagens de contêiner sem tempo de inatividade. Você pode digitalizar imagens usando as ferramentas descritas na seção anterior. 

* Use um pipeline de CI (integração contínua) com a verificação de segurança integrada para criar imagens seguras e enviá-las por push para o registro privado. A verificação de vulnerabilidade incorporada à solução de CI garante que as imagens que passam em todos os testes sejam enviadas para o registro privado a partir do qual as cargas de trabalho de produção são implantadas. 

  Uma falha de pipeline de CI garante que imagens vulneráveis não sejam enviadas para o registro privado que é usado para implantações de carga de trabalho de produção. Ele também automatiza a verificação de segurança de imagem se houver um número significativo de imagens. Caso contrário, a auditoria manual de imagens em busca de vulnerabilidades de segurança pode ser muito demorada e sujeita a erros. 

### <a name="enforce-least-privileges-in-runtime"></a>Impor privilégios mínimos no tempo de execução 

O conceito de privilégios mínimos é uma prática recomendada de segurança básica que também se aplica aos contêineres. Quando uma vulnerabilidade é explorada, geralmente fornece ao invasor acesso e privilégios iguais aos do aplicativo ou processo comprometido. Garantir que os contêineres operem com os privilégios mais baixos e o acesso necessário para que o trabalho seja reduzido reduz a exposição a riscos. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Reduzir a superfície de ataque de contêiner removendo privilégios desnecessários 

Você também pode minimizar a possível superfície de ataque removendo quaisquer processos ou privilégios não utilizados ou desnecessários do tempo de execução do contêiner. Contêineres com privilégios executados como raiz. Se um usuário mal-intencionado ou uma carga de trabalho escapar em um contêiner com privilégios, o contêiner será executado como raiz no sistema.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Arquivos de lista de permissões e executáveis que o contêiner tem permissão para acessar ou executar 

A redução do número de variáveis ou desconhecidas ajuda a manter um ambiente estável e confiável. Limitar contêineres para que eles possam acessar ou executar somente arquivos pré-aprovados ou na lista de permissões e executáveis é um método comprovado de limitar a exposição ao risco.  

É muito mais fácil gerenciar uma lista de permissões quando ela é implementada desde o início. Uma lista de permissões fornece uma medida de controle e capacidade de gerenciamento à medida que você aprende quais arquivos e executáveis são necessários para que o aplicativo funcione corretamente. 

Uma lista de permissões não apenas reduz a superfície de ataque, mas também pode fornecer uma linha de base para anomalias e impedir os casos de uso dos cenários de "vizinho ruidosa" e de debates de contêiner. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Impor a segmentação de rede em contêineres em execução  

Para ajudar a proteger os contêineres em uma sub-rede contra riscos de segurança em outra sub-rede, mantenha a segmentação de rede (ou a segmentação do nano) ou a diferenciação entre contêineres em execução. Manter a segmentação de rede também pode ser necessário para usar contêineres em setores que são necessários para atender às obrigações de conformidade.  

Por exemplo, a ferramenta de parceiro [azul-piscina](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornece uma abordagem automatizada para a segmentação do nano. A água monitora as atividades de rede do contêiner em tempo de execução. Ele identifica todas as conexões de rede de entrada e saída de/para outros contêineres, serviços, endereços IP e a Internet pública. A segmentação do nano é criada automaticamente com base no tráfego monitorado. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorar a atividade do contêiner e o acesso do usuário 

Assim como acontece com qualquer ambiente de ti, você deve monitorar consistentemente a atividade e o acesso do usuário ao seu ecossistema de contêiner para identificar rapidamente qualquer atividade suspeita ou mal-intencionada. O Azure fornece soluções de monitoramento de contêiner, incluindo:

* [Azure monitor para contêineres](../azure-monitor/insights/container-insights-overview.md) para monitorar o desempenho de suas cargas de trabalho implantadas em ambientes kubernetes hospedados no AKs (serviço kubernetes do Azure). O Azure Monitor para contêineres oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. 

* A [solução de monitoramento de contêiner do Azure](../azure-monitor/insights/containers.md) ajuda você a exibir e gerenciar outros hosts de contêiner do Docker e do Windows em um único local. Por exemplo:

  * Exibir informações de auditoria detalhadas que mostram os comandos usados com contêineres. 
  * Solucione problemas de contêineres exibindo e pesquisando logs centralizados sem ter que exibir remotamente os hosts Docker ou Windows.  
  * Encontre contêineres que podem estar com ruídos e consumindo recursos em excesso em um host.
  * Exibir informações de desempenho, memória, armazenamento e CPU centralizadas para contêineres.  

  A solução dá suporte a orquestradores de contêineres, incluindo Docker Swarm, DC/so, kubernetes não gerenciado, Service Fabric e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorar atividade de recursos de contêiner 

Monitore a atividade de recursos, como arquivos, rede e outros recursos que seus contêineres acessam. Monitorar a atividade e o consumo de recursos é útil para o monitoramento de desempenho e como uma medida de segurança. 

O [Azure Monitor](../azure-monitor/overview.md) habilita o monitoramento principal dos serviços do Azure, permitindo a coleta de métricas, logs de atividades e logs de diagnóstico. Por exemplo, o log de atividades informará quando novos recursos forem criados ou modificados. 

Métricas disponíveis que fornecem estatísticas de desempenho para diferentes recursos e até mesmo o sistema operacional dentro de uma máquina virtual. É possível exibir esses dados com um dos gerenciadores no Portal do Azure e criar alertas com base nessas métricas. Azure Monitor fornece o pipeline de métricas mais rápido (5 minutos até 1 minuto), portanto, você deve usá-lo para alertas e notificações de tempo crítico. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Registrar o acesso de usuário administrativo de todos os contêineres para auditoria 

Mantenha uma trilha de auditoria precisa de acesso administrativo ao seu ecossistema de contêiner, registro de contêiner e imagens de contêiner. Esses logs podem ser necessários para fins de auditoria e serão úteis como evidências forenses após qualquer incidente de segurança. Você pode usar a [solução de monitoramento de contêiner do Azure](../azure-monitor/insights/containers.md) para obter essa finalidade. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como gerenciar vulnerabilidades de contêiner com soluções de [segurança](https://www.aquasec.com/solutions/azure-container-security/)de [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e água.

* Saiba mais sobre a [segurança de contêiner no Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).