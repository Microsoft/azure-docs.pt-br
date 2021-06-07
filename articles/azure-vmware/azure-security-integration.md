---
title: Proteger suas VMs de solução do Azure VMware com a integração da central de segurança do Azure
description: Proteja suas VMs de solução do Azure VMware com as ferramentas de segurança nativa do Azure no painel da central de segurança do Azure.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 7292ea4486a61f5b0cfd8f656d2763a3ce655e79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578255"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Proteger suas VMs de solução do Azure VMware com a integração da central de segurança do Azure

As ferramentas de segurança nativa do Azure fornecem proteção para um ambiente híbrido do Azure, solução Azure VMware e VMs (máquinas virtuais) locais. Este artigo mostra como configurar as ferramentas do Azure para segurança de ambiente híbrido. Você usará essas ferramentas para identificar e resolver várias ameaças.

## <a name="azure-native-services"></a>Serviços nativos do Azure

Aqui está um resumo rápido dos serviços nativos do Azure:

- **Espaço de trabalho log Analytics:** Log Analytics espaço de trabalho é um ambiente exclusivo para armazenar dados de log. Cada workspace tem seu próprio repositório de dados e configuração. As fontes de dados e as soluções são configuradas para armazenar seus dados em um espaço de trabalho específico.
- **Central de segurança do Azure:** A central de segurança do Azure é um sistema de gerenciamento de segurança de infraestrutura unificado. Ele reforça a segurança dos data centers e fornece proteção avançada contra ameaças em cargas de trabalho híbridas na nuvem ou localmente.
- **Sentinela do Azure:** O Azure Sentinel é uma solução de SIEM (gerenciamento de eventos de informações de segurança) nativa em nuvem. Ele fornece análise de segurança, detecção de alertas e resposta automatizada contra ameaças em um ambiente.

## <a name="topology"></a>Topologia

