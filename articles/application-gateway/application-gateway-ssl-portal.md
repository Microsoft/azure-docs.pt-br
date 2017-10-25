---
title: Configurar o descarregamento SSL - Gateway de Aplicativo do Azure - Portal do Azure | Microsoft Docs
description: "Este artigo fornece instruções para criar um gateway de aplicativo com descarregamento SSL usando o portal do Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Configurar um gateway de aplicativo para descarregamento SSL usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell clássico do Azure](application-gateway-ssl.md)
> * [CLI 2.0 do Azure](application-gateway-ssl-cli.md)

O Gateway de Aplicativo do Azure pode ser configurado para encerrar a sessão SSL no gateway para evitar que a onerosa tarefa de descriptografia de SSL aconteça no web farm. O descarregamento SSL também simplifica a configuração do servidor front-end e o gerenciamento do aplicativo Web.

## <a name="scenario"></a>Cenário

O cenário a seguir explora a configuração do descarregamento SSL em um gateway de aplicativo existente. O cenário pressupõe que você já seguiu as etapas para [criar um gateway de aplicativo](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Para configurar o descarregamento SSL com um Gateway de Aplicativo, é necessário um certificado. Esse certificado é carregado no gateway de aplicativo e usado para criptografar e descriptografar o tráfego enviado via SSL. O certificado precisa estar no formato de Troca de Informações Pessoais (.pfx). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego.

## <a name="add-an-https-listener"></a>Adicionar um ouvinte HTTPS

O ouvinte HTTPS procura o tráfego com base em sua configuração e ajuda a roteá-lo para os pools de back-ends. Para adicionar um ouvinte HTTPS, siga estas etapas:

   1. Navegue até o portal do Azure e selecione um gateway de aplicativo existente.

   2. Selecione **Ouvintes** e selecione o botão **Adicionar** para adicionar um ouvinte.

   ![Painel Visão Geral do Gateway de Aplicativo][1]


   3. Preencha as seguintes informações obrigatórias para o ouvinte e faça upload do certificado .pfx:
      - **Nome**: o nome amigável do ouvinte.

      - **Configuração de IP do front-end**: a configuração de IP do front-end usada para o ouvinte.

      - **Porta de front-end (Nome/Porta)**: um nome amigável para a porta usada no front-end do gateway de aplicativo e a porta real usada.

      - **Protocolo**: uma opção para determinar se o HTTPS ou HTTP é usado para o front-end.

      - **Certificado (Nome/Senha)**: se o descarregamento SSL for usado, um certificado .pfx será exigido para essa configuração. Também são necessários um nome amigável e uma senha.

   4. Selecione **OK**.

![Adicionar um painel de ouvinte][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Criar uma regra e associá-la ao ouvinte

O ouvinte foi criado. Em seguida, crie uma regra para lidar com o tráfego do ouvinte. As regras definem como o tráfego é roteado para os pools de back-ends com base em vários parâmetros de configuração. Essas configurações incluem o protocolo, a porta e as investigações de integridade, e se a afinidade de sessão baseada em cookie é usada. Para criar e associar uma regra ao ouvinte, siga estas etapas:


   1. Selecione **Regras** do gateway de aplicativo e selecione **Adicionar**.

   ![Painel Regras do Gateway de Aplicativo][3]


   2. Em **Adiciona regra básica**, insira um nome amigável para a regra no campo **Nome** e selecione o **Ouvinte** criado na etapa anterior. Selecione o **Pool de back-ends** e a **Configuração de HTTP** apropriados e selecione **OK**.

   ![Janela Configurações de HTTPS][4]

Agora as configurações são salvas no Gateway de Aplicativo. O processo de salvamento dessas configurações pode demorar um pouco antes que elas estejam disponíveis para exibição pelo portal ou pelo PowerShell. Depois de salvas, o gateway de aplicativo trata a criptografia e a descriptografia do tráfego. Todo o tráfego entre o gateway de aplicativo e os servidores Web de back-end será tratado por HTTP. Toda a comunicação com o cliente, se iniciada por HTTPS, será retornada criptografada para o cliente.

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar uma investigação de integridade personalizada com o Gateway de Aplicativo do Azure, consulte [Criar uma investigação de integridade personalizada](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

