---
title: Autenticação-Data Lake Storage Gen1 com o Azure AD
description: Saiba como autenticar com Azure Data Lake Storage Gen1 usando Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106562"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação no Azure Data Lake Storage Gen1 usando o Azure Active Directory

O Azure Data Lake Storage Gen1 usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funcione com o Data Lake Storage Gen1, você deve decidir como autenticar seu aplicativo com o Azure AD (Azure Active Directory).

## <a name="authentication-options"></a>Opções de autenticação

* **Autenticação do usuário final** – as credenciais de um usuário final do Azure são usadas para a autenticação no Data Lake Storage Gen1. O aplicativo criado para funcionar com o Data Lake Storage Gen1 solicita essas credenciais de usuário. Como resultado, esse mecanismo de autenticação é *interativo* e o aplicativo é executado no contexto do usuário conectado. Para obter mais informações e instruções, confira [Autenticação de usuário final no Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticação serviço a serviço** – use essa opção se desejar que um aplicativo faça sua própria autenticação no Data Lake Storage Gen1. Nesses casos, você cria um aplicativo do AD (Azure Active Directory) e usa a chave do aplicativo do Azure AD para autenticar-se no Data Lake Storage Gen1. Como resultado, esse mecanismo de autenticação *não é interativo*. Para obter mais informações e instruções, confira [Autenticação serviço a serviço no Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

A tabela a seguir ilustra como os mecanismos de autenticação de usuário final e serviço a serviço têm suporte no Data Lake Storage Gen1. Veja como ler a tabela.

* O símbolo ✔* indica que a opção de autenticação é compatível e leva a um artigo que demonstra como usá-la. 
* O símbolo ✔ indica que a opção de autenticação é compatível. 
* As células vazias indicam que a opção de autenticação não tem suporte.


|Use essa opção de autenticação com...                   |.NET         |Java     |PowerShell |CLI do Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Usuário final (sem MFA \*\*)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(preterido)     |    **[✔ *](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Usuário final (com MFA)                           |    **[✔ *](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔ *](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔ *](data-lake-store-get-started-cli-2.0.md)**      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Serviço a serviço (usando a chave de cliente)         |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔ *](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔ *](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔ *](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Serviço a serviço (usando o certificado do cliente) |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Clique no símbolo de <b>✔ \* </b> . É um link.</i><br>
<i>** MFA significa autenticação multifator</i>

Consulte [Cenários de autenticação do Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md) para obter mais informações sobre como usar o Azure Active Directory para autenticação.

## <a name="next-steps"></a>Próximas etapas

* [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)