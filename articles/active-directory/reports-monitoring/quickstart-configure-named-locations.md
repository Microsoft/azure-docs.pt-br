---
title: Configurar locais nomeados no Azure Active Directory | Microsoft Docs
description: Saiba como configurar locais nomeados.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f4d17596936dd9d0ebbdae3c351cac9ed2a570
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89299857"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Início Rápido: Configurar locais nomeados no Azure Active Directory

Com locais nomeados, você pode rotular os intervalos de endereços IP confiáveis em sua organização. O Azure AD usa localizações nomeadas para:
- Detectar falsos positivos em [detecções de risco](../identity-protection/overview-identity-protection.md). Conectar de um local confiável diminui o risco de entrada do usuário.   
- Configure o [acesso condicional com base em localização](../conditional-access/location-condition.md).

Neste início rápido, você aprenderá a configurar locais nomeados no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

* Um locatário do Azure AD. Inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Um usuário, que é um administrador global para o locatário.
* Um intervalo de IP que é estabelecido e confiável na sua organização. O intervalo de IP precisa estar no formato **CIDR (Roteamento entre Domínios sem Classe)** .

## <a name="configure-named-locations"></a>Configurar localizações nomeadas

1. Entre no [portal do Azure](https://portal.azure.com).

2. No painel esquerdo, selecione **Azure Active Directory**, em seguida, selecione **Acesso condicional** da seção **Segurança**.

    ![Guia Acesso Condicional](./media/quickstart-configure-named-locations/entrypoint.png)

3. Na página **Acesso condicional**, selecione **Locais nomeados** e selecione **Novo local**.

    ![Local nomeado](./media/quickstart-configure-named-locations/namedlocation.png)

6. Preencha o formulário na nova página. 

   * Na caixa **Nome**, digite um nome para a localização nomeada.
   * Na caixa **Intervalos de IP**, digite o intervalo de IP no formato CIDR.  
   * Clique em **Criar**.
    
     ![A Nova folha](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

- [Local como uma condição no Acesso condicional](../conditional-access/concept-conditional-access-conditions.md#locations).
- [Relatório de entradas de risco](../identity-protection/overview-identity-protection.md).