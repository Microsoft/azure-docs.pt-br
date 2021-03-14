---
title: Saiba mais sobre os dispositivos descobertos por todos os sensores empresariais
description: Use o inventário do dispositivo no console de gerenciamento local para obter uma visão abrangente das informações do dispositivo de sensores conectados. Use as ferramentas de importação, exportação e filtragem para gerenciar essas informações.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b4d824386a7c14165697f4b93f2e93cf0182a855
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524018"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Investigar todas as detecções do sensor Enterprise no inventário de dispositivos

Você pode exibir informações de dispositivo de sensores conectados usando o *inventário de dispositivos* no console de gerenciamento local. Esse recurso oferece uma visão abrangente de todas as informações de rede. Use as ferramentas de importação, exportação e filtragem para gerenciar essas informações. As informações de status sobre as versões de sensor conectadas também são exibidas.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Captura de tela da tabela de dados de inventário do dispositivo.":::

A tabela a seguir descreve as colunas de tabela no inventário de dispositivos.

| Parâmetro | Descrição |
|--|--|
| **Alertas não confirmados** | O número de alertas sem tratamento associados a este dispositivo. |
| **Unidade de negócios** | A unidade de negócios que contém este dispositivo. |
| **Região** | A região que contém este dispositivo. |
| **Site** | O site que contém este dispositivo. |
| **Zona** | A zona que contém este dispositivo. |
| **Dispositivo** | O sensor do Azure defender para IoT que protege este dispositivo. |
| **Nome** | O nome deste dispositivo como defender para IoT o descobriu. |
| **Tipo** | O tipo de dispositivo, como PLC ou HMI. |
| **Fornecedor** | O nome do fornecedor do dispositivo, conforme definido no endereço MAC. |
| **Sistema operacional** | O sistema operacional do dispositivo. |
| **Firmware** | O firmware do dispositivo. |
| **Endereço IP** | O endereço IP do dispositivo. |
| **VLAN** | A VLAN do dispositivo. |
| **Endereço MAC** | O endereço MAC do dispositivo. |
| **Protocolos** | Os protocolos que o dispositivo usa. |
| **Alertas não confirmados** | O número de alertas sem tratamento associados a este dispositivo. |
| **É autorizado** | O status de autorização do dispositivo:<br />- **True**: o dispositivo foi autorizado.<br />- **False**: o dispositivo não foi autorizado. |
| **É conhecido como scanner** | Se este dispositivo executa atividades do tipo verificação na rede. |
| **É dispositivo de programação** | Se este é um dispositivo de programação:<br />- **Verdadeiro**: o dispositivo executa atividades de programação para PLCs, RTUs e controladores, que são relevantes para estações de engenharia.<br />- **False**: o dispositivo não é um dispositivo de programação. |
| **Grupos** | Grupos nos quais este dispositivo participa. |
| **Última atividade** | A última atividade que o dispositivo realizou. |
| **Descoberto** | Quando este dispositivo foi visto pela primeira vez na rede. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrar dados ao inventário de dispositivos empresariais

Os recursos de integração de dados permitem aprimorar os dados no inventário de dispositivos com informações de outros recursos da empresa. Essas fontes incluem CMDBs, DNS, firewalls e APIs da Web.

Você pode usar essas informações para aprender. Por exemplo:

- Datas de compra do dispositivo e datas de fim de garantia

- Usuários responsáveis por cada dispositivo

- Tíquetes abertos para dispositivos

- A última data em que o firmware foi atualizado

- Dispositivos com permissão de acesso à Internet

- Dispositivos que executam aplicativos antivírus ativos

- Usuários conectados a dispositivos

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Tabela de dados na tela de inventário do dispositivo.":::

Você pode integrar os dados de uma das opções:

- Adicionando-o manualmente

- Executando scripts personalizados que o defender for IoT fornece

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagrama do integrador de dados corporativos.":::

