---
author: baanders
description: incluir arquivo dos Gêmeos Digitais do Azure – pré-requisito para definir um registro de aplicativo
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2ce534972cf6509cdc1ca026f4b29efd3df91afd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96303607"
---
Para autenticar todos os recursos usados neste artigo, você precisará **configurar um registro de aplicativo [Azure Active Directory (AD do Azure)](../articles/active-directory/fundamentals/active-directory-whatis.md)**. Siga as instruções descritas em [*Como criar um registro de aplicativo*](../articles/digital-twins/how-to-create-app-registration.md) para configurar isso. 

Quando você tiver um registro de aplicativo, precisará da **_ID do Aplicativo (cliente)_** e da **_ID do Diretório (locatário)_** do registro ([encontre-as no portal do Azure](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Anote esses valores para usá-los posteriormente a fim de permitir acesso às APIs dos Gêmeos Digitais do Azure.