---
title: "Registrar tíquete de suporte para o StorSimple série 8000 | Microsoft Docs"
description: "Saiba como criar uma solicitação de suporte e iniciar uma sessão de suporte em seu dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli;anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cecc2566b432e897b5eff0c12e66598f0518ed80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="contact-microsoft-support-for-your-storsimple"></a>Contate o Suporte da Microsoft para obter ajuda com seu StorSimple
Se tiver problemas com sua solução Microsoft Azure StorSimple, você poderá criar uma solicitação de serviço de suporte técnico. Em uma sessão online com seu engenheiro de suporte, talvez você precise iniciar uma sessão de suporte em seu dispositivo StorSimple. Este artigo orienta você sobre:

* Como criar uma solicitação de suporte.
* Como iniciar uma sessão de suporte na interface do Windows PowerShell de seu dispositivo StorSimple.

Examine os [SLAs e informações de suporte ao StorSimple 8000 Series](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar uma solicitação de suporte.

## <a name="create-a-support-request"></a>Criar uma solicitação de suporte
Execute as seguintes etapas para criar uma solicitação de suporte:

#### <a name="to-create-a-support-request"></a>Para criar uma solicitação de suporte
1. No [Portal Clássico do Azure](https://manage.windowsazure.com/), no canto superior direito, clique no nome da sua conta e em **Contatar o Suporte da Microsoft**.
   
    ![Contate o Suporte da MS por meio do Portal de Gerenciamento](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. Você será redirecionado para o novo portal do Azure (portal.azure.com). Clique no bloco **Nova solicitação de suporte** .
   
    ![Contate o Suporte da MS por meio do novo portal](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    No lado direito da tela, é exibido o painel **Nova solicitação de suporte** . 
   
    ![Painel de nova solicitação de suporte](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. Na caixa de diálogo **Fundamentos** , preencha o seguinte:                                
   
   1. Na lista suspensa **Tipo de problema**, selecione **Técnico**.
   2. Selecione uma **Assinatura** na lista suspensa.
   3. Na lista suspensa **Serviço**, selecione o **StorSimple**. 
   4. Selecione um **Plano de suporte** na lista suspensa. É necessário um plano de suporte pago para habilitar o Suporte Técnico.
4. Clique em **Avançar**. A caixa de diálogo **Problema** é exibida.
   
    ![Painel de nova solicitação de suporte](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. Na caixa de diálogo **Problema** , preencha o seguinte:
   
   1. Selecione um nível de **Severidade** na lista suspensa.
   2. Selecione um **Tipo de problema** na lista suspensa.
   3. Selecione uma **Categoria** na lista suspensa. 
   4. Descreva seu problema brevemente na caixa **Detalhes** .
   5. Na caixa **Período** , indique a data, hora e fuso horário correspondentes à ocorrência mais recente do seu problema.
   6. Em **Upload do arquivo**, clique no ícone de pasta para procurar o pacote de suporte.
   7. Marque a caixa de seleção **Compartilhar informações de diagnóstico** .
6. Clique em **Avançar**. A caixa de diálogo das **Informações de contato** é exibida.
   
    ![Painel de nova solicitação de suporte](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. Insira suas informações de contato e selecione um método de contato (telefone ou email). 
8. Marque a caixa de seleção **Salvar alterações de contato para futuras solicitações de suporte** .
9. Clique em **Criar**.

Depois que você enviar sua solicitação, um engenheiro de Suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar uma sessão de suporte no Windows PowerShell para StorSimple
Para solucionar problemas que possam ocorrer com o dispositivo StorSimple, você precisará contatar a equipe de Suporte da Microsoft. O Suporte da Microsoft talvez precise usar uma sessão de suporte para fazer logon em seu dispositivo. 

Execute as seguintes etapas para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de suporte
1. Acesse o dispositivo diretamente usando o console serial ou por meio de uma sessão de telnet de um computador remoto. Para fazer isso, execute as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Na sessão que será aberta, pressione a tecla **Enter** para obter um prompt de comando.
3. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.
4. No prompt, digite a seguinte senha: 
   
    `Password1`
5. No prompt, digite o seguinte comando:
   
    `Enable-HcsSupportAccess`
6. Uma cadeia de caracteres criptografada será apresentada a você. Copie a cadeia de caracteres para um editor de texto como o Bloco de Notas.
7. Salve a cadeia de caracteres e envie-a em uma mensagem de email ao Suporte da Microsoft. 

> [!IMPORTANT]
> Você pode desabilitar o acesso ao suporte executando `Disable-HcsSupportAccess`. O dispositivo StorSimple também tentará desabilitar o acesso ao suporte oito horas após a sessão ser iniciada. É uma prática recomendada alterar as credenciais de seu dispositivo StorSimple após iniciar uma sessão de suporte.
> 
> 

