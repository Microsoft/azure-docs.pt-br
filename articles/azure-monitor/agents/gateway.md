---
title: Conectar computadores usando o gateway de Log Analytics | Microsoft Docs
description: Conecte seus dispositivos e computadores monitorados por Operations Manager usando o gateway de Log Analytics para enviar dados para a automação do Azure e o serviço de Log Analytics quando eles não tiverem acesso à Internet.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: bae48dc78eb6973e5bce4d535091bc330c4c897f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509023"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Conectar computadores sem acesso à Internet usando o gateway de Log Analytics no Azure Monitor

>[!NOTE]
>Como as transições Microsoft Operations Management Suite (OMS) para Microsoft Azure monitor, a terminologia está mudando. Este artigo refere-se ao gateway do OMS como o gateway de Log Analytics do Azure. 
>

Este artigo descreve como configurar a comunicação com a automação do Azure e Azure Monitor usando o gateway de Log Analytics quando os computadores conectados diretamente ou que são monitorados pelo Operations Manager não têm acesso à Internet. 

O gateway de Log Analytics é um proxy de encaminhamento HTTP que dá suporte ao túnel HTTP usando o comando HTTP CONNECT. Esse gateway envia dados para a automação do Azure e um espaço de trabalho Log Analytics em Azure Monitor em nome dos computadores que não podem se conectar diretamente à Internet. 

O gateway do Log Analytics dá suporte a:

* Relatando até os mesmos espaços de trabalho Log Analytics configurados em cada agente por trás dele e configurados com Hybrid runbook Workers de automação do Azure.  
* Computadores Windows nos quais o Microsoft Monitoring Agent está diretamente conectado a um espaço de trabalho Log Analytics no Azure Monitor.
* Computadores Linux nos quais um agente Log Analytics para Linux está conectado diretamente a um espaço de trabalho Log Analytics no Azure Monitor.  
* System Center Operations Manager 2012 SP1 com UR7, Operations Manager 2012 R2 com UR3 ou um grupo de gerenciamento no Operations Manager 2016 ou posterior que é integrado com Log Analytics.  

Algumas políticas de segurança de ti não permitem a conexão com a Internet para computadores de rede. Esses computadores desconectados podem ser dispositivos de POS (ponto de venda) ou servidores que dão suporte a serviços de ti, por exemplo. Para conectar esses dispositivos à automação do Azure ou a um Log Analytics espaço de trabalho para que você possa gerenciá-los e monitorá-los, configure-os para se comunicar diretamente com o gateway de Log Analytics. O gateway de Log Analytics pode receber informações de configuração e encaminhar dados em seu nome. Se os computadores estiverem configurados com o agente de Log Analytics para se conectar diretamente a um espaço de trabalho Log Analytics, os computadores se comunicarão com o gateway de Log Analytics.  

O gateway de Log Analytics transfere os dados dos agentes para o serviço diretamente. Ele não analisa nenhum dos dados em trânsito e o gateway não armazena em cache os dados quando ele perde a conectividade com o serviço. Quando o gateway não puder se comunicar com o serviço, o agente continuará a ser executado e enfileirará os dados coletados no disco do computador monitorado. Quando a conexão é restaurada, o agente envia os dados armazenados em cache coletados para Azure Monitor.

Quando um grupo de gerenciamento de Operations Manager é integrado ao Log Analytics, os servidores de gerenciamento podem ser configurados para se conectarem ao gateway de Log Analytics para receber informações de configuração e enviar dados coletados, dependendo da solução que você habilitou.  Os agentes de Operations Manager enviam alguns dados ao servidor de gerenciamento. Por exemplo, os agentes podem enviar Operations Manager alertas, dados de avaliação de configuração, dados de espaço de instância e dados de capacidade. Outros dados de alto volume, como logs de Serviços de Informações da Internet (IIS), dados de desempenho e eventos de segurança, são enviados diretamente para o gateway de Log Analytics. 

Se um ou mais servidores de gateway de Operations Manager são implantados para monitorar sistemas não confiáveis em uma rede de perímetro ou em uma rede isolada, esses servidores não podem se comunicar com um gateway de Log Analytics.  Operations Manager servidores gateway podem relatar somente para um servidor de gerenciamento.  Quando um grupo de gerenciamento de Operations Manager é configurado para se comunicar com o gateway de Log Analytics, as informações de configuração de proxy são distribuídas automaticamente a todos os computadores gerenciados por agente configurados para coletar dados de log para Azure Monitor, mesmo que a configuração esteja vazia.

