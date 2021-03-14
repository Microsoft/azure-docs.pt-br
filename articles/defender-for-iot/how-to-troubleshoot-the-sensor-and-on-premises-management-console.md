---
title: Solucionar problemas do sensor e do console de gerenciamento local
description: Solucione problemas do seu sensor e do console de gerenciamento local para eliminar quaisquer problemas que você esteja tendo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 624f60441f56646c3633a2afa79d326c92ba1a37
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523712"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Solucionar problemas do sensor e do console de gerenciamento local

Este artigo descreve as ferramentas básicas de solução de problemas para o sensor e o console de gerenciamento local. Além dos itens descritos aqui, você pode verificar a integridade do sistema das seguintes maneiras:

**Alertas**: um alerta é criado quando a interface do sensor que monitora o tráfego está inoperante. 

**SNMP**: a integridade do sensor é monitorada por meio do SNMP. O Azure defender para IoT responde a consultas SNMP enviadas de um servidor de monitoramento autorizado. 

**Notificações do sistema**: quando um console de gerenciamento controla o sensor, você pode encaminhar alertas sobre backups de sensor com falha e sensores desconectados.

## <a name="sensor-troubleshooting-tools"></a>Ferramentas de solução de problemas de sensor

### <a name="investigate-password-failure-at-initial-sign-in"></a>Investigar falha de senha na entrada inicial

Ao entrar em um sensor de seta pré-configurada pela primeira vez, você precisará executar a recuperação de senha.

Para recuperar sua senha:

1. Na tela de entrada do defender para IoT, selecione  **recuperação de senha**. A tela **recuperação de senha** é aberta.

1. Selecione **CyberX** ou **suporte** e copie o identificador exclusivo.

1. Navegue até a portal do Azure e selecione **sites e sensores**.  

1. Selecione a guia **recuperar senha do console de gerenciamento local** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Selecione o botão recuperar gerenciamento local para baixar o arquivo de recuperação.":::

1. Insira o identificador exclusivo que você recebeu na tela de **recuperação de senha** e selecione **recuperar**. O `password_recovery.zip` arquivo é baixado.

    > [!NOTE]
    > Não altere o arquivo de recuperação de senha. É um arquivo assinado e não funcionará se você violar.

1. Na tela **recuperação de senha** , selecione **carregar**. **A janela carregar arquivo de recuperação de senha** será aberta.

1. Selecione **procurar** para localizar o `password_recovery.zip` arquivo ou arraste o `password_recovery.zip` para a janela.