Você pode trabalhar com o suporte técnico do defender para IoT para configurar seu sistema para receber consultas de API Web.

Para adicionar dados manualmente:

1. No menu lateral, selecione **inventário de dispositivo** e, em seguida, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Edite as configurações de inventário do seu dispositivo.":::

2. Na caixa de diálogo **configurações de inventário de dispositivo** , selecione **adicionar coluna personalizada**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Adicione uma coluna personalizada ao seu inventário.":::

3. Na caixa de diálogo **adicionar coluna personalizada** , adicione o novo nome de coluna (até 250 caracteres UTF), selecione **manual** e selecione **salvar**. O novo item é exibido na caixa de diálogo **configurações de inventário de dispositivo** .

4. No canto superior direito da janela de **inventário do dispositivo** , selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: e selecione **exportar todos os inventários de dispositivo**. O arquivo CSV é gerado.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="O arquivo CSV exportado.":::

5. Adicione manualmente as informações à nova coluna e salve o arquivo.

6. No canto superior direito da janela de **inventário do dispositivo** , selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: , selecione **importar colunas de entrada manuais** e navegue até o arquivo CSV. Os novos dados são exibidos na tabela de **inventário do dispositivo** .

Para integrar dados de outras entidades da empresa:

1. No canto superior direito da janela de **inventário do dispositivo** , selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: e selecione **exportar todos os inventários de dispositivo**.

2. Na caixa de diálogo **configurações de inventário de dispositivo** , selecione **adicionar coluna personalizada**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Adicione uma coluna personalizada ao seu inventário.":::

3. Na caixa de diálogo **adicionar coluna personalizada** , adicione o novo nome de coluna (até 250 caracteres UTF) e, em seguida, selecione **automático**. As opções **carregar script** e **testar script** são exibidas.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Adicionar colunas personalizadas automaticamente.":::

