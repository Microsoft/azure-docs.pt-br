---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: dd301cb3b18df5d3eb57ac38e9fb6432411d084b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073259"
---
#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/) 

1. Selecione **Registros de aplicativo** e, em seguida, selecione o aplicativo Web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **Minhas APIs**.
1. Selecione a API à qual o aplicativo Web deve receber acesso. Por exemplo, *webapi1*.
1. Em **Permissão**, expanda **demonstração** e, em seguida, selecione os escopos definidos anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **Adicionar Permissões**.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Se for solicitado a selecionar uma conta, selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Sim**.
1. Selecione **Atualizar** e, em seguida, verifique se "Concedido para..." aparece em **Status** para ambos os escopos.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Selecione **Aplicativos (Herdado)** e, em seguida, selecione o aplicativo Web que deve ter acesso à API. Por exemplo, *webapp1*.
1. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
1. Na lista suspensa **Selecionar API**, selecione a API à qual o aplicativo Web deve receber acesso. Por exemplo, *webapi1*.
1. Na lista suspensa **Selecionar Escopos**, selecione os escopos que você definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.
