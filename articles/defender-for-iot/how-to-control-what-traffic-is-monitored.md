---
title: Controlar qual tráfego é monitorado
description: Os sensores executam automaticamente a detecção profunda de pacotes para ti e ficam sobre o tráfego e resolvem informações sobre dispositivos de rede, como atributos de dispositivo e comportamento de rede. Várias ferramentas estão disponíveis para controlar o tipo de tráfego que cada sensor detecta.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a7af817074400bedd8f07adaefc35309566dc931
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522386"
---
# <a name="control-what-traffic-is-monitored"></a>Controlar qual tráfego é monitorado

Os sensores executam automaticamente a detecção profunda de pacotes para ti e ficam sobre o tráfego e resolvem informações sobre dispositivos de rede, como atributos de dispositivo e comportamento. Várias ferramentas estão disponíveis para controlar o tipo de tráfego que cada sensor detecta.

## <a name="learning-and-smart-it-learning-modes"></a>Aprendizado e modos inteligentes de aprendizado de ti

O modo de aprendizado instrui seu sensor a aprender a atividade usual da sua rede. Os exemplos são dispositivos descobertos em sua rede, protocolos detectados na rede, transferências de arquivos entre dispositivos específicos e muito mais. Essa atividade torna-se a sua linha de base de rede.

O modo de aprendizado é habilitado automaticamente após a instalação e permanecerá habilitado até que seja desativado. O período aproximado do modo de aprendizado é entre duas e seis semanas, dependendo do tamanho da rede e da complexidade. Após esse período, quando o modo de aprendizado estiver desabilitado, qualquer nova atividade detectada irá disparar alertas. Os alertas são disparados quando o mecanismo de política descobre desvios da sua linha de base aprendida.

Depois que o período de aprendizado for concluído e o modo de aprendizado estiver desabilitado, o sensor poderá detectar um nível incomum de alterações de linha de base que são o resultado da atividade normal de ti, como solicitações de DNS e HTTP. A atividade é chamada de comportamento de ti não determinística. O comportamento também pode disparar alertas de violação de política e notificações do sistema desnecessários. Para reduzir a quantidade desses alertas e notificações, você pode habilitar a função **inteligente de aprendizado de ti** .

Quando o aprendizado inteligente de ti está habilitado, o sensor rastreia o tráfego de rede que gera um comportamento de ti não determinístico com base em cenários de alerta específicos.

O sensor monitora esse tráfego por sete dias. Se detectar o mesmo tráfego de ti não determinístico dentro dos sete dias, ele continuará a monitorar o tráfego por outros sete dias. Quando o tráfego não é detectado por um total de sete dias, o aprendizado inteligente de ti é desabilitado para esse cenário. O novo tráfego detectado para esse cenário só gerará alertas e notificações.

Trabalhar com o Smart IT Learning ajuda a reduzir o número de alertas e notificações desnecessários causados por cenários de ti ruidosas.

Se o sensor for controlado pelo console de gerenciamento local, você não poderá desabilitar os modos de aprendizado. Em casos como esse, o modo de aprendizado só pode ser desabilitado no console de gerenciamento.

Os recursos de aprendizado (aprendizado e aprendizado inteligente de ti) são habilitados por padrão.

Para habilitar ou desabilitar o aprendizado:

- Selecione **configurações do sistema** e alterne as opções **de aprendizagem e** aprendizado **inteligente de ti** .

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Tela de alternância de configurações do sistema.":::

## <a name="configure-subnets"></a>Configurar sub-redes

As configurações de sub-rede afetam o modo como você vê os dispositivos no mapa do dispositivo.

Por padrão, o sensor descobre a configuração de sub-rede e popula a caixa de diálogo **configuração de sub-rede** com essas informações.

Para habilitar o foco nos dispositivos OT, os dispositivos de ti são agregados automaticamente por sub-rede no mapa do dispositivo. Cada sub-rede é apresentada como uma única entidade no mapa, incluindo um recurso interativo de recolhimento/expansão para "detalhar" em uma sub-rede de ti e de volta.

Quando você estiver trabalhando com sub-redes, selecione as sub-redes do ICS para identificar as sub-redes de OT. Em seguida, você pode concentrar a exibição de mapa em redes de OT e ICS e recolher para uma apresentação mínima dos elementos de rede de ti. Esse esforço reduz o número total de dispositivos mostrados no mapa e fornece uma visão clara dos elementos de rede de OT e ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Captura de tela que mostra a filtragem para uma rede.":::

Você pode alterar a configuração ou alterar as informações de sub-rede manualmente exportando os dados descobertos, alterando-os manualmente e, em seguida, importando de volta a lista de sub-redes que você definiu manualmente. Para obter mais informações sobre exportação e importação, consulte [importar informações do dispositivo](how-to-import-device-information.md).

