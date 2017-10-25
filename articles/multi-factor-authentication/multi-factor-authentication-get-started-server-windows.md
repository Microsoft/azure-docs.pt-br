---
title: "Autenticação do Windows e servidor Azure MFA | Microsoft Docs"
description: "Esta é a página do Azure Multi-Factor Authentication que auxiliará na implantação da Autenticação do Windows e do Servidor Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 7e6384ea8fea686b5cad1a3bc3007252b9cfcd65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação do Windows e Servidor Azure Multi-Factor Authentication
Use a seção de autenticação do Windows do servidor de Autenticação Multifator do Azure para habilitar e configurar a autenticação do Windows para aplicativos. Antes de configurar a autenticação do Windows, lembre-se a lista a seguir:

* Após a instalação, reinicie a Autenticação Multifator do Azure para que os serviços de Terminal entrem em vigor.
* Se a opção 'Exigir correspondência de usuário do Azure Multi-Factor Authentication' for marcada e você não estiver na lista de usuários, não será possível fazer logon no computador após a reinicialização.
* IPs confiáveis dependem de o aplicativo poder fornecer o IP do cliente com a autenticação. Atualmente, apenas os Serviços de Terminal têm suporte.  

> [!NOTE]
> Esse recurso não tem suporte para proteger os Serviços de Terminal no Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Para proteger um aplicativo com a Autenticação do Windows, use o procedimento a seguir.
1. No Servidor Azure Multi-Factor Authentication, clique no ícone de Autenticação do Windows.
   ![Autenticação do Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Marque a caixa de seleção **Habilitar Autenticação do Windows**. Por padrão, essa caixa está desmarcada.
3. A guia Aplicativos permite que o administrador configure um ou mais aplicativos para Autenticação do Windows.
4. Selecione um servidor ou aplicativo — especifique se o servidor/aplicativo está habilitado. Clique em **OK**.
5. Clique em **Adicionar...**
6. A guia IPs Confiáveis permite que você ignore as sessões do Azure Multi-Factor Authentication para Windows originadas de IPs específicos. Por exemplo, se os funcionários usarem o aplicativo no escritório e em casa, você pode decidir que não deseja que seus telefones toquem para o Azure Multi-Factor Authentication enquanto estiverem no escritório. Para isso, você especifica a sub-rede do escritório como entrada de IPs Confiáveis.
7. Clique em **Adicionar...**
8. Selecione **IP Único** se desejar ignorar um único endereço IP.
9. Selecione o **Intervalo de IP** se desejar ignorar um intervalo inteiro de IP. Exemplo 10.63.193.1-10.63.193.100.
10. Selecione **Sub-rede** se desejar especificar um intervalo de IPs usando a notação de sub-rede. Insira o IP inicial da sub-rede e escolha a máscara de rede adequada na lista suspensa.
11. Clique em **OK**.

## <a name="next-steps"></a>Próximas etapas

- [Configure os dispositivos de VPN de terceiros para servidor Azure MFA](multi-factor-authentication-advanced-vpn-configurations.md)

- [Aumentar sua infraestrutura de autenticação atual com a extensão do NPS para o Azure MFA](multi-factor-authentication-nps-extension.md)
