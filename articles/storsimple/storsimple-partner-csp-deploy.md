---
title: Visão geral do Microsoft Azure StorSimple e do programa Cloud Solutions Provider | Microsoft Docs
description: Uma visão geral sobre o StorSimple e o CSP para parceiros do StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: 0dac86a696599a391cb243ad11e16931e00b8921
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629825"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Implantar o StorSimple Virtual Array para o programa Cloud Solution Provider

## <a name="overview"></a>Visão geral

Matriz de StorSimple Virtual pode ser implantado pelos parceiros de provedor de solução de nuvem (CSP) para seus clientes. Um parceiro CSP pode criar um serviço do Gerenciador de dispositivos do StorSimple. Esse serviço, em seguida, pode ser usado para implantar e gerenciar a matriz Virtual do StorSimple e os compartilhamentos associados, volumes e backups.

Este artigo descreve como um parceiro CSP pode adicionar um cliente ou uma nova assinatura para um cliente existente e, em seguida, crie um serviço para implantar uma matriz Virtual StorSimple no CSP.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

- Você está inscrito no programa CSP.
- Você tem credenciais de logon válidas do [Partner Center](https://partnercenter.microsoft.com/). As credenciais permitem que você entrar no portal de parceiros para adicionar novos clientes, procurar clientes ou navegar para uma conta de cliente no painel do parceiro. O CSP pode funcionar como um administrador do StorSimple em nome do cliente no portal do Azure.
                             
## <a name="add-a-customer"></a>Adicionar um cliente

Se você adicionar um cliente, uma assinatura é criada automaticamente. Para adicionar um cliente (e criar automaticamente uma assinatura), execute as seguintes etapas no portal de parceiros.

1. Vá para o [Partner Center](https://partnercenter.microsoft.com/) e entre usando suas credenciais do CSP. Clique em **Painel**.

     ![Painel no Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **Clientes**. No painel direito, clique em **adicionar clientes**. Insira os detalhes do cliente. Clique em **Avançar: Assinaturas** para criar uma assinatura de cliente.

    ![Adicionar cliente](./media/storsimple-partner-csp-deploy/image2.png)

3.  Selecione a oferta **Microsoft Azure**. Role até a parte inferior da página e clique em **revisão**.

    ![Revise as informações da assinatura](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Examine as informações e clique em **Enviar**.

    ![Assinatura de envio](./media/storsimple-partner-csp-deploy/image4.png)

5. Salve as informações de confirmação para referência futura.

    ![Salvar confirmação](./media/storsimple-partner-csp-deploy/image5.png)

6. Localizar ou navegue até o cliente você acabou de adicionar. Clique no **Nome da empresa** para fazer drill down nos detalhes.

    ![Procurar o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione **Gerenciamento de serviços**. No painel direito, em **Administrar serviços**, clique em **Portal de Gerenciamento do Microsoft Azure** para entrar como um administrador do Azure para seu cliente.

    ![Fazer logon no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gerenciador de Dispositivos do StorSimple, clique em **+ Novo** e procure ou navegue até **Série de Dispositivos Virtuais do StorSimple**. Para saber mais, vá para [Implantar um serviço do Gerenciador de Dispositivos do StorSimple](storsimple-virtual-array-manage-service.md).

    ![Criar serviço Gerenciador de Dispositivos do StorSimple](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Adicionar uma assinatura

Em alguns casos, você pode ter um cliente existente e você precisa adicionar uma assinatura. Para adicionar uma assinatura a um cliente existente, execute as seguintes etapas no portal de parceiros.

1. Vá para o [Partner Center](https://partnercenter.microsoft.com/) e entre usando suas credenciais do CSP. Clique em **Painel**.

     ![Painel no Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **Clientes**. Localizar ou navegue para o cliente para o qual você deseja adicionar uma assinatura. Clique o ![ícone de verificação de expansão](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ícone para expandir a linha para o nome da empresa para o cliente. Em detalhes, clique em **adicionar assinaturas**.

    ![Clientes](./media/storsimple-partner-csp-deploy/image10.png)

3. Verificar **Microsoft Azure** para o **principais ofertas** na assinatura e clique em **enviar**. Isso cria uma nova assinatura.

    ![Adicionar nova assinatura](./media/storsimple-partner-csp-deploy/image11.png)

6. Depois de uma nova assinatura for criada, clique em  **<-- Clientes** no painel esquerdo para voltar para a página de **Clientes**. Pesquisa do cliente para o qual você acabou de criar uma assinatura. Clique no **Nome da empresa** para fazer drill down nos detalhes.

    ![Procurar o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione **Gerenciamento de serviços**. No painel direito, em **Administrar serviços**, clique em **Portal de Gerenciamento do Microsoft Azure** para entrar como um administrador do Azure para seu cliente.

    ![Fazer logon no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gerenciador de Dispositivos do StorSimple, clique em **+ Novo** e procure ou navegue até **Série de Dispositivos Virtuais do StorSimple**. Para saber mais, vá para [Implantar um serviço do Gerenciador de Dispositivos do StorSimple](storsimple-virtual-array-manage-service.md).

    ![Criar serviço Gerenciador de Dispositivos do StorSimple](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Próximas etapas

- Se você tiver mais dúvidas relacionadas ao StorSimple no CSP, vá para [StorSimple no CSP: Perguntas frequentes](storsimple-partner-csp-faq.md).
- Se você estiver pronto para implantar seu StorSimple, acesse [Implantar o StorSimple para CSP](storsimple-partner-csp-deploy.md).