Em alguns casos, como ambientes que usam intervalos públicos como intervalos internos, você pode instruir o sensor a resolver todas as sub-redes como sub-redes internas selecionando a opção **não detectar atividade de Internet** . Quando você seleciona essa opção:

- Os endereços IP públicos serão tratados como endereços locais.

- Nenhum alerta será enviado sobre a atividade de Internet não autorizada, o que reduz as notificações e os alertas recebidos em endereços externos.

Para configurar sub-redes:

1. No menu lateral, selecione **configurações do sistema**.

2. Na janela **configuração do sistema** , selecione **sub-redes**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Captura de tela que mostra o configuração da sub-rede."::: 

3. Para adicionar sub-redes automaticamente quando novos dispositivos forem descobertos, mantenha o **aprendizado de sub-redes automáticas** selecionado.

4. Para resolver todas as sub-redes como sub-redes internas, selecione **não detectar atividade da Internet**.

5. Selecione **Adicionar rede** e defina os seguintes parâmetros para cada sub-rede:

    - O endereço IP da sub-rede.
    - O endereço da máscara de sub-rede.
    - O nome da sub-rede. Recomendamos que você nomeie cada sub-rede com um nome significativo que possa identificar facilmente, para que possa diferenciar entre ela e as redes. O nome pode ter até 60 caracteres.

6. Para marcar essa sub-rede como uma sub-rede de OT, selecione **sub-rede do ICS**.

7. Para apresentar a sub-rede separadamente quando você estiver organizando o mapa de acordo com o nível de Purdue, selecione **segregado**.

8. Para excluir uma sub-rede, selecione :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Para excluir todas as sub-redes, selecione **limpar tudo**.

10. Para exportar sub-redes configuradas, selecione **Exportar**. A tabela de sub-rede é baixada para sua estação de trabalho.

11. Selecione **Salvar**.

### <a name="importing-information"></a>Importando informações 

Para importar informações de sub-rede, selecione **importar** e selecione um arquivo CSV para importar. As informações de sub-rede são atualizadas com as informações que você importou. Se você for importante um campo vazio, perderá seus dados.

## <a name="detection-engines"></a>Mecanismos de detecção

Os mecanismos de análise de autoaprendizagem eliminam a necessidade de atualizar assinaturas ou definir regras. Os mecanismos usam a análise comportamental específica do ICS e a ciência de dados para analisar continuamente o tráfego de rede para anomalias, malware, problemas operacionais, violações de protocolo e desvios de atividade de rede de linha de base.

> [!NOTE]
> Recomendamos que você habilite todos os mecanismos de segurança.

Quando um mecanismo detecta um desvio, um alerta é disparado. Você pode exibir e gerenciar alertas na tela de alerta ou em um sistema parceiro.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Captura de tela que mostra a detecção de desvio.":::

O nome do mecanismo que disparou o alerta aparece sob o título do alerta.

### <a name="protocol-violation-engine"></a>Mecanismo de violação de protocolo 

Uma violação de protocolo ocorre quando os valores de campo ou estrutura de pacote não estão em conformidade com a especificação de protocolo.

Cenário de exemplo: alerta *"operação Modbus ilegal (código de função zero)"* . Esse alerta indica que um dispositivo primário enviou uma solicitação com o código de função 0 para um dispositivo secundário. Essa ação não é permitida de acordo com a especificação de protocolo e o dispositivo secundário pode não tratar a entrada corretamente.

### <a name="policy-violation-engine"></a>Mecanismo de violação de política

Uma violação de política ocorre com um desvio do comportamento de linha de base definido nas configurações aprendidas ou configuradas.

Cenário de exemplo: alerta *"agente de usuário http não autorizado"* . Esse alerta indica que um aplicativo que não foi aprendido ou aprovado pela política é usado como um cliente HTTP em um dispositivo. Esse pode ser um novo navegador da Web ou aplicativo nesse dispositivo.

### <a name="malware-engine"></a>Mecanismo de malware

O mecanismo de malware detecta uma atividade de rede mal-intencionada.

Cenário de exemplo: alerta *"suspeita de atividade mal-intencionada (Stuxnet)"* . Esse alerta indica que o sensor detectou atividade de rede suspeita conhecida como relacionada ao malware Stuxnet. Esse malware é uma ameaça persistente avançada voltada para o controle industrial e para redes SCADAs.

### <a name="anomaly-engine"></a>Mecanismo de anomalias

O mecanismo de anomalias detecta anomalias no comportamento de rede.

Cenário de exemplo: alerta *"comportamento periódico no canal de comunicação"* . O componente inspeciona as conexões de rede e localiza o comportamento periódico e cíclico da transmissão de dados. Esse comportamento é comum em redes industriais.

### <a name="operational-engine"></a>Mecanismo operacional