1. Selecione **Avançar** e seu usuário, e a senha gerada pelo sistema para o console de gerenciamento será exibida.

    > [!NOTE]
    > Quando você entra em um sensor ou em um console de gerenciamento local pela primeira vez, ele será vinculado à assinatura à qual você o conectou. Se você precisar redefinir a senha para o CyberX ou o usuário de suporte, precisará selecionar essa assinatura. Para obter mais informações sobre como recuperar uma senha de usuário de CyberX ou de suporte, consulte [redefinindo a senha de um usuário para o sensor ou o console de gerenciamento local](how-to-create-and-manage-users.md#resetting-a-users-password-for-the-sensor-or-on-premises-management-console)

### <a name="investigate-a-lack-of-traffic"></a>Investigue a falta de tráfego

Um indicador aparece na parte superior do console quando o sensor reconhece que não há tráfego em uma das portas configuradas. Esse indicador é visível para todos os usuários.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Captura de tela do alerta de que nenhum tráfego foi detectado.":::
 
Quando essa mensagem for exibida, você poderá investigar onde não há tráfego. Verifique se o cabo de span está conectado e se não houve alteração na arquitetura de span.  

Para obter suporte e informações de solução de problemas, entre em contato com [suporte da Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>Verificar o desempenho do sistema 

Quando um novo sensor é implantado ou, por exemplo, o sensor está funcionando lentamente ou não mostra alertas, você pode verificar o desempenho do sistema.

Para verificar o desempenho do sistema:

1. No painel, verifique se `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Captura de tela de um painel de exemplo."::: 

1. No menu lateral, selecione **dispositivos**.

1. Na janela **dispositivos** , verifique se os dispositivos estão sendo descobertos.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Certifique-se de que os dispositivos sejam descobertos.":::

1. No menu lateral, selecione **mineração de dados**.

1. Na janela **mineração de dados** , selecione **tudo** e gere um relatório.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Gere um novo relatório usando Data Mining.":::

1. Verifique se o relatório contém dados.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Verifique se o relatório contém dados.":::

1. No menu lateral, selecione **tendências & estatísticas**.

1. Na janela **tendências & estatísticas** , selecione **Adicionar widget**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Adicione um widget selecionando-o.":::

1. Adicione um widget e verifique se ele mostra os dados.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Verifique se o widget está mostrando os dados.":::

1. No menu lateral, selecione **alertas**. A janela **alertas** é exibida.

1. Verifique se os alertas foram criados.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Verifique se os alertas foram criados.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Investigar a falta de alertas esperados

Se a janela **alertas** não mostrar um alerta esperado, verifique o seguinte:

- Verifique se o mesmo alerta já aparece na janela **alertas** como uma reação a uma instância de segurança diferente. Se sim, e esse alerta ainda não foi tratado, o console do sensor não mostra um novo alerta.

- Certifique-se de não ter excluído esse alerta usando as regras de **exclusão de alerta** no console de gerenciamento do. 

### <a name="investigate-widgets-that-show-no-data"></a>Investigue os widgets que não mostram dados

Quando os widgets na janela de **Estatísticas de & de tendências** não mostram dados, faça o seguinte:

- [Verifique o desempenho do sistema](#check-system-performance).

- Verifique se as configurações de hora e região estão configuradas corretamente e não estão definidas para um horário futuro. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Investigar um mapa de dispositivo que mostra somente os dispositivos de difusão

Quando os dispositivos mostrados no mapa parecem não estar conectados entre si, algo pode estar errado com a configuração da porta SPAN. Ou seja, você pode estar vendo apenas dispositivos de difusão e nenhum tráfego unicast.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Exiba seus dispositivos de difusão.":::

Nesse caso, você precisa validar que você pode ver apenas o tráfego de difusão. Em seguida, peça ao engenheiro de rede para corrigir a configuração de porta SPAN para que você possa ver o tráfego unicast.

Para validar que você está vendo apenas o tráfego de difusão:

- Na tela de **mineração de dados** , crie um relatório usando a opção **tudo** . Em seguida, veja se apenas o tráfego de difusão e multicast (e nenhum tráfego unicast) aparece no relatório.

Ou:

- Registre um PCAP diretamente do comutador ou conecte um laptop usando o Wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Conectar o sensor ao NTP

Você pode configurar um sensor autônomo e um console de gerenciamento, com os sensores relacionados a ele, para se conectar ao NTP.

Para conectar um sensor autônomo ao NTP:

- [Contate a equipe de suporte para obter assistência](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

Para conectar um sensor controlado pelo console de gerenciamento ao NTP:

- A conexão com o NTP é configurada no console de gerenciamento do. Todos os sensores que o console de gerenciamento controla obtêm a conexão NTP automaticamente.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Investigue quando os dispositivos não são mostrados no mapa ou se você tem vários alertas relacionados à Internet

Às vezes, os dispositivos ICS são configurados com endereços IP externos. Esses dispositivos ICS não são mostrados no mapa. Em vez dos dispositivos, uma nuvem da Internet aparece no mapa. Os endereços IP desses dispositivos estão incluídos na imagem de nuvem.

Outra indicação do mesmo problema é quando vários alertas relacionados à Internet são exibidos.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Vários alertas relacionados à Internet.":::

Para corrigir a configuração:

1. Clique com o botão direito do mouse no ícone de nuvem no mapa do dispositivo e selecione **Exportar endereços IP**. Copie os intervalos públicos que são privados e adicione-os à lista de sub-redes. Para obter mais informações, consulte [Configurar sub-redes](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Gere um novo relatório de mineração de dados para conexões com a Internet.

1. No relatório de mineração de dados, selecione :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: para inserir o modo de administrador e excluir os endereços IP de seus dispositivos ICS.

### <a name="tweak-the-sensors-quality-of-service"></a>Ajustar a qualidade de serviço do sensor

Para salvar os recursos de rede, você pode limitar a largura de banda da interface que o sensor usa para os procedimentos diários.

Para limitar a largura de banda da interface, use a `cyberx-xsense-limit-interface` ferramenta CLI que precisa ser executada com permissões sudo. A ferramenta Obtém os seguintes argumentos:

  - `* -i`: interfaces (exemplo: eth0).

  - `* -l`: limite (exemplo: 30 kbit/1 Mbit). Você pode usar as seguintes unidades de largura de banda: Kbps, Mbps, kbit, Mbit ou bps.

  - `* -c`: Clear (para limpar a limitação da largura de banda da interface).

Para ajustar a qualidade do serviço:

1. Entre na CLI do sensor como um usuário do defender para IoT e digite `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   Por exemplo: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > Para um dispositivo físico, use a interface EM1.

1. Para limpar a limitação de interface, digite `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>Ferramentas de solução de problemas do console de gerenciamento local

### <a name="investigate-a-lack-of-expected-alerts"></a>Investigar a falta de alertas esperados

Se um alerta esperado não for mostrado na janela **alertas** , verifique o seguinte:

- Verifique se o mesmo alerta já aparece na janela **alertas** como uma reação a uma instância de segurança diferente. Se sim, e esse alerta ainda não foi tratado, um novo alerta não é mostrado.

- Verifique se você não excluiu este alerta usando as regras de **exclusão de alertas** no console de gerenciamento local.  

### <a name="tweak-the-quality-of-service"></a>Ajustar a qualidade do serviço

Para salvar os recursos de rede, você pode limitar o número de alertas enviados a sistemas externos (como emails ou SIEM) em uma operação de sincronização entre um dispositivo e o console de gerenciamento local.

O padrão é 50. Isso significa que em uma sessão de comunicação entre um dispositivo e o console de gerenciamento local, não haverá mais de 50 alertas para sistemas externos. 

Para limitar o número de alertas, use a `notifications.max_number_to_report` Propriedade disponível em `/var/cyberx/properties/management.properties` . Nenhuma reinicialização é necessária depois que você altera essa propriedade.

Para ajustar a qualidade do serviço:

1. Entre como um usuário do defender para IoT. 

1. Verifique os valores padrão:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Os seguintes valores padrão são exibidos:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Edite as configurações padrão:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Edite as configurações das seguintes linhas:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Salve as alterações. Nenhuma reinicialização é necessária.

## <a name="export-information-for-troubleshooting"></a>Exportar informações para solução de problemas

Além das ferramentas para monitorar e analisar sua rede, você pode enviar informações à equipe de suporte para uma investigação mais aprofundada. Ao exportar logs, o sensor gerará automaticamente uma OTP (senha de uso único), exclusiva para os logs exportados, em um arquivo de texto separado. 

Para exportar logs:

1. No painel esquerdo, selecione **configurações do sistema**.

1. Selecione **Exportar logs**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Exporte um log para o suporte do sistema.":::

1. Na caixa **nome do arquivo** , digite o nome do arquivo que você deseja usar para a exportação de log. O padrão é a data atual.

1. Para definir quais dados você deseja exportar, selecione as categorias de dados:  

    | Exportar categoria | Descrição |
    |--|--|
    | **Logs do sistema operacional** | Selecione esta opção para obter informações sobre o estado do sistema operacional. |
    | **Logs de instalação/atualização** | Selecione esta opção para investigação dos parâmetros de configuração de instalação e atualização. |
    | **Saída de sanidade do sistema** | Selecione esta opção para verificar o desempenho do sistema. |
    | **Logs de desseção** | Selecione esta opção para permitir a inspeção avançada da desseção de protocolo. |
    | **Despejos de kernel do so** | Selecione esta opção para exportar o despejo de memória do kernel. Um despejo de memória do kernel contém toda a memória que o kernel está usando no momento do problema que ocorreu neste kernel. O tamanho do arquivo de despejo é menor do que o despejo de memória completo. Normalmente, o arquivo de despejo está em cerca de um terço do tamanho da memória física no sistema. |
    | **Encaminhando logs** | Selecione esta opção para investigação das regras de encaminhamento. |
    | **Logs SNMP** | Selecione esta opção para receber informações de verificação de integridade de SNMP. |
    | **Logs de aplicativos principais** | Selecione esta opção para exportar dados sobre a configuração e a operação do aplicativo principal. |
    | **Comunicação com logs CM** | Selecione esta opção se houver problemas contínuos ou interrupções de conexão com o console de gerenciamento do. |
    | **Logs de aplicativos Web** | Selecione esta opção para obter informações sobre todas as solicitações enviadas da interface da Web do aplicativo. |
    | **Backup do sistema** | Selecione esta opção para exportar um backup de todos os dados do sistema para investigar o estado exato do sistema. |
    | **Estatísticas de desseção** | Selecione esta opção para permitir a inspeção avançada de estatísticas de protocolo. |
    | **Logs de banco de dados** | Selecione esta opção para exportar logs do banco de dados do sistema. A investigação de logs do sistema ajuda a identificar problemas do sistema. |
    | **Configuration** | Selecione esta opção para exportar informações sobre todos os parâmetros configuráveis para certificar-se de que tudo foi configurado corretamente. |

1. Para selecionar todas as opções, selecione **selecionar tudo** ao lado de **escolher categorias**.

1. Selecione **Exportar logs**.

Os logs exportados são adicionados à lista de **logs arquivados** . Envie a OTP para a equipe de suporte em uma mensagem separada e média dos logs exportados. A equipe de suporte poderá extrair logs exportados somente usando a OTP exclusiva que é usada para criptografar os logs.

A lista de logs arquivados pode conter até cinco itens. Se o número de itens na lista ultrapassar esse número, o item mais antigo será excluído.

## <a name="next-steps"></a>Próximas etapas

- [Exibir alertas](how-to-view-alerts.md)

- [Configurar o monitoramento de MIB do SNMP](how-to-set-up-snmp-mib-monitoring.md)

- [Entender os eventos de desconexão do sensor](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