4. Carregue e teste o script que você recebeu do [suporte da Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="retrieve-information-from-the-device-inventory"></a>Recuperar informações do inventário de dispositivos

Você pode recuperar uma ampla gama de informações de dispositivo detectadas por sensores gerenciados e integrar essas informações com sistemas de parceiros. Por exemplo, você pode recuperar o sensor, a zona, a ID do site, o endereço IP, o endereço MAC, o firmware, o protocolo e as informações do fornecedor. Filtrar informações que você recupera com base em:

- Dispositivos autorizados e não autorizados.

- Dispositivos associados a sites específicos.

- Dispositivos associados a zonas específicas.

- Dispositivos associados a sensores específicos.

Trabalhe com os comandos do defender for IoT API para recuperar e integrar essas informações. Para obter mais informações, consulte [defender for IOT API sensor e APIs do console de gerenciamento](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtrar o inventário do dispositivo

Você pode filtrar o inventário do dispositivo para mostrar as colunas de interesse. Por exemplo, você pode exibir informações do dispositivo PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Captura de tela do inventário do dispositivo.":::

O filtro é limpo quando você sai da janela.

Para usar o mesmo filtro várias vezes, você pode salvar um filtro ou uma combinação de filtros necessários. Você pode abrir um painel esquerdo e exibir os filtros que você salvou:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Tela de inventários de dispositivo.":::

Para filtrar o inventário do dispositivo:

1. Na coluna que você deseja filtrar, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. Na caixa de diálogo **filtro** , selecione o tipo de filtro:

   - **Equals**: o valor exato de acordo com o qual você deseja filtrar a coluna. Por exemplo, se você filtrar a coluna de protocolo de acordo com **Equals** e `value=ICMP` , a coluna apresentará os dispositivos que usam apenas o protocolo ICMP.

   - **Contains**: o valor contido entre outros valores na coluna. Por exemplo, se você filtrar a coluna de protocolo de acordo com **Contains** e `value=ICMP` , a coluna apresentará dispositivos que usam o protocolo ICMP como parte da lista de protocolos que o dispositivo usa.

3. Para organizar as informações de coluna de acordo com a ordem alfabética, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Organize o pedido selecionando as :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: setas e.

4. Para salvar um novo filtro, defina o filtro e selecione **salvar como**.

5. Para alterar as definições de filtro, altere as definições e selecione **salvar alterações**.

## <a name="view-device-information-per-zone"></a>Exibir informações de dispositivo por zona

Você pode aprender as seguintes informações sobre os dispositivos em uma zona.

### <a name="view-a-device-map"></a>Exibir um mapa do dispositivo

Para exibir um mapa de dispositivo de sensor para uma zona selecionada:

- Na janela **Gerenciamento de site** , selecione **exibir mapa de zona** na barra que contém o nome da zona.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Região padrão para unidade de negócios padrão.":::

A janela **mapa do dispositivo** é exibida. Ele mostra todos os elementos de rede relacionados à zona selecionada, incluindo os sensores, os dispositivos conectados a eles e outras informações.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Captura de tela do mapa de zona.":::

As ferramentas a seguir estão disponíveis para exibir dispositivos e informações de dispositivo do mapa. Para obter detalhes sobre cada um desses recursos, consulte o *Guia do usuário da plataforma defender para IOT*.

- **Mapear exibições de zoom**: exibição simplificada, exibição de conexões e exibição detalhada. A exibição de mapa exibida varia dependendo do nível de zoom do mapa. Alterne entre exibições de mapa ajustando os níveis de zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Ferramentas de layout e pesquisa de mapa**: ferramentas usadas para exibir segmentos variados de rede, dispositivos, grupos de dispositivos ou camadas.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Captura de tela da exibição de ferramentas de pesquisa e layout.":::

- **Rótulos e indicadores em dispositivos:** Por exemplo, o número de dispositivos agrupados em uma sub-rede em uma rede de ti. Neste exemplo, é 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Captura de tela de rótulos e indicadores.":::

- **Exibir Propriedades do dispositivo**: por exemplo, o sensor que está monitorando o dispositivo e as propriedades básicas do dispositivo. Clique com o botão direito do mouse no dispositivo para exibir as propriedades do dispositivo.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Captura de tela da exibição Propriedades do dispositivo.":::

- **Alerta associado a um dispositivo:** Clique com o botão direito do mouse no dispositivo para exibir alertas relacionados.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Captura de tela da exibição mostrar alertas.":::

### <a name="view-alerts-associated-with-a-zone"></a>Exibir alertas associados a uma zona

Para exibir alertas associados a uma zona específica:

- Selecione o ícone de alerta formulário a janela **zona** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="O modo de exibição de unidade de negócios padrão com exemplos.":::

Para obter mais informações, consulte [visão geral: trabalhando com alertas](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Exibir o inventário de dispositivos de uma zona

Para exibir o inventário de dispositivos associado a uma zona específica:

- Selecione **Exibir inventário de dispositivo** na janela **zona** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="A tela de inventário do dispositivo será exibida.":::

Para obter mais informações, consulte [investigar todas as detecções do sensor empresarial em um inventário de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

### <a name="view-additional-zone-information"></a>Exibir informações de zona adicionais

As informações de zona adicionais a seguir estão disponíveis:

- **Detalhes da zona**: exiba o número de dispositivos, alertas e sensores associados à zona.

- **Detalhes do sensor**: exiba o nome, o endereço IP e a versão de cada sensor atribuído à zona.

- **Status de conectividade**: se um sensor for desconectado, conecte-se do sensor. Consulte [conectar sensores ao console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Progresso da atualização**: se o sensor conectado estiver sendo atualizado, o status da atualização será exibido. Durante a atualização, o console de gerenciamento local não recebe informações do dispositivo do sensor.

## <a name="next-steps"></a>Próximas etapas

[Investigar as detecções do sensor em um inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
