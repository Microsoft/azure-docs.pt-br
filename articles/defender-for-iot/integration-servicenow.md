---
title: Sobre a integração do ServiceNow
titleSuffix: Azure Defender for IoT
description: O aplicativo de gerenciamento de ICS do defender para IoT para ServiceNow fornece analistas do SOC com visibilidade multidimensional nos protocolos e dispositivos IoT especializados implantados em ambientes industriais, juntamente com a análise comportamental com reconhecimento de ICS para detectar rapidamente comportamento suspeito ou anormal.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f2a4c3e79a762de19c6e8c029256cd70dedfe3dc
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557291"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>O aplicativo de gerenciamento de ICS do defender para IoT para ServiceNow

O aplicativo de gerenciamento de ICS do defender para IoT para ServiceNow fornece analistas do SOC com visibilidade multidimensional nos protocolos e dispositivos IoT especializados implantados em ambientes industriais, juntamente com a análise comportamental com reconhecimento de ICS para detectar rapidamente comportamento suspeito ou anormal. Essa é uma evolução importante, considerando a convergência contínua de ti e outras para dar suporte a novas iniciativas de IoT, como máquinas inteligentes e inteligência em tempo real.

O aplicativo também habilita a resposta a incidentes e a ele, de dentro de um SOC corporativo.

## <a name="about-defender-for-iot"></a>Sobre o defender para IoT

O defender para IoT fornece a única plataforma ICS e IoT segurança cibernética criada por especialistas de equipe azul com um registro de acompanhamento que defende a infraestrutura nacional crítica e a única plataforma com análise de ameaças com reconhecimento de ICS e aprendizado de máquina patenteados. O defender para IoT fornece:

- Informações imediatas sobre o ICS o cenário do dispositivo com uma ampla gama de detalhes sobre os atributos.

- Conhecimento incorporado profundo com reconhecimento de ICS de OT protocolos, dispositivos, aplicativos e seus comportamentos.

- Informações imediatas sobre vulnerabilidades e ameaças conhecidas de dia zero.

- Uma tecnologia de modelagem de ameaças do ICS automatizada para prever os caminhos mais prováveis de ataques de ICS direcionados por meio de análises proprietárias.

> [!Note]
> Referências a CyberX referem-se ao Azure defender para IoT.

## <a name="about-the-integration"></a>Sobre a integração

A integração do defender para IoT com o ServiceNow fornece um novo nível de visibilidade centralizada, monitoramento e controle para a IoT e para o cenário. Essas plataformas de ponte permitem a visibilidade automatizada do dispositivo e o gerenciamento de ameaças para dispositivos de IoT & ICS anteriormente inacessíveis.

### <a name="threat-management"></a>Gerenciamento de ameaças

O aplicativo de gerenciamento de ICS do defender para IoT ajuda a:

- Reduza o tempo necessário para as organizações de infraestrutura industrial e crítica detectarem, investigarem e agirem em ameaças cibernéticos.

- Obtenha inteligência em tempo real sobre os riscos.

- Correlacione o defender para alertas de IoT com fluxos de trabalho de monitoramento de ameaças e gerenciamento de incidentes do ServiceNow.

- Dispare os tíquetes e fluxos de trabalho do ServiceNow com outros serviços e aplicativos na plataforma ServiceNow.

