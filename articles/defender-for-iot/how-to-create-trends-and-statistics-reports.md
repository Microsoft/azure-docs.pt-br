---
title: Gerar relatórios de tendências e estatísticas
description: Conheça a atividade, as estatísticas e as tendências da rede usando o defender para tendências de IoT e os widgets de estatísticas.
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778994"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendências de sensor e relatórios de estatísticas

Você pode criar gráficos de widgets e gráficos de pizza para obter informações sobre as tendências e as estatísticas da rede. Os widgets podem ser agrupados em painéis definidos pelo usuário.

> [!NOTE]
> Os analistas de administrador e segurança podem criar relatórios de tendências e estatísticas.

O painel consiste em widgets que descrevem graficamente os seguintes tipos de informações:

- Tráfego por porta
- Tráfego principal por porta
- Largura de banda do canal
- Largura de banda total
- Conexão TCP ativa
- Largura de banda principal por VLAN
- Dispositivos:
  - Novos dispositivos
  - Dispositivos ocupados
  - Dispositivos por fornecedor
  - Dispositivos por sistema operacional
  - Número de dispositivos por VLAN
  - Dispositivos desconectados
- A conectividade cai por horas
- Alertas para incidentes por tipo
- Acesso à tabela do banco de dados
- Widgets de desseção de protocolo
- DELTAV
  - Distribuição de operações do DeltaV Roc
  - Eventos DeltaV Roc por nome
  - Eventos de DeltaV por tempo
- AMS
  - Tráfego AMS por porta do servidor
  - Tráfego AMS por comando
- Ethernet e endereço IP:
  - Tráfego de endereço IP e Ethernet pelo serviço CIP
  - Tráfego de endereço IP e Ethernet por classe CIP
  - Tráfego de endereço IP e Ethernet por comando
- OPC
  - Operações de gerenciamento superior do OPC
  - Operações de e/s superiores do OPC
- Siemens S7:
  - Tráfego S7 por função de controle
  - Tráfego S7 por subfunção
- VLAN
  - Número de dispositivos por VLAN
  - Largura de banda principal por VLAN
- 60870-5-104
  - Tráfego IEC-60870 por ASDU
- BACNET
  - Serviços BACnets
- DNP3
  - Tráfego DNP3 por função
- SRTP
  - Tráfego de SRTP por código de serviço
  - Erros de SRTP por dia
- SuiteLink:
  - SuiteLink principais marcas consultadas
  - Comportamento de marca numérica SuiteLink
- Tráfego IEC-60870 por ASDU
- Tráfego DNP3 por função
- Tráfego de MMS por serviço
- Tráfego Modbus por função
- Tráfego OPC-UA por serviço

> [!NOTE]
>  A hora nos widgets é definida de acordo com o tempo do sensor.

## <a name="create-reports"></a>Criar relatórios

Para ver os painéis e widgets:

Selecione **tendências & estatísticas** no menu lateral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Captura de tela de uma investigação.":::

Por padrão, os resultados são exibidos para detecções nos últimos sete dias. Você pode usar as ferramentas de filtro para alterar esse intervalo. Por exemplo, uma pesquisa de texto livre.

## <a name="create-a-dashboard"></a>Criar um painel

Crie um novo painel selecionando o menu suspenso **painel** . Você pode criar e adicionar quantos widgets a um Dashboard.

Você pode criar painéis personalizados usando as seguintes opções:

- Adicionar um widget ao painel

- Excluir um widget do painel

- Modificar o filtro de um widget

- Redimensionar um widget

- Alterar o local de um widget

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Alterar o local de um widget.":::

Para criar um painel personalizado:

1. Selecione **tendências e estatísticas** no painel esquerdo.

1. Selecione o menu suspenso **selecionar painel** e selecione o botão **criar painel** .

1. Insira um nome significativo para o novo painel e selecione **criar**.

1. Selecione **Adicionar widget** na parte superior esquerda da página.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selecione o widget no repositório de widget.":::

1. Os widgets de **segurança** e **operacionais** estão disponíveis no canto superior direito da janela. Escolha entre várias categorias e protocolos. Uma breve descrição com um gráfico em miniatura é exibida com cada widget. Use a barra de rolagem para ver todos os widgets disponíveis.

1. Selecione um widget usando o botão **clique para adicionar** . O widget é exibido imediatamente no painel.

Para excluir um painel:

1. Selecione o nome do painel no menu suspenso.

1. Selecione o ícone **excluir** e, em seguida, selecione **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Selecione o ícone Excluir para excluir o painel.":::

Para editar um nome de painel:

1. Selecione o nome do painel no menu suspenso.

1. Selecione o ícone **Editar** .
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Selecione o ícone Editar para editar o nome do seu painel.":::

1. Insira um novo nome para o painel e selecione **salvar**.
 
Para definir o painel padrão:

1. Selecione o nome do painel no menu suspenso.

1. Selecione o ícone de **estrela** para selecionar o painel a ser definido como o painel padrão.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Selecione o ícone de estrela para escolher o painel padrão."::: 

Para modificar os dados de filtragem em um widget:

1. Selecione o ícone de **filtro** .

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Selecione o ícone de filtro para definir parâmetros para o widget.":::

1. Edite os parâmetros necessários.

1. Selecione **OK**.

Para excluir um widget:

- Selecione o :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: ícone.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Selecione o X para excluir o widget.":::

## <a name="creating-widgets"></a>Criando widgets 

O repositório de widget permite que você selecione widgets por categoria e protocolo. Você pode exibir a **segurança** ou os widgets **operacionais** disponíveis selecionando-os.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selecione o widget no repositório de widget.":::

