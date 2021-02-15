---
title: Adicionar um Certificado de Autoridade de Certificação personalizado – Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como adicionar um Certificado de Autoridade de Certificação personalizado no Gerenciamento de API do Azure. Você também pode ver instruções para excluir um certificado.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102030"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Como adicionar um Certificado de Autoridade de Certificação personalizado no Gerenciamento de API do Azure

O Gerenciamento de API do Azure permite instalar Certificados de Autoridade de Certificação no computador de dentro da raiz confiável e de repositórios de certificados intermediários. Essa funcionalidade deve ser usada se os serviços exigem um Certificado de Autoridade de Certificação personalizado.

O artigo mostra como gerenciar Certificado de Autoridade de Certificação de uma instância do serviço Gerenciamento de API do Azure no portal do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Carregar um Certificado de Autoridade de Certificação

![Adicionar certificados de autoridade de certificação](media/api-management-howto-ca-certificates/00.png)

Siga as etapas abaixo para fazer upload de um novo Certificado de Autoridade de Certificação do cliente. Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte o tutorial [Criar uma instância de serviço de Gerenciamento de API](get-started-create-service-instance.md).

1. Navegue até a instância de serviço do Gerenciamento de API do Azure no portal do Azure.

2. Selecione **Certificados de Autoridade de Certificação** do menu.

3. Clique no botão **+ Adicionar** .  

    ![Captura de tela que mostra o botão + Adicionar para adicionar um certificado de autoridade de certificação.](media/api-management-howto-ca-certificates/01.png)  

4. Procure o certificado e decida quanto ao repositório de certificados. Somente a chave pública é necessária, portanto, a senha não é necessária.

    ![Captura de tela que mostra como procurar o certificado.](media/api-management-howto-ca-certificates/02.png)  

5. Clique em **Salvar** . Esta operação pode levar alguns minutos.

    ![Captura de tela que mostra como salvar o certificado.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Você pode carregar um Certificado de Autoridade de Certificação usando o comando `New-AzApiManagementSystemCertificate` do Powershell.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Excluir um certificado do cliente

Para excluir um certificado, clique no menu de contexto **...** e selecione **Excluir** ao lado do certificado.

![Excluir Certificados de Autoridade de Certificação](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