As ameaças de segurança de ICS e SCADA são identificadas pelo defender para mecanismos de segurança de IoT, que fornecem resposta de alerta imediata para ataques de malware, rede e desvios de política de segurança, bem como anomalias operacionais e de protocolo. Para obter detalhes sobre os detalhes do alerta enviados ao ServiceNow, consulte [relatórios de alertas](#alert-reporting).

### <a name="device-visibility-and-management"></a>Visibilidade e gerenciamento de dispositivos

O banco de dados de gerenciamento de configuração (CMDB) do ServiceNow é aprimorado e complementado com um rico conjunto de atributos de dispositivo enviados pela plataforma defender para IoT. Isso garante uma visibilidade abrangente e contínua no cenário do dispositivo e permite que você monitore e responda de um único painel de vidro. Para obter detalhes sobre os atributos de dispositivo enviados para o ServiceNowSee, confira [Exibir o defender para detecções de IOT no ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Requisitos e arquitetura do sistema

Este artigo descreve:

- **Requisitos de software**  
- **Arquitetura**

## <a name="software-requirements"></a>Requisitos de software

- Serviço de gerenciamento de serviços do ServiceNow versão 3.0.2

- Patch do defender for IoT 2.8.11.1 ou superior

> [!Note]
> Se você já estiver trabalhando com uma integração do defender para IoT e do ServiceNow e atualizar usando o console de gerenciamento local, os dados do anterior recebidos do defender para sensores IoT devem ser apagados do ServiceNow.

## <a name="architecture"></a>Arquitetura

### <a name="on-premises-management-console-architecture"></a>Arquitetura do console de gerenciamento local

O console de gerenciamento local fornece uma fonte unificada para todas as informações de dispositivo e alerta enviadas ao ServiceNow.

Você pode configurar um console de gerenciamento local para se comunicar com uma instância do ServiceNow. O console de gerenciamento local envia dados de sensor para o aplicativo defender para IoT usando a API REST.

Se você estiver configurando seu sistema para trabalhar com um console de gerenciamento local, desabilite a sincronização do ServiceNow, as regras de encaminhamento e as configurações de proxy em sensores, se elas foram configuradas.

Essas configurações devem ser configuradas para o console de gerenciamento local. As instruções de configuração são descritas neste artigo.

### <a name="sensor-architecture"></a>Arquitetura do sensor

Se você quiser configurar seu ambiente para incluir comunicação direta entre sensores e ServiceNow, para cada sensor, defina a sincronização do ServiceNow, as regras de encaminhamento e a configuração de proxy (se for necessário um proxy).

É recomendável configurar sua integração usando o console de gerenciamento local para se comunicar com o ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Criar tokens de acesso no ServiceNow

Este artigo descreve como criar um token de acesso no ServiceNow. O token é necessário para se comunicar com o defender para IoT.

Você precisará da **ID do cliente** e do **segredo do cliente** ao criar o defender para regras de encaminhamento de IOT, que encaminham informações de alerta para o servicenow e ao configurar o defender para IOT para enviar atributos de dispositivo para as tabelas do servicenow.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Configurar o defender para IoT para se comunicar com o ServiceNow

Este artigo descreve como configurar o defender para IoT para se comunicar com o ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Enviar informações de alerta do defender para IoT

Este artigo descreve como configurar o defender para IoT para enviar informações de alerta para tabelas do ServiceNow. Para obter informações sobre os dados de alerta enviados, consulte [relatórios de alertas](#alert-reporting).

Os defensores dos alertas de IoT aparecem no ServiceNow como incidentes de segurança.

Defina uma regra de *encaminhamento* do defender para IOT para enviar informações de alerta para o ServiceNow.

Para definir a regra:

1. No painel do defender para IoT esquerdo, selecione **encaminhamento**.  

1. Selecione o :::image type="content" source="media/integration-servicenow/plus.png" alt-text="botão de mais ícone."::: . A caixa de diálogo Criar regra de encaminhamento é aberta.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Criar regra de encaminhamento":::

1. Adicione um nome de regra.

1. Defina critérios sob os quais o defender para IoT irá disparar a regra de encaminhamento. Trabalhar com critérios de regra de encaminhamento ajuda a identificar e gerenciar o volume de informações enviadas do defender para IoT para o ServiceNow. As seguintes opções estão disponíveis:

    - **Níveis de severidade:** Este é o incidente de nível mínimo de segurança a ser encaminhado. Por exemplo, se a **secundária** estiver selecionada, os alertas secundários e todos os alertas acima desse nível de severidade serão encaminhados. Os níveis são predefinidos pelo defender para IoT.

    - **Protocolos:** Dispare apenas a regra de encaminhamento se o tráfego detectado estiver em execução em protocolos específicos. Selecione os protocolos necessários na lista suspensa ou escolha todos eles.

    - **Mecanismos:** Selecione os mecanismos necessários ou escolha todos eles. Os alertas dos mecanismos selecionados serão enviados.

1. Verifique se as **notificações de alerta de relatório** estão selecionadas.

1. Na seção ações, selecione **Adicionar** e selecione **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Selecione ServiceNow nas opções da lista suspensa.":::

1. Insira os parâmetros de ação do ServiceNow:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Preencher os parâmetros de ação do ServiceNow":::

1. No painel **ações** , defina os seguintes parâmetros:

  | Parâmetro | Descrição |
  |--|--|
  | Domínio | Insira o endereço IP do servidor ServiceNow. |
  | Nome de Usuário | Insira o nome de usuário do servidor ServiceNow. |
  | Senha | Insira a senha do servidor ServiceNow. |
  | ID do Cliente | Insira a ID do cliente que você recebeu para o defender para IoT na página **registros do aplicativo** no ServiceNow. |
  | Segredo do cliente | Insira a cadeia de caracteres secreta do cliente que você criou para o defender para IoT na página **registros do aplicativo** no ServiceNow. |
  | Tipo de Relatório | **Incidentes**: encaminhe uma lista de alertas que são apresentados no ServiceNow com uma ID de incidente e uma breve descrição de cada alerta.<br /><br />**Aplicativo defender para IOT**: encaminhe as informações completas do alerta, incluindo os detalhes do sensor, o mecanismo, a origem e os endereços de destino. As informações são encaminhadas para o defender para IoT no aplicativo ServiceNow. |

1. Selecione **SALVAR**. Os defensores dos alertas de IoT aparecem como incidentes no ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Enviar os atributos do dispositivo defender para IoT

Este artigo descreve como configurar o defender para IoT para enviar por push um amplo intervalo de atributos de dispositivo para as tabelas do ServiceNow. Consulte **_Inventory Information_* _ para obter detalhes sobre o tipo de informações enviadas por push para o ServiceNow.

Para enviar atributos para o ServiceNow, você deve mapear seu console de gerenciamento local para uma instância do ServiceNow. Isso garante que a plataforma defender para IoT possa se comunicar e autenticar com a instância do.

Para adicionar uma instância do ServiceNow:

1. Entre em seu console de gerenciamento local do defender para IoT.

1. Selecione _ *configurações do sistema** e, em seguida, **ServiceNow** na seção integração do console de gerenciamento local.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Selecione o botão ServiceNow.":::

1. Insira os seguintes parâmetros de sincronização na caixa de diálogo do ServiceNow Sync.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="A caixa de diálogo de sincronização do ServiceNow.":::

     Parâmetro | Descrição |
    |--|--|
    | Habilitar sincronização | Habilite e desabilite a sincronização depois de definir parâmetros. |
    | Frequência de sincronização (minutos) | Por padrão, as informações são enviadas por push para o ServiceNow a cada 60 minutos. O mínimo é de 5 minutos. |
    | Instância do ServiceNow | Insira a URL da instância do ServiceNow. |
    | ID do Cliente | Insira a ID do cliente que você recebeu para o defender para IoT na página **registros do aplicativo** no ServiceNow. |
    | Segredo do cliente | Insira a cadeia de caracteres secreta do cliente que você criou para o defender para IoT na página **registros do aplicativo** no ServiceNow. |
    | Nome de Usuário | Insira o nome de usuário para esta instância. |
    | Senha | Insira a senha para esta instância. |

1. Selecione **SALVAR**.

## <a name="verify-communication"></a>Verificar comunicação

Verifique se o console de gerenciamento local está conectado à instância do ServiceNow revisando a data da *última sincronização* .

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Verifique se a comunicação ocorreu olhando para a última sincronização.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Configurar as integrações usando o proxy HTTPS

Ao configurar a integração do defender para IoT e do ServiceNow, o console de gerenciamento local e o servidor ServiceNow se comunicam usando a porta 443. Se o servidor ServiceNow estiver atrás do proxy, a porta padrão não poderá ser usada.

O defender para IoT dá suporte a um proxy HTTPS na integração do ServiceNow, habilitando a alteração da porta padrão usada para integração.

Para configurar o proxy:

1. Editar propriedades globais no console de gerenciamento local:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Adicione os seguintes parâmetros:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Salve e saia.

4. Execute o seguinte comando: `sudo monit restart all`

Após a configuração, todos os dados do ServiceNow são encaminhados usando o proxy configurado.

## <a name="download-the-defender-for-iot-application"></a>Baixar o aplicativo defender para IoT

Este artigo descreve como baixar o aplicativo.

Para acessar o aplicativo defender para IoT:

1. Navegue até <https://store.servicenow.com/>

2. Procure `Defender for IoT` ou `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Procure CyberX na barra de pesquisa.":::

3. Selecione o aplicativo.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Selecione o aplicativo na lista.":::

4. Selecione **solicitar aplicativo.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Entre no aplicativo com suas credenciais.":::

5. Entre e baixe o aplicativo.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Exibir as detecções do defender for IoT no ServiceNow

Este artigo descreve os atributos de dispositivo e as informações de alerta apresentadas no ServiceNow.

Para exibir os atributos do dispositivo:

1. Entre no ServiceNow.

2. Navegue até **plataforma CyberX**.

3. Navegue até **inventário** ou **alerta**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventário ou alerta":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Informações de inventário

O banco de dados de gerenciamento de configuração (CMDB) é aprimorado e complementado por dados enviados pelo defender para IoT para o ServiceNow. Ao adicionar ou atualizar atributos de dispositivo nas tabelas do item de configuração do CMDB do ServiceNow, o defender para IoT pode disparar os fluxos de trabalho e as regras de negócio do ServiceNow.

As informações a seguir estão disponíveis:

- Atributos de dispositivo, por exemplo, o dispositivo MAC, sistema operacional, fornecedor ou protocolo detectado.

- Informações de firmware, por exemplo, a versão do firmware e o número de série.

- Informações de dispositivo conectadas, por exemplo, a direção do tráfego entre a origem e o destino.

### <a name="devices-attributes"></a>Atributos de dispositivos

Este artigo descreve os atributos de dispositivo enviados por push para o ServiceNow.

| Item | Descrição |
|--|--|
| Dispositivo | O nome do sensor que detectou o tráfego. |
| ID | A ID do dispositivo atribuída pelo defender para IoT. |
| Nome | O nome do dispositivo. |
| Endereço IP | Endereço ou endereços IP do dispositivo. |
| Tipo | O tipo de dispositivo, por exemplo, um switch, PLC, Historian ou controlador de domínio. |
| Endereço MAC | O endereço ou endereços MAC do dispositivo. |
| Sistema operacional | O sistema operacional do dispositivo. |
| Fornecedor | O fornecedor do dispositivo. |
| Protocolos | Os protocolos detectados no tráfego gerado pelo dispositivo. |
| Proprietário | Insira o nome do proprietário do dispositivo. |
| Localização | Insira o local físico do dispositivo. |

Exibir dispositivos conectados a um dispositivo nesta exibição.

Para exibir dispositivos conectados:

1. Selecione um dispositivo e, em seguida, **Selecione o dispositivo** listado em para esse dispositivo.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Selecione o dispositivo desejado na lista.":::

1. Na caixa de diálogo **detalhes do dispositivo** , selecione **dispositivos conectados**.

### <a name="firmware-details"></a>Detalhes do firmware

Este artigo descreve as informações de firmware do dispositivo enviadas por push para o ServiceNow.

| Item | Descrição |
|--|--|
| Dispositivo | O nome do sensor que detectou o tráfego. |
| Dispositivo | O nome do dispositivo. |
| Endereço | O endereço IP do dispositivo. |
| Endereço do módulo | O modelo do dispositivo e o número do slot ou a ID. |
| Serial | O número de série do dispositivo. |
| Modelo | O número do modelo do dispositivo. |
| Versão | O número de versão do firmware. |
| Dados adicionais | Mais dados sobre o firmware, conforme definido pelo fornecedor, por exemplo, o tipo de dispositivo. |

### <a name="connection-details"></a>Detalhes da conexão

Este artigo descreve as informações de conexão do dispositivo enviadas por push para o ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="As informações de conexão do dispositivo":::

| Item | Descrição |
|--|--|
| Dispositivo | O nome do sensor que detectou o tráfego. |
| Direção | A direção do tráfego. <br /> <br /> - **Uma maneira** indica que o destino é o servidor e a origem é o cliente. <br /> <br /> - **Duas formas** indica que a origem e o destino são servidores ou que o cliente é desconhecido. |
| ID do dispositivo de origem | O endereço IP do dispositivo que se comunica com o dispositivo conectado. |
| Nome do dispositivo de origem | O nome do dispositivo que se comunica com o dispositivo conectado. |
| ID do dispositivo de destino | O endereço IP do dispositivo conectado. |
| Nome do dispositivo de destino | O nome do dispositivo conectado. |

## <a name="alert-reporting"></a>Relatório de alertas

Os alertas são disparados quando os defensores de mecanismos IoT detectam alterações no tráfego de rede e no comportamento que exigem sua atenção. Para obter detalhes sobre os tipos de alertas gerados por cada mecanismo, consulte [sobre os mecanismos de alerta](#about-alert-engines).

Este artigo descreve as informações de alerta do dispositivo enviadas por push para o ServiceNow.

| Item | Descrição |
|--|--|
| Criado | A hora e a data em que o alerta foi gerado. |
| Mecanismo | O mecanismo que detectou o evento. |
| Título | O título do alerta. |
| Descrição | A descrição do alerta. |
| Protocolo | O protocolo detectado no tráfego. |
| Severity | A severidade do alerta definida pelo defender para IoT. |
| Dispositivo | O nome do sensor que detectou o tráfego. |
| Nome de origem | O nome da origem. |
| Endereço IP de origem| O endereço IP de origem. |
| Nome do destino | O nome de destino. |
| Endereço IP de destino | O endereço IP de destino. |
| Destinatário | Insira o nome do indivíduo atribuído ao tíquete. |

### <a name="updating-alert-information"></a>Atualizando informações de alerta

Selecione a entrada na coluna criado para exibir informações de alerta em um formulário. Você pode atualizar os detalhes do alerta e atribuir o alerta a um indivíduo para revisão e manipulação.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Exibir as informações do alerta.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Sobre os mecanismos de alerta

Este artigo descreve o tipo de alerta que cada mecanismo dispara.

| Tipo de alerta | Descrição |
|--|--|
| Alertas de violação de política | Disparado quando o mecanismo de violação de política detecta um desvio do tráfego aprendido anteriormente. Por exemplo: <br /><br />-Um novo dispositivo é detectado. <br /><br />-Uma nova configuração é detectada em um dispositivo. <br /><br />-Um dispositivo não definido como um dispositivo de programação realiza uma alteração de programação. <br /><br />-Uma versão de firmware foi alterada. |
| Alertas de violação de protocolo | Disparado quando o mecanismo de violação de protocolo detecta uma estrutura de pacotes ou valores de campo que não estão em conformidade com a especificação de protocolo. |
| Alertas operacionais | Disparado quando o mecanismo operacional detecta incidentes operacionais de rede ou problemas de funcionamento do dispositivo. Por exemplo, um dispositivo de rede foi interrompido usando um comando Stop PLC ou uma interface em um sensor interrompeu o tráfego de monitoramento. |
| Alertas de malware | Disparado quando o mecanismo de malware detecta uma atividade de rede mal-intencionada, por exemplo, ataques conhecidos como Conficker. |
| Alertas de anomalias | Disparado quando o mecanismo de anomalias detecta um desvio. Por exemplo, um dispositivo está executando a verificação de rede, mas não está definido como um dispositivo de verificação. |

## <a name="next-steps"></a>Próximas etapas

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
