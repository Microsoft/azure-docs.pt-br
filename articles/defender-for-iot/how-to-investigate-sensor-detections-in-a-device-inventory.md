---
title: Obter informações sobre os dispositivos descobertos por um sensor específico
description: O inventário de dispositivos exibe uma ampla gama de atributos de dispositivo que um sensor detecta.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9dc6849c1b2ad5daa6142e894e36f351663ff9bd
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523984"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Investigar as detecções do sensor em um inventário de dispositivos

O inventário de dispositivos exibe uma ampla gama de atributos de dispositivo que um sensor detecta. As opções estão disponíveis para:

 - Filtre facilmente as informações.

 - Exportar informações para um arquivo CSV.

 - Importar detalhes do registro do Windows.

 - Crie grupos para exibição no mapa do dispositivo.

## <a name="view-device-attributes-in-the-device-inventory"></a>Exibir atributos de dispositivo no inventário de dispositivos

Os atributos a seguir aparecem na tabela de inventário do dispositivo.

| Parâmetro | Descrição |
|--|--|
| Nome | O nome do dispositivo como o sensor o descobriu, ou conforme inserido pelo usuário. |
| Type | O tipo de dispositivo, conforme determinado pelo sensor, ou conforme inserido pelo usuário. |
| Fornecedor | O nome do fornecedor do dispositivo, conforme definido no endereço MAC. |
| Sistema operacional | O sistema operacional do dispositivo, se detectado. |
| Versão do firmware | O firmware do dispositivo, se detectado. |
| Endereço IP | O endereço IP do dispositivo onde definido. |
| VLAN | A VLAN do dispositivo. Para obter detalhes sobre como instruir o sensor a descobrir VLANs, consulte [definir nomes de VLAN](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (instruções-definir-Management-Console-Network-Settings. MD # define-VLAN-Names). |
| Endereço MAC | O endereço MAC do dispositivo. |
| Protocolos | Os protocolos que o dispositivo usa. |
| Alertas não confirmados | O número de alertas não confirmados associados a este dispositivo. |
| É autorizado | O status de autorização definido pelo usuário:<br />- **True**: o dispositivo foi autorizado.<br />- **False**: o dispositivo não foi autorizado. |
| É conhecido como scanner | Definido como um dispositivo de verificação de rede pelo usuário. |
| É dispositivo de programação | Definido como um dispositivo de programação autorizado pelo usuário. <br />- **Verdadeiro**: o dispositivo executa atividades de programação para PLCs, RTUs e controladores, que são relevantes para estações de engenharia. <br />- **False**: o dispositivo não é um dispositivo de programação. |
| Grupos | Os grupos dos quais este dispositivo participa. |
| Última atividade | A última atividade que o dispositivo realizou. |
| Descoberto | Quando este dispositivo foi visto pela primeira vez na rede. |

Para exibir o inventário do dispositivo:

1. No painel esquerdo, selecione **dispositivos**. O painel **dispositivos** é aberto à direita.

2. No painel **dispositivos** , selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Para ocultar e exibir colunas, personalize a tabela de inventário do dispositivo:

1. No menu superior direito do inventário de dispositivos, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Tela de configurações de inventário de dispositivo.":::

2. Na janela **configurações de inventário do dispositivo** , selecione as colunas que você deseja exibir na tabela de inventário do dispositivo.

3. Altere o local das colunas na tabela usando setas.

4. Selecione **Salvar**. A janela **configurações de inventário de dispositivo** é fechada e as novas configurações aparecem na tabela.

### <a name="create-temporary-device-inventory-filters"></a>Criar filtros de inventário de dispositivo temporários

Você pode definir um filtro que define as informações que a tabela exibe. Por exemplo, você pode decidir que deseja exibir apenas as informações do dispositivo PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Dispositivos Learning.":::

O filtro não é salvo quando você sai do inventário.

### <a name="save-device-inventory-filters"></a>Salvar filtros de inventário de dispositivo

Você pode salvar um filtro ou uma combinação de filtros necessários e reaplicá-los no inventário do dispositivo. Crie filtros mais amplos com base em um determinado tipo de dispositivo ou filtros mais estreitos com base em um tipo específico e um protocolo específico.

Os filtros salvos também são salvos como grupos de mapas de dispositivo. Esse recurso fornece um nível adicional de granularidade na exibição de dispositivos de rede no mapa.

Para criar filtros:

1. Na coluna que você deseja filtrar, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. Na caixa de diálogo **filtro** , selecione o tipo de filtro:

   - **Equals**: o valor exato de acordo com o qual você deseja filtrar a coluna. Por exemplo, se você filtrar a coluna de protocolo de acordo com **Equals** e `value=ICMP` , a coluna apresentará os dispositivos que usam apenas o protocolo ICMP.

   - **Contains**: o valor contido entre outros valores na coluna. Por exemplo, se você filtrar a coluna de protocolo de acordo com **Contains** e `value=ICMP` , a coluna apresentará dispositivos que usam o protocolo ICMP como parte da lista de protocolos que o dispositivo usa.

3. Para organizar as informações de coluna de acordo com a ordem alfabética, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Organize o pedido selecionando as :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: setas e.

4. Para salvar um novo filtro, defina o filtro e selecione **salvar como**.

5. Para alterar as definições de filtro, altere as definições e selecione **salvar alterações**.

Para exibir filtros:

- Abra o painel esquerdo e exiba os filtros que você salvou:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Exiba os filtros no painel esquerdo.":::

### <a name="view-filtered-information-as-a-map-group"></a>Exibir informações filtradas como um grupo de mapas

Quando você alterna para o modo de exibição de mapa, os dispositivos filtrados são realçados e filtrados. O grupo de filtros que você salvou aparece no menu lateral no grupo **filtros de inventário de dispositivo** .

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Exiba filtros quando estiver na exibição de mapa.":::

## <a name="learn-windows-registry-details"></a>Saiba mais detalhes do registro do Windows

Além de aprender os dispositivos, você pode descobrir estações de trabalho e servidores do Microsoft Windows. Esses dispositivos também são exibidos no inventário de dispositivos. Depois de aprender os dispositivos, você pode enriquecer o inventário do dispositivo com informações detalhadas do Windows, como:

- Versão do Windows instalada

- Aplicativos instalados

- Informações de nível de patch

- Abrir portas

- Informações mais robustas sobre versões do sistema operacional

Duas opções estão disponíveis para recuperar essas informações:

- Sondagem ativa usando verificações WMI agendadas. 

- Pesquisa local por meio da distribuição e execução de um script no dispositivo. Trabalhar com scripts locais ignora os riscos de executar sondagem WMI em um ponto de extremidade. Também é útil para redes regulamentadas com cascata e elementos unidirecionais.

Este artigo descreve como Pesquisar localmente o registro de ponto de extremidade do Windows com um script. Essas informações serão usadas para gerar alertas, notificações, Data Mining relatórios, avaliações de risco e relatórios de vetor de ataque.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Captura de tela de mineração de dados.":::

Você pode pesquisar os seguintes sistemas operacionais Windows:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Antes de começar

Para trabalhar com o script, você precisa atender aos seguintes requisitos:

- São necessárias permissões de administrador para executar o script no dispositivo.

- O sensor já deve ter aprendido o dispositivo Windows. Isso significa que, se o dispositivo já existir, o script recuperará suas informações.

- Um sensor está monitorando a rede à qual o computador Windows está conectado.

### <a name="acquire-the-script"></a>Adquirir o script

Para receber o script, [entre em contato com o atendimento ao cliente](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>Implantar o script

Você pode implantar o script uma vez ou agendar consultas em andamento usando ferramentas e métodos de implantação automatizados padrão.

### <a name="about-the-script"></a>Sobre o script

- O script é executado como um utilitário e não como um programa instalado. A execução do script não afeta o ponto de extremidade.

- Os arquivos que o script gera permanecem na unidade local até que você os exclua.

- Os arquivos que o script gera estão localizados ao lado uns dos outros. Não os separe.

- Se você executar o script novamente no mesmo local, esses arquivos serão substituídos.

Para executar o script:  

1. Copie o script em uma unidade local e descompacte-o. Os seguintes arquivos são exibidos:

    - start.bat

    - settings.jsem

    - Data. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Exibição dos arquivos no explorador de arquivos.":::

2. Execute o `run.bat` arquivo.

3. Depois que o registro é investigado, o arquivo CX-snapshot é exibido com as informações do registro.

4. O nome do arquivo indica o nome do sistema e a data e hora do instantâneo. Um exemplo de nome de arquivo é `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Importar detalhes do dispositivo

As informações aprendidas em cada ponto de extremidade devem ser importadas para o sensor.

Os arquivos gerados por meio das consultas podem ser colocados em uma pasta que você pode acessar de sensores. Use ferramentas e métodos padrão e automatizados para mover os arquivos de cada ponto de extremidade do Windows para o local onde você irá importá-los para o sensor.

Não atualizar nomes de arquivo.

Para importar:

1. Selecione **Importar configurações** na caixa de diálogo **importar configuração do Windows** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importe suas configurações do Windows.":::

2. Selecione **Adicionar** e, em seguida, selecione todos os arquivos (Ctrl + A).

3. Selecione **Fechar**. As informações de registro do dispositivo são importadas. Se houver um problema ao carregar um dos arquivos, você será informado de que o upload do arquivo falhou.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="O carregamento de arquivos adicionados foi bem-sucedido.":::

## <a name="export-device-inventory-information"></a>Exportar informações de inventário do dispositivo

Você pode exportar informações de inventário de dispositivo para um arquivo do Excel.

Para exportar um arquivo CSV:

- No menu superior direito do inventário de dispositivos, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . O relatório CSV é gerado e baixado.

## <a name="next-steps"></a>Próximas etapas

[Investigar todas as detecções do sensor corporativo em um inventário de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Trabalhar com exibições de mapa do site](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
