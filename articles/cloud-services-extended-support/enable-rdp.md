---
title: Aplicar o Área de Trabalho Remota em serviços de nuvem (suporte estendido)
description: Habilitar Área de Trabalho Remota para serviços de nuvem (suporte estendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 53f873013a6f16ce5a28ee5d915afa556057f643
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744149"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Aplicar a extensão de Área de Trabalho Remota aos serviços de nuvem do Azure (suporte estendido)

O portal do Azure usa a extensão de área de trabalho remota para habilitar a área de trabalho remota mesmo depois que o aplicativo é implantado. As configurações da área de trabalho remota para seu serviço de nuvem permitem que você habilite a área de trabalho remota, atualize a conta de administrador local, selecione os certificados usados na autenticação e defina a data de validade para esses certificados. 

## <a name="apply-remote-desktop--extension"></a>Aplicar extensão de Área de Trabalho Remota
1. Navegue até o serviço de nuvem para o qual você deseja habilitar a área de trabalho remota e selecione **"área de trabalho remota"** no painel de navegação esquerdo.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Imagem mostra a seleção da opção Área de Trabalho Remota no portal do Azure":::

2. Selecione **Adicionar**.
3. Escolha as funções para habilitar a área de trabalho remota para.
4. Preencha os campos obrigatórios para nome de usuário, senha, expiração e certificado (não obrigatório).

    :::image type="content" source="media/remote-desktop-2.png" alt-text="Imagem mostra a colocação das informações necessárias para se conectar à área de trabalho remota.":::

5. Quando terminar, selecione **Avançar**. Levará alguns minutos para que as instâncias da função estejam prontas para receber conexões.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Conectar-se a instâncias de função com Área de Trabalho Remota habilitado
Depois que a área de trabalho remota estiver habilitada nas funções, você poderá iniciar uma conexão diretamente do portal do Azure.

1. Clique em **funções e instâncias** para abrir as configurações de instância.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="Imagem mostra a seleção da opção funções e instâncias na folha configuração.":::

2. Selecione uma instância de função que tenha a área de trabalho remota configurada.
3. Clique em **conectar** para baixar um arquivo de conexão de área de trabalho remota.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Imagem mostra a seleção da instância de função de trabalho na portal do Azure.":::
    
4. Abra o arquivo para se conectar à instância de função.


## <a name="next-steps"></a>Próximas etapas 
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para serviços de nuvem (suporte estendido).
- Examine as [perguntas](faq.md) frequentes sobre os serviços de nuvem (suporte estendido).
- Implantar um serviço de nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).