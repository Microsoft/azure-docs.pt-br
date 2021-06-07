---
title: Integração do Palo Alto
description: O defender para IoT integrou sua plataforma contínua de monitoramento de ameaças do ICS aos firewalls de última geração do Palo Alto para permitir o bloqueio de ameaças críticas, de forma mais rápida e eficiente.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783924"
---
# <a name="about-the-palo-alto-integration"></a>Sobre a integração do Palo Alto

O defender para IoT integrou sua plataforma contínua de monitoramento de ameaças do ICS aos firewalls de última geração do Palo Alto para permitir o bloqueio de ameaças críticas, de forma mais rápida e eficiente.

Os seguintes tipos de integração estão disponíveis:

- Opção de bloqueio automático: integração direta do defender para IoT-Palo alto

- Enviando recomendações para bloquear o sistema de gerenciamento central: defender para IoT-Panorama integração

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Configurar o bloqueio imediato por um firewall do Palo Alto especificado

Em casos críticos, como alertas relacionados a malware, você pode habilitar o bloqueio automático. Isso é feito com a configuração de uma regra de encaminhamento no defender para IoT que envia o comando de bloqueio diretamente para um firewall de Palo Alto específico.

Quando o defender para IoT identifica uma ameaça crítica, ele envia um alerta que inclui uma opção de bloquear a fonte infectada. A seleção de **Bloquear origem** nos detalhes do alerta ativa a regra de encaminhamento, que envia o comando de bloqueio para o Firewall do Palo Alto especificado.

Para configurar:

1. No painel esquerdo, selecione **encaminhamento**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="A tela de alerta de encaminhamento.":::

1. Selecione **criar regra de encaminhamento**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Criar regra de encaminhamento":::

1. Para configurar a regra de encaminhamento do Palo Alto NGFW:  
 
   - Defina os parâmetros de regra padrão e, na caixa suspensa **ações** , selecione **Enviar para Palo Alto NGFW**.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Edite sua regra de encaminhamento.":::

1. No painel Ações, defina os seguintes parâmetros:

   - **Host**: Insira o endereço IP do servidor NGFW.
   - **Porta**: Insira a porta do servidor NGFW.
   - **Nome de usuário**: Insira o nome de usuário do servidor NGFW.
   - **Senha**: Insira a senha do servidor NGFW.
   - **Configurar**: Configure as seguintes opções para permitir o bloqueio das fontes suspeitas pelo firewall Palo Alto:
     - **Bloquear códigos de função ilegais**: violações de protocolo-valor de campo inválido violando a especificação do protocolo ICS (exploração em potencial).
     - **Bloquear atualizações de firmware/programação de PLC não autorizadas**: alterações de PLC não autorizadas.
     - **Bloquear interrupção não autorizada de PLC**: parada de PLC (tempo de inatividade).
     - **Bloquear alertas relacionados a malware**: bloqueio de tentativas de malware industrial (Triton, NotPetya, etc.). Você pode selecionar a opção de **bloqueio automático**. Nesse caso, o bloqueio é executado automaticamente e imediatamente.
     - **Bloquear a verificação não autorizada**: verificação não autorizada (reconhecimento em potencial).
     
1. Selecione **Enviar**.

Para bloquear a origem suspeita: 

1. No painel **alertas** , selecione o alerta relacionado à integração do Palo Alto. A caixa de diálogo **detalhes do alerta** é exibida.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Detalhes do alerta":::

1. Para bloquear automaticamente a origem suspeita, selecione **Bloquear origem**. A caixa de diálogo **confirmar** é exibida.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirme o bloqueio na tela confirmar.":::

1. Na caixa de diálogo **confirmar** , selecione **OK**. A fonte suspeita é bloqueada pelo firewall Palo Alto.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Enviando políticas de bloqueio para o Palo Alto panorama

