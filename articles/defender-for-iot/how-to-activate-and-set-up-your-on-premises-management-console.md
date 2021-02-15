---
title: Ativar e configurar seu console de gerenciamento local
description: A ativação e a configuração do console de gerenciamento garantem que os sensores sejam registrados no Azure e enviem informações para o console de gerenciamento local e que o console de gerenciamento local Execute tarefas de gerenciamento em sensores conectados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d326a90ffb957604dba74982d8983acedc6ab85d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522573"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Ativar e configurar seu console de gerenciamento local 

A ativação e a configuração do console de gerenciamento local garantem que:

- Os dispositivos de rede que você está monitorando por sensores conectados são registrados com uma conta do Azure.

- Os sensores enviam informações para o console de gerenciamento local.

- O console de gerenciamento local executa tarefas de gerenciamento em sensores conectados.

- Você instalou um certificado SSL.

## <a name="sign-in-for-the-first-time"></a>Entrar pela primeira vez

Para entrar no console de gerenciamento:

- Abra um navegador da Web e insira o endereço IP e a senha que você recebeu para o console de gerenciamento local durante a instalação do sistema. Se você esqueceu sua senha, selecione **recuperar senha** e consulte [recuperação de senha](how-to-manage-the-on-premises-management-console.md#password-recovery).

## <a name="upload-an-activation-file"></a>Carregar um arquivo de ativação

Após o início da primeira vez, ative o console de gerenciamento local baixando um arquivo de ativação da página de **preços** do portal do Azure defender para IOT. Esse arquivo contém os dispositivos confirmados agregados definidos durante o processo de integração. Os **dispositivos confirmados** indicam o número de dispositivos que o defender para IOT monitorará por assinatura.

Para carregar um arquivo de ativação:

1. Vá para a página de **preços** do defender para IOT.
1. Selecione a guia **baixar o arquivo de ativação para o console de gerenciamento** . O arquivo de ativação é baixado.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Baixe o arquivo de ativação.":::

1. Selecione **configurações do sistema** no console de gerenciamento.
1. Selecione **ativação**.
1. Selecione **escolher um arquivo** e selecione o arquivo que você salvou.

Após a ativação inicial, o número de dispositivos monitorados pode exceder o número de dispositivos confirmados definidos durante a integração. Isso pode acontecer, por exemplo, se você conectar mais sensores ao console de gerenciamento. Se houver uma discrepância entre o número de dispositivos monitorados e o número de dispositivos confirmados, um aviso aparecerá no console de gerenciamento. Se isso acontecer, você deverá carregar um novo arquivo de ativação.

## <a name="set-up-a-certificate"></a>Configurar um certificado

Após a instalação do console de gerenciamento do, um certificado autoassinado local é gerado e usado para acessar o console do. Depois que um administrador entra no console de gerenciamento pela primeira vez, esse usuário é solicitado a integrar um certificado SSL/TLS. 

Dois níveis de segurança estão disponíveis:

- Atenda aos requisitos específicos de certificado e criptografia solicitados por sua organização carregando o certificado assinado por AC.
- Permitir a validação entre o console de gerenciamento e sensores conectados. A validação é avaliada em relação a uma lista de certificados revogados e à data de expiração do certificado. *Se a validação falhar, a comunicação entre o console de gerenciamento e o sensor será interrompida e um erro de validação será apresentado no console.* Essa opção é habilitada por padrão após a instalação.  

O console do oferece suporte aos seguintes tipos de certificados:

- Infraestrutura privada e de chave corporativa (PKI particular)

- Infraestrutura de chave pública (PKI pública)

- Gerado localmente no dispositivo (autoassinado localmente) 

  > [!IMPORTANT]
  > Recomendamos que você não use um certificado autoassinado. O certificado não é seguro e deve ser usado somente para ambientes de teste. O proprietário do certificado não pode ser validado e a segurança do sistema não pode ser mantida. Nunca use essa opção para redes de produção.

Para carregar um certificado:

1. Quando for solicitado após a entrada, defina um nome de certificado.
1. Carregue os arquivos CRT e de chave.
1. Insira uma frase secreta e carregue um arquivo PEM, se necessário.

Talvez seja necessário atualizar a tela depois de carregar o certificado assinado por uma autoridade de certificação.

Para desabilitar a validação entre o console de gerenciamento e os sensores conectados:

1. Selecione **Avançar**.
1. Desative a alternância **Habilitar validação em todo o sistema** .

Para obter informações sobre como carregar um novo certificado, arquivos de certificado com suporte e itens relacionados, consulte [gerenciar o console de gerenciamento local](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Conectar sensores ao console de gerenciamento local

Você deve garantir que os sensores enviem informações para o console de gerenciamento local e que o console de gerenciamento local possa executar backups, gerenciar alertas e realizar outras atividades nos sensores. Para fazer isso, use os procedimentos a seguir para verificar se você faz uma conexão inicial entre os sensores e o console de gerenciamento local.

Duas opções estão disponíveis para conectar os sensores do Azure defender para IoT ao console de gerenciamento local:

- Conectar a partir do console do sensor

- Conectar-se usando o túnel

Após a conexão, você deve configurar um site com esses sensores.

### <a name="connect-sensors-from-the-sensor-console"></a>Conectar sensores do console do sensor

Para conectar sensores específicos ao console de gerenciamento local no console do sensor:

1. No painel esquerdo do console do sensor, selecione **configurações do sistema**.

2. Selecione **conexão com o gerenciamento**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Captura de tela da janela de status de um console de gerenciamento local, mostrando desconectado.":::

3. Na caixa de texto **endereço** , digite o endereço IP do console de gerenciamento local ao qual você deseja se conectar.

4. Selecione **Conectar**. O status é alterado:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Captura de tela da janela de status de um console de gerenciamento local, mostrando conectado.":::

### <a name="connect-sensors-by-using-tunneling"></a>Conectar sensores usando o túnel

Habilite uma conexão de túnel protegida entre sensores organizacionais e o console de gerenciamento local. Essa configuração evita a interação com o firewall organizacional e, como resultado, reduz a superfície de ataque.

O uso de túnel permite que você se conecte ao console de gerenciamento local por meio de seu endereço IP e uma única porta (ou seja, 9000) a qualquer sensor.

Para configurar o túnel no console de gerenciamento local:

- Entre no console de gerenciamento local e execute os seguintes comandos:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Para configurar o túnel no sensor:

1. Abra a porta TCP 9000 no sensor (Network. Properties) manualmente. Se a porta não estiver aberta, o sensor rejeitará a conexão do console de gerenciamento local.

2. Entre em cada sensor e execute os seguintes comandos:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Configurar um site

O mapa Enterprise padrão fornece uma visão geral de seus dispositivos de acordo com vários níveis de locais geográficos.

A exibição de seus dispositivos pode ser necessária onde a estrutura organizacional e as permissões de usuário são complexas. Nesses casos, a configuração do site pode ser determinada por uma estrutura organizacional global, além da estrutura padrão do site ou da zona.

Para dar suporte a esse ambiente, você precisa criar uma topologia de negócios global com base nas unidades de negócios, regiões, sites e zonas de sua organização. Você também precisa definir permissões de acesso de usuário em relação a essas entidades usando grupos de acesso.

Os grupos de acesso permitem um melhor controle sobre onde os usuários gerenciam e analisam dispositivos na plataforma defender para IoT.

### <a name="how-it-works"></a>Como isso funciona

Para cada site, você pode definir uma unidade de negócios e uma região. Em seguida, você pode adicionar zonas, que são entidades lógicas em sua rede. 

Para cada zona, você deve atribuir pelo menos um sensor. O modelo de cinco níveis fornece a flexibilidade e a granularidade necessárias para fornecer o sistema de proteção que reflete a estrutura da sua organização.

Você pode editar seus sites diretamente de qualquer uma das exibições de mapa. Quando você está abrindo um site de uma exibição de mapa, o número de alertas abertos é exibido ao lado de cada zona.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Captura de tela de um mapa do console de gerenciamento local com a sobreposição de dados Berlim.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagrama mostrando sensores e relação regional.":::

Para configurar um site:

1. Adicione novas unidades de negócios para refletir a estrutura lógica da sua organização.

2. Adicione novas regiões para refletir as regiões da sua organização.

3. Adicione um site.

4. Adicionar zonas a um site.

5. Conecte os sensores.

6. Atribua um sensor às zonas do site.

Para adicionar unidades de negócios:

1. Na exibição Enterprise, selecione **todos os sites**  >  **gerenciar unidades de negócios**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Captura de tela mostrando a exibição gerenciar unidades de negócios.":::

2. Insira o nome da nova unidade de negócios e selecione **Adicionar**.

Para adicionar uma nova região:

1. Na exibição Enterprise, selecione **todas as regiões**  >  **gerenciar regiões**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Captura de tela mostrando a exibição gerenciar regiões.":::

2. Insira o nome da nova região e selecione **Adicionar**.

Para adicionar um novo site:

1. Na exibição Enterprise, selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: na barra superior. O cursor aparece como um sinal de adição ( **+** ).

2. Posicione o **+** no local do novo site e selecione-o. A caixa de diálogo **criar novo site** é aberta.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Captura de tela da exibição criar novo site.":::

3. Defina o nome e o endereço físico para o novo site e selecione **salvar**. O novo site aparece no mapa do site.

Para excluir um site:

1. Na janela **Gerenciamento de site** , selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: na barra que contém o nome do site e, em seguida, selecione **excluir site**. A caixa de confirmação é exibida, verificando se você deseja excluir o site.

2. Na caixa de confirmação, selecione **Sim**. A caixa de confirmação é fechada e a janela **Gerenciamento de site** é exibida sem o site que você excluiu.

## <a name="create-enterprise-zones"></a>Criar zonas corporativas

As zonas são entidades lógicas que permitem dividir dispositivos dentro de um site em grupos de acordo com várias características. Por exemplo, você pode criar grupos para linhas de produção, subestações, áreas de site ou tipos de dispositivos. Você pode definir zonas com base em qualquer característica adequada à sua organização.

Você configura zonas como parte do processo de configuração do site.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Captura de tela da exibição de zonas de gerenciamento de site.":::

A tabela a seguir descreve os parâmetros na janela de **Gerenciamento do site** .

| Parâmetro | Descrição |
|--|--|
| Nome | O nome do sensor. Você pode alterar esse nome somente do sensor. Para obter mais informações, consulte o guia do usuário do defender para IoT. |
| IP | O endereço IP do sensor. |
| Versão | A versão do sensor. |
| Conectividade | O status de conectividade do sensor. O status pode ser **conectado** ou **desconectado**. |
| Última atualização | A data da última atualização. |
| Andamento da atualização | A barra de progresso mostra o status do processo de atualização, da seguinte maneira:<br />-Carregando pacote<br />-Preparando para instalar<br />-Parando processos<br />-Fazendo backup de dados<br />-Tirando instantâneo<br />-Atualizando configuração<br />-Atualizando dependências<br />-Atualizando bibliotecas<br />-Aplicação de patches em bancos de dados<br />-Iniciando processos<br />-Validando a sanidade do sistema<br />-Validação bem-sucedida<br />-Êxito<br />-Falha<br />-Atualização iniciada<br />-Iniciando a instalaçãoogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Para obter detalhes sobre como atualizar, consulte [suporte da Microsoft](https://support.microsoft.com/) para obter ajuda. |
| Dispositivos | O número de dispositivos em que o sensor monitora. |
| Alertas | O número de alertas no sensor. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Habilita a atribuição de um sensor a zonas. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Habilita a exclusão de um sensor desconectado do site. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Indica quantos sensores estão conectados à zona no momento. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Indica quantos ativos de OT estão atualmente conectados à zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Indica o número de alertas enviados por sensores atribuídos à zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Cancele a atribuição de sensores de zonas. |

Para adicionar uma zona a um site:

1. Na janela **Gerenciamento de site** , selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: na barra que contém o nome do site e, em seguida, selecione **Adicionar zona**. A caixa de diálogo **criar nova zona** é exibida.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Captura de tela da exibição criar nova zona.":::

2. Insira o nome da zona.

3. Insira uma descrição para a nova zona que indique claramente as características que você usou para dividir o site em zonas.

4. Selecione **SALVAR**. A nova zona aparece na janela **Gerenciamento de site** no site ao qual essa zona pertence.

Para editar uma zona:

1. Na janela **Gerenciamento de site** , selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: na barra que contém o nome da zona e, em seguida, selecione **Editar zona**. A caixa de diálogo **Editar zona** é exibida.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Captura de tela que mostra a caixa de diálogo Editar zona.":::

2. Edite os parâmetros de zona e selecione **salvar**.

Para excluir uma zona:

1. Na janela **Gerenciamento de site** , selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: na barra que contém o nome da zona e, em seguida, selecione **excluir zona**.

2. Na caixa de confirmação, selecione **Sim**.

Para filtrar de acordo com o status de conectividade:

- No canto superior esquerdo, selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: Avançar para **conectividade** e, em seguida, selecione uma das seguintes opções:

  - **Todos**: apresenta todos os sensores que se reportam a este console de gerenciamento local.

  - **Conectado**: apresenta apenas sensores conectados.

  - **Desconectado**: apresenta apenas sensores desconectados.

Para filtrar de acordo com o status de atualização:

- No canto superior esquerdo, selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: Avançar para atualizar o **status** e selecione uma das seguintes opções:

  - **Todos**: apresenta todos os sensores que se reportam a este console de gerenciamento local.

  - **Válido**: apresenta sensores com um status de atualização válido.

  - **Em andamento**: apresenta os sensores que estão no processo de atualização.

  - **Com falha**: apresenta sensores cujo processo de atualização falhou.

## <a name="assign-sensors-to-zones"></a>Atribuir sensores a zonas

Para cada zona, você precisa atribuir sensores que executam a análise de tráfego local e alertas. Você pode atribuir apenas os sensores que estão conectados ao console de gerenciamento local.

Para atribuir um sensor:

1. Selecione **Gerenciamento de site**. Os sensores não atribuídos aparecem no canto superior esquerdo da caixa de diálogo.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Captura de tela da exibição de sensores não atribuídos.":::

2. Verifique se o status de **conectividade** está conectado. Caso contrário, consulte [conectar sensores ao console de gerenciamento local](#connect-sensors-to-the-on-premises-management-console) para obter detalhes sobre como se conectar. 

3. Selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: para o sensor que você deseja atribuir.

4. Na caixa de diálogo **atribuir sensor** , selecione a unidade de negócios, a região, o site e a zona a serem atribuídos.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Captura de tela da exibição atribuir sensor.":::

5. Selecione **atribuir**.

Para cancelar a atribuição e excluir um sensor:

1. Desconecte o sensor do console de gerenciamento local. Consulte [conectar sensores ao console de gerenciamento local](#connect-sensors-to-the-on-premises-management-console) para obter detalhes.

2. Na janela **Gerenciamento de site** , selecione o sensor e selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . O sensor aparece na lista de sensores não atribuídos após alguns instantes.

3. Para excluir o sensor não atribuído do site, selecione o sensor na lista de sensores não atribuídos e selecione :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="next-steps"></a>Próximas etapas

[Solucionar problemas do sensor e do console de gerenciamento local](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
