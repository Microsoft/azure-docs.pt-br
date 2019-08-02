---
title: Criar tíquete ou caso de suporte para o StorSimple série 8000 | Microsoft Docs
description: Saiba como registrar uma solicitação de suporte e iniciar uma sessão de suporte em seu dispositivo StorSimple da série 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5394feff80c1df8b7a6cf7f6e6ea56678f575823
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612516"
---
# <a name="contact-microsoft-support"></a>Contatar o Suporte da Microsoft

O Gerenciador de Dispositivos StorSimple fornece a capacidade de **fazer uma nova solicitação de suporte** na folha de resumo de serviço. Se tiver problemas com sua solução StorSimple, você poderá criar uma solicitação de serviço de suporte técnico. Em uma sessão online com seu engenheiro de suporte, talvez você precise iniciar uma sessão de suporte em seu dispositivo StorSimple. Este artigo orienta você sobre:

* Como criar uma solicitação de suporte.
* Como gerenciar o ciclo de vida de uma solicitação de suporte de dentro do portal.
* Como iniciar uma sessão de suporte na interface do Windows PowerShell de seu dispositivo StorSimple.

Examine os [SLAs e informações de suporte ao StorSimple 8000 Series](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar uma solicitação de suporte.

## <a name="create-a-support-request"></a>Criar uma solicitação de suporte

Dependendo do seu [plano de suporte](https://azure.microsoft.com/support/plans/), você pode criar tíquetes de suporte para um problema no seu dispositivo StorSimple diretamente da folha de resumo do serviço do Gerenciador de Dispositivos do StorSimple. Execute as seguintes etapas para criar uma solicitação de suporte:

#### <a name="to-create-a-support-request"></a>Para criar uma solicitação de suporte

1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Nas configurações de resumo de serviço, vá até a seção **SUPORTE + SOLUÇÃO DE PROBLEMAS** e, em seguida, clique em **Nova solicitação de suporte**.
     
    ![Contate o Suporte da MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Na folha **Nova solicitação de suporte**, selecione **Noções Básicas**. Na folha **Básico**, execute as seguintes etapas:
   1. Na lista suspensa **Tipo de problema**, selecione **Técnico**.
   2. O tipo de **Assinatura**, **Serviço** e o **Recurso** (serviço de Gerenciador de Dispositivos StorSimple) atuais são automaticamente escolhidos. 
   3. Selecione um **Plano de suporte** na lista suspensa se você tiver vários planos associados à sua assinatura. É necessário um plano de suporte pago para habilitar o Suporte Técnico.
   4. Clique em **Avançar**.

       ![Contate o Suporte da MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Na folha **Nova solicitação de suporte**, selecione **Etapa 2 Problema**. Na folha **Problema**, execute as seguintes etapas:
    
    1. Escolha a **Gravidade**.
    2. Especifique se o problema está relacionado ao dispositivo ou ao serviço do Gerenciador de Dispositivos do StorSimple.
    3. Escolha uma **Categoria** para este problema e forneça mais **Detalhes** sobre ele.
    4. Forneça a data de início e a hora para o problema.
    5. Em **Upload do arquivo**, clique no ícone de pasta para procurar o pacote de suporte.
    6. Marque **Compartilhar informações de diagnóstico**.
    7. Clique em **Avançar**.

       ![Contate o Suporte da MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Na folha **Nova solicitação de suporte**, clique em **Etapa 3 Informações de contato**. Na folha **Informações de contato**, execute as seguintes etapas:

   1. Nas **Opções de contato**, forneça seu método de contato preferencial (telefone ou email) e o idioma. O tempo de resposta é selecionado automaticamente com base em seu plano de assinatura.
   2. As informações de contato, forneça seu nome, o email, o contato opcional, o país/região. Marque a caixa de seleção **Salvar alterações de contato para futuras solicitações de suporte** .
   3. Clique em **Criar**.
   
       ![Contate o Suporte da MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      O Microsoft Support usará essas informações para entrar em contato com você para obter mais informações, diagnóstico e resolução.
      Depois que você enviar sua solicitação, um engenheiro de Suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

## <a name="manage-a-support-request"></a>Gerenciar uma solicitação de suporte

Depois de criar um tíquete de suporte, você pode gerenciar o ciclo de vida do tíquete de dentro do portal.

#### <a name="to-manage-your-support-requests"></a>Para gerenciar suas solicitações de suporte

1. Para acessar a página de ajuda e suporte navegue até **Procurar -> Ajuda + suporte**.

    ![Gerenciar solicitações de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Uma listagem tabular de Todas as solicitações de suporte é exibida na folha **Ajuda + suporte**.

    ![Gerenciar solicitações de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecione e clique em uma solicitação de suporte. Você pode exibir o status e os detalhes para esta solicitação. Clique em **+ Nova mensagem** se desejar acompanhar esta solicitação.

    ![Gerenciar solicitações de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar uma sessão de suporte no Windows PowerShell para StorSimple

Para solucionar problemas que possam ocorrer com o dispositivo StorSimple, você precisará contatar a equipe de Suporte da Microsoft. O Suporte da Microsoft talvez precise usar uma sessão de suporte para fazer logon em seu dispositivo.

Execute as seguintes etapas para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de suporte

1. Acesse o dispositivo diretamente usando o console serial ou por meio de uma sessão de telnet de um computador remoto. Para fazer isso, execute as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
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


## <a name="next-steps"></a>Próximas etapas

Saiba como [diagnosticar e resolver problemas relacionados ao dispositivo StorSimple da série 8000](storsimple-8000-troubleshoot-deployment.md)
