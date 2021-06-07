---
title: Azure Defender para servidores – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para servidores.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 36c32c4eefdaa1c7604f2b0f19e98cf6a6d4310d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096523"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Introdução ao Azure Defender para servidores

O Azure Defender para servidores adiciona proteções avançadas e a detecção contra ameaças aos seus computadores Windows e Linux.

No Windows, o Azure Defender integra-se aos serviços do Azure para monitorar e proteger seus computadores baseados no Windows. A Central de Segurança apresenta os alertas e as sugestões de correção de todos esses serviços em um formato fácil de usar.

No Linux, o Azure Defender coleta os registros de auditoria dos computadores Linux usando o **auditd**, uma das estruturas de auditoria mais comuns do Linux. auditd reside no kernel principal. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Quais são os benefícios do Azure Defender para servidores?

As funcionalidades de detecção e proteção contra ameaças fornecidos no Azure Defender para servidores incluem:

- **Licença integrada para o Microsoft Defender para ponto de extremidade (apenas Windows)** – O Azure Defender para servidores inclui o [Microsoft Defender para Ponto de Extremidade](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Juntas, elas fornecem recursos abrangentes de Detecção e resposta de ponto de extremidade (EDR). Para obter mais informações, confira [Proteger os seus pontos de extremidade](security-center-wdatp.md).

    Quando o Defender para Ponto de Extremidade detecta uma ameaça, ele dispara um alerta. O alerta é mostrado na Central de Segurança. Na Central de Segurança, você pode fazer a dinamização para o console do Defender para Ponto de Extremidade e realizar uma investigação detalhada para descobrir o escopo do ataque. Saiba mais sobre o Microsoft Defender para Ponto de Extremidade.

    > [!IMPORTANT]
    > O sensor do **Microsoft Defender para Ponto de Extremidade** é habilitado automaticamente em servidores Windows que usam a Central de Segurança.

- **Verificação de avaliação de vulnerabilidades para VMs**: o verificador de vulnerabilidades incluído na Central de Segurança do Azure é fornecido pela Qualys. 

    O verificador da Qualys é uma das ferramentas líder para a identificação em tempo real de vulnerabilidades nas suas máquinas virtuais híbridas e do Azure. Você não precisa de uma licença do Qualys nem de uma conta do Qualys: tudo é tratado diretamente na Central de Segurança. Para obter mais informações, confira [Solução de avaliação de vulnerabilidade integrada do Azure Defender para computadores híbridos e do Azure](deploy-vulnerability-assessment-vm.md).

- **Acesso JIT (Just-In-Time) à VM (máquina virtual)** : atores de ameaça buscam ativamente computadores acessíveis com portas de gerenciamento abertas, como RDP ou SSH. Todas as suas máquinas virtuais são alvos potenciais de um ataque. Quando uma VM é comprometida com êxito, ela é usada como o ponto de entrada para atacar outros recursos no seu ambiente.

    Ao habilitar o Azure Defender para servidores, você pode usar o acesso just-in-time à VM para bloquear o tráfego de entrada nas suas VMs, reduzindo a exposição a ataques e fornecendo fácil acesso para se conectar às VMs quando necessário. Para obter mais informações, confira [Noções básicas sobre o acesso à VM do JIT](just-in-time-explained.md).

- **FIM (Monitoramento de Integridade do Arquivo)** : também conhecido como monitoramento de alterações, examina os arquivos e os Registros do sistema operacional, de programas de software de aplicativos e outros em busca de alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do arquivo é diferente da última verificação do arquivo. Você pode aproveitar essa comparação para determinar se foram feitas modificações válidas ou suspeitas em seus arquivos.

    Ao habilitar o Azure Defender para servidores, você pode usar o FIM para validar a integridade de arquivos do Windows, Registros do Windows e arquivos do Linux. Para obter mais informações, confira [Monitoramento de integridade de arquivo na Central de Segurança do Azure](security-center-file-integrity-monitoring.md).

- **AAC (controles de aplicativo adaptáveis)** : os controles de aplicativos adaptáveis são uma solução inteligente e automatizada para definir as listas de permissões de aplicativos seguros conhecidos para seus computadores.

    Quando você habilitar e configurar os controles de aplicativo adaptáveis, obterá alertas de segurança se qualquer aplicativo for executado além daqueles que você definiu como seguros. Para obter mais informações, confira [Usar controles de aplicativos adaptáveis para reduzir as superfícies de ataque dos computadores](security-center-adaptive-application.md).

- **ANH (proteção de rede adaptável)** : a aplicação de NSGs (grupos de segurança de rede) para filtrar o tráfego nos recursos aprimora sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui pelo NSG seja um subconjunto das regras NSG definidas. Nesses casos, é possível aprimorar ainda mais a postura de segurança protegendo as regras NSG, com base nos padrões reais de tráfego.

    A proteção de rede adaptável fornece recomendações para proteger ainda mais as regras NSG. Ela usa um algoritmo de machine learning que inclui o tráfego real, a configuração confiável conhecida, a inteligência contra ameaças e outros indicadores de comprometimento e, em seguida, fornece recomendações para permitir o tráfego somente de tuplas de IP/porta específicas. Para obter mais informações, confira [Melhorar a sua postura de segurança de rede com a proteção de rede adaptável](security-center-adaptive-network-hardening.md).

- **Proteção de host do Docker**: a Central de Segurança do Azure identifica contêineres não gerenciados hospedados em VMs IaaS do Linux ou em outros computadores Linux que executam contêineres do Docker. A Central de Segurança avalia continuamente as configurações desses contêineres. Em seguida, ela as compara com o benchmark do CIS (Center for Internet Security) do Docker. A Central de Segurança inclui todo o conjunto de regras do benchmark do CIS do Docker e alerta você se os contêineres não atendem a nenhum dos controles. Para obter mais informações, confira [Proteger os hosts do Docker](harden-docker-hosts.md).

- **Detecção de ataque sem arquivos (somente Windows)** : os ataques sem arquivos injetam conteúdo mal-intencionado na memória para evitar a detecção por técnicas de verificação baseadas em disco. Depois, o conteúdo do invasor persiste na memória de processos comprometidos e executa uma ampla gama de atividades mal-intencionadas.

  Com a detecção de ataques sem arquivos, as técnicas forenses de memória automatizada identificam os toolkits, as técnicas e os comportamentos de ataques sem arquivos. Essa solução verifica periodicamente seu computador em runtime e extrai insights diretamente da memória de processos. Os insights específicos incluem a identificação de: 

  - Kits de ferramentas conhecidos e programas de software de mineração de criptografia 

  - Shellcode, que é uma pequena parte do código geralmente usada como conteúdo na exploração de uma vulnerabilidade de software.

  - Executável mal-intencionado injetado na memória do processo

  A detecção de ataque sem arquivos gera alertas de segurança detalhados contendo as descrições com metadados de processo adicionais, como a atividade de rede. Isso acelera a triagem de alertas, a correlação e o tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos e proporcionam uma maior cobertura de detecção.

  Para obter detalhes sobre os alertas de detecção de ataques sem arquivos, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-windows).

