---
title: Configurar um nome de domínio personalizado para a sua instância de Gerenciamento de API do Azure | Microsoft Docs
description: Este tópico descreve como configurar um nome de domínio personalizado para sua instância de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: a771b437258046f937b97a9e37ffedbe0a17c1c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693580"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado 

Quando você cria uma instância de Gerenciamento de API (APIM), o Azure a atribui a um subdomínio do azure-api.net (por exemplo, `apim-service-name.azure-api.net`). No entanto, você também pode expor seus pontos de extremidade do APIM usando seu próprio nome de domínio, como **contoso.com**. Este tutorial mostra como mapear um nome DNS personalizado existente para pontos de extremidade expostos por uma instância de Gerenciamento de API do Azure.

> [!WARNING]
> Clientes que desejam usar a anexação de certificado para aumentar a segurança de seus aplicativos devem usar um nome de domínio personalizado > e o certificado que gerenciam, não o padrão. Clientes que fixam o certificado padrão > assumirão uma dependência forte das propriedades do certificado que não controlam, o que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Um nome de domínio personalizado que pertence a você. O nome de domínio personalizado que você deseja usar deve ser adquirido separadamente e hospedado em um servidor DNS. Este tópico não dá instruções sobre como hospedar um nome de domínio personalizado.
+ Você deve ter um certificado válido com chaves pública e privada (.PFX). A entidade ou o SAN (nome alternativo da entidade) deve corresponder ao nome de domínio (isso permite que o APIM exponha com segurança URLs sobre SSL).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usar o portal do Azure para definir um nome de domínio personalizado

1. Navegue para sua instância do APIM no [Portal do Azure](https://portal.azure.com/).
1. Clique em **Domínios personalizados e SSL**.
    
    Há vários pontos de extremidade ao qual você pode atribuir um nome de domínio personalizado. No momento, os seguintes pontos de extremidade estão disponíveis: 
   + **Proxy** (o padrão é: `<apim-service-name>.azure-api.net`), 
   + **Portal** (o padrão é: `<apim-service-name>.portal.azure-api.net`),     
   + **Gerenciamento** (o padrão é: `<apim-service-name>.management.azure-api.net`), 
   + **SCM** (o padrão é: `<apim-service-name>.scm.azure-api.net`).

     >[!NOTE]
     > Você pode atualizar todos os pontos de extremidade ou alguns deles. Normalmente, os clientes atualizam **Proxy** (essa URL é usada para chamar a API exposta por meio do Gerenciamento de API) e **Portal** (URL do portal do desenvolvedor). Os pontos de extremidade **Gerenciamento** e **SCM** são usados internamente pelos clientes do APIM e, portanto, recebem com menos frequência um nome de domínio personalizado.

1. Selecione o ponto de extremidade que você deseja atualizar. 
1. Na janela à direita, clique em **Personalizado**.

   + Em **Nome de domínio personalizado**, especifique o nome que você deseja usar. Por exemplo, `api.contoso.com`. Nomes de domínio curinga (por exemplo, *.domain.com) também têm suporte.
   + No **certificado**, selecione um certificado do Cofre de chaves. Você também pode carregar um válido. PFX do arquivo e forneça sua **senha**, se o certificado é protegido com uma senha.

     > [!TIP]
     > Se você usar o Azure Key Vault para gerenciar o certificado SSL de domínio personalizado, verifique se o certificado é inserido no cofre de chaves [como um *certificado*](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), e não um *segredo*. Se o certificado for definido como autorotate, gerenciamento de API selecionará a versão mais recente automaticamente.

1. Clique em Aplicar.

    >[!NOTE]
    >O processo de atribuição do certificado pode levar aproximadamente 15 minutos dependendo do tamanho da implantação. O SKU de desenvolvedor tem tempo de inatividade, SKU Básico e posteriores não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Próximas etapas

[Atualizar e colocar em escala o serviço](upgrade-and-scale.md)
