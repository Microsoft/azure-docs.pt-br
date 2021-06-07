---
title: Configurar o Microsoft Endpoint Configuration Manager-Azure
description: Como configurar o Microsoft Endpoint Configuration Manager para implantar atualizações de software no Windows 10 Enterprise Multi-Session na área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88010116"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Configurar o Microsoft Endpoint Configuration Manager

Este artigo explica como configurar o Microsoft Endpoint Configuration Manager para aplicar automaticamente atualizações a um host de área de trabalho virtual do Windows que executa o Windows 10 Enterprise Multi-Session.

## <a name="prerequisites"></a>Pré-requisitos

Para definir essa configuração, você precisará dos seguintes itens:

   - Verifique se você instalou o Microsoft Endpoint Configuration Manager Agent em suas máquinas virtuais.
   - Certifique-se de que sua versão do Microsoft Endpoint Configuration Manager seja pelo menos no nível do Branch 1906. Para obter melhores resultados, use o nível de ramificação 1910 ou superior.

## <a name="configure-the-software-update-point"></a>Configurar o ponto de atualização de software

Para receber atualizações para o Windows 10 Enterprise Multi-Session, você precisa habilitar o Windows Server, versão 1903 e posterior como um produto dentro do Microsoft Endpoint Configuration Manager. Essa configuração de produto também se aplica se você usar o serviço de atualização do Windows Server para implantar atualizações em seus sistemas.

Para receber atualizações:

1. Abra o Microsoft Endpoint Configuration Manager e selecione **sites**.
2. Selecione **configurar componentes do site**.
3. Selecione **ponto de atualização de software** no menu suspenso.
4. Selecione a guia **Produtos**.
5. Marque a caixa de seleção que diz **Windows Server, versão 1903 e posterior**.
6. Vá para **biblioteca de software**  >  **visão geral**  >  **software atualizações**  >  **todas as atualizações de software** e selecione **sincronizar atualizações de software**.
7. Verifique o arquivo wsyncmgr. log em **arquivos de programas**  >  **Microsoft Configuration Manager**  >  **logs** para certificar-se de que suas alterações foram salvas. Pode levar alguns minutos para sincronizar as atualizações.

## <a name="create-a-query-based-collection"></a>Criar uma coleção baseada em consulta

Para criar uma coleção de máquinas virtuais com várias sessões do Windows 10 Enterprise, uma coleção baseada em consulta pode ser usada para identificar a SKU específica do sistema operacional.

Para criar uma coleção:

1. Selecione **ativos e conformidade**.
2. Acesse **visão geral**  >  **coleções de dispositivos** e clique com o botão direito do mouse em **coleções de dispositivos** e selecione **criar coleção de dispositivos** no menu suspenso.
3. Na guia **geral** do menu que é aberto, insira um nome que descreva sua coleção no campo **nome** . No campo **Comentário** , você pode fornecer informações adicionais que descrevem o que é a coleção. Em **limitando a coleta**, defina quais computadores você está incluindo na consulta de coleção.
4. Na guia **regras de associação** , adicione uma regra para sua consulta selecionando **Adicionar regra** e, em seguida, selecionando **regra de consulta**.
5. Em **Propriedades da regra de consulta**, insira um nome para a regra e defina os parâmetros da regra selecionando **Editar instrução de consulta**.
6. Selecione **Mostrar instrução de consulta**.
7. Na instrução, insira a seguinte cadeia de caracteres:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Selecione **OK** para criar a coleção.
9. Para verificar se você criou a coleção com êxito, acesse **ativos e conformidade**  >  **visão geral**  >  **coleções de dispositivos**.