O mecanismo operacional detecta incidentes operacionais ou entidades com defeito.

Cenário de exemplo: alerta *"o dispositivo é suspeito de ser desconectado (sem resposta)"* . Esse alerta é gerado quando um dispositivo não está respondendo a nenhum tipo de solicitação por um período predefinido. Esse alerta pode indicar um desligamento, desconexão ou mau funcionamento do dispositivo.

### <a name="enable-and-disable-engines"></a>Habilitar e desabilitar mecanismos

Quando você desabilita um mecanismo de política, as informações geradas pelo mecanismo não estarão disponíveis para o sensor. Por exemplo, se você desabilitar o mecanismo de anomalias, não receberá alertas sobre anomalias de rede. Se você criou uma regra de encaminhamento, as anomalias que o motor aprende não serão enviadas. Para habilitar ou desabilitar um mecanismo de política, selecione **habilitado** ou **desabilitado** para o mecanismo específico.

A pontuação geral é exibida no canto inferior direito da tela Configurações do **sistema** . A pontuação indica a porcentagem de proteção disponível habilitada por meio dos mecanismos de proteção contra ameaças. Cada mecanismo é de 20% da proteção disponível.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Captura de tela que mostra uma pontuação.":::

## <a name="configure-dhcp-address-ranges"></a>Configurar intervalos de endereços DHCP

Sua rede pode consistir em endereços IP estáticos e dinâmicos. Normalmente, os endereços estáticos são encontrados em OT redes por meio de historiadores, controladores e dispositivos de infraestrutura de rede, como comutadores e roteadores. A alocação de IP dinâmico normalmente é implementada em redes convidadas com laptops, PCs, smartphones e outros equipamentos portáteis (usando Wi-Fi ou conexões físicas de LAN em locais diferentes).

Se você estiver trabalhando com redes dinâmicas, você lida com as alterações de endereço IP que ocorrem quando novos endereços IP são atribuídos. Você faz isso definindo intervalos de endereços DHCP.

As alterações podem acontecer, por exemplo, quando um servidor DHCP atribui endereços IP.

Definir endereços IP dinâmicos em cada sensor permite um suporte abrangente e transparente em instâncias de alterações de endereço IP. Isso garante um relatório abrangente para cada dispositivo exclusivo.

O console do sensor apresenta o endereço IP mais atual associado ao dispositivo e indica quais dispositivos são dinâmicos. Por exemplo:

- O relatório de mineração de dados e o relatório de inventário de dispositivo consolidam todas as atividades aprendidas do dispositivo como uma entidade, independentemente do endereço IP alterado. Esses relatórios indicam quais endereços foram definidos como endereços DHCP.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Captura de tela que mostra o inventário de dispositivo.":::

- A janela **Propriedades do dispositivo** indica se o dispositivo foi definido como um dispositivo DHCP.

Para definir um intervalo de endereços DHCP:

1.  No menu lateral, selecione **intervalos DHCP** na janela **configurações do sistema** .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Captura de tela que mostra a seleção de intervalos DHCP.":::

2.  Defina um novo intervalo definindo os valores **de** e **para** .

3.  Opcionalmente: defina o nome do intervalo, até 256 caracteres.

4.  Para exportar os intervalos para um arquivo CSV, selecione **Exportar**.

5. Para adicionar manualmente vários intervalos de um arquivo CSV, selecione **importar** e, em seguida, selecione o arquivo.

    > [!NOTE]
    > Os intervalos que você importa de um arquivo CSV substituem as configurações de intervalo existentes.

6. Selecione **Salvar**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Configurar servidores DNS para resolução de pesquisa inversa

Para aprimorar o enriquecimento do dispositivo, você pode configurar vários servidores DNS para realizar pesquisas inversas. Você pode resolver nomes de host ou FQDNs associados aos endereços IP detectados em sub-redes de rede. Por exemplo, se um sensor descobre um endereço IP, ele pode consultar vários servidores DNS para resolver o nome do host.

Todos os formatos CIDR têm suporte.

O nome do host aparece no inventário do dispositivo e no mapa do dispositivo e em relatórios.

Você pode agendar agendas de resolução de pesquisa inversa para intervalos específicos por hora, como a cada 12 horas. Ou você pode agendar uma hora específica.

Para definir servidores DNS:

1. Selecione **configurações do sistema** e, em seguida, selecione **configurações de DNS**.

2. Selecione **Adicionar servidor DNS**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Captura de tela que mostra a seleção de adicionar servidor DNS.":::

3. No campo **agendar pesquisa de DNS reverso** , escolha:

    - Intervalos (por hora).
  
    - Uma hora específica. Use a formatação Europeia. Por exemplo, use **14:30** e não **2:30 PM**.

4. No campo **endereço do servidor DNS** , insira o endereço IP do DNS.