Cada widget contém informações específicas sobre o tráfego do sistema, estatísticas de rede, estatísticas de protocolo, dispositivo e informações de alerta. Uma mensagem é exibida quando não há dados para um widget.

Você pode remover uma seção da pizza, em um gráfico de pizza, para ver a significância relativa das fatias restantes mais claramente. Selecione o nome da fatia na legenda na parte inferior da tela para fazer isso.

As seções a seguir apresentam exemplos de casos de uso para alguns dos widgets.

### <a name="busy-devices-widget"></a>Widget dispositivos ocupados

Esse widget lista os cinco principais dispositivos mais ocupados. No modo de **edição** , você pode filtrar por protocolos conhecidos.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Uma exibição do widget dispositivo ocupado.":::

### <a name="total-bandwidth-widget"></a>Widget de largura de banda total

Esse widget rastreia a largura de banda em Mbps (megabits por segundo). A largura de banda é indicada no eixo y, com a data que aparece no eixo x. O modo de **edição** permite filtrar os resultados de acordo com o cliente, servidor, porta do servidor ou sub-rede. Uma dica de ferramenta é exibida quando você passa o cursor sobre o gráfico.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Uma exibição do widget largura de banda total.":::

### <a name="channels-bandwidth-widget"></a>Widget largura de banda dos canais

Esse widget exibe os cinco principais canais de tráfego. Você pode filtrar por endereço e definir o número de resultados apresentados. Selecione a seta para baixo para mostrar mais canais.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Uma exibição do widget de largura de banda dos canais.":::

### <a name="traffic-by-port-widget"></a>Tráfego por widget de porta

Esse widget exibe o tráfego por porta, que é indicado por um gráfico de pizza com cada porta designada por uma cor diferente. A quantidade de tráfego em cada porta é proporcional ao tamanho de sua parte da pizza.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Uma exibição do widget tráfego por porta.":::

### <a name="new-devices-widget"></a>Widget novos dispositivos

Esse widget exibe o novo gráfico de barras de dispositivos, que indica quantos novos dispositivos foram descobertos em uma data específica.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Uma exibição do widget novos dispositivos.":::

### <a name="protocol-dissection-widgets"></a>Widgets de desseção de protocolo

Esse widget exibe um gráfico de pizza que fornece uma olhada no tráfego por protocolo, disparado por códigos de função e serviços. O tamanho de cada fatia da pizza é proporcional à quantidade de tráfego em relação às outras fatias.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Uma exibição do widget de desseção de protocolo.":::

### <a name="active-tcp-connections-widget"></a>Widget conexões TCP ativas

Esse widget exibe um gráfico que mostra o número de conexões TCP ativas no sistema.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Uma exibição do widget conexões TCP ativas.":::

### <a name="incident-by-type-widget"></a>Widget incidente por tipo

Esse widget exibe um gráfico de pizza que mostra o número de incidentes por tipo. Este é o número de alertas gerados por cada mecanismo durante um período de tempo predefinido.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Uma exibição do widget incidente por tipo.":::

## <a name="devices-by-vendor-widget"></a>Dispositivos por widget de fornecedor

Esse widget exibe um gráfico de pizza que mostra o número de dispositivos por fornecedor. O número de dispositivos para um fornecedor específico é proporcional ao tamanho da parte do fornecedor do dispositivo do disco em relação a outros fornecedores de dispositivo.

## <a name="number-of-devices-per-vlan-widget"></a>Número de dispositivos por widget de VLAN

Esse widget exibe um gráfico de pizza que mostra o número de dispositivos descobertos por VLAN. O tamanho de cada fatia da pizza é proporcional ao número de dispositivos descobertos em relação às outras fatias.

Cada VLAN aparece com a marca VLAN atribuída pelo sensor ou pelo nome que você adicionou manualmente.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Uma exibição do widget número de dispositivos.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Largura de banda principal por widget de VLAN

Esse widget exibe o consumo de largura de banda por VLAN. Por padrão, o widget mostra cinco VLANs com o maior uso de largura de banda.

Você pode filtrar os dados pelo período apresentado no widget. Selecione a seta para baixo para mostrar mais resultados.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Uma exibição da largura de banda principal por widget de VLAN.":::

## <a name="system-report"></a>Relatório do sistema

Para baixar o relatório do sistema: 

1. Selecione **tendências & estatísticas** no menu lateral.

1. Selecione **relatório do sistema** no canto superior direito. O relatório será baixado automaticamente.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Selecione o botão relatório do sistema para baixar uma cópia do relatório do sistema.":::

O relatório do sistema é um arquivo PDF que contém todos os dados no sistema:

  - Dispositivos

  - Alertas

  - Informações de política de rede

## <a name="devices-in-a-system-report"></a>Dispositivos em um relatório do sistema 

O relatório do sistema mostra uma lista de todos os dispositivos e suas informações. Por exemplo, tipo, nome e protocolos usados. O relatório do sistema também mostra uma lista de dispositivos por fornecedor.

## <a name="alerts-in-system-report"></a>Alertas no relatório do sistema 

O relatório do sistema mostra uma lista de todos os alertas com suas informações, como data e severidade.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Uma exibição dos alertas nos relatórios do sistema.":::

## <a name="network-information-in-system-report"></a>Informações de rede no relatório do sistema 

O relatório do sistema é mostrado em detalhes, sua linha de base de rede. Por exemplo, código de função DNP3 e portas abertas por conexão.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Uma exibição da função DNP3 do relatório do sistema.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Uma exibição do relatório porta aberta por conexão.":::

## <a name="see-also"></a>Veja também

Relatórios de avaliação de [risco](how-to-create-risk-assessment-reports.md) 
 Consultas de Data Mining de [sensor](how-to-create-data-mining-queries.md) 
 [Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)
