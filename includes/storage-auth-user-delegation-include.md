---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76118083"
---
## <a name="about-the-user-delegation-sas"></a>Sobre a SAS de delegação de usuário

Um token SAS para acesso a um contêiner ou blob pode ser protegido usando as credenciais do Azure AD ou uma chave de conta. Uma SAS protegida com as credenciais do Azure AD é chamada de SAS de delegação de usuário, pois o token 2,0 do OAuth usado para assinar a SAS é solicitado em nome do usuário.

A Microsoft recomenda que você use as credenciais do Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando seu design de aplicativo requer assinaturas de acesso compartilhado, use as credenciais do Azure AD para criar uma SAS de delegação de usuário para segurança superior. Para obter mais informações sobre a SAS de delegação de usuário, consulte [criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Qualquer cliente que possua uma SAS válida pode acessar dados em sua conta de armazenamento, conforme permitido por essa SAS. É importante proteger uma SAS contra uso mal-intencionado ou indesejado. Use o critério de distribuição de uma SAS e tenha um plano em vigor para revogar uma SAS comprometida.

Para obter mais informações sobre assinaturas de acesso compartilhado, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../articles/storage/common/storage-sas-overview.md).
