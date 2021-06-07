---
title: Trabalhar com o mapa do dispositivo do sensor
description: O mapa do dispositivo fornece uma representação gráfica dos dispositivos de rede detectados. Use o mapa para analisar e gerenciar informações de dispositivo, fatias de rede e gerar relatórios.
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: f7579cbca618baef404236556993c9831dd84bdf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784587"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Investigar as detecções do sensor no mapa do dispositivo

O mapa do dispositivo fornece uma representação gráfica dos dispositivos de rede detectados. Use o mapa para:

  - Recuperar, analisar e gerenciar informações do dispositivo.

  - Analise fatias de rede, por exemplo, grupos específicos de camadas de interesse ou Purdue.

  - Gere relatórios, por exemplo, exportar detalhes e resumos do dispositivo.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Captura de tela do mapa do dispositivo.":::

Para acessar o mapa:

  - Selecione **mapa do dispositivo** na tela principal do console.

## <a name="map-search-and-layout-tools"></a>Ferramentas de layout e pesquisa de mapa

As ferramentas a seguir são usadas para trabalhar no mapa.

Sua função de usuário determina quais ferramentas estão disponíveis na janela do mapa do dispositivo. Consulte [criar e gerenciar usuários](how-to-create-and-manage-users.md) para obter detalhes sobre as funções de usuário.

| Símbolo | Descrição |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Pesquise por endereço IP ou endereço MAC para um dispositivo específico. Insira o endereço IP ou o endereço MAC na caixa de texto. O mapa exibe o dispositivo que você pesquisou com dispositivos conectados a ele. |
| Realce e filtros de grupo <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Captura de tela dos realces e filtros do grupo."::: | Filtre ou realce o mapa com base em grupos de dispositivos padrão e personalizados. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Ele recolhe o modo de exibição, para habilitar uma exibição focada em OT dispositivos e agrupar dispositivos de ti.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Manter a organização do dispositivo atual no mapa. Por exemplo, se você arrastar dispositivos para novos locais no mapa, os dispositivos permanecerão nesses locais ao sair do mapa. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Ajustar à tela |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -Exibir a camada Purdue identificada para este dispositivo, incluindo automático, controle de processo, supervisão e empresa <br /> -Exibir conexões entre dispositivos.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Mostrar ou ocultar entre difusão e multicast. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filtre os dispositivos no mapa de acordo com o tempo que eles se comunicam pela última vez com outros dispositivos. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="notificações" border="false"::: | Exibir notificações sobre um dispositivo. Por exemplo, se um novo IP foi detectado para um dispositivo usando um endereço MAC existente |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Exportar" border="false"::: | Exportar/importar informações do dispositivo. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | Exiba as propriedades básicas do dispositivo para os dispositivos selecionados. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Ampliar" border="false"::: ou :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="reduzir" border="false"::: | Amplie ou reduza os dispositivos no mapa. |

## <a name="view-ot-elements-only"></a>Exibir apenas elementos de OT

Por padrão, os dispositivos de ti são agregados automaticamente por sub-rede, de modo que a exibição do mapa se concentra em redes ICS e de OT. A apresentação dos elementos de rede de ti é recolhida para um mínimo, o que reduz o número total de dispositivos apresentados no mapa e fornece uma visão clara dos elementos de rede de OT e ICS.

Cada sub-rede é apresentada como uma única entidade no mapa do dispositivo, incluindo um recurso interativo de recolhimento e expansão para examinar os detalhes de uma sub-rede de ti e de volta.

A figura a seguir mostra uma sub-rede de ti recolhida com 27 elementos de rede de ti.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="sub-rede de ti recolhida com 27 elementos de rede de ti":::

Para habilitar o recurso de recolhimento de redes de ti:

- Na janela **configurações do sistema** , verifique se a funcionalidade alternar agrupamento de redes de ti está habilitada.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Janela de configuração do sistema":::

Para expandir uma sub-rede de ti:

1. Para diferenciar entre as redes IT e OT, na tela Configurações do sistema, selecione **sub-redes**.

   > [!NOTE]
   > É recomendável nomear cada sub-rede com nomes significativos no usuário para identificar facilmente para diferenciar entre ela e as redes.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Configuração de sub-redes":::

2. Na janela **Editar sub-redes configuração** , desmarque a caixa de seleção **sub-rede do ICS** para cada sub-rede que você deseja definir como uma sub-rede de ti. As sub-redes de ti aparecem recolhidas no mapa do dispositivo com as notificações para dispositivos ICS, como um controlador ou PLC, em redes de ti.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Editar configuração de sub-redes":::

3. Para expandir a rede de ti no mapa, na janela dispositivos, clique com o botão direito do mouse e selecione **expandir rede**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Expanda sua exibição da sua rede.":::

