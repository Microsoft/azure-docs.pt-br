---
title: "Como reter um endereço IP virtual constante para um serviço de nuvem do Azure | Microsoft Docs"
description: "Saiba como assegurar que o VIP (endereço IP virtual) do seu serviço de nuvem do Azure não mude."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: a76bcba5ab4ca8e1a4899e4aa28f734c09af2aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Reter um endereço IP virtual constante para um serviço de nuvem do Azure
Quando você atualiza um serviço de nuvem que é hospedado no Azure, você precisará garantir que o VIP (endereço IP virtual) do serviço não mude. Muitos serviços de gerenciamento de domínio usam o DNS (sistema de nome de domínio) para o registro de nomes de domínio. O DNS só funciona se o VIP permanece o mesmo. Você pode usar o **Assistente de Publicação** nas Ferramentas do Azure para garantir que o VIP do seu serviço de nuvem não seja alterado quando você atualizá-lo. Para obter mais informações sobre como usar o gerenciamento de domínio DNS para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](cloud-services/cloud-services-custom-domain-name.md).

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>Publicar um serviço de nuvem sem alterar seu VIP
O VIP de um serviço de nuvem é alocado ao implantá-lo pela primeira vez no Azure em um ambiente específico, como o ambiente de produção. O VIP só será alterado se você excluir a implantação explicitamente ou se ela for excluída implicitamente pelo processo de atualização de implantação. Para manter o VIP, você não pode excluir sua implantação e deve certificar-se de que ela não seja excluída automaticamente pelo Visual Studio. 

Você pode especificar configurações de implantação no **Assistente de Publicação**, que dá suporte a várias opções de implantação. Você pode especificar uma nova implantação ou uma implantação de atualização, que pode ser incremental ou simultânea. Ambos esses tipos de implantação de atualização retêm o VIP. Para obter definições desses tipos diferentes de implantação, consulte o [Assistente Publicar Aplicativo no Azure](vs-azure-tools-publish-azure-application-wizard.md). Além disso, você pode controlar se a implantação anterior de um serviço de nuvem deve ou não ser excluída caso um erro ocorra. O VIP poderá mudar inesperadamente se você não definir essa opção corretamente.

## <a name="update-a-cloud-service-without-changing-its-vip"></a>Para atualizar um serviço de nuvem sem alterar o respectivo VIP
1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio. 

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto. No menu de atalho, selecione **Publicar**.

    ![Menu Publicar](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. Na caixa de diálogo **Publicar Aplicativo do Azure**, selecione a assinatura do Azure na qual você deseja implantar. Entre se necessário e selecione **Avançar**.

    ![Página Publicar Aplicativo do Azure – Entrar](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. Na guia **Configurações Comuns**, verifique se o nome do serviço de nuvem no qual você está implantando, o **Ambiente**, a **Configuração de build** e a **Configuração do serviço** estão todos corretos.

    ![Guia Publicar aplicativo do Azure – Configurações Comuns](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. Na guia **Configurações Avançadas**, verifique se o **Rótulo de implantação** e a **Conta de armazenamento** estão corretas. Verifique se a caixa de seleção **excluir implantação em caso de falha** está desmarcada e se a caixa de seleção **Atualização de implantação** está selecionada. Ao limpar a caixa de seleção **Excluir implantação em caso de falha**, você garante que o VIP não será perdido se ocorrer um erro durante a implantação. Ao marcar a caixa de seleção **Atualização de implantação**, você garante que a implantação não será excluída e que o VIP não será perdido quando você republicar o aplicativo. 

    ![Guia Publicar aplicativo do Azure – Configurações Avançadas](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. Para especificações adicionais de como você deseja que as funções sejam atualizadas, selecione **Configurações**, ao lado de **Atualização de implantação**. Selecione **Atualização incremental** ou **Atualização simultânea** e selecione **OK**. Escolha **Atualização incremental** para atualizar cada instância do aplicativo uma após a outra, de modo que o aplicativo fica sempre disponível. Escolha **Atualização simultânea** para atualizar todas as instâncias do aplicativo simultaneamente. A atualização simultânea é mais rápida, mas o serviço pode não estar disponível durante o processo de atualização. Quando tiver terminado, selecione **Avançar**.

    ![Página Publicar Aplicativo do Azure – Configurações de Implantação](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. Na caixa de diálogo **Publicar Aplicativo do Azure**, selecione **Avançar** até que a página **Resumo** seja exibida. Verifique as configurações e, em seguida, selecione **Publicar**.
   
    ![Página Publicar Aplicativo do Azure – Resumo](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Próximas etapas
- [Usando o Assistente Publicar Aplicativo do Azure do Visual Studio](vs-azure-tools-publish-azure-application-wizard.md)

