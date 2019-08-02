---
title: Habilitar a conexão de Área de Trabalho Remota para uma função nos Serviços de Nuvem do Azure | Microsoft Docs
description: Como configurar seu aplicativo de serviço de nuvem do Azure para permitir conexões de área de trabalho remota
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: bea4e0c43d6ae6e0ea05c43343535195a25cf3e2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359523"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Habilitar a conexão de Área de Trabalho Remota para uma função nos Serviços de Nuvem do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A área de trabalho remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar a conexão da área de trabalho remota para solucionar e diagnosticar problemas com seu aplicativo durante a execução.

Você pode habilitar uma conexão de Área de Trabalho Remota em sua função durante o desenvolvimento, incluindo os módulos de Área de Trabalho Remota em sua definição de serviço, ou você pode optar por habilitar a Área de Trabalho Remota por meio da Extensão de Área de Trabalho Remota. A abordagem preferida é usar a extensão de Área de Trabalho Remota, pois você poderá habilitar a Área de Trabalho Remota mesmo depois que o aplicativo for implantado, sem precisar reimplantar o aplicativo.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configurar a Área de Trabalho Remota do Portal do Azure

O Portal do Azure usa a abordagem de Extensão da Área de Trabalho Remota para que você possa habilitar a Área de Trabalho Remota, mesmo depois que o aplicativo for implantado. As configurações da **Área de Trabalho Remota** de seu Serviço de Nuvem permitem habilitar a Área de Trabalho Remota, alterar a conta do administrador local usada para conexão às máquinas virtuais, o certificado usado na autenticação e definir a data de validade.

1. Clique em **Serviços de Nuvem**, selecione o nome do serviço de nuvem e depois selecione **Área de Trabalho Remota**.

    ![Área de trabalho remota dos serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Escolha se você quer habilitar a Área de Trabalho Remota para uma função individual ou para todas as funções, e altere o valor do alternador para **Habilitado**.

3. Preencha os campos obrigatórios de nome de usuário, senha, expiração e certificado.

    ![Área de trabalho remota dos serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando você ativa área de trabalho remota pela primeira vez e selecione **OK** (marca de seleção). Para evitar a reinicialização, o certificado usado para criptografar a senha deve estar instalado na função. Para evitar uma reinicialização, [carregue um certificado para o serviço de nuvem](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) e retorne a esta caixa de diálogo.

4. Em **Funções**, selecione a função que você deseja atualizar ou selecione **Tudo** para todas as funções.

5. Ao concluir as atualizações da configuração, selecione **Salvar**. Levará alguns minutos para que as instâncias da função estejam prontas para receber conexões.

## <a name="remote-into-role-instances"></a>Remoto em instâncias de função

Após a habilitação da Área de Trabalho Remota nas funções, você poderá iniciar uma conexão diretamente do Portal do Azure:

1. Clique em **Instâncias** para abrir as configurações de **Instâncias**.
2. Selecione uma instância de função com a área de trabalho remota configurada.
3. Clique em **Conectar** para baixar um arquivo RDP para a instância da função.

    ![Área de trabalho remota dos serviços de nuvem](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Clique em **Abrir** e em **Conectar** para iniciar a conexão de Área de Trabalho Remota.

>[!NOTE]
> Se o serviço de nuvem estiver atrás de um NSG, talvez você precisará criar regras que permitam tráfego nas portas **3389** e **20000**.  A Área de Trabalho Remota usa a porta **3389**.  Instâncias de serviço de nuvem têm a carga balanceada, de modo que você não pode controlar diretamente a escolha da instância à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* gerenciam o tráfego RDP e permitem que o cliente envie um cookie RDP e especifique uma instância individual à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000*** esteja aberta, que poderá estar bloqueada se você tiver um NSG.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar serviços de nuvem](cloud-services-how-to-configure-portal.md)