Para fornecer alta disponibilidade para grupos de gerenciamento de operações ou conectadas diretamente que se comunicam com um Log Analytics espaço de trabalho por meio do gateway, use o NLB (balanceamento de carga de rede) para redirecionar e distribuir o tráfego entre vários servidores de gateway. Dessa forma, se um servidor de gateway falhar, o tráfego será redirecionado para outro nó disponível.  

O computador que executa o gateway de Log Analytics requer que o Log Analytics agente do Windows identifique os pontos de extremidade de serviço com os quais o gateway precisa se comunicar. O agente também precisa direcionar o gateway para reportar para os mesmos espaços de trabalho que os agentes ou o grupo de gerenciamento de Operations Manager por trás do gateway são configurados com. Essa configuração permite que o gateway e o agente se comuniquem com seu espaço de trabalho atribuído.

Um gateway pode ser de hospedagem múltipla para até quatro espaços de trabalho. Este é o número total de espaços de trabalho aos quais um agente do Windows dá suporte.  

Cada agente deve ter conectividade de rede com o gateway para que os agentes possam transferir dados de e para o gateway automaticamente. Evite instalar o gateway em um controlador de domínio. Computadores Linux que estão atrás de um servidor gateway não podem usar o método de [instalação de script do wrapper](../agents/agent-linux.md#install-the-agent-using-wrapper-script) para instalar o agente de log Analytics para Linux. O agente deve ser baixado manualmente, copiado para o computador e instalado manualmente, pois o gateway só oferece suporte à comunicação com os serviços do Azure mencionados anteriormente.

O diagrama a seguir mostra os dados que fluem de agentes diretos, por meio do gateway, para a automação do Azure e Log Analytics. A configuração de proxy do agente deve corresponder à porta com a qual o gateway de Log Analytics está configurado.  

![Diagrama de comunicação direta do agente com serviços](./media/gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama a seguir mostra o fluxo de dados de um grupo de gerenciamento do Operations Manager para o Log Analytics.   

![Diagrama de comunicação Operations Manager com Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurar seu sistema

Os computadores designados para executar o gateway de Log Analytics devem ter a seguinte configuração:

* Windows 10, Windows 8.1 ou Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Pelo menos um processador de 4 núcleos e 8 GB de memória 
* Um [agente log Analytics para Windows](../agents/agent-windows.md) que está configurado para relatar ao mesmo espaço de trabalho que os agentes que se comunicam por meio do gateway

### <a name="language-availability"></a>Disponibilidade de idiomas

O gateway de Log Analytics está disponível nestes idiomas:

- Chinês (Simplificado)
- Chinês (Tradicional)
- Tcheco
- Holandês
- Inglês
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polonês
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol (internacional)

### <a name="supported-encryption-protocols"></a>Protocolos de criptografia com suporte

O gateway de Log Analytics dá suporte apenas a TLS (segurança de camada de transporte) 1,0, 1,1 e 1,2.  Ele não dá suporte a protocolo SSL (SSL).  Para garantir a segurança dos dados em trânsito para Log Analytics, configure o gateway para usar pelo menos o TLS 1,2. Versões mais antigas de TLS ou SSL são vulneráveis. Embora eles atualmente permitam compatibilidade com versões anteriores, evite usá-los.  

Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](../logs/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Número de conexões de agente com suporte

A tabela a seguir mostra aproximadamente Quantos agentes podem se comunicar com um servidor de gateway. O suporte é baseado em agentes que carregam cerca de 200 KB de dados a cada 6 segundos. Para cada agente testado, o volume de dados é de cerca de 2,7 GB por dia.

|Gateway |Agentes com suporte (aproximado)|  
|--------|----------------------------------|  
|CPU: processador Intel Xeon E5-2660 v3 \@ 2,6 GHz, 2 núcleos<br> Memória: 4 GB<br> Largura de banda de rede: 1 Gbps| 600|  
|CPU: processador Intel Xeon E5-2660 v3 \@ 2,6 GHz, 4 núcleos<br> Memória: 8 GB<br> Largura de banda de rede: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Fazer download do gateway do Log Analytics

Obtenha a versão mais recente do arquivo de instalação do gateway de Log Analytics do centro de download da Microsoft ([link de download](https://go.microsoft.com/fwlink/?linkid=837444)) ou o portal do Azure.

Para obter o gateway de Log Analytics do portal do Azure, siga estas etapas:

1. Navegue pela lista de serviços e selecione **Log Analytics**. 
1. Selecione um workspace.
1. Na folha do seu workspace, em **Geral**, selecione **Início Rápido**. 
1. Em **Escolher uma fonte de dados para conectar ao workspace**, selecione **Computadores**.
1. Na folha **agente direto** , selecione **baixar log Analytics gateway**.
 
   ![Captura de tela das etapas para baixar o gateway de Log Analytics](./media/gateway/download-gateway.png)

ou 

1. Na folha do seu workspace em **Configurações**, selecione **Configurações avançadas**.
1. Acesse **fontes conectadas**  >  **servidores Windows** e selecione **baixar log Analytics gateway**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Instalar Log Analytics gateway usando o assistente para instalação

Para instalar um gateway usando o assistente de instalação, siga estas etapas. 

1. Na pasta de destino, clique duas vezes em **Log Analytics gateway.msi**.
1. Sobre o **boas-vindas** página, selecione **próxima**.

   ![Captura de tela da página de boas-vindas no assistente de instalação do gateway](./media/gateway/gateway-wizard01.png)

1. Na página **contrato de licença** , selecione **aceito os termos do contrato de licença** para concordar com os termos de licença para software Microsoft e, em seguida, selecione **Avançar**.
1. Na página **Porta e endereço de proxy**:

   a. Insira o número da porta TCP a ser usado para o gateway. A instalação usa esse número de porta para configurar uma regra de entrada no firewall do Windows.  O valor padrão é 8080.
      O intervalo válido do número da porta é de 1 a 65535. Se a entrada não estiver dentro desse intervalo, uma mensagem de erro será exibida.

   b. Se o servidor no qual o gateway está instalado precisar se comunicar por meio de um proxy, insira o endereço de proxy para o qual o gateway precisa se conectar. Por exemplo, insira `http://myorgname.corp.contoso.com:80`.  Se você deixar esse campo em branco, o gateway tentará se conectar diretamente à Internet.  Se o servidor proxy exigir autenticação, insira um nome de usuário e senha.

   c. Selecione **Avançar**.

   ![Captura de tela da configuração do proxy de gateway](./media/gateway/gateway-wizard02.png)

1. Se você não tiver o Microsoft Update habilitado, a página Microsoft Update aparecerá e você poderá optar por habilitá-lo. Faça uma seleção e, em seguida, selecione **Avançar**. Caso contrário, prossiga para a próxima etapa.
1. Na página **pasta de destino** , deixe a pasta padrão C:\Program c:\programfiles\oms gateway ou insira o local onde você deseja instalar o gateway. Em seguida, selecione **Avançar**.
1. Na página **pronto para instalar** , selecione **instalar**. Se o controle de conta de usuário solicitar permissão para instalar, selecione **Sim**.
1. Após a conclusão da instalação, selecione **concluir**. Para verificar se o serviço está em execução, abra o snap-in Services. msc e verifique se o **gateway do OMS** aparece na lista de serviços e se seu status está **em execução**.

   ![Captura de tela de serviços locais, mostrando que o gateway do OMS está em execução](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instalar o gateway de Log Analytics usando a linha de comando

O arquivo baixado para o gateway é um pacote Windows Installer que dá suporte à instalação silenciosa da linha de comando ou de outro método automatizado. Se você não estiver familiarizado com as opções de linha de comando padrão para Windows Installer, consulte [Opções de linha de comando](/windows/desktop/msi/command-line-options).
 
A tabela a seguir realça os parâmetros com suporte na instalação do.

|Parâmetros| Observações|
|----------|------| 
|PORTNUMBER | Número da porta TCP para o gateway a ser escutado |
|ACIONISTA | Endereço IP do servidor proxy |
|INSTALLDIR | Caminho totalmente qualificado para especificar o diretório de instalação dos arquivos de software do gateway |
|USERNAME | ID de usuário para autenticar com o servidor proxy |
|PASSWORD | Senha da ID de usuário para autenticar com o proxy |
|LicenseAccepted | Especifique um valor de **1** para verificar se você aceita o contrato de licença |
|HASAUTH | Especifique um valor de **1** quando os parâmetros de nome de usuário/senha forem especificados |
|HASPROXY | Especifique um valor de **1** ao especificar o endereço IP para o parâmetro de **proxy** |

Para instalar silenciosamente o gateway e configurá-lo com um endereço de proxy específico, número da porta, digite o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

O uso da opção de linha de comando/qn oculta a instalação,/QB mostra a instalação durante a instalação silenciosa.  

Se você precisar fornecer credenciais para autenticar com o proxy, digite o seguinte:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Após a instalação, você pode confirmar que as configurações são aceitas (excluindo o nome de usuário e a senha) usando os seguintes cmdlets do PowerShell:

- **Get-OMSGatewayConfig** – retorna a porta TCP que o gateway está configurado para escutar.
- **Get-OMSGatewayRelayProxy** – retorna o endereço IP do servidor proxy com o qual você o configurou para se comunicar.

## <a name="configure-network-load-balancing"></a>Configurar o balanceamento de carga de rede

Você pode configurar o gateway para alta disponibilidade usando o NLB (balanceamento de carga de rede) usando o [NLB (balanceamento de carga de rede)](/windows-server/networking/technologies/network-load-balancing)da Microsoft, [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)ou balanceadores de carga baseados em hardware. O balanceador de carga gerencia o tráfego redirecionando as conexões solicitadas dos servidores de gerenciamento do Operations Manager ou de agentes do Log Analytics entre seus nós. Se um servidor de Gateway falhar, o tráfego será redirecionado para outros nós.

### <a name="microsoft-network-load-balancing"></a>Balanceamento de carga de rede da Microsoft

Para saber como projetar e implantar um cluster de balanceamento de carga de rede Windows Server 2016, confira [Balanceamento de carga de rede](/windows-server/networking/technologies/network-load-balancing). As etapas a seguir descrevem como configurar uma cluster de balanceamento de carga de rede Microsoft Network.  

1. Entre no servidor Windows que seja membro do cluster NLB com uma conta administrativa.  
2. Abra o Gerenciador de Balanceamento de Carga de Rede no gerenciador de servidores, clique em **Ferramentas** e clique em **Gerenciador de Balanceamento de Carga de Rede**.
3. Para conectar um servidor do gateway do Log Analytics ao Microsoft Monitoring Agent instalado, clique com o botão direito no endereço IP do cluster e clique em **Adicionar Host ao Cluster**. 

    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster](./media/gateway/nlb02.png)
 
4. Digite o endereço IP do servidor de gateway que deseja conectar. 

    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster: Conectar](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Para saber como projetar e implantar um Azure Load Balancer, consulte [o que é Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Para implantar um balanceador de carga básico, siga as etapas descritas neste guia de [início rápido](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , excluindo as etapas descritas na seção **criar servidores back-end**.   

> [!NOTE]
> Configurar o Azure Load Balancer usando o **SKU básico** requer que as máquinas virtuais do Azure pertençam a um conjunto de disponibilidade. Para saber mais sobre conjuntos de disponibilidade, confira [gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](../../virtual-machines/availability.md). Para adicionar máquinas virtuais existentes a um conjunto de disponibilidade, consulte [definir Azure Resource Manager conjunto de disponibilidade da VM](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Depois que o balanceador de carga é criado, um pool de back-end precisa ser criado, o que distribui o tráfego para um ou mais servidores de gateway. Siga as etapas descritas na seção do artigo de início rápido [criar recursos para o balanceador de carga](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Ao configurar a investigação de integridade, ela deve ser configurada para usar a porta TCP do servidor de gateway. A investigação de integridade adiciona ou remove dinamicamente os servidores de gateway da rotação do balanceador de carga com base em sua resposta às verificações de integridade. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configurar o agente de Log Analytics e o grupo de gerenciamento de Operations Manager

Nesta seção, você verá como configurar agentes de Log Analytics conectados diretamente, um grupo de gerenciamento de Operations Manager ou Hybrid runbook Workers de automação do Azure com o gateway de Log Analytics para se comunicar com a automação do Azure ou Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configurar um agente de Log Analytics autônomo

Ao configurar o agente de Log Analytics, substitua o valor do servidor proxy pelo endereço IP do servidor de gateway de Log Analytics e seu número de porta. Se você tiver implantado vários servidores de gateway atrás de um balanceador de carga, a configuração de proxy do Log Analytics Agent será o endereço IP virtual do balanceador de carga.  

>[!NOTE]
>Para instalar o agente de Log Analytics no gateway e computadores Windows que se conectam diretamente ao Log Analytics, consulte [conectar computadores Windows ao serviço de log Analytics no Azure](../agents/agent-windows.md). Para conectar computadores Linux, consulte [conectar computadores Linux ao Azure monitor](../agents/agent-linux.md). 
>

Depois de instalar o agente no servidor de gateway, configure-o para relatar para o espaço de trabalho ou agentes de espaço de trabalho que se comunicam com o gateway. Se o Log Analytics agente do Windows não estiver instalado no gateway, o evento 300 será gravado no log de eventos do gateway do OMS, indicando que o agente precisa ser instalado. Se o agente estiver instalado, mas não estiver configurado para relatar ao mesmo espaço de trabalho que os agentes que se comunicam por meio dele, o evento 105 será gravado no mesmo log, indicando que o agente no gateway precisa ser configurado para relatar ao mesmo espaço de trabalho que os agentes que se comunicam com o gateway.

Depois de concluir a configuração, reinicie o serviço do **gateway do OMS** para aplicar as alterações. Caso contrário, o gateway rejeitará agentes que tentam se comunicar com Log Analytics e relatará o evento 105 no log de eventos do gateway do OMS. Isso também ocorrerá quando você adicionar ou remover um espaço de trabalho da configuração do agente no servidor de gateway.

Para obter informações relacionadas ao Hybrid Runbook Worker de automação, consulte [automatizar recursos em seu datacenter ou nuvem usando Hybrid runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configurar Operations Manager, onde todos os agentes usam o mesmo servidor proxy

A configuração do proxy do Operations Manager é aplicada automaticamente a todos os agentes que se reportam ao Operations Manager, mesmo se a configuração está vazia.  

Para usar o gateway do OMS para dar suporte a Operations Manager, você deve ter:

* Microsoft Monitoring Agent (versão 8.0.10900.0 ou posterior) instalada no servidor de gateway do OMS e configurada com os mesmos espaços de trabalho de Log Analytics que seu grupo de gerenciamento está configurado para relatar.
* Conectividade com a Internet. Como alternativa, o gateway do OMS deve estar conectado a um servidor proxy que está conectado à Internet.

> [!NOTE]
> Se você não especificar nenhum valor para o gateway, os valores em branco serão enviados por push para todos os agentes.
>

Se o grupo de gerenciamento do Operations Manager estiver se registrando com um espaço de trabalho do Log Analytics pela primeira vez, você não verá a opção de especificar a configuração de proxy para o grupo de gerenciamento no console de operações. Essa opção só estará disponível se o grupo de gerenciamento tiver sido registrado com o serviço.  

Para configurar a integração, atualize a configuração de proxy do sistema usando netsh no sistema em que você está executando o console de operações e em todos os servidores de gerenciamento no grupo de gerenciamento. Siga estas etapas:

1. Abra um prompt de comando com privilégios elevados:

   a. Selecione **Iniciar** e insira **cmd**.  

   b. Clique com o botão direito do mouse em **prompt de comando** e selecione **Executar como administrador**.  

1. Insira o seguinte comando:

   `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir a integração com o Log Analytics, remova a alteração executando `netsh winhttp reset proxy` . Em seguida, no console de operações, use a opção **Configurar servidor proxy** para especificar o servidor gateway log Analytics. 

1. No console do Operations Manager, em **Operations Management Suite**, selecione **conexão** e, em seguida, selecione **Configurar servidor proxy**.

   ![Captura de tela de Operations Manager, mostrando a seleção configurar servidor proxy](./media/gateway/scom01.png)

1. Selecione **usar um servidor proxy para acessar o Operations Management Suite** e, em seguida, insira o endereço IP do servidor de gateway de log Analytics ou o endereço IP virtual do balanceador de carga. Tenha cuidado para começar com o prefixo `http://` .

   ![Captura de tela de Operations Manager, mostrando o endereço do servidor proxy](./media/gateway/scom02.png)

1. Selecione **Concluir**. O grupo de gerenciamento do Operations Manager agora está configurado para se comunicar por meio do servidor de gateway para o serviço de Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configurar Operations Manager, em que agentes específicos usam um servidor proxy

Para ambientes grandes ou complexos, talvez você queira que apenas servidores específicos (ou grupos) usem o servidor gateway Log Analytics.  Para esses servidores, você não pode atualizar o agente de Operations Manager diretamente, pois esse valor é substituído pelo valor global do grupo de gerenciamento.  Em vez disso, substitua a regra usada para enviar esses valores por push.  

> [!NOTE] 
> Use essa técnica de configuração se você quiser permitir vários servidores de gateway Log Analytics em seu ambiente.  Por exemplo, você pode exigir que determinados servidores de gateway Log Analytics sejam especificados em uma base regional.
>

Para configurar servidores ou grupos específicos para usar o servidor gateway Log Analytics: 

1. Abra o console do Operations Manager e selecione o workspace **Criação** .  
1. No workspace de Criação, selecione **Regras**. 
1. Na barra de ferramentas Operations Manager, selecione o botão **escopo** . Se esse botão não estiver disponível, verifique se você selecionou um objeto, e não uma pasta, no painel **monitoramento** . A caixa de diálogo **Delimitar Objetos do Pacote de Gerenciamento** exibe uma lista de objetos, grupos ou classes comuns de destino. 
1. No campo **procurar** , insira **serviço de integridade** e selecione-o na lista. Selecione **OK**.  
1. Procure **regra de configuração de proxy do Advisor**. 
1. Na barra de ferramentas Operations Manager, selecione **substituições** e, em seguida, aponte para **substituir o Rule\For de um objeto específico da classe: serviço de integridade** e selecione um objeto da lista.  Ou crie um grupo personalizado que contenha o objeto de serviço de integridade dos servidores aos quais você deseja aplicar essa substituição. Em seguida, aplique a substituição ao seu grupo personalizado.
1. Na caixa de diálogo **Propriedades da substituição** , adicione uma marca de seleção na coluna **substituir** ao lado do parâmetro **WebProxyAddress** .  No campo **valor de substituição** , insira a URL do servidor de gateway de log Analytics. Tenha cuidado para começar com o prefixo `http://` .  

    >[!NOTE]
    > Você não precisa habilitar a regra. Ele já é gerenciado automaticamente com uma substituição no pacote de gerenciamento de substituição de referência segura do Microsoft System Center Advisor que tem como alvo o grupo de servidores de monitoramento do Microsoft System Center Advisor.
    > 

1. Selecione um pacote de gerenciamento na lista **selecionar pacote de gerenciamento de destino** ou crie um novo pacote de gerenciamento sem lacre selecionando **novo**. 
1. Quando terminar, selecione **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurar para Hybrid runbook Workers de automação

Se você tiver Hybrid runbook Workers de automação em seu ambiente, siga estas etapas para configurar o gateway para dar suporte aos trabalhadores.

Consulte a seção [configurar sua rede](../../automation/automation-hybrid-runbook-worker.md#network-planning) da documentação de automação para encontrar a URL para cada região.

Se o seu computador estiver registrado como um Hybrid Runbook Worker automaticamente, por exemplo, se a solução de Gerenciamento de Atualizações estiver habilitada para uma ou mais VMs, siga estas etapas:

1. Adicione as URLs de serviço de dados de runtime do trabalho à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço do gateway do Log Analytics usando o seguinte cmdlet do PowerShell: `Restart-Service OMSGatewayService`

Se o computador estiver ingressado na automação do Azure usando o cmdlet de registro Hybrid Runbook Worker, siga estas etapas:

1. Adicione a URL do registro de serviço do agente à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adicione as URLs de serviço de dados de runtime do trabalho à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço do gateway do Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell

Você pode usar cmdlets para concluir as tarefas para atualizar as definições de configuração do gateway de Log Analytics. Antes de usar os cmdlets, certifique-se de:

1. Instale o gateway de Log Analytics (Microsoft Windows Installer).
1. Abra uma janela do console do PowerShell.
1. Importe o módulo digitando este comando: `Import-Module OMSGateway`
1. Se nenhum erro tiver ocorrido na etapa anterior, o módulo foi importado com êxito, e os cmdlets poderão ser usados. Inserir `Get-Module OMSGateway`
1. Depois de usar os cmdlets para fazer alterações, reinicie o serviço do gateway do OMS.

Um erro na etapa 3 significa que o módulo não foi importado. O erro pode ocorrer quando o PowerShell não consegue localizar o módulo. Você pode encontrar o módulo no caminho de instalação do gateway do OMS: *c:\Arquivos de PROGRAMAS\MICROSOFT OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (obrigatória) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy de retransmissão (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de Usuário<br> Senha (cadeia de caracteres segura) |Define o endereço (e as credenciais) do proxy de retransmissão (upstream) |1. Defina um proxy de retransmissão e uma credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Defina um proxy de retransmissão que não precise de autenticação: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. desmarque a configuração de proxy de retransmissão:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o host atualmente permitido (somente o host permitido configurado localmente, os hosts permitidos que não foram baixados automaticamente) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obrigatório) |Adiciona o host à lista de permissões |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obrigatório) |Remove o host da lista de permissões |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o assunto do certificado do cliente à lista de permissões |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o assunto do certificado do cliente da lista de permissões |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém os assuntos do certificado do cliente atualmente permitidos (somente os assuntos permitidos configurados localmente, não os assuntos permitidos automaticamente baixados) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Solução de problemas

Para coletar eventos registrados pelo gateway, você deve ter o agente de Log Analytics instalado.

![Captura de tela da lista de Visualizador de Eventos no log do gateway de Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Descrições e IDs de eventos do gateway Log Analytics

A tabela a seguir mostra as IDs e descrições de eventos para Log Analytics eventos de log do gateway.

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicativo que não tenha uma ID específica. |
| 401 |Configuração incorreta. Por exemplo, listenPort = "texto" em vez de um inteiro. |
| 402 |Exceção ao analisar mensagens de handshake TLS. |
| 403 |Rede de rede. Por exemplo, não é possível se conectar ao servidor de destino. |
| 100 |Informações gerais. |
| 101 |Serviço iniciado. |
| 102 |Serviço interrompido. |
| 103 |Um comando HTTP CONNECT foi recebido do cliente. |
| 104 |Não é um comando HTTP CONNECT. |
| 105 |O servidor de destino não está na lista de permissões ou a porta de destino não é segura (443). <br> <br> Verifique se o agente do MMA no servidor de gateway do OMS e os agentes que se comunicam com o gateway do OMS estão conectados ao mesmo espaço de trabalho de Log Analytics. |
| 105 |ERRO TcpConnection – certificado de cliente inválido: CN = gateway. <br><br> Verifique se você está usando o gateway do OMS versão 1.0.395.0 ou superior. Verifique também se o agente MMA no servidor de gateway do OMS e os agentes que se comunicam com o gateway do OMS estão conectados ao mesmo espaço de trabalho de Log Analytics. |
| 106 |Versão do protocolo TLS/SSL sem suporte.<br><br> O gateway de Log Analytics dá suporte apenas a TLS 1,0, TLS 1,1 e 1,2. Ele não oferece suporte ao protocolo SSL.|
| 107 |A sessão TLS foi verificada. |

### <a name="performance-counters-to-collect"></a>Contadores de desempenho a serem coletados

A tabela a seguir mostra os contadores de desempenho disponíveis para o gateway do Log Analytics. Use o monitor de desempenho para adicionar os contadores.

| **Nome** | **Descrição** |
| --- | --- |
| Gateway do Log Analytics/Conexão de Cliente Ativo |Número de conexões de rede de cliente ativo (TCP) |
| Gateway do Log Analytics/Contagem de Erros |Número de erros |
| Gateway do Log Analytics/Cliente Conectado |Número de clientes conectados |
| Gateway do Log Analytics/Contagem de Rejeição |Número de rejeições devido a qualquer erro de validação de TLS |

![Captura de tela da interface Log Analytics gateway, mostrando contadores de desempenho](./media/gateway/counters.png)

## <a name="assistance"></a>Assistência

Quando você estiver conectado à portal do Azure, poderá obter ajuda com o gateway de Log Analytics ou qualquer outro serviço ou recurso do Azure.
Para obter ajuda, selecione o ícone de ponto de interrogação no canto superior direito do portal e selecione **nova solicitação de suporte**. Em seguida, preencha o novo formulário de solicitação de suporte.

![Captura de tela de uma nova solicitação de suporte](./media/gateway/support.png)

## <a name="next-steps"></a>Próximas etapas

[Adicione fontes de dados](./../agents/agent-data-sources.md) para coletar dados de fontes conectadas e armazene os dados em seu espaço de trabalho do log Analytics.