5. No campo **porta do servidor DNS** , insira a porta DNS.

6. Resolva os endereços IP de rede para FQDNs de dispositivo. No campo **número de rótulos** , adicione o número de rótulos de domínio a serem exibidos. Até 30 caracteres são exibidos da esquerda para a direita.

7. No campo **sub-redes** , insira as sub-redes que você deseja que o servidor DNS consulte.

8. Selecione **habilitar** alternância se você quiser iniciar a pesquisa inversa.

### <a name="test-the-dns-configuration"></a>Testar a configuração de DNS 

Usando um dispositivo de teste, verifique se as configurações definidas funcionam corretamente:

1. Habilite a alternância de **pesquisa de DNS** .

2. Selecione **Testar**.

3. Insira um endereço no **endereço de pesquisa** para a caixa de diálogo **teste de pesquisa inversa de DNS para servidor** .

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Captura de tela que mostra a área de endereço de pesquisa.":::

4. Selecione **Testar**.

## <a name="configure-windows-endpoint-monitoring"></a>Configurar o monitoramento de ponto de extremidade do Windows

Com o recurso de monitoramento de ponto de extremidade do Windows, você pode configurar o Azure defender para IoT para investigar seletivamente os sistemas Windows. Isso fornece informações mais focalizadas e precisas sobre seus dispositivos, como níveis de service pack.

Você pode configurar a investigação com intervalos e hosts específicos e configurá-lo para ser executado somente com a frequência desejada. Você realiza investigação seletiva usando o Instrumentação de Gerenciamento do Windows (WMI), que é a linguagem de script padrão da Microsoft para gerenciar sistemas Windows.

> [!NOTE]
> - Você pode executar apenas uma verificação por vez.
> - Você Obtém os melhores resultados com os usuários que têm privilégios de administrador local ou de domínio.
> - Antes de começar a configuração do WMI, configure uma regra de firewall que abra o tráfego de saída do sensor para a sub-rede digitalizada usando a porta UDP 135 e todas as portas TCP acima de 1024.

Quando a investigação é concluída, um arquivo de log com todas as tentativas de investigação está disponível na opção de exportar um log. O log contém todos os endereços IP que foram investigados. Para cada endereço IP, o log mostra informações de êxito e falha. Também há um código de erro, que é uma cadeia de caracteres livre derivada da exceção. A verificação do último log só é mantida no sistema.

Você pode executar verificações agendadas ou verificações manuais. Quando uma verificação for concluída, você poderá exibir os resultados em um arquivo CSV.

**Pré-requisitos**

Configure uma regra de firewall que abra o tráfego de saída do sensor para a sub-rede digitalizada usando a porta UDP 135 e todas as portas TCP acima de 1024.

Para configurar uma verificação automática:

1. No menu lateral, selecione **configurações do sistema**.

2. Selecione **monitoramento de ponto de extremidade do Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Captura de tela que mostra a seleção do monitoramento do ponto de extremidade do Windows.":::

3. No painel **agendamento de verificação** , configure as opções da seguinte maneira:

      - **Por intervalos fixos (em horas)**: defina o agendamento da verificação de acordo com os intervalos em horas.

      - **Por horários específicos**: defina a agenda de verificação de acordo com as horas específicas e selecione **salvar verificação**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Captura de tela que mostra o botão salvar verificação.":::

4. Para definir o intervalo de verificação, selecione **definir intervalos de verificação**.

5. Defina o intervalo de endereços IP e adicione seu usuário e senha.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Captura de tela que mostra a adição de um usuário e senha.":::

6. Para excluir um intervalo de IP de uma verificação, selecione **desabilitar** ao lado do intervalo.

7. Para remover um intervalo, selecione :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: ao lado do intervalo.

8. Selecione **Salvar**. A caixa de diálogo **Editar configuração de intervalos de verificação** fecha e o número de intervalos é exibido no painel **intervalos de digitalização** .

Para executar uma verificação manual:

1. No menu lateral, selecione **configurações do sistema**.

2. Selecione **monitoramento de ponto de extremidade do Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Captura de tela que mostra o Windows Endpoint Monitoring setup.":::

3. No painel **ações** , selecione **Iniciar verificação**. Uma barra de status aparece no painel **ações** e mostra o progresso do processo de verificação.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Captura de tela que mostra o botão Iniciar verificação.":::

Para exibir os resultados da verificação:

1. Quando a verificação for concluída, no painel **ações** , selecione **exibir resultados da verificação**. O arquivo CSV com os resultados da verificação é baixado em seu computador.

## <a name="next-steps"></a>Próximas etapas

[Investigar detecções de sensor em um inventário](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 de dispositivos [Investigar as detecções do sensor no mapa do dispositivo](how-to-work-with-the-sensor-device-map.md)
