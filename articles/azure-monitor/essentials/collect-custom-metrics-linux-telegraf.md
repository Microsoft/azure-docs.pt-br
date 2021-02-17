---
title: Coletar métricas personalizadas para VM Linux com o agente InfluxData Telegraf
description: Instruções sobre como implantar o agente InfluxData telegraf em uma VM do Linux no Azure e configurar o agente para publicar métricas para Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: fbed48dda3d6b022d30bb9fb0507c3f9f203771d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100604904"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Coletar métricas personalizadas para uma VM do Linux com o Agente InfluxData Telegraf

Usando o Azure Monitor, você pode coletar métricas personalizadas por meio de sua telemetria de aplicativo, um agente em execução em seus recursos do Azure ou até mesmo fora para dentro sistemas de monitoramento. Em seguida, você pode enviá-los diretamente para o Azure Monitor. Este artigo fornece instruções sobre como implantar o agente [InfluxData](https://www.influxdata.com/) Telegraf em uma VM do Linux no Azure e configurar o agente para publicar as métricas para o Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>Agente InfluxData Telegraf 

O [Telegraf](https://docs.influxdata.com/telegraf/) é um agente controlado por plug-in que permite a coleta de métricas de mais de 150 fontes diferentes. Dependendo de quais cargas de trabalho executam em sua VM, você pode configurar o agente para aproveitar plug-ins de entrada especializados em coletar métrica. Exemplos são o Apache, MySQL e NGINX. Ao usar o plug-ins de saída, o agente pode então escrever destinos que você escolhe. O agente Telegraf integrou-se diretamente à API REST de métricas personalizadas do Azure Monitor. Dá suporte a um plug-in de saída do Azure Monitor. Usar esse plug-in, o agente pode coletar métricas de carga de trabalho específica em sua VM do Linux e enviá-los em métricas personalizadas para o Azure Monitor. 

 ![Visão Geral do Agente Telegraf](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> Não há suporte para métricas personalizadas em todas as regiões. As regiões com suporte estão listadas [aqui](../platform/metrics-custom-overview.md#supported-regions)

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas 

Para este tutorial, podemos implantar uma VM do Linux que executa o sistema operacional Ubuntu 16.04 LTS. A maioria dos sistemas operacionais Linux tem suporte para o agente Telegraf. Os pacotes Debian e RPM estão disponíveis junto com binários descompactados do Linux no portal de download do [InfluxData](https://portal.influxdata.com/downloads). Consulte este [guia de instalação Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) para instruções de instalação adicionais e opções. 

Entre no [portal do Azure](https://portal.azure.com).

> [!NOTE]  
> Se você quiser migrar as regras de alerta clássicas e usar uma máquina virtual do Linux existente, verifique se o computador do virtual tem uma identidade atribuída pelo sistema definida como **ativada**.

Crie uma nova VM do Linux: 

1. Selecione a opção **criar um recurso** no painel de navegação à esquerda. 
1. Pesquise **Máquina Virtual**.  
1. Selecione **Ubuntu 16.04 LTS** e selecione **Criar**. 
1. Forneça um nome de VM como **MyTelegrafVM**.  
1. Deixe o tipo de disco como **SSD**. Em seguida, forneça um **nome de usuário**, como **azureuser**. 
1. Para **tipo de autenticação**, selecione **senha**. Insira uma senha que você usará posteriormente para SSH para essa VM. 
1. Escolha **criar novo grupo de recursos**. Em seguida, forneça um nome, como **MyResource**. Escolha seu **local**. Depois, selecione **OK**. 

    ![Criar uma VM do Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selecione um tamanho para a VM. Você pode filtrar por **Tipo de computação** ou **Tipo de disco**, por exemplo. 

    ![Visão Geral do Agente Telegraf para o tamanho da Máquina Virtual](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na página **configurações** , em grupo de segurança de rede **de rede**  >    >  ,**selecione portas de entrada públicas**, selecione **http** e **SSH (22)**. Deixe o restante dos padrões e selecione **OK**. 

1. Na página Resumo, selecione **Criar** para iniciar a implantação da máquina virtual. 

1. A VM será fixada ao painel do portal do Azure. Depois que a implantação for concluída, o resumo da VM abre automaticamente. 

1. No painel VM, navegue até a guia **identidade** . Verifique se sua VM tem uma identidade atribuída pelo sistema definida como **ativada**. 
 
    ![Visualização de identidade da VM Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Conectar-se à VM 

Crie uma conexão SSH com a VM. Selecione o botão **Conectar** na página visão geral da sua VM. 

![Página de visão geral de VM Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na página **Conectar-se à máquina virtual**, mantenha as opções padrão para conectar-se ao nome DNS pela porta 22. Em **logon usando a conta local da VM**, é mostrado um comando de conexão. Selecione o botão para copiar o comando. O exemplo a seguir mostra a aparência do comando de conexão SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Cole o comando de conexão SSH em um shell, como o Azure Cloud Shell ou Bash no Ubuntu no Windows, ou usar um cliente SSH de sua escolha para criar a conexão. 

## <a name="install-and-configure-telegraf"></a>Instalar e configurar o Telegraf 

Para instalar o pacote Telegraf Debian na VM, execute os seguintes comandos de sua sessão SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
O arquivo de configuração do Telegraf define as operações. Por padrão, um arquivo de configuração de exemplo é instalado no caminho **/etc/telegraf/telegraf.conf**. O arquivo de configuração de exemplo lista todos os plug-ins de entrada e saída possíveis. No entanto, vamos criar um arquivo de configuração personalizado e fazer com que o agente o use, executando os seguintes comandos: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> O código anterior permite somente dois plug-ins de entrada: **cpu** e **mem**. Você pode adicionar mais entradas plug-ins, dependendo da carga de trabalho que é executado em seu computador. Exemplos são o Docker, MySQL e NGINX. Para obter uma lista completa de entrada de plug-ins, consulte a seção **Adicionais de configuração**. 

Por fim, para que o agente comece a usar a nova configuração, nós o forçamos a parar e iniciar executando os comandos a seguir: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Agora o agente coletará as métricas de cada um dos plug-ins de entrada especificados e os emitirá para o Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Plotar as métricas do Telegraf no portal do Azure 

1. Abra o [Portal do Azure](https://portal.azure.com). 

1. Navegue até a nova guia **Monitor** . Em seguida, selecione **métricas**.  

     ![Monitor - métricas (visualização)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selecione sua VM no seletor de recursos.

     ![Gráfico de métrica](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selecione o namespace **Telegraf/CPU**, depois selecione a métrica **usage_system**. É possível optar por filtrar conforme as dimensões nessa métrica ou dividi-las.  

     ![Selecione o namespace e métrica](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configuração adicional 

O guia de explicação anterior fornece informações sobre como configurar o agente Telegraf para coletar métricas de alguns plug-ins de entrada básicos. O agente Telegraf tem suporte para mais de 150 plug-ins de entrada, com algumas opções de configuração adicionais de suporte. O InfluxData publicou uma [lista de plug-ins com suporte](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) e instruções sobre [como configurá-los](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/).  

Além disso, neste passo a passo, você usou o agente Telegraf para emitem métricas sobre o agente é implantado na VM. O agente Telegraf também pode ser usado como um coletor e encaminhador de métricas para outros recursos. Para saber como configurar o agente para emitir métricas para outros recursos do Azure, consulte [Saída de Métrica Personalizada do Azure Monitor para Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Limpar os recursos 

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos para a máquina virtual e selecione **excluir**. Em seguida, confirme o nome do grupo de recursos para excluir. 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](../platform/metrics-custom-overview.md).
