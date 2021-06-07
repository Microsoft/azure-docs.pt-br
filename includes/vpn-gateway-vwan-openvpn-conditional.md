---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77471479"
---
O acesso condicional permite o controle de acesso refinado em uma base por aplicativo. Para usar o acesso condicional, você deve ter um licenciamento Azure AD Premium 1 ou superior aplicado aos usuários que estarão sujeitos às regras de acesso condicional.

1. Navegue até a página **aplicativos empresariais – todos os aplicativos** e clique em **VPN do Azure**.

   - Clique em **acesso condicional**.
   - Clique em **Nova política** para abrir o painel **Novo**.
2. No painel **novo** , navegue até **atribuições – > usuários e grupos**. Na guia **usuários e grupos – >** **incluir** :

   - Clique em **Selecionar usuários e grupos**.
   - Verifique **usuários e grupos**.
   - Clique em **selecionar** para selecionar um grupo ou conjunto de usuários a ser afetado pela MFA.
   - Clique em **Concluído**.

   ![Atribuições](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. No painel **novo** , navegue até o painel **controles de acesso – > concessão** :

   - Clique em **conceder acesso**.
   - Clique em **exigir autenticação multifator**.
   - Clique em **exigir todos os controles selecionados**.
   - Clique em **Selecionar**.
   
   ![Conceder acesso-MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Na seção **habilitar política** :

   - Selecione **Ativada**.
   - Clique em **Criar**.

   ![Habilitar política](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)