4. Uma caixa de confirmação é exibida, notificando que a alteração de layout não pode ser refeita.

5. Selecione **OK**. Os elementos da sub-rede de ti aparecem no mapa.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Para recolher uma sub-rede de ti:

1.  No painel esquerdo, selecione **dispositivos**.

2. Na janela dispositivos, selecione o ícone recolher. O número em vermelho indica quantas sub-redes de ti expandidas aparecem atualmente no mapa.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Janela Dispositivo":::

3. Selecione as sub-redes que você deseja recolher ou selecione **recolher tudo**. A sub-rede selecionada aparece recolhida no mapa.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Recolher Tudo":::

O ícone recolher é atualizado com o número atualizado de sub-redes de ti expandidas.

## <a name="view-or-highlight-device-groups"></a>Exibir ou realçar grupos de dispositivos

Você pode personalizar a exibição do mapa com base em grupos de dispositivos. Por exemplo, grupos de dispositivos associados a um protocolo, VLAN ou sub-rede específico. Os grupos predefinidos estão disponíveis e os grupos personalizados podem ser criados.

Exibir grupos por:

  - **Realce:** Realce os dispositivos que pertencem a um grupo específico em azul.

  - **Filtragem:** Exibe somente os dispositivos que pertencem a um grupo específico.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Modo de exibição padrão de sua porta":::

Os seguintes grupos predefinidos estão disponíveis:

| Nome do grupo | Descrição |
|--|--|
| **Aplicativos conhecidos** | Dispositivos que usam portas reservadas, como TCP.  |
| **portas não padrão (padrão)** | Dispositivos que usam portas não padrão ou portas que não foram atribuídos a um alias. |
| **Protocolos de OT (padrão)** | Dispositivos que manipulam o tráfego de OT. |
| **Autorização (padrão)** | Dispositivos que foram descobertos na rede durante o processo de aprendizado ou que foram oficialmente autorizados na rede. |
| **Filtros de inventário de dispositivo** | Dispositivos agrupados de acordo com os filtros salvos na tabela de inventário do dispositivo. |
| **Intervalos de sondagem** | Dispositivos agrupados por intervalos de sondagem. Os intervalos de sondagem são gerados automaticamente de acordo com os canais cíclicos ou pontos. Por exemplo, 15,0 segundos, 3,0 segundos, 1,5 segundos ou qualquer intervalo. A revisão dessas informações ajuda você a aprender se os sistemas estão sondando com muita rapidez ou lentidão. |
| **Programação** | Estações de engenharia e máquinas de programação. |
| **Sub-redes** | Dispositivos que pertencem a uma sub-rede específica. |
| **VLAN** | Dispositivos associados a uma ID de VLAN específica. |
| **Conexões entre sub-redes** | Dispositivos que se comunicam de uma sub-rede a outra sub-rede. |
| **Alertas fixados** | Dispositivos para os quais o usuário fixa um alerta. |
| **Simulações de vetor de ataque** | Dispositivos vulneráveis detectados em relatórios de vetor de ataque. Para exibir esses dispositivos no mapa, marque a caixa de seleção **Exibir no mapa do dispositivo** ao gerar o vetor de ataque. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Adicionar simulações de vetor de ataque":::. |
| **Última visualização** | Dispositivos agrupados pelo período em que foram vistos pela última vez, por exemplo: uma hora, seis horas, um dia, sete dias. |
| **Não está em Active Directory** | Todos os dispositivos não PLC que não estão se comunicando com o Active Directory. |

Para realçar ou filtrar dispositivos:

1. Selecione **mapa do dispositivo** no menu lateral.

2. Selecione o ícone de filtro. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menu":::

3. No painel grupos, selecione o grupo que você deseja realçar ou filtrar dispositivos.

4. Selecione **realçar** ou **Filtrar**. Alterne a mesma seleção para remover o realce ou o filtro.

## <a name="define-custom-groups"></a>Definir grupos personalizados

Além de exibir grupos predefinidos, você pode definir grupos personalizados. Os grupos aparecem no mapa do dispositivo, no inventário de dispositivos e nos relatórios de mineração de dados.

> [!NOTE]
> Você também pode criar grupos do inventário de dispositivos.

Para criar um grupo:

1. Selecione **dispositivos** no menu lateral. O mapa do dispositivo é exibido.

1. Selecione :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="configuração de grupo"::: para exibir as configurações de grupos.

1. Selecione :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="grupos"::: para criar um novo grupo personalizado.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Criar uma tela de grupo personalizada":::

1. Adicione o nome do grupo, use até 30 caracteres.

1. Selecione os dispositivos relevantes, da seguinte maneira:

   - Adicione os dispositivos desse menu selecionando-os na lista (selecione no botão de seta),<br /> Ou, <br /> 
   - Adicione os dispositivos desse menu copiando-os de um grupo selecionado (selecione no botão de seta)

