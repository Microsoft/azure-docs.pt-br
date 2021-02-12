---
title: Gerenciar sensores do console de gerenciamento local
description: Saiba como gerenciar sensores do console de gerenciamento do, incluindo a atualização de versões do sensor, o envio de configurações do sistema para sensores e a habilitação e a desabilitação de mecanismos em sensores.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 73eb693ab9f06535fd523eb386969e2cce961eef
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624584"
---
# <a name="manage-sensors-from-the-management-console"></a>Gerenciar sensores do console de gerenciamento

Este artigo descreve como gerenciar sensores do console de gerenciamento do, incluindo:

- Enviar por push configurações do sistema para sensores

- Habilitar e desabilitar mecanismos em sensores

- Atualizar versões do sensor

## <a name="push-configurations"></a>Configurações por push

Você pode definir várias configurações do sistema e aplicá-las automaticamente aos sensores que estão conectados ao console de gerenciamento. Isso poupa tempo e ajuda a garantir configurações simplificadas em seus sensores empresariais.

Você pode definir as seguintes configurações do sistema de sensor no console de gerenciamento:

- Servidor de emails

- Monitoramento de MIB SNMP

- Active Directory

- Configurações DNS

- Sub-redes

- Aliases de porta

Para aplicar as configurações do sistema:

1. No painel esquerdo do console, selecione **configurações do sistema**.

2. No painel **Configurar sensores** , selecione uma das opções.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="As opções de configuração do sistema para um sensor.":::

   O exemplo a seguir descreve como definir parâmetros de servidor de email para seus sensores empresariais.

3. Selecione **servidor de email**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Selecione seu servidor de email na tela Configurações do sistema.":::

4. Selecione um sensor à esquerda.

5. Defina os parâmetros do servidor de email e selecione **duplicar**. Cada item na árvore do sensor é exibido com uma caixa de seleção ao lado dele.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Verifique se as caixas de seleção estão marcadas para seus sensores.":::

6. Na árvore do sensor, selecione os itens aos quais você deseja aplicar a configuração.

7. Clique em **Salvar**.

## <a name="update-versions"></a>Versões de atualização

Você pode atualizar vários sensores simultaneamente no console de gerenciamento local.

Para atualizar vários sensores:

1. Acesse o [portal do Azure](https://portal.azure.com/).

2. Vá para Azure defender para IoT.

3. Vá para a página **atualizações** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Captura de tela da exibição do painel de atualizações.":::

4. Selecione **baixar** na seção **sensores** e salve o arquivo.

5. Entre no console de gerenciamento e selecione **configurações do sistema**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Captura de tela do menu Administração para selecionar configurações do sistema.":::

6. Marque os sensores que você deseja atualizar na seção de **configuração do mecanismo do sensor** e, em seguida, selecione **atualizações automáticas**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Dois sensores mostrando o modo de aprendizado e as atualizações automáticas.":::

7. Selecione **Salvar alterações**.

8. No painel **atualização de versão dos sensores** , selecione :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Tela de atualização de versão do sensor para exibir arquivos.":::

9. Uma caixa de diálogo **carregar arquivo** é aberta. Carregue o arquivo que você baixou da página **atualizações** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Selecione o botão procurar para carregar o arquivo.":::

10. Durante o processo de atualização, o status de atualização de cada sensor aparece na janela de **Gerenciamento do site** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Observe o progresso de sua atualização.":::

## <a name="update-threat-intelligence-packages"></a>Atualizar pacotes de inteligência contra ameaças 

O pacote de dados para inteligência contra ameaças é fornecido com cada nova versão do defender para IoT ou, se necessário, entre as versões. O pacote contém assinaturas (incluindo assinaturas de malware), CVEs e outros conteúdos de segurança. 

Você pode carregar manualmente esse arquivo da página **atualizações** do portal do defender para IOT e atualizá-lo automaticamente para sensores. 

Para atualizar os dados de inteligência contra ameaças: 

1. Vá para a página de **atualizações** do defender para IOT. 

2. Baixe e salve o arquivo.

3. Entre no console de gerenciamento do. 

4. No menu lateral, selecione **configurações do sistema**. 

5. Selecione os sensores que devem receber a atualização na seção de **configuração do mecanismo do sensor** .  

6. Na seção **selecionar dados de inteligência contra ameaças** , selecione o sinal de adição ( **+** ). 

7. Carregue o pacote que você baixou da página de **atualizações** do defender for IOT.

## <a name="understand-sensor-disconnection-events"></a>Entender os eventos de desconexão do sensor

A janela **Gerenciador de sites** exibe informações de desconexão se os sensores se desconectarem de seu console de gerenciamento local atribuído. As informações de desconexão do sensor a seguir estão disponíveis:

- "O console de gerenciamento local não pode processar dados recebidos do sensor."

- "Vezes que a descompasso foi detectada. O console de gerenciamento local foi desconectado do sensor. "

- "O sensor não está se comunicando com o console de gerenciamento local. Verifique a conectividade de rede ou a validação de certificado. "

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Captura de tela da exibição da zona 1.":::

Você pode enviar alertas a terceiros com informações sobre sensores desconectados. Para obter mais informações, consulte [encaminhar alertas de falha do sensor](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Habilitar ou desabilitar sensores

Os sensores são protegidos por cinco defender para mecanismos de IoT. Você pode habilitar ou desabilitar os mecanismos para sensores conectados.

| Mecanismo | Descrição | Cenário de exemplo |
|--|--|--|
| Mecanismo de violação de protocolo | Uma violação de protocolo ocorre quando os valores de campo ou estrutura de pacote não estão em conformidade com a especificação de protocolo. | Alerta "operação MODBUS ilegal (código de função zero)". Esse alerta indica que um dispositivo primário enviou uma solicitação com o código de função 0 para um dispositivo secundário. Isso não é permitido de acordo com a especificação de protocolo e o dispositivo secundário pode não tratar a entrada corretamente. |
| Mecanismo de violação de política | Uma violação de política ocorre com um desvio do comportamento de linha de base definido na política aprendida ou configurada. | Alerta "agente de usuário HTTP não autorizado". Esse alerta indica que um aplicativo que não foi aprendido ou aprovado pela política é usado como um cliente HTTP em um dispositivo. Esse pode ser um novo navegador da Web ou aplicativo nesse dispositivo. |
| Mecanismo de malware | O mecanismo de malware detecta uma atividade de rede mal-intencionada. | Alerta "suspeita de atividade mal-intencionada (Stuxnet)". Esse alerta indica que o sensor encontrou uma atividade de rede suspeita conhecida por estar relacionada ao malware Stuxnet, que é uma ameaça persistente avançada voltada para o controle industrial e para as redes SCADA. |
| Mecanismo de anomalias | O mecanismo de malware detecta uma anomalia no comportamento de rede. | "Comportamento periódico no canal de comunicação." Esse é um componente que inspeciona as conexões de rede e localiza o comportamento periódico ou cíclico da transmissão de dados, que é comum em redes industriais. |
| Mecanismo operacional | Esse mecanismo detecta incidentes operacionais ou entidades com defeito. | `Device is Suspected to be Disconnected (Unresponsive)` alerta. Esse alerta é disparado quando um dispositivo não está respondendo a nenhuma solicitação por um período predefinido. Isso pode indicar um desligamento, desconexão ou mau funcionamento do dispositivo.
|

Para habilitar ou desabilitar mecanismos para sensores conectados:

1. No painel esquerdo do console, selecione **configurações do sistema**.

2. Na seção **configuração do mecanismo do sensor** , selecione **habilitar** ou **desabilitar** para os mecanismos.
         
3. Selecione **salvar alterações**.

   Um ponto de exclamação vermelho aparecerá se houver uma incompatibilidade de mecanismos habilitados em um dos sensores de sua empresa. O mecanismo pode ter sido desabilitado diretamente do sensor.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Incompatibilidade de mecanismos habilitados."::: 

## <a name="define-sensor-backup-schedules"></a>Definir agendamentos de backup de sensor

Você pode agendar backups de sensor e executar backups de sensor sob demanda no console de gerenciamento local. Isso ajuda a proteger contra falhas de disco rígido e perda de dados.

- O que é feito backup: configurações e dados.

- O que não é feito backup: arquivos e logs do PCAP. Você pode fazer backup e restaurar o PCAPs e os logs manualmente.

Por padrão, os sensores são submetidos a backup automaticamente às 3:00 AM diariamente. O recurso de agendamento de backup para sensores permite coletar esses backups e armazená-los no console de gerenciamento local ou em um servidor de backup externo. A cópia de arquivos de sensores para o console de gerenciamento local ocorre em um canal criptografado.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Uma exibição da tela de backup do sensor.":::

Quando o local de backup do sensor padrão é alterado, o console de gerenciamento local recupera automaticamente os arquivos do novo local no sensor ou em um local externo, desde que o console tenha permissão para acessar o local. 

Quando os sensores não são registrados com o console de gerenciamento local, a caixa de diálogo **agendamento de backup do sensor** indica que não há sensores gerenciados.  

O processo de restauração é o mesmo, independentemente de onde os arquivos estão armazenados.

### <a name="backup-storage-for-sensors"></a>Armazenamento de backup para sensores

Você pode usar o console de gerenciamento local para manter até nove backups de cada sensor gerenciado, desde que os arquivos de backup não excedam o espaço máximo de backup alocado. 

O espaço disponível é calculado com base no modelo de console de gerenciamento com o qual você está trabalhando: 

- **Modelo de produção**: o armazenamento padrão é 40 GB; o limite é de 100 GB. 

- **Modelo médio**: o armazenamento padrão é de 20 GB; o limite é de 50 GB. 

- **Modelo de laptop**: o armazenamento padrão é 10 GB; o limite é 25 GB. 

- **Modelo fino**: o armazenamento padrão é 2 GB; o limite é 4 GB. 

- **Modelo resistente**: o armazenamento padrão é 10 GB; o limite é 25 GB. 

A alocação padrão é exibida na caixa de diálogo **agendamento de backup do sensor** . 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="A tela Editar configuração do servidor de email.":::

Não há nenhum limite de armazenamento quando você está fazendo backup em um servidor externo. No entanto, você deve definir um limite de alocação superior no  >  campo **caminho personalizado** do agendamento de backup do sensor. Há suporte para os seguintes números e caracteres: `/, a-z, A-Z, 0-9, and _` . 

Aqui estão informações sobre exceder os limites de armazenamento de alocação:

- Se você exceder o espaço de armazenamento alocado, não será feito backup do sensor. 

- Se você estiver fazendo backup de mais de um sensor, o console de gerenciamento tentará recuperar os arquivos de sensor para os sensores gerenciados.  

- Se a recuperação de um sensor exceder o limite, o console de gerenciamento tentará recuperar as informações de backup do próximo sensor. 

Quando você excede o número retido de backups definidos, o arquivo de backup mais antigo é excluído para acomodar o novo.

Os arquivos de backup do sensor são nomeados automaticamente no seguinte formato: `<sensor name>-backup-version-<version>-<date>.tar` . Por exemplo: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Para fazer backup de sensores:

1. Selecione **agendar backup do sensor** na janela **configurações do sistema** . Os sensores que o console de gerenciamento local gerencia aparecem na caixa de diálogo **agendamento de backup do sensor** .  

2. Habilite a alternância **coletar backups** .  

3. Selecione um intervalo, uma data e um fuso horário do calendário. O formato de hora é baseado em um relógio de 24 horas. Por exemplo, digite 6:00 PM como **18:00**. 

4. No campo **alocação de armazenamento de backup** , insira o armazenamento que você deseja alocar para os backups. Você será notificado se exceder o espaço máximo.

5. No campo **reter último** , indique o número de backups por sensor que você deseja reter. Quando o limite é excedido, o backup mais antigo é excluído.  

6. Escolha um local de backup:  

   - Para fazer backup no console de gerenciamento local, desabilite a alternância de **caminho personalizado** . O local padrão é `/var/cyberx/sensor-backups`.  

   - Para fazer backup em um servidor externo, habilite a alternância de **caminho personalizado** e insira um local. Há suporte para os seguintes números e caracteres: `/, a-z, A-Z, 0-9, and, _` . 

7. Clique em **Salvar**. 

Para fazer backup imediatamente: 

- Selecione **fazer backup agora**. O console de gerenciamento local cria e coleta arquivos de backup de sensor. 

### <a name="receiving-backup-notifications-for-sensors"></a>Recebendo notificações de backup para sensores 

A caixa de diálogo **agendamento de backup do sensor** e o log de backup listam automaticamente as informações sobre êxitos e falhas de backup.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Exiba seus sensores e onde eles estão localizados e todas as informações relevantes.":::

Podem ocorrer falhas porque:    

- Nenhum arquivo de backup encontrado. 

- Um arquivo foi encontrado, mas não pode ser recuperado.  

- Há uma falha de conexão de rede. 

- Não há espaço suficiente alocado para o console de gerenciamento local para concluir o backup.  

Você pode enviar uma notificação por email, atualizações de syslog e notificações do sistema quando ocorrer uma falha. Para fazer isso, crie uma regra de encaminhamento em **notificações do sistema**. 

### <a name="restoring-sensors"></a>Restaurando sensores 

Você pode restaurar backups do console de gerenciamento local e usando a CLI.  

Para restaurar do console do: 

- Selecione **Restaurar imagem** na janela de configuração do **sistema do sensor** .

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Restaure um backup de sua imagem.":::

  O console, em seguida, exibe falhas de restauração.  

Para restaurar usando a CLI: 

- Entre em uma conta administrativa e digite `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Salvar um backup de sensor em um servidor SMB Externo

Para configurar um servidor SMB para que você possa salvar um backup de sensor em uma unidade externa: 

1. Crie uma pasta compartilhada no servidor SMB Externo. 

2. Obtenha o caminho da pasta, o nome de usuário e a senha necessários para acessar o servidor SMB. 

3. No defender para IoT, crie um diretório para os backups: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Editar fstab:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Edite ou crie credenciais para compartilhar. Estas são as credenciais para o servidor SMB: 

   `sudo nano /etc/samba/user` 

6. Adicionar:  

   `username=<user name>` 

   `password=<password>` 

7. Monte o diretório: 

   `sudo mount -a` 

8. Configure um diretório de backup para a pasta compartilhada no sensor do defender para IoT:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Defina `Backup.shared_location` como `<backup_folder_name_on_cyberx_server>`.

## <a name="see-also"></a>Veja também

[Gerenciar sensores individuais](how-to-manage-individual-sensors.md)