As redes do defender for IoT e Palo Alto têm uma integração com a prateleira que cria automaticamente novas políticas no Palo Alto Networks NMS, panorama. Essa integração requer confirmação pelo administrador do panorama e não permite o bloqueio automático.

A integração é destinada aos seguintes incidentes:

- **Alterações de PLC não autorizadas:** Uma atualização para a lógica de escada e o firmware de um dispositivo. Isso pode representar uma atividade legítima ou uma tentativa de comprometer o dispositivo. O comprometimento pode acontecer com a inserção de código mal-intencionado, como um cavalo de Troia de acesso remoto (RAT) ou parâmetros que causam o processo físico, como uma turbina giratória, para operar de maneira não segura.

- **Violação de protocolo:** Uma estrutura de pacote ou um valor de campo que viola a especificação de protocolo. Isso pode representar um aplicativo configurado incorretamente ou uma tentativa mal-intencionada de comprometer o dispositivo. Por exemplo, causando uma condição de estouro de buffer no dispositivo de destino.

- **Parada de PLC:** Um comando que faz com que o dispositivo pare de funcionar, arriscando, assim, o processo físico que está sendo controlado pelo PLC.

- **Malware industrial encontrado na rede ICS:** Malware que manipula dispositivos ICS usando seus protocolos nativos, como TRITON e Industroyer. O defender para IoT também detecta malware de ti que foi movido mais tarde para o ambiente ICS e SCADA, como Conficker, WannaCry e NotPetya.

- **Verificando malware:** Ferramentas de reconhecimento que coletam dados sobre a configuração do sistema em uma fase de ataque prévio. Por exemplo, o cavalo de Troia Havex examina redes industriais para dispositivos que usam OPC, que é um protocolo padrão usado por sistemas SCADA baseados em Windows para se comunicar com dispositivos ICS.

## <a name="the-process"></a>O processo

Quando o defender para IoT detecta um caso de uso pré-configurado, o botão **Bloquear origem** é adicionado ao alerta. Em seguida, quando o usuário **CyberX** seleciona o botão **Bloquear origem** , o defender para IOT cria políticas no panorama enviando a regra de encaminhamento predefinida.

A política é aplicada somente quando o administrador do panorama a envia para o NGFW relevante na rede.

Nas redes de ti, pode haver endereços IP dinâmicos. Portanto, para essas sub-redes, a política deve ser baseada no FQDN (nome DNS) e não no endereço IP. O defender para IoT executa a pesquisa inversa e faz a correspondência de dispositivos com o endereço IP dinâmico para seu FQDN (nome DNS) a cada número de horas configuradas.

Além disso, o defender para IoT envia um email para o usuário de panorama relevante para notificar que uma nova política criada pelo defender para IoT está aguardando a aprovação. A figura a seguir apresenta a arquitetura de integração do defender for IoT-Panorama.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Arquitetura de integração do CyberX-panorama":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Criar políticas de bloqueio panorâmico no defender para configuração de IoT

### <a name="to-configure-dns-lookup"></a>Para configurar a pesquisa de DNS

1. No painel esquerdo, selecione **configurações do sistema**.

1. No painel **configurações do sistema** , selecione **configurações de DNS** :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Defina as configurações de DNS.":::

1. Na caixa de diálogo **Editar configurações de DNS** , defina os seguintes parâmetros:

   - **Status**: o status do resolvedor de DNS.

   - **Endereço do servidor DNS**: Insira o endereço IP ou o FQDN do servidor DNS da rede.
   - **Porta do servidor DNS**: Insira a porta usada para consultar o servidor DNS.
   - **Sub-redes**: defina o intervalo de sub-rede do endereço IP dinâmico. O intervalo que o defender para IoT reverte pesquisa seu endereço IP no servidor DNS para corresponder ao nome FQDN atual.
   - **Agendar pesquisa inversa**: defina as opções de agendamento da seguinte maneira:
     - Por horários específicos: especifique quando executar a pesquisa inversa diariamente.
     - Por intervalos fixos (em horas): defina a frequência para executar a pesquisa inversa.
   - **Número de rótulos**: Instrua o defender para IOT a resolver automaticamente os endereços IP de rede para FQDNs do dispositivo. <br />Para configurar a resolução de FQDN de DNS, adicione o número de rótulos de domínio a serem exibidos. Até 30 caracteres são exibidos da esquerda para a direita.
