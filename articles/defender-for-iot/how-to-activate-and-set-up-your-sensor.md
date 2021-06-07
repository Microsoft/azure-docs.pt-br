---
title: Ativar e configurar seu sensor
description: Este artigo descreve como entrar e ativar um console do sensor.
ms.date: 1/12/2021
ms.topic: how-to
ms.openlocfilehash: 2fdfa0dd7048bf39ae5b53a729aef578054b30ac
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779181"
---
# <a name="activate-and-set-up-your-sensor"></a>Ativar e configurar seu sensor

Este artigo descreve como ativar um sensor e executar a configuração inicial.

Os usuários administradores executam a ativação ao entrar pela primeira vez e quando o gerenciamento de ativação é necessário. A instalação garante que o sensor esteja configurado para detectar e alertar de maneira ideal.

Analistas de segurança e usuários somente leitura não podem ativar um sensor ou gerar uma nova senha.

## <a name="sign-in-and-activation-for-administrator-users"></a>Entrada e ativação para usuários administradores

Os administradores que entram pela primeira vez devem verificar se eles têm acesso aos arquivos de recuperação de senha e de ativação que foram baixados durante a integração do sensor. Caso contrário, eles precisam de permissões do administrador de segurança do Azure, colaborador de assinatura ou proprietário da assinatura para gerar esses arquivos no portal do Azure defender para IoT.

### <a name="first-time-sign-in-and-activation-checklist"></a>Lista de verificação de entrada e ativação pela primeira vez

Antes de entrar no console do sensor, os usuários administradores devem ter acesso a:

- O endereço IP do sensor que foi definido durante a instalação.

- Credenciais de entrada do usuário para o sensor. Se você baixou um ISO para o sensor, use as credenciais padrão que você recebeu durante a instalação. Recomendamos que você crie um novo usuário *administrador* após a ativação.

- Uma senha inicial. Se você comprou um sensor pré-configurado da seta, precisará gerar uma senha ao entrar pela primeira vez.

- O arquivo de ativação associado a este sensor. O arquivo foi gerado e baixado durante a integração do sensor no portal do defender para IoT.

- Um certificado assinado por AC SSL/TLS que sua empresa requer.

### <a name="about-activation-files"></a>Sobre arquivos de ativação

O sensor foi integrado ao Azure defender para IoT em um modo de gerenciamento específico:

| Tipo de modo | Descrição |
|--|--|
| **Modo conectado à nuvem** | As informações detectadas pelo sensor são exibidas no console do sensor. As informações de alerta também são fornecidas por meio do Hub IoT e podem ser compartilhadas com outros serviços do Azure, como o Azure Sentinel. |
| **Modo conectado localmente** | As informações detectadas pelo sensor são exibidas no console do sensor. As informações de detecção também são compartilhadas com o console de gerenciamento local, se o sensor estiver conectado a ela. |

Um arquivo de ativação conectado localmente ou em nuvem foi gerado e baixado para este sensor durante a integração. O arquivo de ativação contém instruções para o modo de gerenciamento do sensor. *Um arquivo de ativação exclusivo deve ser carregado para cada sensor que você implantar.*  Na primeira vez que você entrar, será necessário carregar o arquivo de ativação relevante para esse sensor.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure defender para Portal de IoT, sensor integrado.":::

### <a name="about-certificates"></a>Sobre certificados

Após a instalação do sensor, um certificado autoassinado local é gerado e usado para acessar o console do sensor. Depois que um administrador entra no console pela primeira vez, esse usuário é solicitado a integrar um certificado SSL/TLS.

Dois níveis de segurança estão disponíveis:

- Atenda aos requisitos específicos de certificado e criptografia solicitados por sua organização, carregando o certificado assinado por AC.
- Permitir a validação entre o console de gerenciamento e sensores conectados. A validação é avaliada em relação a uma lista de certificados revogados e à data de expiração do certificado. *Se a validação falhar, a comunicação entre o console de gerenciamento e o sensor será interrompida e um erro de validação aparecerá no console.* Essa opção é habilitada por padrão após a instalação.  

O console do oferece suporte aos seguintes tipos de certificado:

- Infraestrutura privada e de chave corporativa (PKI particular)

- Infraestrutura de chave pública (PKI pública)

