---
title: Conectar o Operations Manager para o Azure Monitor | Microsoft Docs
description: Para manter seu investimento existente no System Center Operations Manager e usar funcionalidades estendidas com o Log Analytics, você pode integrar o Operations Manager ao seu workspace.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 336a9d9c76114920e92de2000152e500f7dce46f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445313"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Conectar o Operations Manager para o Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para manter seu investimento existente no [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) e usar funcionalidades estendidas com o Azure Monitor, você pode integrar o Operations Manager ao seu espaço de trabalho do Log Analytics. Isso permite que aproveitar as oportunidades de logs no Azure Monitor enquanto continua a usar o Operations Manager para:

* Monitorar a integridade dos seus serviços de TI com o Operations Manager
* Manter a integração com suas soluções de ITSM com suporte ao gerenciamento de incidentes e problemas
* Gerenciar o ciclo de vida dos agentes implantados em máquinas virtuais locais e em IaaS em nuvem pública que podem ser monitorados com o Operations Manager

A integração com o System Center Operations Manager agrega valor à sua estratégia de operações de serviço, usando a velocidade e eficiência do Azure Monitor ao coletar, armazenar e analisar dados de log do Operations Manager. Azure monitorar log de consulta ajuda correlacionar e trabalho identificar as falhas dos problemas e identificar recorrências para dar suporte ao seu processo de gerenciamento de problemas existente. A flexibilidade do mecanismo de consulta para examinar o desempenho, eventos e alerta de dados, com painéis avançados e recursos de relatórios para expor esses dados de maneiras significativas demonstra a potência do Azure Monitor traz para complementar o Operations Manager.

Agentes que se reportam ao grupo de gerenciamento do Operations Manager coletam dados de seus servidores com base nas [fontes de dados do Log Analytics](agent-data-sources.md) e soluções que tiver habilitado em seu espaço de trabalho. Dependendo as soluções habilitadas, seus dados são ou enviados diretamente de um servidor de gerenciamento do Operations Manager para o serviço ou por causa do volume de dados coletados no sistema gerenciado por agente, são enviados diretamente do agente para um espaço de trabalho do Log Analytics. O servidor de gerenciamento encaminha os dados diretamente para o serviço; eles nunca são gravados no banco de dados operacional ou do data warehouse. Quando um servidor de gerenciamento perde a conectividade com o Azure Monitor, ele armazena em cache os dados localmente até que a comunicação é restabelecida. Se o servidor de gerenciamento estiver offline devido à manutenção planejada ou paralisação inesperada, outro servidor de gerenciamento no grupo de gerenciamento continuará a conectividade com o Azure Monitor.  