![Um diagrama que mostra a arquitetura da segurança integrada do Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

O agente de Log Analytics permite a coleta de dados de log do Azure, da solução Azure VMware e de VMs locais. Os dados de log são enviados para Azure Monitor logs e são armazenados em um espaço de trabalho Log Analytics. Você pode implantar o agente de Log Analytics usando servidores habilitados para Arc com [suporte de extensões de VM](../azure-arc/servers/manage-vm-extensions.md) para VMs novas e existentes. 

Depois que os logs são coletados pelo espaço de trabalho Log Analytics, você pode configurar o espaço de trabalho Log Analytics com a central de segurança do Azure. A central de segurança do Azure avaliará o status da vulnerabilidade das VMs da solução Azure VMware e emitirá um alerta para qualquer Vulnerabilidade crítica. Por exemplo, ele avalia patches de sistema operacional ausentes, configurações incorretas de segurança e [proteção de ponto de extremidade](../security-center/security-center-services.md).

Você pode configurar o espaço de trabalho Log Analytics com o Azure Sentinel para detecção de alertas, visibilidade de ameaças, busca e resposta a ameaças. No diagrama anterior, a central de segurança do Azure está conectada ao Azure Sentinel usando o conector da central de segurança do Azure. A central de segurança do Azure encaminhará a vulnerabilidade do ambiente para o Azure Sentinel para criar um incidente e mapear com outras ameaças. Você também pode criar a consulta de regras agendadas para detectar atividades indesejadas e convertê-las nos incidentes.

## <a name="benefits"></a>Benefícios

- Os serviços nativos do Azure podem ser usados para segurança de ambiente híbrido no Azure, solução Azure VMware e serviços locais.
- Usando um espaço de trabalho Log Analytics, você pode coletar os dados ou os logs para um único ponto e apresentar os mesmos dados a diferentes serviços nativos do Azure.
- A central de segurança do Azure oferece muitos recursos, incluindo:
    - Monitoramento de integridade do arquivo
    - Detecção de ataque de não-arquivo
    - Avaliação de patch do sistema operacional 
    - Avaliação de configurações incorretas de segurança
    - Avaliação da proteção de ponto de extremidade
- O Azure Sentinel permite que você:
    - Colete dados em escala de nuvem em todos os usuários, dispositivos, aplicativos e infraestrutura, tanto localmente quanto em várias nuvens.
    - Detectar ameaças anteriormente não detectadas.
    - Investigue as ameaças com inteligência artificial e procure atividades suspeitas em escala.
    - Responda a incidentes de forma rápida com orquestração interna e automação de tarefas comuns.

## <a name="create-a-log-analytics-workspace"></a>Criar um workspace do Log Analytics

Você precisará de um espaço de trabalho Log Analytics para coletar dados de várias fontes. Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../azure-monitor/logs/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Implantar a central de segurança e configurar VMs da solução Azure VMware

A central de segurança do Azure é uma ferramenta pré-configurada que não requer implantação. Na portal do Azure, pesquise a **central de segurança** e selecione-a.

### <a name="enable-azure-defender"></a>Habilitar o Azure Defender

O Azure defender estende a proteção avançada contra ameaças da central de segurança do Azure em suas cargas de trabalho híbridas, tanto localmente quanto na nuvem. Portanto, para proteger suas VMs de solução do Azure VMware, você precisará habilitar o Azure defender. 

1. Na central de segurança, selecione **Guia de introdução**.

2. Selecione a guia **Atualizar** e, em seguida, selecione sua assinatura ou espaço de trabalho. 

3. Selecione **Atualizar** para habilitar o Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Adicionar VMs da solução Azure VMware à central de segurança

1. Na portal do Azure, pesquise no **arco do Azure** e selecione-o.

2. Em recursos, selecione **servidores** e **+ Adicionar**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Uma captura de tela mostrando a página de servidores Arc do Azure para adicionar uma VM da solução Azure VMware ao Azure.":::

3. Selecione **gerar script**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Uma captura de tela da página de arco do Azure mostrando a opção para adicionar um servidor usando o script interativo."::: 
 
4. Na guia **pré-requisitos** , selecione **Avançar**.

5. Na guia **detalhes do recurso** , preencha os seguintes detalhes: 
    - Subscription
    - Resource group
    - Região 
    - Sistema operacional
    - Detalhes do servidor proxy
    
    Em seguida, selecione **próximo: marcas**.

6. Na guia **marcas** , selecione **Avançar**.

7. Na guia **baixar e executar script** , selecione **baixar**.

8. Especifique seu sistema operacional e execute o script em sua VM de solução do Azure VMware.

## <a name="view-recommendations-and-passed-assessments"></a>Exibir recomendações e avaliações aprovadas

1. Na central de segurança do Azure, selecione **inventário** no painel esquerdo.

2. Para tipo de recurso, selecione **servidores – arco do Azure**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Uma captura de tela da página de inventário da central de segurança do Azure mostrando servidores – arco do Azure selecionado em tipo de recurso.":::

3. Selecione o nome do recurso. Uma página é aberta mostrando os detalhes da integridade de segurança do recurso.

4. Na **lista recomendação**, selecione as guias **recomendações**, **avaliações passadas** e **avaliações não disponíveis** para exibir esses detalhes.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Uma captura de tela da central de segurança do Azure mostrando as recomendações e as avaliações de segurança.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Implantar um espaço de trabalho do Azure Sentinel

O Azure Sentinel é criado sobre um espaço de trabalho Log Analytics. Sua primeira etapa na integração do Azure Sentinel é selecionar o Log Analytics espaço de trabalho que você deseja usar para essa finalidade.

1. Na portal do Azure, pesquise **Azure Sentinel** e selecione-o.

2. Na página espaços de trabalho do Azure Sentinel, selecione **+ Adicionar**.

3. Selecione o espaço de trabalho Log Analytics e selecione **Adicionar**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Habilitar o coletor de dados para eventos de segurança em VMs de solução do Azure VMware

Agora você está pronto para conectar o Azure Sentinel com suas fontes de dados, nesse caso, eventos de segurança.

1. Na página espaços de trabalho do Azure Sentinel, selecione o espaço de trabalho configurado.

2. Em configuração, selecione **conectores de dados**.

3. Na coluna nome do conector, selecione **eventos de segurança** na lista e, em seguida, selecione a **página abrir conector**.

4. Na página conector, selecione os eventos que você deseja transmitir e, em seguida, selecione **aplicar alterações**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Captura de tela da página de eventos de segurança no Azure Sentinel, onde você pode selecionar quais eventos transmitir.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Conectar o Azure Sentinel à central de segurança do Azure  

1. Na página do espaço de trabalho do Azure Sentinel, selecione o espaço de trabalho configurado.

2. Em configuração, selecione **conectores de dados**.

3. Selecione **central de segurança do Azure** na lista e, em seguida, selecione **abrir página do conector**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Captura de tela da página conectores de dados no Azure Sentinel mostrando a seleção para conectar-se à central de segurança do Azure com o Azure Sentinel.":::

4. Selecione **conectar** para conectar-se à central de segurança do Azure com o Azure Sentinel.

5. Habilite **criar incidente** para gerar um incidente para a central de segurança do Azure.

## <a name="create-rules-to-identify-security-threats"></a>Criar regras para identificar ameaças de segurança

Depois de conectar fontes de dados ao Azure Sentinel, você pode criar regras para gerar alertas para ameaças detectadas. No exemplo a seguir, criaremos uma regra para as tentativas de entrar no Windows Server com a senha incorreta.

1. Na página Visão geral do Sentinela do Azure, em configurações, selecione **análise**.

2. Em configurações, selecione **análise**.

3. Selecione **+ criar** e, na lista suspensa, selecione **regra de consulta agendada**.

4. Na guia **geral** , insira as informações necessárias.

    - Nome
    - Descrição
    - Táticas
    - Severidade
    - Status

    Selecione **Avançar: definir >de lógica de regra**.

5. Na guia **definir lógica de regra** , insira as informações necessárias.

    - Consulta de regra (aqui está mostrando nossa consulta de exemplo)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Mapear entidades
    - Agendamento de consulta
    - Limite de alerta
    - Agrupamento de eventos
    - Supressão

    Selecione **Avançar**.

6. Na guia **configurações de incidente** , habilite **criar incidentes de alertas disparados por esta regra de análise** e selecione **avançar: resposta automatizada >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Captura de tela do assistente de regra analítica para criar uma nova regra no Azure Sentinel. Mostra criar incidentes de alertas disparados por esta regra como habilitado.":::

7. Selecione **Avançar: examinar >**.

8. Na guia **revisar e criar** , examine as informações e selecione **criar**.

Após a terceira tentativa com falha de entrar no Windows Server, a regra criada dispara um incidente para cada tentativa malsucedida.

## <a name="view-alerts"></a>Exibir alertas

Você pode exibir incidentes gerados com o Azure Sentinel. Você também pode atribuir incidentes e fechá-los depois que eles forem resolvidos, tudo no Azure Sentinel.

1. Vá para a página Visão geral do Sentinela do Azure.

2. Em gerenciamento de ameaças, selecione **incidentes**.

3. Selecione um incidente. Em seguida, você pode atribuir o incidente a uma equipe para resolução.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Captura de tela da página incidentes do Azure Sentinel com o incidente selecionado e a opção para atribuir o incidente para resolução.":::

    Depois de resolver o problema, você pode fechá-lo.

## <a name="hunt-security-threats-with-queries"></a>Ameaças de busca de segurança com consultas

Você pode criar consultas ou usar a consulta predefinida disponível no Azure Sentinel para identificar ameaças em seu ambiente. As etapas a seguir executam uma consulta predefinida.

1. Vá para a página Visão geral do Sentinela do Azure.

2. Em gerenciamento de ameaças, selecione **busca**. Uma lista de consultas predefinidas é exibida.

3. Selecione uma consulta e, em seguida, selecione **Executar consulta**.

4. Selecione **exibir resultados** para verificar os resultados.

### <a name="create-a-new-query"></a>Criar uma consulta

1.  Em gerenciamento de ameaças, selecione **busca** e **+ nova consulta**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Captura de tela da página de busca do Azure Sentinel com + nova consulta realçada.":::

2. Preencha as informações a seguir para criar uma consulta personalizada.

    - Nome
    - Descrição
    - Consulta personalizada
    - Inserir mapeamento
    - Táticas
    
3. Selecione **Criar**. Em seguida, você pode selecionar a consulta criada, **executar a consulta** e **exibir os resultados**.

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou como proteger suas VMs de solução do Azure VMware, talvez queira saber mais sobre:

- Usando o [painel do Azure defender](../security-center/azure-defender-dashboard.md).
- [Detecção avançada de ataques de multiestágio no Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md).
- [Gerenciamento do ciclo de vida de VMs de solução do Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).
