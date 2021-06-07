---
title: 'Azure AD Connect: opções de dispositivo| Microsoft Docs'
description: Este documento detalha as opções de dispositivo disponíveis no Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d8cc39174f6392b0c532948305fa713535f0c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279220"
---
# <a name="azure-ad-connect-device-options"></a>Do Azure AD Connect: opções de dispositivo

A documentação a seguir fornece informações sobre as várias opções de dispositivo disponíveis no Azure AD Connect. Você pode usar o Azure AD Connect para configurar as duas operações a seguir: 
* **Ingresso no Azure AD híbrido**: se o ambiente tiver um volume de memória do AD local e você quiser os benefícios do Azure AD, poderá implementar dispositivos ingressados ao Azure AD híbrido. Esses dispositivos são ingressados ao Active Directory local e ao Azure Active Directory.
* **Write-back de dispositivo**: o Write-back de dispositivo é usado para habilitar o Acesso Condicional baseado em dispositivos para os dispositivos protegidos do AD FS (2012 R2 ou superior)

## <a name="configure-device-options-in-azure-ad-connect"></a>Opções de logon único do usuário no Azure AD Connect

1.  Executar o Azure AD Connect. Na página **Tarefas adicionais**, selecione **Configurar opções de dispositivo**.  Clique em **Avançar**.
    ![Configurar opções do dispositivo](./media/how-to-connect-device-options/deviceoptions.png) 

    A página de **visão geral** exibe os detalhes.
    ![Visão geral](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > As novas opções de dispositivo de configuração estão disponíveis somente na versão 1.1.819.0 e mais recente.

2.  Depois de fornecer as credenciais do Microsoft Azure Active Directory, você pode escolher a operação a ser executada na página de opções do dispositivo.
    ![Operações do Dispositivo](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Próximas etapas

* [Configurar associação Híbrida do Microsoft Azure Active Directory](../devices/hybrid-azuread-join-plan.md)
* [Configurar/desativar o write-back do dispositivo](how-to-connect-device-writeback.md)