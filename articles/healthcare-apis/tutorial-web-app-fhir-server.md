---
title: Tutorial do aplicativo Web – configurar a API do Azure para FHIR
description: Este tutorial apresenta um exemplo de implantação de um aplicativo Web simples. Este primeiro tutorial descreve os pré-requisitos e a implantação da API do Azure para FHIR
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: cb183b5c8aff018d4dc73819b938b24ad0daa934
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975783"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>Implantar o aplicativo JavaScript para ler dados do serviço do FHIR
Neste tutorial, você implantará um pequeno aplicativo JavaScript que lê dados de um serviço FHIR. Siga as etapas neste tutorial são:
1. Implantar um servidor FHIR
1. Registrar um aplicativo cliente público
1. Testar o acesso ao aplicativo
1. Criar um aplicativo Web que leia esses dados do FHIR

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este conjunto de tutoriais, você precisará dos seguintes itens:
1. Uma assinatura do Azure
1. Um locatário do Azure Active Directory
1. [Postman](https://www.getpostman.com/) instalado

> [!NOTE]
> Para este tutorial, o serviço FHIR, o aplicativo do Azure AD e os usuários do Azure AD estão todos no mesmo locatário do Azure AD. Se esse não for o caso, você ainda poderá acompanhar este tutorial, mas talvez precise se aprofundar em alguns dos documentos referenciados para executar etapas adicionais.

## <a name="deploy-azure-api-for-fhir"></a>Implantar a API do Azure para FHIR
A primeira etapa do tutorial é obter a configuração da API do Azure para FHIR corretamente.

1. Se ainda não fez isso, implante a [API do Azure para FHIR](fhir-paas-portal-quickstart.md).
1. Depois que a API do Azure para FHIR for implantada, defina as configurações do [CORS](configure-cross-origin-resource-sharing.md) acessando a API do Azure para FHIR e selecionando CORS. 
    1. Defina **Origens** como *
    1. Defina **Cabeçalhos** como *
    1. Em **Métodos**, escolha **Selecionar tudo**
    1. Defina a **Idade máxima** como **600**

## <a name="next-steps"></a>Próximas etapas
Agora que você tem a API do Azure para FHIR implantada, está pronto para registrar um aplicativo cliente público.

>[!div class="nextstepaction"]
>[Registrar aplicativo cliente público](tutorial-web-app-public-app-reg.md)
