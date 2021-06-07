---
title: Criar uma conta de atualização de dispositivo na atualização do dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Crie uma conta de atualização de dispositivo na atualização do dispositivo para o Hub IoT do Azure.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558475"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Atualização de Dispositivo para gerenciamento de recursos do Hub IoT

Para começar a usar a atualização do dispositivo, você precisará criar uma conta de atualização de dispositivo, instância e definir funções de controle de acesso. 

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a um Hub IoT. É recomendável usar uma camada S1 (Standard) ou posterior. 
* Navegadores compatíveis:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Criar uma conta de atualização de dispositivo

1. Ir para [portal do Azure](https://portal.azure.com)

2. Clique em criar um recurso e pesquise "atualização do dispositivo para o Hub IoT"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Captura de tela da atualização do dispositivo para o recurso do Hub IoT." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Clique em criar-> atualização de dispositivo para o Hub IoT

4. Especifique a assinatura do Azure a ser associada à conta de atualização do dispositivo e ao grupo de recursos

5. Especifique um nome e um local para sua conta de atualização de dispositivo

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Captura de tela dos detalhes da conta." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Você pode ir para a [página produtos por região do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) para descobrir as regiões em que a atualização de dispositivo para o Hub IOT está disponível. Se a atualização do dispositivo para o Hub IoT não estiver disponível na sua região, você poderá optar por criar uma conta em uma região disponível mais próxima de você. 

6. Clique em "Avançar: revisar + criar>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Captura de tela da revisão de detalhes da conta." lightbox="media/create-device-update-account/account-review.png":::

7. Examine os detalhes e, em seguida, selecione "criar". Você verá que sua implantação está em andamento. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Captura de tela de implantação de conta em andamento." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Você verá o status da implantação alterar para "concluído" em alguns minutos. Clique em "ir para o recurso"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Captura de tela da implantação da conta concluída." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Criar uma instância de atualização de dispositivo 

Uma instância de atualização de dispositivo é associada a um único Hub IoT. Selecione o Hub IoT que será usado com a atualização do dispositivo. Criaremos uma nova política de acesso compartilhado durante essa etapa para garantir que a atualização do dispositivo use apenas as permissões necessárias para trabalhar com o Hub IoT (gravação do registro e conexão do serviço). Essa política garante que o acesso seja limitado apenas à atualização do dispositivo.

Para criar uma instância de atualização de dispositivo após a criação de uma conta.

1. Quando você estiver em seu recurso de conta criado recentemente, vá para a folha gerenciamento de instância "instâncias"

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Captura de tela do gerenciamento de instância na conta." lightbox="media/create-device-update-account/instance-blade.png":::

2. Clique em "criar e especifique um nome de instância e selecione o Hub IoT

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Captura de tela dos detalhes da instância." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > O Hub IoT vinculado ao recurso de atualização do dispositivo não precisa estar na mesma região que a conta de atualização do dispositivo. No entanto, para obter melhor desempenho, é recomendável que o Hub IoT esteja em uma região igual ou próximo à região da sua conta de atualização de dispositivo. 

3. Clique em "Criar". Você verá a instância em um estado "criando". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Captura de tela da criação da instância." lightbox="media/create-device-update-account/instance-creating.png":::

4. Permitir que 5-10 minutos para a implantação da instância seja concluída. Atualize o status até que você veja o "estado de provisionamento" para "êxito".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Captura de tela da criação da instância bem-sucedida." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Configurar o Hub IoT 

Para que a atualização do dispositivo receba notificações de alteração do Hub IoT, a atualização do dispositivo integra-se ao Hub de eventos "interno". Clicar no botão "configurar o Hub IoT" configura as rotas de mensagens necessárias e a política de acesso necessária para se comunicar com dispositivos IoT. 

Para configurar o Hub IoT

1. Depois que a instância "estado de provisionamento" se transformar em "êxito", selecione a instância na folha gerenciamento de instância. Clique em "configurar o Hub IoT"

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Captura de tela da configuração do Hub IoT para uma instância." lightbox="media/create-device-update-account/instance-configure.png":::

2. Selecione "Concordo para fazer essas alterações"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Captura de tela de concordar em configurar o Hub IoT para uma instância." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Clique em "atualizar"

[Saiba mais sobre as rotas de mensagens que estão configuradas.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Configurar funções de controle de acesso

Para que outros usuários tenham acesso à atualização de dispositivo, os usuários devem receber acesso a esse recurso. 

1. Ir para controle de acesso (IAM) dentro da conta de atualização do dispositivo

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Captura de tela do controle de acesso na conta de atualização do dispositivo." lightbox="media/create-device-update-account/account-access-control.png":::

2. Clique em "Adicionar atribuições de função"

3. Em "selecionar uma função", selecione uma função de atualização de dispositivo nas opções especificadas
     - Administrador de atualização de dispositivo
     - Leitor de atualização de dispositivo
     - Administrador de conteúdo de atualização de dispositivo
     - Leitor de conteúdo de atualização de dispositivo
     - Administrador de implantações de atualização de dispositivo
     - Leitor de implantações de atualização de dispositivo
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Captura de tela de atribuições de função de controle de acesso na conta de atualização do dispositivo." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Saiba mais sobre o controle de acesso baseado em função na atualização do dispositivo para o Hub IoT](device-update-control-access.md) 
    
4. Atribuir acesso a um usuário ou a um grupo do Azure AD
5. Clique em Salvar
6. Agora você está pronto para usar a experiência de atualização do dispositivo de dentro do Hub IoT

## <a name="next-steps"></a>Próximas etapas

Tente atualizar um dispositivo usando um dos seguintes tutoriais rápidos:

 - [Atualização de dispositivo em um simulador](device-update-simulator.md)
 - [Atualização de dispositivo no Raspberry Pi](device-update-raspberry-pi.md)
 - [Atualização de dispositivo no Agente do pacote do Ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

[Saiba mais sobre a conta e a instância de atualização de dispositivo.](device-update-resources.md) 

[Saiba mais sobre as funções de controle de acesso de atualização de dispositivo. ](device-update-control-access.md) 

