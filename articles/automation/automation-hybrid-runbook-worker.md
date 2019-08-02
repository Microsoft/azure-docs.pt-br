---
title: Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece informações sobre como instalar e usar o Hybrid Runbook Worker, que é um recurso do Azure Automation que você pode usar para executar runbooks em máquinas no datacenter local ou no provedor de nuvem.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fceeed47ee77207e00ebfc619226ecbb5956bc3d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478525"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatize recursos em seu datacenter ou nuvem usando o Hybrid Runbook Worker

Os runbooks na Automação do Azure talvez não tenham acesso aos recursos em outras nuvens ou no seu ambiente local por serem executados na plataforma de nuvem do Azure. É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. Runbooks são armazenados e gerenciados na Automação do Azure e, em seguida, entregues a um ou mais computadores atribuídos.

A imagem a seguir ilustra essa funcionalidade:

![Visão geral do Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente. Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade.

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado. Cada operador no grupo de sonda de automação do Azure para ver se todos os trabalhos estão disponíveis. Se um trabalho estiver disponível, o primeiro funcionário a obter o trabalho o fará. O tempo de processamento da fila de trabalhos depende do perfil de hardware do Hybrid worker e a carga. Você não pode especificar um trabalhador específico. Hybrid Runbook Workers não compartilham muitos dos limites que têm áreas restritas do Azure. Eles não têm os mesmos limites de espaço em disco, memória ou soquetes de rede. Hybrid Runbook Workers são limitadas apenas pelos recursos em Hybrid Runbook Worker em si. Além disso, Hybrid Runbook Workers não compartilham o limite de tempo de 180 minutos [justo](automation-runbook-execution.md#fair-share)que as áreas restritas do Azure fazem. Para saber mais sobre os limites de serviço para áreas restritas do Azure e o Hybrid Runbook Workers, consulte a página [limites](../azure-subscription-service-limits.md#automation-limits) de trabalho.

## <a name="install-a-hybrid-runbook-worker"></a>Instalar um trabalhador de runbook híbrido

O processo para instalar um Hybrid Runbook Worker depende do sistema operacional. A tabela a seguir contém links para os métodos que você pode usar para a instalação.

Para instalar e configurar um Windows Hybrid Runbook Worker, você pode usar dois métodos. O método recomendado é usar um runbook de automação para automatizar completamente o processo de configuração de um computador com Windows. O segundo método é seguir um procedimento passo a passo para instalar e configurar a função manualmente. Para máquinas Linux, você executa um script Python para instalar o agente na máquina.

|SO  |Tipos de implantação  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Para gerenciar a configuração de seus servidores que suportam a função de executante de caderno híbrido com DSC (Configuração de estado desejado), você precisa adicioná-los como nós DSC. Para saber mais sobre a integração deles para gerenciamento com DSC, confira [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md).
>
>Se você habilitar a [ solução de Gerenciamento de Atualizações ](automation-update-management.md), qualquer computador conectado ao seu espaço de trabalho do Log Analytics do Azure será automaticamente configurado como um Operador de Runbook Híbrido para oferecer suporte a runbooks incluídos nessa solução. No entanto, o computador não está registrado em nenhum grupo de Hybrid Worker já definido em sua conta de automação. O computador pode ser adicionado a um grupo de executável de manual híbrido em sua conta de automação para oferecer suporte a registros de execução de automação, desde que você esteja usando a mesma conta para a solução e a associação de grupo de trabalhador de executável híbrido. Esta funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

Examine o [informações para planejar sua rede](#network-planning) antes de você começar a implantar um Hybrid Runbook Worker. Depois de implantar com êxito o trabalhador, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

O computador pode ser adicionado a um grupo de executável de manual híbrido em sua conta de automação para oferecer suporte a registros de execução de automação, desde que você esteja usando a mesma conta para a solução e a associação de grupo de trabalhador de executável híbrido. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.
## <a name="remove-a-hybrid-runbook-worker"></a>Remova um trabalhador de runbook híbrido

Você pode remover um ou mais trabalhadores de runbook híbridos de um grupo ou remover o grupo, dependendo dos seus requisitos. Para remover um Hybrid Runbook Worker de um computador local, use as seguintes etapas:

1. No portal do Azure, vá para sua conta de automação.
2. Em **Configurações da conta**, selecione **Chaves** e anote os valores para **URL** e **Chave de acesso primária**. Você precisará dessas informações para a próxima etapa.

### <a name="windows"></a>Windows

Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir ‑ . Use a opção **-Verbose** para obter um log detalhado do processo de remoção.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Para remover computadores antigos do grupo do Hybrid Worker, use o parâmetro opcional `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Você pode usar o comando `ls /var/opt/microsoft/omsagent` no Hybrid Runbook Worker para obter a workspaceid. Há uma pasta no diretório em que o nome da pasta é a ID do espaço de trabalho.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Este código não remove o Microsoft Monitoring Agent do computador, apenas a funcionalidade e a configuração da função Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo, você primeiro precisa remover o Hybrid Runbook Worker de cada computador que seja membro do grupo usando o procedimento mostrado anteriormente. Em seguida, execute as seguintes etapas para remover o grupo:

1. Abra a conta de Automação no Portal do Azure.
2. Em **Automação de Processo**, selecione **Grupos de trabalho híbrido**. Selecione o grupo que você deseja excluir. A página de propriedades desse grupo é exibida.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Excluir**. Uma mensagem pede para você confirmar esta ação. Selecione **Sim** se tiver certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Esse processo pode levar vários segundos para terminar. Você pode acompanhar o progresso em **Notificações** no menu.

## <a name="network-planning"></a>Configurar sua rede

### <a name="hybrid-worker-role"></a>Função de trabalhador híbrido

Para o Hybrid Runbook Worker conectar e registre-se com a automação do Azure, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. Esse acesso é na parte superior para o [portas e URLs necessárias para o Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) para conectar-se aos logs do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se você usar um servidor proxy para comunicação entre o agente e o serviço de automação do Azure, certifique-se de que os recursos apropriados estejam acessíveis. O tempo limite para solicitações de Hybrid Runbook Worker e os serviços de automação é de 30 segundos. Depois de 3 tentativas a solicitação falhará. Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso. Se você usar o gateway do Log Analytics como um proxy, verifique se ele está configurado para hybrid workers. Para obter instruções sobre como fazer isso, confira [Configurar o Gateway do Log Analytics para Hybrid Workers de Automação](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

A porta e URLs a seguir são necessárias para a função do Hybrid Runbook Worker se comunicar com a Automação do Azure:

* Porta: Somente a TCP 443 é necessária para acesso à Internet de saída.
* URL global: *.azure-automation.net
* URL global do EUA Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, baixe os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Oeste dos EUA 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Leste da Austrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.
>
> É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

### <a name="update-management"></a>Gerenciamento de atualizações

Na parte superior dos endereços padrão e portas que exige o Hybrid Runbook Worker, os endereços a seguir são necessários especificamente para Gerenciamento de Atualizações. A comunicação para esses endereços é feita pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para saber como solucionar problemas de seus trabalhadores com runbook híbrido, consulte [Solucionando problemas de trabalhadores com runbook híbrido](troubleshoot/hybrid-runbook-worker.md#general)

