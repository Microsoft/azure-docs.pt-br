---
title: Sobre a integração do Cisco ISE pxGrid
description: A ponte dos recursos do defender for IoT com o Cisco ISE pxGrid fornece às equipes de segurança visibilidade sem precedentes do dispositivo para o ecossistema empresarial.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784077"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Sobre a integração do Cisco ISE pxGrid

O defender para IoT fornece a única plataforma ICS e IoT segurança cibernética criada por especialistas de equipe azul com um registro de acompanhamento que defende a infraestrutura nacional crítica e a única plataforma com análise de ameaças com reconhecimento de ICS e aprendizado de máquina patenteados. O defender para IoT fornece:

- Informações imediatas sobre dispositivos ICS, vulnerabilidades e ameaças conhecidas e de dia zero.

- Conhecimento incorporado profundo com reconhecimento de ICS de OT protocolos, dispositivos, aplicativos e seus comportamentos.

- Uma tecnologia de modelagem de ameaças do ICS automatizada para prever os caminhos mais prováveis de ataques de ICS direcionados por meio de análises proprietárias.

## <a name="powerful-device-visibility-and-control"></a>Visibilidade e controle avançados do dispositivo

A integração do defender for IoT com o Cisco ISE pxGrid fornece um novo nível de visibilidade, monitoramento e controle centralizados para o cenário de OT.

Essas plataformas de ponte permitem a visibilidade e a proteção automatizada do dispositivo para dispositivos ICS e IIoT anteriormente inacessíveis.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Visibilidade do dispositivo ICS e IIoT: abrangente e profundo

O defender patenteado para tecnologias de IoT garante o gerenciamento de inventário e de descoberta de dispositivos IIoT e de ICS abrangente e profundo para dados corporativos.

Tipos de dispositivo, fabricantes, portas abertas, números de série, revisões de firmware, endereços IP e dados de endereço MAC e mais são atualizados em tempo real. O defender para IoT pode aprimorar ainda mais a visibilidade, a descoberta e a análise dessa linha de base integrando-se a fontes de dados empresariais críticas. Por exemplo, CMDBs, DNS, firewalls e APIs da Web.

Além disso, a plataforma defender para IoT combina monitoramento passivo e técnicas de investigação seletiva opcionais para fornecer o inventário mais preciso e detalhado de dispositivos em organizações de infraestruturas industriais e críticas.

### <a name="bridged-capabilities"></a>Recursos de ponte

A ponte desses recursos com o Cisco ISE pxGrid fornece à equipe de segurança visibilidade de dispositivo sem precedentes para o ecossistema empresarial.

Integração direta e robusta com o Cisco ISE pxGrid garante que nenhum dispositivo fique sem descoberta e que nenhuma informação do dispositivo seja perdida.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Exemplo das categorias de ponto de extremidade OUI.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Pontos de extremidade de exemplo no sistema":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Captura de tela dos atributos localizados no sistema.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Cobertura de caso de uso: políticas do ISE baseadas em atributos do defender para IoT

Use políticas avançadas do ISE com base no defender para aprendizado profundo de IoT para lidar com os requisitos de caso de uso do ICS e do IoT.

Trabalhar com políticas permite que você feche o ciclo de segurança e traga sua rede para conformidade em tempo real.

Por exemplo, os clientes podem usar o defender para IoT ICS e os atributos de IoT para criar políticas que lidem com os seguintes casos de uso, como:

- Crie uma política de autorização para permitir que dispositivos conhecidos e autorizados em uma zona confidencial com base em atributos permitidos – por exemplo, versão de firmware específica para o Rockwell Automation PLCs.

- Notifique as equipes de segurança quando um dispositivo de ICS for detectado em uma zona não-OT.

- Corrija computadores com fornecedores desatualizados ou não compatíveis.

- Colocar em quarentena e bloquear dispositivos conforme necessário.

- Gere relatórios sobre o PLCs ou RTUs executando o firmware com CVEs (vulnerabilidades conhecidas).

### <a name="about-this-article"></a>Sobre este artigo

Este artigo descreve como configurar o pxGrid e o defender para a plataforma IoT para garantir que o defender para IoT insira atributos para o Cisco ISE.

### <a name="getting-more-information"></a>Obtendo mais informações

Para obter mais informações sobre os requisitos de integração do Cisco ISE pxGrid, consulte <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Requisitos do sistema de integração

Este artigo descreve os requisitos do sistema de integração:

Requisitos do defender for IoT

- Defender para IoT versão 2,5

Requisitos da Cisco

- pxGrid versão 2,0

- Cisco ISE versão 2,4

Requisitos de rede

- Verifique se o dispositivo defender para IoT tem acesso à interface de gerenciamento do Cisco ISE.

- Verifique se você tem acesso à CLI para todos os defender para dispositivos IoT em sua empresa.

> [!NOTE]
> Somente dispositivos com endereços MAC são sincronizados com o Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Instalação do Cisco ISE pxGrid

Este artigo descreve como:

- Configurar a comunicação com o pxGrid

- Assinar o tópico de dispositivo de ponto de extremidade

- Gerar certificados

- Definir configurações de pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Configurar a comunicação com o pxGrid

Este artigo descreve como configurar a comunicação com o pxGrid.

Para configurar a comunicação:

1. Entre no Cisco ISE.

1. Selecione  > **sistema** de administração e **implantação**.

1. Selecione o nó necessário. Na guia Configurações gerais, marque a **caixa de seleção pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Verifique se a caixa de seleção pxgrid está selecionada.":::

1. Selecione a guia **configuração de criação de perfil** .

1. Marque a **caixa de seleção pxGrid**. Adicione uma descrição.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Captura de tela da descrição adicionar":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Assinar o tópico de dispositivo de ponto de extremidade

Verifique se o nó ISE pxGrid assinou o tópico dispositivo de ponto de extremidade. Navegue até **Administração** > **serviços de pxGrid** > **clientes Web**. Lá, você pode verificar se o ISE assinou o tópico de dispositivo do ponto de extremidade.

## <a name="generate-certificates"></a>Gerar certificados

Este artigo descreve como gerar certificados.

Para gerar:

1. Selecione **Administração**  >  **Serviços pxGrids** e, em seguida, selecione **certificados**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Selecione a guia certificados para gerar um certificado.":::

1. No campo **eu** quero, selecione **gerar um único certificado (sem uma solicitação de assinatura de certificado)**.

1. Preencha os campos restantes e selecione **criar**.

1. Crie a chave de certificado do cliente e o certificado do servidor e converta-as no formato do repositório de chaves Java. Você precisará copiá-las para cada sensor do defender para IoT em sua rede.

## <a name="define-pxgrid-settings"></a>Definir configurações de pxGrid

Para definir as configurações:

1. Selecione **Administração**  >  **serviços de pxGrid** e selecione **configurações**.

1. Habilite a **aprovação automática de novas contas com base em certificado** e **permita a criação de conta baseada em senha.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Certifique-se de que ambas as caixas de seleção estejam selecionadas.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Configurar o defender para dispositivos IoT

Este artigo descreve como configurar o defender para dispositivos IoT para se comunicar com o pxGrid. A configuração deve ser realizada em todos os defender para dispositivos IoT que injetarão dados no Cisco ISE.

Para configurar dispositivos:

1. Entre na CLI do sensor.

1. Copie `trust.jks` e, que foram criados anteriormente no sensor. Copie-os para: `/var/cyberx/properties/` .

1. Edite `/var/cyberx/properties/pxgrid.properties`:

    1. Adicione uma chave e uma relação de confiança e, em seguida, armazene nomes de e senhas.

    2. Adicione o nome do host da instância de pxGrid.

    3. `Enabled=true`

1. Reinicie o dispositivo.

1. Repita essas etapas para cada dispositivo em sua empresa que injetará dados.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Exibir e gerenciar detecções no Cisco ISE

1. As detecções de ponto de extremidade são exibidas na   >  guia **pontos de extremidades** de visibilidade do contexto do ISE.

1. Selecione **política**  >  **criação de perfil**  >  **Adicionar**  >  **regras**  >  **+ condição**  >  **criar nova condição**.

1. Selecione **atributo** e use os dicionários do dispositivo IOT para criar uma regra de criação de perfil com base nos atributos injetados. Os atributos a seguir são injetados.

    - Tipo de dispositivo

    - Revisão de hardware

    - Endereço IP

    - Endereço MAC

    - Nome

    - Produto ID

    - Protocolo

    - Número de série

    - Revisão de software

    - Fornecedor

Somente dispositivos com endereços MAC são sincronizados.

## <a name="troubleshooting-and-logs"></a>Solução de problemas e logs

Os logs podem ser encontrados em:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Próximas etapas

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