O diagrama a seguir mostra a conexão entre os servidores de gerenciamento e agentes em um grupo de gerenciamento do System Center Operations Manager e o Azure Monitor, incluindo a direção e as portas.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Se suas políticas de segurança TI não permitirem que os computadores em sua rede conectem a Internet, os servidores de gerenciamento poderão ser configurados para conectarem o gateway do Log Analytics para receber informações de configuração e enviar os dados coletados dependendo das soluções habilitadas. Para obter mais informações e etapas sobre como configurar o grupo de gerenciamento do Operations Manager para se comunicar através de um gateway do Log Analytics para o Azure Monitor, consulte [computadores se conectem usando o gateway do Log Analytics do Azure Monitor](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, revise os seguintes requisitos.

* O Azure Monitor só dá suporte ao System Center Operations Manager 2016 ou posterior, Operations Manager 2012 SP1 UR6 ou posterior e o Operations Manager 2012 R2 UR2 ou posterior. Foi adicionado suporte a proxy ao Operations Manager 2012 SP1 UR7 e Operations Manager 2012 R2 UR3.
* A integração do System Center Operations Manager 2016 com a nuvem do governo dos EUA exige um pacote de gerenciamento do Advisor atualizado incluído com o Update Rollup 2 ou posterior. System Center Operations Manager 2012 R2 requer um pacote de gerenciamento do Advisor atualizado incluído com o pacote cumulativo de atualizações 3 ou posterior.
* Todos os agentes do Operations Manager devem atender aos requisitos de suporte mínimos. Verifique se os agentes estão com a atualização mínima, caso contrário, a comunicação do agente do Windows poderá falhar e gerar erros no log de eventos do Operations Manager.
* Um espaço de trabalho do Log Analytics. Para mais informações, consulte [Visão geral do espaço de trabalho do Log Analytics](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json).   
* Você autentica no Azure com uma conta que seja membro da função [Log Analytics Contributor](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users).

* Regiões com suporte - apenas as seguintes regiões do Azure têm suporte pelo System Center Operations Manager para se conectar a um espaço de trabalho do Log Analytics:
    - Centro-Oeste dos EUA
    - Sudeste da Austrália
    - Europa Ocidental
    - East US
    - Sudeste da Ásia
    - Leste do Japão
    - Sul do Reino Unido
    - Índia Central
    - Canadá Central
    - Oeste dos EUA 2

>[!NOTE]
>Alterações recentes a APIs do Azure impedirá que os clientes sejam capazes de se configurar com êxito a integração entre o seu grupo de gerenciamento e Monitor do Azure pela primeira vez. Para os clientes que já integraram seu grupo de gerenciamento ao serviço, você não será afetado, a menos que precise reconfigurar sua conexão existente.  
>Um novo pacote de gerenciamento foi lançado para as seguintes versões do Operations Manager:
>  
>* Para o System Center Operations Manager 1801, baixe o pacote de gerenciamento de [aqui](https://www.microsoft.com/download/details.aspx?id=57173)  
>* Para o System Center 2016 - Operations Manager, faça o download do pacote de gerenciamento de [aqui](https://www.microsoft.com/download/details.aspx?id=57172)  
>* Para o System Center Operations Manager 2012 R2, baixe o pacote de gerenciamento de [aqui](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Esta atualização do pacote de gerenciamento não é aplicável ao System Center Operations Manager 1807, que é uma atualização da versão 1801 e não uma versão completa do produto.   

### <a name="network"></a>Rede

As informações abaixo listam as informações de configuração de proxy e firewall necessárias para o agente do Operations Manager, servidores de gerenciamento e console de operações para se comunicar com o Azure Monitor. O tráfego de cada componente é de saída da sua rede para o Azure Monitor.   

|Recurso | Número da porta| Ignorar a Inspeção de HTTP|  
|---------|------|-----------------------|  
|**Agente**|||  
|\*.ods.opinsights.azure.com| 443 |Sim|  
|\*.oms.opinsights.azure.com| 443|Sim|  
|\*.blob.core.windows.net| 443|Sim|  
|\*.azure-automation.net| 443|Sim|  
|**Servidor de gerenciamento**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Sim|  
|\*.ods.opinsights.azure.com| 443| Sim|  
|*.azure-automation.net | 443| Sim|  
|**Console do Operations Manager para o Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 e 443||  
|\*.microsoft.com| 80 e 443||  
|\*.microsoftonline.com| 80 e 443||  
|\*.mms.microsoft.com| 80 e 443||  
|login.windows.net| 80 e 443||  
|portal.loganalytics.io| 80 e 443||
|api.loganalytics.io| 80 e 443||
|docs.loganalytics.io| 80 e 443||  

### <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para o Azure Monitor, recomendamos que você configure o grupo de gerenciamento e o agente para usar pelo menos segurança de camada de transporte (TLS) 1.2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**. Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Conectando o Operations Manager para o Azure Monitor

Realize a série de etapas a seguir para configurar o grupo de gerenciamento do Operations Manager para se conectar a um dos seus workspaces do Log Analytics.

Durante o registro inicial do grupo de gerenciamento do Operations Manager com um espaço de trabalho do Log Analytics, a opção para especificar a configuração do proxy para o grupo de gerenciamento não está disponível no console de Operações.  O grupo de gerenciamento deve ser registrado com êxito com o serviço antes que essa opção esteja disponível.  Para contornar isso, você precisa atualizar a configuração do proxy do sistema usando o Netsh no sistema em que está executando o console de Operações para configurar a integração e todos os servidores de gerenciamento no grupo de gerenciamento.  

1. Abra um prompt de comando com privilégios elevados.
   a. Vá para **Iniciar** e digite **cmd**.
   b. Clique com o botão direito do mouse no **prompt de comando** e selecione Executar como administrador**.
1. Digite o comando a seguir e pressione **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir as etapas a seguir para integrar com o Azure Monitor, você pode remover a configuração executando `netsh winhttp reset proxy` e, em seguida, usar o **configurar servidor de proxy** opção no console de operações para especificar o proxy ou Log Servidor de gateway do Analytics.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Expanda o nó do Operations Management Suite e clique em **Conexão**.
1. Clique no link **Registrar-se no Operations Management Suite** .
1. Na página **Assistente de Integração do Operations Management Suite: Autenticação**, insira o endereço de email ou o número de telefone e a senha da conta de administrador associada à sua assinatura do OMS e, em seguida, clique em **Entrar**.

   >[!NOTE]
   >O nome do Operations Management Suite foi desativado.

1. Depois que você estiver autenticado com êxito, na página **Assistente de Integração do Operations Management Suite: Selecionar espaço de trabalho**, será solicitado que você selecione o locatário do Azure, a assinatura e o espaço de trabalho do Log Analytics. Se você tiver mais de um workspace, selecione aquele em que você deseja registrar-se com o grupo de gerenciamento do Operations Manager da lista suspensa e clique em **Avançar**.

   > [!NOTE]
   > O Operations Manager dá suporte a apenas um espaço de trabalho do Log Analytics por vez. A conexão e os computadores que foram registrados para o Azure Monitor com o espaço de trabalho anterior são removidos do Azure Monitor.
   >
   >
1. Na página **Assistente de Integração do Operations Management Suite: Resumo**, confirme suas configurações e, se elas estiverem corretas, clique em **Criar**.
1. Na página **Assistente de Integração do Operations Management Suite: Concluir**, clique em **Fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores gerenciados por agente

Depois de configurar a integração com o seu espaço de trabalho do Log Analytics, ele estabelece apenas uma conexão com o serviço, nenhum dado é coletado dos agentes que reportam ao seu grupo de gerenciamento. Isso só acontecerá depois de configurar quais computadores gerenciados por agente específicos coletam dados de log do Azure Monitor. Você pode selecionar os objetos de computador individualmente ou um grupo que contém objetos de computador do Windows. Não é possível selecionar um grupo que contém instâncias de outra classe, como discos lógicos ou Bancos de Dados SQL.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Expanda o nó do Operations Management Suite e clique em **Conexão**.
1. Clique no link **Adicionar um Computador/Grupo** no cabeçalho Ações no lado direito do painel.
1. Na caixa de diálogo **Pesquisa de Computador**, é possível pesquisar computadores ou grupos monitorados pelo Operations Manager. Selecione os computadores ou grupos para carregar no Azure Monitor, clique em **Add**e, em seguida, clique em **Okey**.

Você pode exibir computadores e grupos configurados para coletar dados do nó Computadores Gerenciados no Operations Management Suite no workspace **Administração** do Console de operações. Aqui, é possível adicionar ou remover computadores e grupos conforme necessário.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Definir configurações de proxy no console de Operações

Se um servidor proxy interno estiver entre o grupo de gerenciamento e o Azure Monitor, execute as seguintes etapas. Essas configurações são centralmente gerenciadas do grupo de gerenciamento e distribuídas para sistemas gerenciados por agentes que estão incluídos no escopo para coletar dados de log para o Azure Monitor.  Isso é útil para quando determinadas soluções ignorarem o servidor de gerenciamento e enviarem dados diretamente para o serviço do Log Analytics.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Expanda Operations Management Suite e clique em **Conexões**.
1. Na exibição Conexão do OMS, clique em **Configurar Servidor Proxy**.
1. Na página **Assistente do Operations Management Suite: servidor proxy**, selecione **Usar um servidor proxy para acessar o Operations Management Suite**, digite a URL com o número da porta, por exemplo, http://corpproxy:80 e, em seguida, clique em **Concluir**.

Se o servidor proxy exigir autenticação, execute as seguintes etapas para configurar as credenciais e configurações que precisam ser propagadas para computadores gerenciados que relatam para o Azure Monitor no grupo de gerenciamento.

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Em **Configuração RunAs**, selecione **Perfis**.
1. Abra o perfil **System Center Advisor executado como Proxy de perfil** .
1. No Assistente de perfil Executar como, clique em Adicionar para usar uma conta Executar como. Você pode criar uma conta [Executar Como](https://technet.microsoft.com/library/hh321655.aspx) ou usar uma conta existente. Essa conta deve ter permissões suficientes para passar pelo servidor proxy.
1. Para definir a conta para gerenciar, escolha **Uma classe, grupo ou objeto selecionado** e clique em **Selecione...** e, em seguida, clique em **Grupo...** para abrir a caixa **Pesquisa de Grupo**.
1. Procure e selecione o **Grupo de Servidores de Monitoramento do Microsoft System Center Advisor**. Clique em **OK** depois de selecionar o grupo para fechar a caixa **Pesquisa de Grupo**.
1. Clique em **OK** para fechar a caixa **Adicionar uma conta Executar como**.
1. Clique em **Salvar** para concluir o assistente e salvar suas alterações.

Depois que a conexão é criada e você configurar quais agentes coletarão e relatarão dados de log para o Azure Monitor, a seguinte configuração é aplicada no grupo de gerenciamento, não necessariamente nesta ordem:

* A conta Executar como **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** é criada. Ela está associada ao perfil Executar como Blob de perfil Executar como do **Microsoft System Center Advisor** e está voltada para duas classes: **Servidor de Coleta** e **Grupo de Gerenciamento do Operations Manager**.
* Dois conectores são criados.  O primeiro é denominado **Microsoft.SystemCenter.Advisor.DataConnector** e é automaticamente configurado com uma assinatura que encaminha todos os alertas gerados de instâncias de todas as classes no grupo de gerenciamento para o Azure Monitor. O segundo conector é **conector do Advisor**, que é responsável por comunicar-se com o Azure Monitor e compartilhamento de dados.
* Agentes e grupos que você selecionou para coletar dados do grupo de gerenciamento são adicionados ao **Grupo de Servidores de Monitoramento do Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Atualizações do pacote de gerenciamento

Após a configuração for concluída, o grupo de gerenciamento do Operations Manager estabelece uma conexão com o Azure Monitor. O servidor de gerenciamento é sincronizado com o serviço Web e receberá informações de configuração atualizadas na forma de pacotes de gerenciamento para as soluções que você habilitou integradas ao Operations Manager. O Operations Manager verifica atualizações para esses pacotes de gerenciamento, baixando-as e importando-as imediatamente quando elas estão disponíveis. Há duas regras específicas que controlam esse comportamento:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -atualiza os pacotes de gerenciamento base do Azure Monitor. Executada a cada 12 horas por padrão.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - atualiza os pacotes de gerenciamento de solução habilitados no seu workspace. Executada a cada cinco (5) minutos por padrão.

Você pode substituir essas duas regras para impedir o download automático desabilitando-as ou modificando a frequência com que frequência o servidor de gerenciamento sincroniza com o Azure Monitorto determinam se um novo pacote de gerenciamento está disponível e deve ser baixado. Siga as etapas em [Como substituir uma regra ou monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar o parâmetro **Frequency** com um valor em segundos para alterar o agendamento de sincronização ou modificar o parâmetro **Enabled** para desabilitar as regras. Direcionar as substituições a todos os objetos da classe Grupo de Gerenciamento do Operations Manager.

Para continuar seguindo seu processo de controle de alterações existente para controlar as versões do pacote de gerenciamento em seu grupo de gerenciamento de produção, é possível desativar as regras e ativá-las durante horários específicos em que as atualizações são permitidas. Se você tiver um desenvolvimento ou um grupo de gerenciamento de garantia de qualidade em seu ambiente e ele tiver conectividade com a Internet, configure esse grupo de gerenciamento com um espaço de trabalho do Log Analytics para dar suporte a esse cenário. Isso permite que você analise e avalie as versões iterativas dos pacotes de gerenciamento do Azure Monitor antes de liberá-los para seu grupo de gerenciamento de produção.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Alternar um grupo do Operations Manager para um novo espaço de trabalho do Log Analytics

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics** e crie um workspace.  
1. Abra o console do Operations Manager com uma conta que seja membro da função Administradores do Operations Manager e selecione o workspace **Administração**.
1. Expanda o Log Analytics e selecione **Conexões**.
1. Selecione o link **Reconfigurar o Operation Management Suite** no meio do painel.
1. Siga o **Assistente de Integração do Log Analytics** e insira o endereço de email ou número de telefone e a senha da conta de administrador associada ao seu novo espaço de trabalho do Log Analytics.

   > [!NOTE]
   > A página **Assistente de Integração do Operations Management Suite: selecionar workspace** apresenta o workspace existente que está em uso.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Validar a integração do Operations Manager com o Azure Monitor

Há algumas maneiras diferentes, você pode verificar que o Azure Monitor para integração do Operations Manager foi bem-sucedida.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Para confirmar a integração do portal do Azure

1. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada.
1. Na lista de workspaces do Log Analytics, selecione o workspace aplicável.  
1. Selecione **Configurações avançadas**, **Fontes Conectadas** e **System Center**.
1. Na tabela da seção System Center Operations Manager, você deve ver o nome do grupo de gerenciamento listado com o número de agentes e status de quando os dados foram recebidos pela última vez.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar a integração do console de Operações

1. Abra o console do Operations Manager e selecione o workspace **Administração**.
1. Clique em **Pacotes de Gerenciamento** e, na caixa de texto **Procurar por:** , digite **Advisor** ou **Intelligence**.
1. Dependendo das soluções que tiver habilitado, você verá um pacote de gerenciamento correspondente listado nos resultados da pesquisa.  Por exemplo, se tiver habilitado a solução de Gerenciamento de Alertas, o pacote de gerenciamento do Gerenciamento de Alertas do Microsoft System Center Advisor constará na lista.
1. Da exibição **Monitoramento**, navegue até a exibição **Operations Management Suite\Estado de Integridade**.  Selecione um servidor de Gerenciamento no painel **Estado do Servidor de Gerenciamento** e, no painel **Exibição de Detalhes**, confirme se o valor da propriedade **URI do serviço de autenticação** coincide com a ID do espaço de trabalho do Log Analytics.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Remover a integração com o Azure Monitor

Quando você não precisar mais da integração entre o grupo de gerenciamento do Operations Manager e o espaço de trabalho do Log Analytics, há várias etapas necessárias para remover corretamente a conexão e a configuração do grupo de gerenciamento. O procedimento a seguir tem a atualizar seu espaço de trabalho do Log Analytics, excluindo a referência do seu grupo de gerenciamento, excluindo os conectores do Azure Monitor e, em seguida, excluir pacotes de gerenciamento der suporte à integração com o serviço.  

Pacotes de gerenciamento para as soluções que você habilitou que se integram com o Operations Manager e os pacotes de gerenciamento necessários para dar suporte à integração com o Azure Monitor não podem ser facilmente excluídos do grupo de gerenciamento. Isso ocorre porque alguns dos pacotes de gerenciamento do Azure Monitor têm dependências em outros pacotes de gerenciamento relacionados. Para excluir pacotes de gerenciamento com uma dependência em outros pacotes de gerenciamento, baixe o script [remover um pacote de gerenciamento com dependências](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) do TechNet Script Center.  

1. Abra o Shell de Comando do Operations Manager com uma conta que seja membro da função Administradores do Operations Manager.

    > [!WARNING]
    > Verifique se você não tem pacotes de gerenciamento personalizados com a palavra Advisor ou IntelligencePack no nome antes de prosseguir, caso contrário, as etapas a seguir os excluirão do grupo de gerenciamento.
    >

1. No prompt de shell de comando, digite `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Em seguida, digite `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Para remover quaisquer pacotes de gerenciamento restantes que tenham uma dependência de outros pacotes de gerenciamento do System Center Advisor, use o script *RecursiveRemove.ps1* baixado anteriormente do TechNet Script Center.  

    > [!NOTE]
    > A etapa para remover os pacotes de gerenciamento do Advisor com o PowerShell não excluirá automaticamente os pacotes de gerenciamento internos do Microsoft System Center Advisor ou do Microsoft System Center Advisor.  Não tente excluí-los.  
    >  

1. Abra o Console de operações do Operations Manager com uma conta que seja membro da função Administradores do Operations Manager.
1. Em **Administração**, selecione o nó **Pacotes de Gerenciamento** e, na caixa **Procurar por:** , digite **Advisor** e verifique se os seguintes pacotes de gerenciamento ainda foram importados no grupo de gerenciamento:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Interno

1. No portal do Azure, clique no bloco **Configurações**.
1. Selecione **Fontes Conectadas**.
1. Na tabela na seção System Center Operations Manager, você verá o nome do grupo de gerenciamento que deseja remover do workspace. Na coluna **Últimos Dados**, clique em **Remover**.  

    > [!NOTE]
    > O link **Remover** não estará disponível até depois de 14 dias, se não for detectada nenhuma atividade pelo grupo de gerenciamento conectado.  
    >

1. Uma janela será exibida solicitando que você confirme se deseja continuar com a remoção.  Clique em **Sim** para confirmar.

Para excluir os dois conectores, Microsoft.SystemCenter.Advisor.DataConnector e o Conector do Advisor, salve o script do PowerShell abaixo em seu computador e execute-o usando os exemplos a seguir:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> O computador no qual você executará esse script, se não for um servidor de gerenciamento, deverá ter o shell de comando do Operations Manager instalado, dependendo da versão do seu grupo de gerenciamento.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

No futuro, se você pretender reconectar o grupo de gerenciamento a um espaço de trabalho do Log Analytics, será necessário importar novamente o arquivo de pacote de gerenciamento `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb`. Dependendo da versão do System Center Operations Manager implantado em seu ambiente, você poderá encontrar esse arquivo no seguinte local:

* Na mídia de origem sob a pasta `\ManagementPacks` para o System Center 2016 - Operations Manager e superior.
* Do rollup de atualização mais recente aplicado ao seu grupo de gerenciamento. Para o Operations Manager 2012, é a pasta de origem `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` e para 2012 R2, ele está localizado em `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Próximas etapas

Para adicionar funcionalidade e coletar dados, consulte [soluções de adicionar o Azure Monitor da Galeria de soluções](../../azure-monitor/insights/solutions.md).
