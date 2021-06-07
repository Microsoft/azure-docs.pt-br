---
title: Configurar o Grafana para visualizar as métricas emitidas do Azure Instância Gerenciada para Apache Cassandra
description: Saiba como instalar e configurar o Grafana em uma VM para visualizar as métricas emitidas de um Instância Gerenciada do Azure para o cluster do Apache Cassandra.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744180"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Configurar o Grafana para visualizar as métricas emitidas do cluster de instância gerenciada

> [!IMPORTANT]
> A Instância Gerenciada do Azure para Apache Cassandra está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando você implanta um Instância Gerenciada do Azure para o cluster apache Cassandra, o serviço provisiona um servidor que hospeda [Prometheus](https://prometheus.io/) , que pode ser consumido por várias ferramentas de cliente. Prometheus é uma solução de monitoramento de código-fonte aberto. A instância gerenciada emitirá métricas e manterá 10 minutos ou 10 GB de dados (o limite atingir primeiro). Este artigo descreve como configurar o Grafana para visualizar as métricas emitidas do cluster de instância gerenciada. As seguintes tarefas são necessárias para visualizar as métricas:

* Implante uma máquina virtual Ubuntu dentro da rede virtual do Azure onde a instância gerenciada está presente.
* Instale a [ferramenta Grafana](https://grafana.com/grafana/) de código-fonte aberto para criar painéis e Visualizar métricas emitidas de Prometheus.

## <a name="deploy-a-ubuntu-server"></a>Implantar um servidor Ubuntu

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Navegue até o grupo de recursos em que o cluster de instância gerenciada está localizado. Selecione **Adicionar** e pesquise a imagem do **Ubuntu Server 18, 4 LTS** :

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Localize a imagem do servidor Ubuntu do portal do Azure." border="true":::

1. Escolha a imagem e selecione **criar**.

1. Na folha **criar uma máquina virtual** , insira valores para os campos a seguir, você pode deixar os valores padrão para outros campos:

   * **Nome da máquina virtual** – Insira um nome para a VM.
   * **Região** – selecione a mesma região em que sua rede virtual foi implantada.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Preencha o formulário para criar uma VM com a imagem do Ubuntu Server." border="true":::

1. Na guia **rede** , selecione o entrada na rede virtual que sua instância gerenciada está implantada:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Defina as configurações de rede do servidor Ubuntu." border="true":::

1. Por fim, selecione **revisar + criar** para criar o servidor Grafana.

## <a name="install-grafana"></a>Instalar o Grafana

1. No portal do Azure, abra a rede virtual onde você implantou a instância gerenciada e o servidor Grafana. Você deverá ver uma instância do conjunto de dimensionamento de máquinas virtuais denominada **Cassandra-salto (instância 0)**. Essas métricas de Prometheus estão hospedadas neste conjunto de dimensionamento de máquinas virtuais. Anote o endereço IP desta instância:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Obter o endereço IP da instância de Prometheus." border="true":::

1. Conecte-se ao seu servidor Ubuntu recém-criado usando [CLI do Azure](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) ou sua ferramenta de cliente preferida para se conectar via SSH.

1. Depois de se conectar à VM, você precisa instalar e configurar o Grafana para se conectar ao conjunto de dimensionamento de máquinas virtuais em que as métricas são hospedadas. Abra um prompt de comando e insira o `nano` comando para abrir um editor de texto do nano. Cole o script a seguir no editor de texto, certifique-se de substituir o `<prometheus IP address>` pelo endereço IP que você gravou na etapa anterior:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Digite `ctrl + X` para salvar o arquivo. Você pode nomear o arquivo `grafana.sh` .

1. Insira o `./grafana.sh` comando no prompt de comando para instalar o Grafana.

1. Após a conclusão da instalação, o Grafana estará disponível na **porta 3000** no endereço IP do servidor, conforme mostrado na seguinte captura de tela:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Execute Grafana na porta 3000." border="true":::

1. Você pode escolher entre os painéis de software livre criados para o Apache Cassandra no Grafana, como o arquivo JSON de [visão geral do cluster](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) . Baixe e importe a definição de JSON do painel em Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Importar definição de JSON Grafana." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Carregue a definição de JSON Grafana." border="true":::

1. Em seguida, você pode monitorar seu cluster de instância gerenciada do Cassandra com o painel escolhido:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Exiba as métricas de instância gerenciada do Cassandra no painel." border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar os painéis para visualizar as métricas no Prometheus usando o Grafana. Saiba mais sobre o Azure Instância Gerenciada para Apache Cassandra com os seguintes artigos:

* [Visão geral do Azure Instância Gerenciada para Apache Cassandra](introduction.md)
* [Implantar um cluster Apache Spark gerenciado com Azure Databricks (versão prévia)](deploy-cluster-databricks.md)