- Gerado localmente no dispositivo (autoassinado localmente) 

  > [!IMPORTANT]
  > Recomendamos que você não use o certificado autoassinado padrão. O certificado não é seguro e deve ser usado somente para ambientes de teste. O proprietário do certificado não pode ser validado e a segurança do sistema não pode ser mantida. Nunca use essa opção para redes de produção.

### <a name="sign-in-and-activate-the-sensor"></a>Entrar e ativar o sensor

Para entrar e ativar:

1. Vá para o console do sensor do seu navegador usando o IP definido durante a instalação. A caixa de diálogo de entrada é aberta.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Sensor do Azure defender para IoT.":::

1. Insira as credenciais definidas durante a instalação do sensor ou selecione a opção **recuperação de senha** . Se você comprou um sensor pré-configurado da seta, gere uma senha primeiro. Para obter mais informações sobre recuperação de senha, consulte [investigar falha de senha na entrada inicial](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Depois de entrar, a caixa de diálogo **ativação** é aberta. Selecione **carregar** e vá até o arquivo de ativação que você baixou durante a integração do sensor.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Selecione carregar e vá para o arquivo de ativação.":::

1. Selecione o link de **configuração de rede do sensor** se desejar alterar a configuração de rede do sensor antes da ativação. Consulte [Atualizar configuração de rede do sensor antes da ativação](#update-sensor-network-configuration-before-activation).

1. Aceite os termos e as condições.

1. Selecione **Ativar**. A caixa de diálogo certificado SSL/TLS é aberta.

1. Defina um nome de certificado.
1. Carregue os arquivos CRT e de chave.
1. Insira uma frase secreta e carregue um arquivo PEM, se necessário.
1. Selecione **Avançar**. A tela validação é aberta. Por padrão, a validação entre o console de gerenciamento e os sensores conectados está habilitada.
1. Desative a alternância **Habilitar validação em todo o sistema** para desabilitar a validação. Recomendamos que você habilite a validação.
1. Selecione **Salvar**.  

Talvez seja necessário atualizar a tela depois de carregar o certificado assinado por uma autoridade de certificação.

Para obter informações sobre como carregar um novo certificado, parâmetros de certificado com suporte e como trabalhar com comandos de certificado da CLI, consulte [gerenciar sensores individuais](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Atualizar a configuração de rede do sensor antes da ativação  

Os parâmetros de configuração de rede do sensor foram definidos durante a instalação do software ou quando você comprou um sensor pré-configurado. Os seguintes parâmetros foram definidos:

- Endereço IP
- DNS
- Gateway padrão
- Máscara de sub-rede
- Nome do host

Talvez você queira atualizar essas informações antes de ativar o sensor. Por exemplo, talvez seja necessário alterar os parâmetros pré-configurados definidos pela seta. Você também pode definir as configurações de proxy antes de ativar o sensor.

Para atualizar os parâmetros de configuração de rede do sensor:

1. Selecione o link de **configuração de rede do sensor** formulário a caixa de diálogo **ativação** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Configuração de rede do sensor.":::

2. Os parâmetros definidos durante a instalação são exibidos. A opção de definir o proxy também está disponível. Atualize as configurações conforme necessário e selecione **salvar**.

### <a name="subsequent-sign-ins"></a>Entradas subsequentes

Após a ativação pela primeira vez, o console do sensor do Azure defender para IoT é aberto após a entrada sem exigir um arquivo de ativação. Você precisa apenas de suas credenciais de logon.

Depois de entrar, o console do Azure defender para IoT é aberto.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Console do Azure defender para IoT.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Instalação inicial e aprendizagem (para administradores)

Após sua primeira entrada, o sensor do Azure defender para IoT começa a monitorar sua rede automaticamente. Os dispositivos de rede aparecerão nas seções mapa do dispositivo e inventário do dispositivo. O Azure defender para IoT começará a detectar e alertar você sobre todos os incidentes operacionais e de segurança que ocorrem em sua rede. Em seguida, você pode criar relatórios e consultas com base nas informações detectadas.

Inicialmente, essa atividade é executada no modo de aprendizado, que instrui seu sensor a aprender a atividade usual da sua rede. Por exemplo, o sensor aprende os dispositivos descobertos em sua rede, os protocolos detectados na rede e as transferências de arquivos que ocorrem entre dispositivos específicos. Essa atividade torna-se a atividade de linha de base da sua rede.

### <a name="review-and-update-basic-system-settings"></a>Examinar e atualizar as configurações básicas do sistema

Examine as configurações do sistema do sensor para certificar-se de que o sensor está configurado para detectar e alertar de maneira ideal.

Defina as configurações do sistema do sensor. Por exemplo:

- Defina ICS (ou IoT) e sub-redes segregas.

- Defina aliases de porta para protocolos específicos do site.

- Defina VLANs e nomes que estão em uso.

- Se o DHCP estiver em uso, defina intervalos DHCP legítimos.

- Defina a integração com o Active Directory e o servidor de email conforme apropriado.

### <a name="disable-learning-mode"></a>Desabilitar o modo de aprendizado

Depois de ajustar as configurações do sistema, você pode permitir que o sensor do Azure defender para IoT seja executado no modo de aprendizado até que você ache que as detecções do sistema refletem com precisão sua atividade de rede.

O modo de aprendizado deve ser executado por cerca de 2 a 6 semanas, dependendo do tamanho e da complexidade da sua rede. Depois de desabilitar o modo de aprendizado, qualquer atividade que difere da atividade de linha de base irá disparar um alerta.

Para desabilitar o modo de aprendizado:

- Selecione **configurações do sistema** e desative a opção de **aprendizado** .

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Início da primeira vez para analistas de segurança e usuários somente leitura

Antes de entrar, verifique se você tem:

- O endereço IP do sensor.
- Credenciais de entrada que o administrador forneceu.

## <a name="console-tools-overview"></a>Ferramentas de console: visão geral

Você acessa as ferramentas do console no menu lateral.

**Navegação** 

| Janela | ícone | Descrição |
| -----------|--|--|
| Painel | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Exiba um instantâneo intuitivo do estado da segurança da rede. |
| Mapa do dispositivo | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Exiba os dispositivos de rede, as conexões de dispositivo e as propriedades de dispositivo em um mapa. Várias opções de zoom, realce e filtro estão disponíveis para exibir a rede. |
| Inventário de dispositivo | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | O inventário de dispositivo exibe uma lista de atributos de dispositivo que esse sensor detecta. As opções estão disponíveis para: <br /> -Classifique ou filtre as informações de acordo com os campos de tabela e veja as informações filtradas exibidas. <br /> -Exportar informações para um arquivo CSV. <br /> -Importar detalhes do registro do Windows.|
| Alertas | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Exibe alertas quando ocorrem violações de política, desvios do comportamento de linha de base ocorrem ou qualquer tipo de atividade suspeita na rede é detectada. |
| Relatórios | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Exibir relatórios baseados em consultas de mineração de dados. |

**Analisa**

| Janela| ícone | Descrição |
|---|---|---|
| Linha do tempo do evento | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Exiba uma linha do tempo com informações sobre alertas, eventos de rede (informativos) e operações de usuário, como entradas de usuário e exclusões de usuários.|

**Navegação**

| Janela | ícone | Descrição |
|---|---|---|
| Mineração de dados | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Gere informações abrangentes e granulares sobre os dispositivos de sua rede em várias camadas. |
| Investigação | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Exiba tendências e estatísticas em uma ampla gama de widgets. |
| Avaliação de risco | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Exibir a janela **vulnerabilidades** . |

**Administrador**

| Janela | ícone | Descrição |
|---|---|---|
| Usuários | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Defina usuários e funções com vários níveis de acesso. |
| Encaminhamento | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Encaminhe informações de alerta para parceiros e fontes internas (por exemplo, Azure Sentinel) integrando-se com o defender para IoT, endereços de email, servidores de webhook e muito mais. <br /> Consulte [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md) para obter detalhes. |
| Configurações de sistema | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Defina as configurações do sistema. Por exemplo, defina as configurações de DHCP, forneça detalhes do servidor de email ou crie aliases de porta. |
| Configurações de importação | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Exibir a janela **configurações de importação** . Você pode executar alterações manuais nas informações de um dispositivo.<br /> Consulte [importar informações do dispositivo](how-to-import-device-information.md) para obter detalhes. |

**Suporte**

| Janela| ícone | Descrição |
|----|---|---|
| Suporte | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Contate a [suporte da Microsoft](https://support.microsoft.com/) para obter ajuda. |

## <a name="see-also"></a>Veja também

[Integrar um sensor](getting-started.md#onboard-a-sensor)

[Gerenciar arquivos de ativação do sensor](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Controlar qual tráfego é monitorado](how-to-control-what-traffic-is-monitored.md)
