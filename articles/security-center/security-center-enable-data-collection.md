---
title: Implantar agentes automaticamente para a Central de Segurança do Azure | Microsoft Docs
description: Este artigo descreverá de que modo configurar um provisionamento automático do agente do Log Analytics, bem como outros agentes e extensões usados pela Central de Segurança do Azure
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 17f3440df4fa88995f2148680aba926207a0e46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561255"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Configurar um provisionamento automático para agentes e extensões da Central de Segurança do Azure

A Central de Segurança do Azure coleta dados de seus recursos usando um agente ou extensões relevantes para eles, bem como o tipo de coleta de dados habilitado. Use os procedimentos a seguir para garantir que seus recursos tenham as extensões e os agentes necessários usados pela Central de Segurança.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Caso não tenha uma assinatura, será possível inscrever-se para criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="availability"></a>Disponibilidade

| Aspecto                  | Detalhes                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado da versão:          | **Recurso**: O provisionamento automático está em GA (disponibilidade geral)<br>**Agente e extensões**: O agente do Log Analytics para VMs do Azure está em GA, o Microsoft Dependency Agent está em versão prévia e o complemento de Política para Kubernetes está em GA                |
| Preço:                | Gratuita                                                                                                                                                                                                                         |
| Destinos compatíveis: | ![Sim](./media/icons/yes-icon.png) Computadores do Azure<br>![Não](./media/icons/no-icon.png) Computadores do Azure Arc<br>![Não](./media/icons/no-icon.png) Nós do Kubernetes<br>![Não](./media/icons/no-icon.png) Conjuntos de Dimensionamento de Máquinas Virtuais |
| Nuvens:                 | ![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) US Gov, governo da China e outros governos                                                                                                      |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>De que modo a Central de Segurança coleta dados?

A Central de Segurança coleta dados das VMs (máquinas virtuais) do Azure, dos conjuntos de dimensionamento de máquinas virtuais, dos contêineres de IaaS e de computadores não Azure (incluindo os locais) para monitorar as vulnerabilidades e as ameaças de segurança. 

É necessário coletar dados para dar visibilidade às atualizações ignoradas, às configurações de segurança de SO incorretas, ao status de proteção do ponto de extremidade, à integridade e à proteção contra ameaças. A coleta de dados será necessária somente para recursos de computação, como VMs, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure. 

É possível se beneficiar da Central de Segurança do Azure mesmo se você não provisionar agentes. No entanto, você terá uma segurança limitada. Além disso, as funcionalidades listadas acima não terão suporte.  

Os dados são coletados usando:

- O **agente do Log Analytics**, que lê uma variedade de configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace visando a análise. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP e usuário conectado.
- As **extensões de segurança**, como o [Complemento do Azure Policy para Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), que também pode fornecer dados à Central de Segurança em relação aos tipos de recursos especializados.

> [!TIP]
> Conforme a Central de Segurança se expandiu, os tipos de recursos que podem ser monitorados também aumentaram. O número de extensões também aumentou. O provisionamento automático foi expandido para dar suporte a tipos de recursos adicionais, aproveitando as funcionalidades do Azure Policy.

