---
title: Carregar um Certificado de Gerenciamento de Serviços do Azure | Microsoft Docs
description: Saiba como carregar o certificado de Gerenciamento de Serviços para o portal do Azure.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 014a26c2500959502eeb1c50d3f311584c1ad84e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742898"
---
# <a name="upload-an-azure-service-management-certificate"></a>Carregar um Certificado de Gerenciamento de Serviços do Azure
Os certificados de gerenciamento permitem que você autentique com o modelo de implantação clássico fornecido pelo Azure. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usam esses certificados para automatizar a configuração e a implantação de diversos serviços do Azure. 

> [!WARNING]
> Portanto, tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que os utilize para autenticação gerencie a assinatura à qual eles estão associados.
>
>

Se você quiser obter mais informações sobre certificados do Azure (incluindo a criação de um certificado autoassinado), veja [Visão geral de certificados para Serviços de Nuvem do Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Você também pode usar o [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para autenticar o código cliente, para fins de automação.

**Observação:** Você deve ser um coadministrador na assinatura para realizar operações em certificados de gerenciamento. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=849300) sobre como Adicionar ou Remover Coadministradores no novo Portal do Azure 

## <a name="upload-a-management-certificate"></a>Carregar um certificado de gerenciamento
Após criar um certificado de gerenciamento (arquivo .cer somente com a chave pública), você poderá carregá-lo no portal. Quando o certificado estiver disponível no portal, qualquer pessoa com um certificado correspondente (chave privada) pode conectar-se por meio da API de Gerenciamento e acessar os recursos para a assinatura associada.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique em **Todos os serviços** na lista de serviços inferior do Azure e selecione **Assinaturas** no grupo de serviços _Geral_.

    ![Menu de assinatura](./media/azure-api-management-certs/subscriptions_menu.png)

3. Selecione a assinatura correta à qual você deseja associar um certificado.     
4. Depois que você tiver selecionado a assinatura correta, pressione **Certificados de gerenciamento** no grupo _Configurações_.

    ![Configurações](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Pressione o botão **Carregar** .

    ![Carregar na página de certificados](./media/azure-api-management-certs/certificates_page.png)
6. Preencha as informações de caixa de diálogo e pressione **Carregar**.

    ![Configurações](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você tem um certificado de gerenciamento associado a uma assinatura, você pode (depois de ter instalado localmente o certificado correspondente) conectar-se programaticamente à [API REST do modelo de implantação clássico](/azure/#pivot=sdkstools) e automatizar os diversos recursos do Azure que também estão associados à assinatura.
