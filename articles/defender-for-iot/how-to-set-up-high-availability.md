---
title: Configurar a alta disponibilidade
description: Aumente a resiliência de sua implantação do defender para IoT instalando um dispositivo de alta disponibilidade do console de gerenciamento local. Implantações de alta disponibilidade garantem que os sensores gerenciados reportem continuamente para um console de gerenciamento local ativo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: eb5f2c6293042f44de5e3c061c6d379bee6e5b06
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820578"
---
# <a name="about-high-availability"></a>Sobre alta disponibilidade

Aumente a resiliência de sua implantação do defender para IoT instalando um dispositivo de alta disponibilidade do console de gerenciamento local. Implantações de alta disponibilidade garantem que os sensores gerenciados reportem continuamente para um console de gerenciamento local ativo.

Essa implantação é implementada com um par de console de gerenciamento local que inclui um dispositivo primário e um secundário.

## <a name="about-primary-and-secondary-communication"></a>Sobre a comunicação primária e secundária

Quando um console de gerenciamento local primário e secundário estiver emparelhado:

- Um certificado SLL do console de gerenciamento local é aplicado para criar uma conexão segura entre os dispositivos primário e secundário. O SLL pode ser o certificado autoassinado instalado por padrão ou um certificado instalado pelo cliente.

- Os dados primários do console de gerenciamento local são submetidos a backup automaticamente no console de gerenciamento local secundário a cada 10 minutos. O backup das configurações do console de gerenciamento local e dos dados do dispositivo é feito. Os arquivos e logs do PCAP não são incluídos no backup. Você pode fazer backup e restaurar PCAPs e logs manualmente.

- A configuração principal no console de gerenciamento é duplicada no secundário; por exemplo, configurações do sistema. Se essas configurações forem atualizadas no primário, elas também serão atualizadas no secundário.

- Antes que a licença do secundário expire, você deve defini-la como a primária para atualizar a licença.

## <a name="about-failover-and-failback"></a>Sobre failover e failback

Se um sensor não puder se conectar ao console de gerenciamento local primário, ele se conectará automaticamente ao secundário. O sistema terá suporte tanto pelo primário quanto pelo secundário simultaneamente, se menos da metade dos sensores estiverem se comunicando com o secundário. O secundário assume quando mais da metade dos sensores está se comunicando com ele. O failover do primário para o secundário leva aproximadamente três minutos. Quando o failover ocorre, o console de gerenciamento local primário congela. Quando isso acontece, você pode entrar no secundário usando as mesmas credenciais de entrada.

Durante o failover, os sensores continuam tentando se comunicar com o dispositivo primário. Quando mais da metade dos sensores gerenciados obtiverem sucesso na comunicação com o primário, o primário será restaurado. A mensagem a seguir é exibida no console secundário quando o primário é restaurado.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Captura de tela de uma mensagem que aparece no console secundário quando o primário é restaurado.":::

Entre novamente no dispositivo primário após o redirecionamento.

## <a name="high-availability-setup-overview"></a>Visão geral da configuração de alta disponibilidade

Os procedimentos de instalação e configuração são executados em quatro estágios principais:

1. Instale um dispositivo primário do console de gerenciamento local. 

2. Configure o dispositivo primário do console de gerenciamento local. Por exemplo, configurações de backup agendadas, configurações de VLAN. Consulte o guia do usuário do console de gerenciamento local para obter detalhes. Todas as configurações são aplicadas automaticamente ao dispositivo secundário após o emparelhamento.

3. Instale um dispositivo secundário do console de gerenciamento local. Para obter mais informações, consulte [sobre a instalação do defender para IOT](how-to-install-software.md).

4. Emparelhe os dispositivos de console de gerenciamento locais primários e secundários, conforme descrito [aqui](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console). O console de gerenciamento local primário deve gerenciar pelo menos dois sensores para executar a configuração.

## <a name="high-availability-requirements"></a>Requisitos de alta disponibilidade

Verifique se você atende aos seguintes requisitos de alta disponibilidade:

- Requisitos de certificado

- Requisitos de software e hardware

- Requisitos de acesso à rede

### <a name="software-and-hardware-requirements"></a>Requisitos de software e hardware

- Os dispositivos de console de gerenciamento local primário e secundário devem estar executando modelos de hardware e versões de software idênticos.

- O sistema de alta disponibilidade só pode ser configurado pelo defender para usuários de IoT, usando ferramentas da CLI.

### <a name="network-access-requirements"></a>Requisitos de acesso à rede

Você precisa verificar se a política de segurança organizacional permite o acesso aos seguintes serviços no console de gerenciamento local primário e secundário. Esses serviços também permitem a conexão entre os sensores e o console de gerenciamento local secundário:

|Porta|Serviço|Descrição|
|----|-------|-----------|
|**443 ou TCP**|HTTPS|Concede acesso ao console Web do console de gerenciamento local.|
|**22 ou TCP**|SSH|Sincroniza os dados entre os dispositivos de console de gerenciamento local primário e secundário|
|**123 ou UDP**|NTP| A sincronização de tempo de NTP do console de gerenciamento local. Verifique se os dispositivos ativos e passivos estão definidos com o mesmo fuso horário.|

## <a name="create-the-primary-and-secondary-pair"></a>Criar o par primário e secundário

Verifique se os dispositivos de console de gerenciamento local primário e secundário estão ligados antes de iniciar o procedimento.  

### <a name="on-the-primary"></a>No primário

1. Entre na CLI como um usuário do defender para IoT.

2. Execute o seguinte comando no primário:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>Neste documento, o principal console de gerenciamento local é chamado de primário, e o agente é chamado de secundário.

3. Insira o endereço IP do dispositivo secundário no ```<Secondary ip>``` campo e selecione Enter. O endereço IP é validado e o certificado SSL é baixado para o primário. A inserção do endereço IP também associa os sensores ao dispositivo secundário.

4. Execute o seguinte comando no primário para verificar se o certificado foi instalado corretamente:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Execute o seguinte comando no primário. **Não execute com o sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Isso permite a conexão entre os dispositivos primário e secundário para fins de backup e restauração entre eles.

6. Insira o endereço IP do secundário e selecione Enter.

### <a name="on-the-secondary"></a>No secundário

1. Entre na CLI como um usuário do defender para IoT.

2. Execute o comando a seguir no secundário. **Não execute com o sudo**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Isso permite a conexão entre os dispositivos primário e secundário para fins de backup e restauração entre eles.

3. Insira o endereço IP do primário e pressione Enter.

### <a name="track-high-availability-activity"></a>Acompanhar atividade de alta disponibilidade

Os logs do aplicativo principal podem ser exportados para a equipe de suporte do defender for IoT para lidar com problemas de alta disponibilidade.  

Para acessar os logs principais:

1. Selecione **Exportar** na janela **configurações do sistema** .

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Atualizar o console de gerenciamento local com alta disponibilidade

Execute a atualização de alta disponibilidade na seguinte ordem. Certifique-se de que cada etapa esteja concluída antes de começar uma nova etapa.

Para atualizar com alta disponibilidade:

1. Atualize o console de gerenciamento local primário.

2. Atualize o console de gerenciamento local secundário.

3. Atualize os sensores.

## <a name="see-also"></a>Consulte também

[Ativar e configurar seu console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md)