## <a name="why-use-auto-provisioning"></a>Por que usar o provisionamento automático?
Qualquer uma das extensões ou agentes descritos nesta página *pode* ser instalado manualmente (confira [Instalação manual do agente do Log Analytics](#manual-agent)). No entanto, o **provisionamento automático** reduz a sobrecarga de gerenciamento instalando todas as extensões e todos os agentes necessários em computadores novos e existentes a fim de garantir uma cobertura de segurança mais rápida para todos os recursos compatíveis. 

Recomendamos habilitar o provisionamento automático, mas ele está desabilitado por padrão.

## <a name="how-does-auto-provisioning-work"></a>Como funciona o provisionamento automático?
As configurações de provisionamento automático da Central de Segurança têm uma alternância para cada tipo de extensão compatível. Ao habilitar o provisionamento automático de uma extensão, atribua uma política apropriada, como **Implantar se não existir**. Essa política garantirá que a extensão seja provisionada em todos os recursos existentes e futuros desse tipo.

> [!TIP]
> Saiba mais sobre os efeitos do Azure Policy, incluindo a política Implantar se não existir em [Noções básicas sobre os efeitos do Azure Policy](../governance/policy/concepts/effects.md).


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>Habilitar o provisionamento automático do agente do Log Analytics e das extensões <a name="auto-provision-mma"></a>

Quando o provisionamento automático estiver habilitado para o agente do Log Analytics, a Central de Segurança implantará o agente em todas as VMs do Azure compatíveis, bem como naquelas que forem criadas. Para ver a lista das plataformas compatíveis, consulte [Plataformas compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md).

Para habilitar o provisionamento automático do agente do Log Analytics:

1. No menu da Central de Segurança, selecione **Preço e configurações**.
1. Selecione a assinatura relevante.
1. Na página **Provisionamento automático**, defina o status do agente do Log Analytics como **Ativado**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Habilitar o provisionamento automático do agente do Log Analytics":::

1. No painel Opções de configuração, defina o workspace a ser usado.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Opções de configuração para o provisionamento automático dos agentes do Log Analytics para VMs" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Conectar VMs do Azure aos workspaces padrão criados pela Central de Segurança**: a Central de Segurança cria um grupo de recursos e o workspace padrão na mesma geolocalização e conecta o agente a esse workspace. Se uma assinatura contiver VMs de várias geolocalizações, a Central de Segurança criará vários workspaces para garantir a conformidade com os requisitos de privacidade de dados.

        A convenção de nomenclatura para o grupo de recursos e o workspace é: 
        - Workspace: DefaultWorkspace-[ID da assinatura]-[localização geográfica] 
        - Grupo de recursos: DefaultResourceGroup- [geo] 

        A Central de segurança habilita automaticamente uma solução da Central de Segurança no workspace por tipo de preço definido para a assinatura. 

        > [!TIP]
        > Caso tenha dúvidas sobre os workspaces padrão, confira:
        >
        > - [Sou cobrado pelos logs do Azure Monitor nos workspaces criados pela Central de Segurança?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Onde o workspace do Log Analytics padrão é criado?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Posso excluir os workspaces padrão criados pela Central de Segurança?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Conectar VMs do Azure a outro workspace**: na lista suspensa, selecione o workspace para armazenar os dados coletados. A lista suspensa inclui todos os workspaces em todas as suas assinaturas. Você pode usar essa opção para coletar dados de máquinas virtuais em execução em diferentes assinaturas e armazená-las no workspace selecionado.  

        Se você já tiver um workspace do Log Analytics, o ideal será usar o mesmo workspace (exige permissões de leitura e gravação no workspace). Essa opção será útil se você estiver utilizando um workspace centralizado na sua organização e quiser usá-lo para coleta de dados de segurança. Saiba mais em [Gerenciar o acesso a dados de log e workspaces no Azure Monitor](../azure-monitor/logs/manage-access.md).

        Caso o workspace selecionado já tenha uma solução "Security" ou "SecurityCenterFree" habilitada, o preço será definido de modo automático. Caso contrário, instale uma solução da Central de Segurança no workspace:

        1. No menu da Central de Segurança, abra **Preço e configurações**.
        1. Selecione o workspace ao qual você conectará os agentes.
        1. Selecione **Azure Defender ativado** ou **Azure Defender desativado**.

1. Na configuração de **eventos de segurança do Windows**, selecione o volume de dados de eventos brutos a ser armazenado:
    - **Nenhum** – Desabilitar armazenamento de eventos de segurança. Essa é a configuração padrão.
    - **Mínimo**: um pequeno conjunto de eventos para quando você desejar minimizar o volume dos eventos.
    - **Comum**: um conjunto de eventos que satisfaz a maioria dos clientes e fornece uma trilha de auditoria completa.
    - **Todos os eventos** – Para clientes que querem garantir que todos os eventos sejam armazenados.

    > [!TIP]
    > Para definir essas opções no nível do workspace, confira [Como definir a opção de evento de segurança no nível do workspace](#setting-the-security-event-option-at-the-workspace-level).
    > 
    > Para obter mais informações sobre essas opções, confira [Opções de eventos de segurança do Windows para o agente do Log Analytics](#data-collection-tier).

1. Selecione **Aplicar** no painel de configuração.

1. Para habilitar o provisionamento automático de uma extensão que não seja o agente do Log Analytics: 

    1. Caso esteja habilitando um provisionamento automático para o Microsoft Dependency Agent, verifique se o agente do Log Analytics está configurado para executar uma implantação automática.
    1. Alterne o status para **Ativado** da extensão relevante.

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Alternância para habilitar o provisionamento automático para o complemento de política do K8s":::

    1. Selecione **Salvar**. A política do Azure é atribuída, e uma tarefa de correção é criada.

        |Extensão  |Política  |
        |---------|---------|
        |Complemento do Policy para Kubernetes|[Implantar Complemento do Azure Policy para clusters do Serviço de Kubernetes do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft Dependency Agent (versão prévia) (VMs do Windows)|[Implantar o Dependency Agent nas máquinas virtuais do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft Dependency Agent (versão prévia) (VMs do Linux)|[Implantar o Dependency Agent nas máquinas virtuais do Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |||

1. Selecione **Salvar**. Se for necessário provisionar um workspace, a instalação do agente poderá levar até 25 minutos.

1. Será exibido um aviso perguntando se você deseja reconfigurar as VMs monitoradas que anteriormente estavam conectadas a um workspace padrão:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Examinar as opções para reconfigurar as VMs monitoradas":::

    - **Não**: as novas configurações de workspace serão aplicadas somente às VMs recém-descobertas que não têm o agente do Log Analytics instalado.
    - **Sim**: as novas configurações de workspace serão aplicadas a todas as VMs e todas as VMs atualmente conectadas a um workspace criado pela Central de Segurança serão reconectadas ao novo workspace de destino.

   > [!NOTE]
   > Se você selecionar **Sim**, não exclua os workspaces criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo workspace de destino. Essa operação falhará se um workspace for excluído muito cedo.


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Opções de eventos de segurança do Windows para o agente do Log Analytics <a name="data-collection-tier"></a> 

A seleção de uma camada de coleta de dados na Central de Segurança do Azure afeta apenas o *armazenamento* de eventos de segurança no workspace do Log Analytics. O agente do Log Analytics ainda coletará e analisará os eventos de segurança necessários para a proteção contra ameaças da Central de Segurança, independentemente do nível de eventos de segurança que você escolher armazenar no workspace. A escolha de armazenamento dos eventos de segurança permite a investigação, a pesquisa e a auditoria desses eventos no workspace.

### <a name="requirements"></a>Requisitos 
O Azure Defender é necessário para armazenar dados de eventos de segurança do Windows. [Saiba mais sobre o Azure Defender](azure-defender.md).

O armazenamento de dados no Log Analytics pode gerar cobranças adicionais. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Informações para usuários do Azure Sentinel 
Usuários do Azure Sentinel: observem que a coleta de eventos de segurança dentro do contexto de um workspace pode ser configurada da Central de Segurança do Azure ou do Azure Sentinel, mas não de ambos. Se você pretende adicionar o Azure Sentinel a um workspace que já está recebendo alertas da Central de Segurança do Azure e está definido para coletar eventos de segurança, há duas opções:
- Deixe a coleção de Eventos de Segurança na Central de Segurança do Azure no estado em que se encontra. Você poderá consultar e analisar esses eventos no Azure Sentinel, bem como no Azure Defender. No entanto, não será possível monitorar o status de conectividade do conector nem alterar a configuração dele no Azure Sentinel. Se isso for importante para você, considere a segunda opção.
- Desabilite a coleta de eventos de segurança na Central de Segurança do Azure (definindo **Eventos de segurança do Windows** como **Nenhum** na configuração do agente do Log Analytics). Em seguida, adicione o conector de eventos de segurança no Azure Sentinel. Assim como aconteceu com a primeira opção, você poderá consultar e analisar eventos no Azure Sentinel e no Azure Defender/ASC, mas agora poderá monitorar o status de conectividade do conector ou alterar a configuração dele no (e apenas no) Azure Sentinel.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Quais tipos de eventos são armazenados nas opções "Comum" e "Mínimo"?
Esses conjuntos foram projetados para lidar com cenários típicos. Avalie qual atende às suas necessidades antes de implementá-los.

Para determinar os eventos das opções **Comum** e **Mínimo**, trabalhamos com os clientes e os padrões do setor para aprender mais sobre a frequência não filtrada de cada evento e o respectivo uso. Neste processo, usamos as seguintes diretrizes:

- **Mínimo** – Certificar-se de que esse conjunto aborde apenas os eventos que possam indicar uma violação com êxito e eventos importantes que tenham um volume muito baixo. Por exemplo, este conjunto conterá logons de usuário bem-sucedidos e malsucedidos (IDs de evento 4624 e 4625), mas não conterá o logoff importante para a auditoria, mas não significativo para a detecção e que tenha um volume relativamente alto. A maior parte do volume de dados desse conjunto é dos eventos de logon e do evento de criação de processo (ID de evento 4688).
- **Comum** – fornecer uma trilha de auditoria de usuário completa neste conjunto. Por exemplo, este conjunto contém tanto os logons quanto os logoffs de usuário (ID de evento 4634). Incluímos ações de auditoria, como alterações de grupo de segurança, operações Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações do setor.

Os eventos com um volume muito baixo foram incluídos no conjunto comum, pois a principal motivação para escolhê-los dentre todos os eventos é reduzir o volume em vez de filtrar eventos específicos.

Aqui está um detalhamento completo das IDs de eventos de Segurança e do AppLocker para cada conjunto:

| Camada de dados | Indicadores de eventos coletados |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comum | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se você está usando o GPO (Objeto de Política de Grupo), é recomendável habilitar o Evento de Criação de Processo 4688 das políticas de auditoria, bem como o campo *CommandLine* dentro do evento 4688. Para obter mais informações sobre o Processo de Criação do Evento 4688, consulte as [Perguntas Frequentes](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) da Central de Segurança. Para obter mais informações sobre essas políticas de auditoria, consulte [Recomendações de Política de Auditoria](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Para habilitar a coleta de dados para [Controles de Aplicativo Adaptáveis](security-center-adaptive-application.md), a Central de Segurança configura uma política do AppLocker local no modo de Auditoria para permitir todos os aplicativos. Isso fará com que o AppLocker gere eventos que são coletados e aproveitados pela Central de Segurança. É importante observar que essa política não será configurada em computadores nos quais já houver uma política do AppLocker configurada. 
> - Para coletar a Plataforma para Filtros do Windows [ID de evento 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), será preciso habilitar a [Conexão da Plataforma para Filtros de Auditoria](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Como configurar a opção de evento de segurança no nível do workspace

Você pode definir o nível dos dados de evento de segurança a serem armazenados no nível do workspace.

1. No menu da Central de Segurança no portal do Azure, selecione **Preço e configurações**.
1. Selecione o workspace relevante. Os únicos eventos de coleta de dados para um workspace são os eventos de segurança do Windows descritos nesta página.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Como definir os dados de evento de segurança a serem armazenados em um workspace":::

1. Selecione o volume de dados de eventos brutos a serem armazenados e escolha **Salvar**.

## <a name="manual-agent-provisioning"></a>Provisionamento manual de agente <a name="manual-agent"></a>
 
Para instalar manualmente o agente do Log Analytics:

1. Desabilite o provisionamento automático.

1. Opcionalmente, crie um workspace.

1. Habilite o Azure Defender no workspace no qual você está instalando o agente do Log Analytics:

    1. No menu da Central de Segurança, selecione **Preço e configurações**.

    1. Defina o workspace no qual você está instalando o agente. Verifique se o workspace está na mesma assinatura usada na Central de Segurança e se você tem permissões de leitura/gravação no workspace.

    1. Escolha **Azure Defender ativado** e **Salvar**.

       >[!NOTE]
       >Se o workspace já tiver uma solução de **Segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 

1. Para implantar agentes em novas VMs usando um modelo do Resource Manager, instale o agente do Log Analytics:

   - [Instalar o agente do Log Analytics para Windows](../virtual-machines/extensions/oms-windows.md)
   - [Instalar o agente do Log Analytics para Linux](../virtual-machines/extensions/oms-linux.md)

1. Para implantar agentes nas VMs existentes, siga as instruções descritas em [Coletar dados sobre as Máquinas Virtuais do Azure](../azure-monitor/vm/quick-collect-azurevm.md) (a seção **Coletar dados de evento e desempenho** é opcional).

1. Para usar o PowerShell para implantar os agentes, use as instruções da documentação de máquinas virtuais:

    - [Para computadores Windows](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Para computadores Linux](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> Para obter instruções sobre como integrar a Central de Segurança usando o PowerShell, consulte [Automatizar a integração da Central de Segurança do Azure usando o PowerShell](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Provisionamento automático em casos de uma instalação de agente preexistente <a name="preexisting"></a> 

Os casos de uso a seguir especificam como o provisionamento automático funciona quando já existe um agente ou extensão instalados. 

- **O agente do Log Analytics está instalado no computador, mas não como uma extensão (agente direto)** – se o agente do Log Analytics estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de Segurança instalará a extensão do agente do Log Analytics e poderá atualizá-lo para a última versão.
O agente instalado continuará a gerar relatórios para os workspaces já configurados e também para o workspace configurado na Central de Segurança (há compatibilidade com a hospedagem múltipla em computadores Windows).
Caso o workspace configurado seja um workspace do usuário (em vez de um workspace padrão da Central de Segurança), será necessário instalar a solução "Security" ou "SecurityCenterFree" nele para que a Central de Segurança comece a processar eventos de VMs e computadores que geram relatórios para esse workspace.

    Em computadores Linux, a hospedagem múltipla do Agente não é compatível. Portanto, se uma instalação de agente existente for detectada, o provisionamento automático não ocorrerá e a configuração do computador não será alterada.

    Nos computadores existentes com as assinaturas integradas à Central de Segurança antes de 17 de março de 2019, quando um agente existente for detectado, a extensão do agente do Log Analytics não será instalada e o computador não será afetado. No caso desses computadores, confira a recomendação "Resolver problemas de integridade do agente de monitoramento nos seus computadores" para solucionar problemas de instalação de agente.
  
- **O agente do System Center Operations Manager está instalado no computador**: a Central de Segurança instalará a extensão do agente do Log Analytics lado a lado com o Operations Manager existente. O agente existente do Operations Manager continuará a gerar relatórios para o servidor do Operations Manager normalmente. O agente do Operations Manager e o agente do Log Analytics compartilham bibliotecas comuns em tempo de execução, que serão atualizadas para a versão mais recente durante esse processo. Se o agente do Operations Manager da versão 2012 estiver instalado, **não** habilite o provisionamento automático.

- **Já há uma extensão de VM existente**:
    - Quando o Agente de Monitoramento está instalado como uma extensão, a configuração de extensão permite gerar relatórios somente para um workspace. A Central de Segurança não substitui as conexões existentes nos workspaces do usuário. A Central de Segurança armazenará dados de segurança da VM no workspace já conectado, caso a solução "Security" ou "SecurityCenterFree" tenha sido instalada nele. A Central de Segurança pode atualizar a versão da extensão para a mais recente nesse processo.
    - Para ver para qual workspace a extensão existente está enviando dados, execute o teste para [Validar a conectividade com a Central de Segurança do Azure](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). Outra opção é abrir workspaces do Log Analytics, selecionar um workspace, selecionar a VM e examinar a conexão do agente do Log Analytics.
    - Se você tiver um ambiente em que o agente do Log Analytics está instalado nas estações de trabalho do cliente e envia relatórios para um workspace do Log Analytics existente, veja a lista de [sistemas operacionais compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md) para verificar se seu sistema operacional é compatível. Para saber mais, confira [Clientes existentes de análise de logs](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Desabilitar o provisionamento automático <a name="offprovisioning"></a>

Quando você desabilita o provisionamento automático, os agentes não são provisionados em novas VMs.

Para desativar o provisionamento automático de um agente:

1. No menu da Central de Segurança no portal, selecione **Preço e configurações**.
1. Selecione a assinatura relevante.
1. Selecione **Provisionamento automático**.
1. Alterne o status para **Desativado** do agente relevante.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Alternâncias para desabilitar o provisionamento automático por tipo de agente":::

1. Selecione **Salvar**. Quando o provisionamento automático está desabilitado, a seção de configuração do workspace padrão não é exibida:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Quando o provisionamento automático está desabilitado, a célula de configuração fica vazia":::


> [!NOTE]
>  Desabilitar o provisionamento automático não remove o agente do Log Analytics das VMs do Azure em que ele foi provisionado. Para obter informações sobre como remover a extensão do OMS, consulte [Como fazer para remover extensões OMS instaladas pela Central de Segurança](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Solução de problemas

-   Para identificar problemas de instalação do provisionamento automático, confira [Problemas de integridade do agente de monitoramento](security-center-troubleshooting-guide.md#mon-agent).
-  Para identificar os requisitos de rede do agente de monitoramento, veja [Solução de problemas dos requisitos de rede do agente de monitoramento](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas de integração manual, consulte [Como solucionar problemas de integração do Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).



## <a name="next-steps"></a>Próximas etapas
Esta página explicou de que modo habilitar um provisionamento automático para o agente do Log Analytics e outras extensões da Central de Segurança. Ela também descreveu de que modo definir um workspace do Log Analytics no qual você armazenará os dados coletados. As duas operações são necessárias para habilitar a coleta de dados. Armazenar dados no Log Analytics poderá incorrer em encargos adicionais para o armazenamento de dados, não importa se você usa um workspace novo ou existente. Para obter detalhes de preço na sua moeda preferencial e de acordo com a sua região, confira [Preço da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).