- **Alertas do auditd do Linux e integração do agente do Log Analytics (somente Linux)** : o sistema auditd consiste em um subsistema no nível de kernel, que é responsável por monitorar as chamadas do sistema. Ele as filtra de acordo com um conjunto de regras especificado e grava mensagens para eles em um soquete. A Central de Segurança integra as funcionalidades do pacote auditd no agente do Log Analytics. Essa integração habilita a coleta de eventos auditd em todas as distribuições Linux com suporte, sem nenhum pré-requisito.

    Os registros auditd são coletados, aprimorados e agregados em eventos usando o agente do Log Analytics para o agente do Linux. A Central de Segurança adiciona continuamente novos recursos de análise que usam sinais do Linux para detectar comportamentos mal-intencionados em máquinas Linux locais e na nuvem. Semelhante aos recursos do Windows, esses recursos de análise abrangem processos suspeitos, tentativas de entrada duvidosa, carregamento de módulos kernel e outras atividades. Essas atividades podem indicar que um computador está sob ataque ou foi violado.  

    Para obter uma lista dos alertas do Linux, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Como simular alertas

Você pode simular alertas baixando um dos seguintes guias estratégicos:

- Para Windows: [Guia estratégico da Central de Segurança do Azure: Alertas de segurança](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Para Linux: [Guia estratégico da Central de Segurança do Azure: Detecções do Linux](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu mais sobre o Azure Defender para servidores. 

Para obter material relacionado, consulte os seguintes artigos: 

- Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Habilitar o Azure Defender](enable-azure-defender.md)