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
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 9eb03be5cd9704c3b124bfb16fd30c5c3466890d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326133"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando você cria uma instância de serviço de gerenciamento de API do Azure, o Azure atribui um subdomínio de azure-api.net (por `apim-service-name.azure-api.net`exemplo,). No entanto, você pode expor seus pontos de extremidade de gerenciamento de API usando seu próprio nome de domínio personalizado, como **contoso.com**. Este tutorial mostra como mapear um nome DNS personalizado existente para pontos de extremidade expostos por uma instância de gerenciamento de API.

> [!WARNING]
> Clientes que desejam usar a anexação de certificado para aumentar a segurança de seus aplicativos devem usar um nome de domínio personalizado > e o certificado que gerenciam, não o padrão. Clientes que fixam o certificado padrão > assumirão uma dependência forte das propriedades do certificado que não controlam, o que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

-   Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de gerenciamento de API. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
-   Um nome de domínio personalizado que pertence a você. O nome de domínio personalizado que você deseja usar deve ser adquirido separadamente e hospedado em um servidor DNS. Este tópico não dá instruções sobre como hospedar um nome de domínio personalizado.
-   Você deve ter um certificado válido com chaves pública e privada (.PFX). A entidade ou o nome alternativo da entidade (SAN) deve corresponder ao nome de domínio (isso permite que a instância do gerenciamento de API exponha URLs com segurança por SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usar o portal do Azure para definir um nome de domínio personalizado

1. Navegue até sua instância de gerenciamento de API no [portal do Azure](https://portal.azure.com/).
1. Clique em **Domínios personalizados e SSL**.

    Há vários pontos de extremidade aos quais você pode atribuir um nome de domínio personalizado. No momento, os seguintes pontos de extremidade estão disponíveis:

    - **Proxy** (o padrão é: `<apim-service-name>.azure-api.net`),
    - **Portal** (o padrão é: `<apim-service-name>.portal.azure-api.net`),
    - **Gerenciamento** (o padrão é: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (o padrão é: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Você pode atualizar todos os pontos de extremidade ou alguns deles. Normalmente, os clientes atualizam **Proxy** (essa URL é usada para chamar a API exposta por meio do Gerenciamento de API) e **Portal** (URL do portal do desenvolvedor). Os pontos de extremidade de **Gerenciamento** e **SCM** são usados internamente apenas pelos proprietários da instância do gerenciamento de API e, portanto, são atribuídos com menos frequência um nome de domínio personalizado. Na maioria dos casos, apenas um único nome de domínio personalizado pode ser definido para um determinado ponto de extremidade. No entanto, a camada **Premium** dá suporte à configuração de vários nomes de host para o ponto de extremidade de **proxy** .

1. Selecione o ponto de extremidade que você deseja atualizar.
1. Na janela à direita, clique em **Personalizado**.

    - Em **Nome de domínio personalizado**, especifique o nome que você deseja usar. Por exemplo, `api.contoso.com`. Também há suporte para nomes de domínio \*curinga (por exemplo,. domain.com).
    - No **certificado**, selecione um certificado de Key Vault. Você também pode carregar um válido. Arquivo PFX e forneça sua **senha**, se o certificado estiver protegido com uma senha.

    > [!TIP]
    > É recomendável usar Azure Key Vault para gerenciar certificados e defini-los para rotação automática.
    > Se você usar Azure Key Vault para gerenciar o certificado SSL de domínio personalizado, verifique se o certificado foi inserido no Key Vault [como um _certificado_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), não como um _segredo_.
    >
    > Para buscar um certificado SSL, o gerenciamento de API deve ter a lista uma permissão obter segredos no Azure Key Vault que contém o certificado. Ao usar portal do Azure todas as etapas de configuração necessárias serão concluídas automaticamente. Ao usar as ferramentas de linha de comando ou a API de gerenciamento, essas permissões devem ser concedidas manualmente. Isso é feito em duas etapas. Primeiro, use a página identidades gerenciadas na instância de gerenciamento de API para garantir que a identidade gerenciada esteja habilitada e anote a ID da entidade de segurança mostrada nessa página. Em segundo lugar, dê à lista de permissões e obtenha permissões de segredos para essa ID de entidade de segurança no Azure Key Vault que contém o certificado.
    >
    > Se o certificado for definido para rotação automática, o gerenciamento de API selecionará a versão mais recente automaticamente sem nenhum tempo de inatividade para o serviço (se sua camada de gerenciamento de API tiver SLA-i. e. em todas as camadas, exceto a camada de desenvolvedor).

1. Clique em Aplicar.

    > [!NOTE]
    > O processo de atribuição do certificado pode levar aproximadamente 15 minutos dependendo do tamanho da implantação. O SKU de desenvolvedor tem tempo de inatividade, SKU Básico e posteriores não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuração de DNS

Ao configurar o DNS para seu nome de domínio personalizado, você tem duas opções:

- Configure um registro CNAME que aponte para o ponto de extremidade do seu nome de domínio personalizado configurado.
- Configure um registro A que aponta para seu endereço IP do gateway de gerenciamento de API.

> [!NOTE]
> Embora o endereço IP da instância de gerenciamento de API seja estático, ele pode ser alterado em alguns cenários. Por isso, é recomendável usar CNAME ao configurar o domínio personalizado. Leve isso em consideração ao escolher o método de configuração de DNS. Leia mais nas [perguntas frequentes sobre a API Mananagement](https://docs.microsoft.com/azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules).

## <a name="next-steps"></a>Próximas etapas

[Atualizar e colocar em escala o serviço](upgrade-and-scale.md)
