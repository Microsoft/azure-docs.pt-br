---
title: Não é possível adicionar o domínio personalizado usando Key Vault certificado
titleSuffix: Azure API Management
description: Saiba como solucionar o problema no qual você não pode adicionar um domínio personalizado no gerenciamento de API do Azure usando um certificado do Key Vault.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75430586"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Falha ao atualizar os nomes de host do serviço de gerenciamento de API

Este artigo descreve o erro "falha ao atualizar nomes de host de serviço de gerenciamento de API" que você pode experimentar ao adicionar um domínio personalizado para o serviço de gerenciamento de API do Azure. Este artigo fornece etapas de solução de problemas para ajudá-lo a resolver o problema.

## <a name="symptoms"></a>Sintomas

Ao tentar adicionar um domínio personalizado ao serviço de gerenciamento de API usando um certificado do Azure Key Vault, você receberá a seguinte mensagem de erro:

- Falha ao atualizar os nomes de host do serviço de gerenciamento de API. A solicitação para o recurso ' https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 ' falhou com StatusCode: proibido para RequestId:. Mensagem de exceção: a operação retornou um código de status inválido ' proibido '.

## <a name="cause"></a>Causa

O serviço de gerenciamento de API não tem permissão para acessar o cofre de chaves que você está tentando usar para o domínio personalizado.

## <a name="solution"></a>Solução

Para resolver esse problema, siga estas etapas:

1. Vá para a [portal do Azure](Https://portal.azure.com), selecione sua instância de gerenciamento de API e, em seguida, selecione **identidades gerenciadas**. Verifique se a opção **registrar com Azure Active Directory** está definida como **Sim**. 
    ![Registrando com o Azure active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. No portal do Azure, abra o serviço de **cofres de chaves** e selecione o cofre de chaves que você está tentando usar para o domínio personalizado.
1. Selecione **políticas de acesso**e verifique se há uma entidade de serviço que corresponda ao nome da instância do serviço de gerenciamento de API. Se houver, selecione a entidade de serviço e verifique se ela tem a permissão **obter** listada em **permissões secretas**.  
    ![Adicionando política de acesso para entidade de serviço](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Se o serviço de gerenciamento de API não estiver na lista, selecione **Adicionar política de acesso**e, em seguida, crie a seguinte política de acesso:
    - **Configurar do modelo**: nenhum
    - **Selecionar entidade**: Pesquise o nome do serviço de gerenciamento de API e, em seguida, selecione-o na lista
    - **Permissões de chave**: nenhuma
    - **Permissões secretas**: obter
    - **Permissões de certificado**: nenhuma
1. Selecione **OK** para criar a política de acesso.
1. Selecione **Salvar** para salvar as alterações.

Verifique se o problema foi resolvido. Para fazer isso, tente criar o domínio personalizado no serviço de gerenciamento de API usando o certificado Key Vault.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o serviço de gerenciamento de API:

- Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

* [Crie uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).
* [Gerencie sua primeira API](import-and-publish.md).