1. Selecione **SALVAR**.
1. Para garantir que as configurações de DNS estejam corretas, selecione **teste de pesquisa**. O teste garante que o endereço IP do servidor DNS e a porta do servidor DNS estejam definidos corretamente.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Para configurar uma regra de encaminhamento para bloquear o tráfego suspeito com o firewall Palo Alto

1. No painel esquerdo, selecione **encaminhamento**. O painel encaminhamento é exibido.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="A tela de encaminhamento.":::

1. No painel **encaminhamento** , selecione **criar regra de encaminhamento**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Criar regra de encaminhamento":::

1. Para configurar a regra de encaminhamento do Palo Alto Panorama:

   Defina os parâmetros de regra padrão e, na caixa suspensa **ações** , selecione **Enviar para Palo Alto panorama**. O painel detalhes da ação é exibido.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Ação selecionar":::

1. No painel Ações, defina os seguintes parâmetros:

   - **Host**: Insira o endereço IP do servidor de panorama.

   - **Porta**: Insira a porta do servidor de panorama.
   - **Nome de usuário**: Insira o nome de usuário do servidor de panorama.
   - **Senha**: Insira a senha do servidor de panorama.
   - **Endereço do relatório**: Defina como o bloqueio é executado, da seguinte maneira:
   
     - **Por endereço IP**: sempre cria políticas de bloqueio em Panorama com base no endereço IP.
     
     - **Por FQDN ou endereço IP**: cria políticas de bloqueio no panorama com base no FQDN, caso ele exista, caso contrário, pelo endereço IP.
     
   - **Email**: defina o endereço de email para o email de notificação da política
     > [!NOTE]
     > Verifique se você configurou um servidor de email no defender para IoT. Se nenhum endereço de email for inserido, o defender para IoT não enviará um email de notificação.
   - **Executar uma pesquisa de DNS mediante detecção de alerta (caixa de seleção)**: quando a opção por FQDN ou endereço IP é definida no endereço do relatório. Essa caixa de seleção é selecionada por padrão. Se apenas o endereço IP estiver definido, essa opção será desabilitada.
   - **Configurar**: Configure as seguintes opções para permitir o bloqueio das fontes suspeitas pelo Palo Alto Panorama:
   
     - **Bloquear códigos de função inválidos**: violações de protocolo-valor de campo inválido violando o ICS, especificação de protocolo (exploração em potencial).
     
     - **Bloquear atualizações de firmware/programação de PLC não autorizadas**: alterações de PLC não autorizadas.
     
     - **Bloquear interrupção não autorizada de PLC**: parada de PLC (tempo de inatividade).
     
     - **Bloquear alertas relacionados a malware**: bloqueio de tentativas de malware industrial (Triton, NotPetya, etc.). Você pode selecionar a opção de **bloqueio automático**. Nesse caso, o bloqueio é executado automaticamente e imediatamente.
     
     - **Bloquear a verificação não autorizada**: verificação não autorizada (reconhecimento em potencial).
     
1. Selecione **Enviar**.

### <a name="to-block-the-suspicious-source"></a>Para bloquear a origem suspeita

1. No painel **alertas** , selecione o alerta relacionado à integração do Palo Alto. A caixa de diálogo **detalhes do alerta** é exibida.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Detalhes do alerta":::        

1. Para bloquear automaticamente a origem suspeita, selecione **Bloquear origem**.

1. Na caixa de diálogo **confirmar** , selecione **OK.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Veja":::

## <a name="next-steps"></a>Próximas etapas

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