1. Selecione **Adicionar grupo** para adicionar grupos existentes a grupos personalizados.

### <a name="add-devices-to-a-custom-group"></a>Adicionar dispositivos a um grupo personalizado

Você pode adicionar dispositivos a um grupo personalizado ou criar um novo grupo personalizado e o dispositivo.

1. Clique com o botão direito do mouse em um ou mais dispositivos no mapa.

1. Selecione **Adicionar ao grupo**.

1. Insira um nome de grupo no campo grupo e selecione +. O novo grupo é exibido. Se o grupo já existir, ele será adicionado ao grupo personalizado existente.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Nome do grupo":::

1. Adicione dispositivos a um grupo repetindo as etapas de 1-3.

## <a name="map-zoom-views"></a>Mapear exibições de zoom

Trabalhar com exibições de mapa ajuda a acelerar a análise forense ao analisar redes grandes.

Três exibições de detalhes do dispositivo podem ser exibidas:

  - [Visão dos olhos da pássaro](#birds-eye-view)

  - [Tipo de dispositivo e exibição de conexão](#device-type-and-connection-view)

  - [Exibição detalhada](#detailed-view)

### <a name="birds-eye-view"></a>Visão dos olhos da pássaro

Essa exibição fornece uma visão geral dos dispositivos representados da seguinte maneira:

  - Pontos vermelhos indicam dispositivos com alerta (s)

  - Pontos estrelado indicam os dispositivos marcados como importantes

  - Pontos pretos indicam dispositivos sem alertas

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Exibição de olho de pássaro":::

### <a name="device-type-and-connection-view"></a>Tipo de dispositivo e exibição de conexão 

Essa exibição apresenta dispositivos representados como ícones no mapa para realçar dispositivos com alertas, tipos de dispositivo e dispositivos conectados.

  - Dispositivos com alertas são exibidos com um anel vermelho

  - Dispositivos sem alertas são exibidos com um anel cinza

  - Os dispositivos exibidos como uma estrela foram marcados como importantes

O ícone do tipo de dispositivo é mostrado com dispositivos conectados.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="exibição de conexão":::

### <a name="detailed-view"></a>Exibição detalhada 

A exibição detalhada apresenta dispositivos e rótulos de dispositivo e indicadores com as seguintes informações:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Exibição detalhada":::

### <a name="control-the-zoom-view"></a>Controlar a exibição de zoom

A exibição de mapa exibida depende do nível de zoom do mapa. Alternar entre os modos de exibição de mapa é feito alterando os níveis de zoom.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Controlar a exibição de zoom":::

### <a name="enable-simplified-zoom-views"></a>Habilitar exibições de zoom simplificadas

Os administradores que desejam que os analistas de segurança e os usuários do RO acessem as exibições de conexão de dispositivo e tipo de pássaro, devem habilitar a opção de exibição simplificada.

Para habilitar exibições de mapa simplificadas:

  - Selecione **configurações do sistema** e, em seguida, alterne a opção de **exibição do mapa simplificado** .

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Simplificar a exibição do mapa":::

## <a name="learn-more-about-devices"></a>Saiba mais sobre os dispositivos

Há uma ampla gama de ferramentas disponíveis para saber mais sobre os dispositivos que formam o mapa do dispositivo:

- [Indicadores e rótulos de dispositivo](#device-labels-and-indicators)

- [Exibições rápidas do dispositivo](#device-quick-views)

- [Exibir e gerenciar Propriedades de dispositivo](#view-and-manage-device-properties)

- [Exibir tipos de dispositivo](#view-device-types)

- [Backplane](#backplane-properties)

- [Exibir uma linha do tempo de eventos para o dispositivo](#view-a-timeline-of-events-for-the-device)

- [Analisar detalhes e alterações de programação](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Indicadores e rótulos de dispositivo

Os seguintes rótulos e indicadores podem aparecer em dispositivos no mapa:

| Rótulo do dispositivo | Descrição |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="Nome do host IP"::: | Endereço IP nome do host e endereço IP ou endereços de sub-rede |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Número de alertas"::: | Número de alertas associados ao dispositivo |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Ícone de tipo de dispositivo, por exemplo, armazenamento, PLC ou Historian. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="dispositivos agrupados"::: | Número de dispositivos agrupados em uma sub-rede em uma rede de ti. Neste exemplo 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="período de aprendizagem do dispositivo"::: | Um dispositivo que foi detectado após o período de aprendizagem e não foi autorizado como um dispositivo de rede. |
| Linha sólida | Conexão lógica entre dispositivos |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Novo dispositivo"::: | Novo dispositivo descoberto após o aprendizado ser concluído. |

### <a name="device-quick-views"></a>Exibições rápidas do dispositivo

Acesse as propriedades e conexões do dispositivo do mapa.

Para abrir o menu de Propriedades rápidas:

  - Selecione :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="o menu Propriedades rápidas.":::

#### <a name="quick-device-properties"></a>Propriedades rápidas do dispositivo

Selecione um dispositivo ou vários dispositivos enquanto a tela de Propriedades rápidas estiver aberta para ver os destaques desses dispositivos:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Propriedades rápidas do dispositivo":::

#### <a name="quick-connection-properties"></a>Propriedades de conexão rápida

Selecione uma conexão enquanto a tela Propriedades rápidas estiver aberta para ver os protocolos que são utilizados nesta conexão e quando eles foram vistos pela última vez:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Propriedades de conexão rápida":::

## <a name="view-and-manage-device-properties"></a>Exibir e gerenciar Propriedades de dispositivo

Você pode exibir dispositivos Propriedades para cada dispositivo exibido no mapa. Por exemplo, o nome do dispositivo, o tipo ou o sistema operacional ou o firmware ou o fornecedor.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Exibir e gerenciar Propriedades de dispositivo":::

As informações a seguir podem ser atualizadas manualmente. As informações inseridas manualmente substituirão as informações descobertas pelo defender para IoT.

  - Nome

  - Type

  - Sistema operacional

  - Camada Purdue

  - Descrição

| Item | Descrição |
|--|--|
| Informações Básicas | As informações básicas necessárias. |
| Nome | O nome do dispositivo. <br /> Por padrão, o sensor descobre o nome do dispositivo como definido na rede. Por exemplo, um nome definido no servidor DNS. <br /> Se esses nomes não tiverem sido definidos, o endereço IP do dispositivo aparecerá nesse campo. <br /> Você pode alterar um nome de dispositivo manualmente. Dê aos seus dispositivos nomes significativos que reflitam sua funcionalidade. |
| Type | O tipo de dispositivo detectado pelo sensor. <br /> Para obter mais informações, consulte [Exibir tipos de dispositivo](#view-device-types). |
| Fornecedor | O fornecedor do dispositivo. Isso é determinado pelos caracteres à esquerda do endereço MAC do dispositivo. Este campo é somente leitura. |
| Sistema Operacional | O sistema operacional do dispositivo detectado pelo sensor. |
| Camada Purdue | A camada Purdue identificada pelo sensor para este dispositivo, incluindo: <br /> -Automático <br /> -Controle de processo <br /> -Supervisão <br /> - Enterprise |
| Descrição | Um campo de texto livre. <br /> Adicione mais informações sobre o dispositivo. |
| Atributos | Todas as informações adicionais que foram descobertas sobre o dispositivo durante o período de aprendizagem e que não pertencem a outras categorias aparecerão na seção atributos. <br /> As informações são RO. |
| Configurações | Você pode alterar manualmente as configurações do dispositivo para evitar falsos positivos: <br /> - **Dispositivo autorizado**: durante o período de aprendizado, todos os dispositivos descobertos na rede são identificados como dispositivos autorizados. Quando um dispositivo é descoberto após o período de aprendizagem, ele aparece como um dispositivo não autorizado por padrão. Você pode alterar essa definição manualmente. <br /> - **Conhecido como scanner**: Habilite esta opção se você souber que esse dispositivo é conhecido como scanner e não há necessidade de alertá-lo sobre ele. <br /> - **Dispositivo de programação**: Habilite esta opção se você souber que este dispositivo é conhecido como um dispositivo de programação e é usado para fazer alterações de programação. Identificá-lo como um dispositivo de programação impedirá alertas para alterações de programação originadas desse ativo. |
| Grupos personalizados | Os grupos personalizados no mapa do dispositivo no qual este dispositivo participa. |
| Estado | O status de segurança e autorização do dispositivo: <br /> -O status é `Secured` quando não há alertas <br /> -Quando houver alertas sobre o dispositivo, o número de alertas será exibido <br /> -O status `Unauthorized` é exibido para dispositivos que foram adicionados à rede após o período de aprendizado. Você pode definir manualmente o dispositivo como `Authorized Device` nas configurações <br /> -No caso, o endereço desse dispositivo é definido como um endereço dinâmico, `DHCP` é adicionado ao status. |


| Rede | Descrição |
|--|--|
| Interfaces | As interfaces do dispositivo. Um campo RO. |
| Protocolos | Os protocolos usados pelo dispositivo. Um campo RO. |
| Firmware | Se as informações do backplane estiverem disponíveis, as informações de firmware não serão exibidas. |
| Endereço | O endereço IP do dispositivo. |
| Serial | O número de série do dispositivo. |
| Endereço do módulo | O modelo do dispositivo e o número do slot ou a ID. |
| Modelo | O número do modelo do dispositivo. |
| Versão do Firmware | O número de versão do firmware. |

Para exibir as informações do dispositivo:

1. Selecione **dispositivos** no menu lateral.

2. Clique com o botão direito do mouse em um dispositivo e selecione **Exibir Propriedades**. O dispositivo janela Propriedades é exibido.

3. Selecione o alerta necessário na parte inferior desta janela para exibir informações detalhadas sobre alertas para este dispositivo.

### <a name="view-device-types"></a>Exibir tipos de dispositivo

O tipo de dispositivo é identificado automaticamente pelo sensor durante o processo de descoberta do dispositivo. Você pode alterar o tipo manualmente.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Exibir tipos de dispositivo":::

A tabela a seguir apresenta todos os tipos no sistema:

| Categoria | Tipo de dispositivo |
|--|--|
| PARTILHA | Estação de engenharia <br /> PLC <br />Historian <br />HMI <br />NEGADO <br />Controlador DCS <br />RTU <br />Sistema de empacotamento industrial <br />Escala industrial <br />Robô industrial <br />Slot <br />Medidor <br />Unidade de frequência variável  <br />Controlador de robô <br />Unidade servo <br />Dispositivo pneumático <br />Marquee |
| TI | Controlador de domínio <br />Servidor de BD <br />Estação de Trabalho <br />Servidor <br />Estação de terminal <br />Armazenamento <br />Smartphone <br />Tablet <br />Servidor de backup |
| IoT | Câmera de IP <br />Impressora  <br />Relógio de perfuração <br />ATM <br />Smart TV <br />Console do jogo <br />DVR <br />Painel de controle da porta <br />HVAC <br />Termostato <br />Alarme de incêndio <br />Luz inteligente <br />Comutador inteligente <br />Detector de incêndio <br />Telefone IP <br />Sistema de alarme <br />Siren de alarme <br />Detector de Movimento <br />Elevador <br />Sensor de umidade <br />Scanner de Código de Barras <br />No-break <br />Sistema de contador de pessoas <br />Intercom <br />Dupla |
| Rede | Ponto de acesso sem fio <br />Roteador <br />Comutador <br />Firewall <br />Gateway de VPN <br />Servidor NTP <br />Pineapple WiFi <br />Local físico <br />Adaptador de e/s <br /> Conversor de protocolo |

Para exibir as informações do dispositivo:

1.  Selecione **dispositivos** no menu lateral.

2. Clique com o botão direito do mouse em um dispositivo e selecione **Exibir Propriedades**. O dispositivo janela Propriedades é exibido.

3. Selecione o alerta necessário para exibir informações detalhadas sobre alertas para este dispositivo.

### <a name="backplane-properties"></a>Propriedades do backplane

Se um PLC contiver vários módulos separados em racks e slots, as características poderão variar entre as placas de módulo. Por exemplo, se o endereço IP e o endereço MAC forem os mesmos, o firmware poderá ser diferente.

Você pode usar a opção backplane para revisar vários controladores/placas e seus dispositivos aninhados como uma entidade com uma variedade de definições. Cada slot na exibição de backplane representa os dispositivos subjacentes – os dispositivos que foram descobertos por trás dele.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Propriedades do backplane":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Propriedades do dispositivo de backplane":::

Um backplane pode conter até 30 placas de controlador e até 30 unidades de rack. O número total de dispositivos incluídos nos vários níveis pode ser de até 200 dispositivos.

O painel do backplane é mostrado no dispositivo janela Propriedades quando os detalhes do backplane são detectados.

Cada slot aparece com o número de dispositivos subjacentes e o ícone que mostra o tipo de módulo.

| ícone | Tipo de módulo |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Fonte de energia"::: | Fonte de Alimentação |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="E/s analógica"::: | E/s analógica |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Adaptador de comunicação"::: | Adaptador de comunicação |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="E/s digital"::: | E/s digital |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Genérico"::: | Genérico |

Quando você seleciona um slot, os detalhes do slot são exibidos:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="selecionar um slot":::

Para exibir os dispositivos subjacentes por trás do slot, selecione **Exibir no mapa**. O slot é apresentado no mapa do dispositivo com todos os módulos e dispositivos subjacentes conectados a ele.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="EXIBIR NO MAPA":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Exibir uma linha do tempo de eventos para o dispositivo

Exibir uma linha do tempo de eventos associados a um dispositivo.

Para exibir a linha do tempo:

1. Clique com o botão direito do mouse em um dispositivo do mapa.

2. Selecione **Mostrar eventos**. A janela linha do tempo de evento é aberta com informações sobre os eventos detectados para o dispositivo selecionado.

Consulte [linha do tempo do evento](#event-timeline) para obter detalhes.

## <a name="analyze-programming-details-and-changes"></a>Analisar detalhes e alterações de programação

Aprimore a perícia exibindo eventos de programação realizados em seus dispositivos de rede e analisando as alterações de código. Essas informações ajudam a descobrir atividades de programação suspeitas, por exemplo:

  - Erro humano: um engenheiro está programando o dispositivo errado.

  - Automação de programação corrompida: a programação é executada erroneamente devido a uma falha de automação.

  - Sistemas invadidos: usuários não autorizados conectados a um dispositivo de programação.

Você pode exibir um dispositivo programado e percorrer várias alterações de programação realizadas por outros dispositivos.

Exiba o código que foi adicionado, alterado, removido ou recarregado pelo dispositivo de programação. Procure alterações de programação com base em tipos de arquivo, datas ou horas de interesse.

### <a name="when-to-review-programming-activity"></a>Quando examinar a atividade de programação 

Talvez seja necessário examinar a atividade de programação:

  - Depois de exibir um alerta em relação à programação não autorizada

  - Após uma atualização planejada para os controladores

  - Quando um processo ou computador não está funcionando corretamente (para ver quem realizou a última atualização e quando)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Programando log de alterações":::

Outras opções permitem que você:

  - Marque os eventos de interesse com uma estrela.

  - Baixe um arquivo *. txt com o código atual.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Sobre eventos de programação autorizados vs não autorizados 

Os eventos de programação não autorizados são executados por dispositivos que não foram aprendidos ou definidos manualmente como dispositivos de programação. Os eventos de programação autorizados são executados por dispositivos que foram resolvidos ou definidos manualmente como dispositivos de programação.

A janela de análise de programação exibe eventos de programação autorizados e não autorizados.

### <a name="accessing-programming-details-and-changes"></a>Acessando detalhes e alterações de programação

Acesse a janela de análise de programação do:

- [Linha do tempo do evento](#event-timeline)

- [Alertas de programação não autorizados](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Linha do tempo do evento

Use a linha do tempo de evento para exibir uma linha do tempo de eventos nos quais as alterações de programação foram detectadas.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Uma exibição da linha do tempo do evento.":::

### <a name="unauthorized-programming-alerts"></a>Alertas de programação não autorizados

Os alertas são disparados quando dispositivos de programação não autorizados executam atividades de programação.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Alertas de programação não autorizados":::

> [!NOTE]
> Você também pode exibir informações básicas de programação no dispositivo janela Propriedades e no inventário de dispositivos.

### <a name="working-in-the-programming-timeline-window"></a>Trabalhando na janela linha do tempo de programação

Esta seção descreve como exibir arquivos de programação e comparar versões. Pesquisar arquivos específicos enviados a um dispositivo programado. Pesquisar arquivos com base em:

  - Data

  - Tipo de arquivo

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="janela de linha do tempo de programação":::

|Tipo de linha do tempo de programação | Descrição |
|--|--|
| Dispositivo programado | Fornece detalhes sobre o dispositivo que foi programado, incluindo o nome do host e o arquivo. |
| Eventos recentes | Exibe os 50 eventos mais recentes detectados pelo sensor. <br />Para realçar um evento, passe o mouse sobre ele e clique na estrela. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Os últimos 50 eventos podem ser exibidos. |
| Arquivos | Exibe os arquivos detectados para a data escolhida e o tamanho do arquivo no dispositivo programado. <br /> Por padrão, o número máximo de arquivos disponíveis para exibição por dispositivo é 300. <br /> Por padrão, o tamanho máximo do arquivo para cada arquivo é 15 MB. |
| Status do arquivo :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Os rótulos de arquivo indicam o status do arquivo no dispositivo, incluindo: <br /> **Adicionado**: o arquivo foi adicionado ao ponto de extremidade na data ou hora selecionada. <br /> **Atualizado**: o arquivo foi atualizado na data ou hora selecionada. <br /> **Excluído**: este arquivo foi removido. <br /> **Nenhum rótulo**: o arquivo não foi alterado.   |
| Dispositivo de programação | O dispositivo que fez a alteração de programação. Vários dispositivos podem ter executado alterações de programação em um dispositivo programado. O nome do host, a data ou a hora da alteração e o usuário conectado são exibidos. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Exibe o arquivo atual instalado no dispositivo programado. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Baixe um arquivo de texto do código exibido. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Compare o arquivo atual com o arquivo detectado em uma data selecionada. |

### <a name="choose-a-file-to-review"></a>Escolha um arquivo para examinar

Esta seção descreve como escolher um arquivo para revisão.

Para escolher um arquivo a ser revisado:

1. Selecione um evento no painel **eventos recentes**

2. Selecione um arquivo no painel arquivo. O arquivo aparece no painel atual.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Selecione o arquivo com o qual trabalhar.":::

### <a name="compare-files"></a>Comparar arquivos

Esta seção descreve como comparar arquivos de programação.

Para comparar:

1. Selecione um evento no painel eventos recentes.

2. Selecione um arquivo no painel arquivo. O arquivo aparece no painel atual. Você pode comparar esse arquivo com outros arquivos.

3. Selecione o indicador de comparação.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Comparar indicador":::

   A janela exibe todas as datas em que o arquivo selecionado foi detectado no dispositivo programado. O arquivo pode ter sido atualizado no dispositivo programado por vários dispositivos de programação.

   O número de diferenças detectadas aparece no canto superior direito da janela. Talvez seja necessário rolar para baixo para exibir as diferenças.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Role para baixo até sua seleção":::

   O número é calculado por linhas adjacentes de texto alterado. Por exemplo, se oito linhas consecutivas de código tiverem sido alteradas (excluídas, atualizadas ou adicionadas), isso será calculado como uma diferença.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="A exibição de linha do tempo de programação.":::

4. Selecione uma data. O arquivo detectado na data selecionada aparece na janela.

5. O arquivo selecionado no painel eventos/arquivos recentes sempre aparece à direita.

### <a name="device-programming-information-other-locations"></a>Informações de programação de dispositivo: outros locais

Além de revisar os detalhes na linha do tempo de programação, você pode acessar informações de programação no dispositivo janela Propriedades e no inventário do dispositivo.

| Tipo de dispositivo | Descrição |
|--|--|
| Propriedades do dispositivo | A janela Propriedades do dispositivo fornece informações sobre o último evento de programação detectado no device\. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="As propriedades do seu dispositivo"::: |
| O inventário do dispositivo | O inventário de dispositivo indica se o dispositivo é um device\. de programação :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="O inventário de dispositivos"::: |

## <a name="manage-device-information-from-the-map"></a>Gerenciar informações do dispositivo a partir do mapa

O sensor não atualiza nem afeta os dispositivos diretamente na rede. As alterações feitas aqui só afetam o modo como o analisa o dispositivo.

### <a name="delete-devices"></a>Excluir dispositivos

Talvez você queira excluir um dispositivo se as informações aprendidas não forem relevantes. Por exemplo,

  - Um prestador de parceiros em uma estação de trabalho de engenharia conecta-se temporariamente para realizar atualizações de configuração. Depois que a tarefa for concluída, o dispositivo será removido.

  - Devido a alterações na rede, alguns dispositivos não estão mais conectados.

Se você não excluir o dispositivo, o sensor continuará a monitorá-lo. Após 60 dias, uma notificação será exibida, recomendando que você exclua.

Você pode receber um alerta indicando que o dispositivo não está respondendo se outro dispositivo tentar acessá-lo. Nesse caso, sua rede pode estar configurada incorretamente.

O dispositivo será removido do mapa do dispositivo, do inventário do dispositivo e dos relatórios de mineração de dados. Outras informações, por exemplo: informações armazenadas em widgets serão mantidas.

O dispositivo deve estar inativo por pelo menos 10 minutos para excluí-lo.

Para excluir um dispositivo do mapa do dispositivo:

1. Selecione **dispositivos** no menu lateral.

2. Clique com o botão direito do mouse em um dispositivo e selecione **excluir**.

### <a name="merge-devices"></a>Dispositivos de mesclagem

Em determinadas circunstâncias, talvez seja necessário mesclar dispositivos. Isso pode ser necessário se o sensor descobrir entidades de rede separadas associadas a um dispositivo exclusivo. Por exemplo,

  - Um PLC com quatro placas de rede.

  - Um laptop com Wi-Fi e cartão físico.
  
  - Uma estação de trabalho com duas ou mais placas de rede.

Ao mesclar, você instrui o sensor a combinar as propriedades do dispositivo de dois dispositivos em um. Quando você fizer isso, os relatórios de sensor e janela Propriedades de dispositivo serão atualizados com os novos detalhes de propriedade de dispositivo.

Por exemplo, se você mesclar dois dispositivos, cada um com um endereço IP, os dois endereços IP aparecerão como interfaces separadas no dispositivo janela Propriedades. Você só pode mesclar dispositivos autorizados.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="janela Propriedades de dispositivo":::

A linha do tempo do evento apresenta o evento Merge.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="A linha do tempo do evento com eventos mesclados.":::

Não é possível desfazer uma mesclagem de dispositivos. Se você tiver mesclado por engano dois dispositivos, exclua o dispositivo e aguarde o sensor redescobrir os dois.

Para mesclar dispositivos:

1. Selecione dois dispositivos (Shift-clique) e clique com o botão direito do mouse em um deles.

2. Selecione **mesclar** para mesclar os dispositivos. Pode levar até 2 minutos para concluir a mesclagem.

3. Na caixa de diálogo definir atributos de dispositivo de mesclagem, escolha um nome de dispositivo.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="caixa de diálogo atributos":::

4. Selecione **Salvar**.

### <a name="authorize-and-unauthorize-devices"></a>Autorizar e desautorizar dispositivos

Durante o período de aprendizagem, todos os dispositivos descobertos na rede são identificados como dispositivos autorizados. O rótulo **autorizado** não aparece nesses dispositivos no mapa do dispositivo.

Quando um dispositivo é descoberto após o período de aprendizagem, ele aparece como um dispositivo não autorizado. Além de ver os dispositivos não autorizados no mapa, você também pode vê-los no inventário do dispositivo.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Inventário de dispositivo":::

**Novo dispositivo vs não autorizado**

Novos dispositivos detectados após o período de aprendizagem aparecerão com um `New` `Unauthorized` rótulo e.

Se você mover um dispositivo no mapa ou alterar manualmente as propriedades do dispositivo, o `New` rótulo será removido do ícone do dispositivo.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivos não autorizados – vetores de ataque e relatórios de avaliação de risco

Os dispositivos não autorizados são incluídos nos relatórios de avaliação de risco e de vetores de ataque.

- **Relatórios de vetor de ataque:** Os dispositivos marcados como não autorizados são resolvidos no vetor de ataque como dispositivos mal-intencionados suspeitos que podem ser uma ameaça à rede.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Exibição seus relatórios de vetor de ataque":::

- **Relatórios de avaliação de risco:** Os dispositivos marcados como não autorizados são:

  - Identificado em relatórios de avaliação de risco

Para autorizar ou desautorizar dispositivos manualmente:

1. Clique com o botão direito do mouse no dispositivo no mapa e selecione **não autorizar**

### <a name="mark-devices-as-important"></a>Marcar dispositivos como importantes

Você pode marcar dispositivos de rede significativos como importantes, por exemplo, servidores críticos para os negócios. Esses dispositivos são marcados com uma estrela no mapa. A estrela varia de acordo com o nível de zoom do mapa.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivos importantes – vetores de ataque e relatórios de avaliação de risco

Os dispositivos importantes são calculados durante a geração de relatórios de avaliação de risco e relatórios de vetores de ataque.

  - Relatórios de vetor de ataque os dispositivos marcados como importantes são resolvidos no vetor de ataque como alvos de ataque. 

  - Relatórios de avaliação de risco: os dispositivos marcados como importantes são calculados ao fornecer a pontuação de segurança no relatório de avaliação de risco.

## <a name="generate-activity-reports-from-the-map"></a>Gerar relatórios de atividade do mapa

Gere um relatório de atividade para um dispositivo selecionado em 1, 6, 12 ou 24 horas. As informações a seguir estão disponíveis:

  - Categoria: informações básicas de detecção baseadas em cenários de tráfego.

  - Dispositivos de origem e de destino

  - Dados: informações adicionais com defeito.

  - A data e a hora da última visualização.

Você pode salvar o relatório como um arquivo do Word ou do Microsoft Excel.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Atividade recente":::

Para gerar um relatório de atividade para um dispositivo:

1. Clique com o botão direito do mouse em um dispositivo do mapa.

2. Selecione um relatório de atividade.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Exiba um relatório de sua atividade.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Gerar relatórios de vetor de ataque do mapa

Simule um relatório de vetor de ataque para saber se um dispositivo no mapa selecionado é um alvo de ataque vulnerável.

Os relatórios de vetor de ataque fornecem uma representação gráfica de uma cadeia de vulnerabilidade de dispositivos exploráveis. Essas vulnerabilidades podem dar a um invasor acesso aos principais dispositivos de rede. O simulador de vetor de ataque calcula os vetores de ataque em tempo real e analisa todos os vetores de ataque por um destino específico.

Para exibir um dispositivo em um relatório de vetor de ataque:

1. Clique com o botão direito do mouse em um dispositivo do mapa.

2. Selecione **simular vetores de ataque**. A caixa de diálogo vetor de ataque é aberta com o dispositivo selecionado como o alvo do ataque.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Adicionar simulação de vetor de ataque":::

3. Adicione os parâmetros restantes à caixa de diálogo e selecione **Adicionar simulação**.

## <a name="export-device-information-from-the-map"></a>Exportar informações do dispositivo do mapa

Exporte as seguintes informações do dispositivo do mapa.

  - Detalhes do dispositivo (Microsoft Excel)

  - Um resumo do dispositivo (Microsoft Excel)

  - Um arquivo do Word com grupos (Microsoft Word)

Para exportar:

1. Selecione o ícone exportar do mapa.

1. Selecione uma opção de exportação.

## <a name="see-also"></a>Veja também

[Investigar detecções de sensor em um inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
