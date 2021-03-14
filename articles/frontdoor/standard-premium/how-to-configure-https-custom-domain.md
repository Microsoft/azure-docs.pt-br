---
title: Configurar HTTPS para seu domínio personalizado em uma configuração de SKU padrão/Premium da porta do Azure
description: Neste artigo, você aprenderá a integrar um domínio personalizado ao SKU Standard/Premium da porta de front-end do Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740810"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Configurar HTTPS em um domínio personalizado de SKU padrão/Premium da porta (versão prévia) usando o portal do Azure

> [!NOTE]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

O padrão de porta frontal do Azure/Premium permite a entrega de TLS segura para seus aplicativos por padrão quando um domínio personalizado é adicionado. Usando o protocolo HTTPS em seu domínio personalizado, você garante que seus dados confidenciais sejam entregues com segurança com criptografia TLS/SSL quando enviados pela Internet. Quando seu navegador da Web é conectado a um site da Web via HTTPS, ele valida o certificado de segurança do site da Web e verifica se ele é emitido por uma autoridade de certificação legítima. Esse processo oferece segurança e protege seus aplicativos Web contra ataques.

O Standard/Premium da porta do Azure dá suporte ao certificado gerenciado do Azure e aos certificados gerenciados pelo cliente. Por padrão, a porta frontal do Azure habilita automaticamente o HTTPS para todos os seus domínios personalizados usando os certificados gerenciados do Azure. Nenhuma etapa adicional é necessária para obter um certificado gerenciado do Azure. Um certificado é criado durante o processo de validação de domínio. Você também pode usar seu próprio certificado integrando o Azure front door Standard/Premium com seu Key Vault.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para saber mais, confira os [**Termos de uso complementares das versões prévias do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Antes de configurar o HTTPS para seu domínio personalizado, você deve primeiro criar um perfil padrão/Premium da porta do Azure. Para obter mais informações, consulte [início rápido: criar um perfil padrão/Premium da porta do Azure](create-front-door-portal.md).

* Se você ainda não tiver um domínio personalizado, deverá primeiro comprar um com um provedor de domínio. Por exemplo, confira [Comprar um nome de domínio personalizado](../../app-service/manage-custom-dns-buy-domain.md).

* Se você estiver usando o Azure para hospedar seus [domínios DNS](../../dns/dns-overview.md), você deve delegar o DNS (sistema de nomes de domínio) do provedor de domínio para um DNS do Azure. Confira [Delegar um domínio ao DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)para saber mais. Caso contrário, se você estiver usando um provedor de domínio para manipular seu domínio DNS, você deverá validar manualmente o domínio inserindo registros TXT solicitados do DNS.

## <a name="azure-managed-certificates"></a>Certificados gerenciados do Azure

1. Em configurações para o perfil padrão/Premium da porta do Azure, selecione **domínios** e, em seguida, selecione **+ Adicionar** para adicionar um novo domínio.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Captura de tela da página inicial de configuração de domínio.":::

1. Na página **Adicionar um domínio** , para *Gerenciamento de DNS* , selecione a opção **DNS gerenciado do Azure** . 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Captura de tela da página Adicionar um domínio com o DNS gerenciado do Azure selecionado.":::

1. Valide e associe o domínio personalizado a um ponto de extremidade seguindo as etapas em habilitando o [domínio personalizado](how-to-add-custom-domain.md).

1. Depois que o domínio personalizado é associado ao ponto de extremidade com êxito, um certificado gerenciado do Azure é implantado na porta frontal. Esse processo pode levar alguns minutos para ser concluído.

## <a name="using-your-own-certificate"></a>Usando seu próprio certificado

Você também pode optar por usar seu próprio certificado TLS. Esse certificado deve ser importado para um Azure Key Vault antes que você possa usá-lo com o Azure front door Standard/Premium. Consulte [importar um certificado](../../key-vault/certificates/tutorial-import-certificate.md) para Azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparar a conta e o certificado do Azure Key Vault
 
1. Você deve ter uma conta de Azure Key Vault em execução na mesma assinatura que o Standard/Premium da porta do Azure que deseja habilitar o HTTPS personalizado. Crie uma conta do Azure Key Vault caso não tenha uma.

    > [!WARNING]
    > No momento, o Azure Front Door oferece suporte apenas a contas do Key Vault na mesma assinatura que a configuração do Front Door. Escolher um Key Vault em uma assinatura diferente do padrão/Premium da porta do Azure resultará em uma falha.

1. Se você já tiver um certificado, poderá carregá-lo diretamente em sua conta de Azure Key Vault. Caso contrário, crie um novo certificado diretamente por meio de Azure Key Vault de uma das autoridades de certificação de parceiro que Azure Key Vault se integra ao. Faça upload do certificado como um objeto **certificate**, em vez de um **secret**.

    > [!NOTE]
    > Para seu certificado TLS/SSL, o Front Door não é compatível com certificados com algoritmos de criptografia de curva elíptica.

#### <a name="register-azure-front-door"></a>Registrar o Azure Front Door

Registre a entidade de serviço para o Azure Front Door como aplicativo no seu Azure Active Directory, via PowerShell.

> [!NOTE]
> Essa ação requer permissões de Administrador global e precisa ser executada apenas **uma vez** por locatário.

1. Se for necessário, instale o [Azure PowerShell](/powershell/azure/install-az-ps) no PowerShell em seu computador local.

1. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Conceder acesso ao seu cofre de chaves para o Azure Front Door
 
Conceda permissão ao Azure Front Door para acessar os certificados na sua conta do Azure Key Vault.

1. Em sua conta do cofre de chaves, em configurações, selecione **políticas de acesso**. Em seguida, selecione **Adicionar novo** para criar uma nova política.

1. Em **selecionar entidade de segurança**, procure **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** e escolha * * Microsoft. AzureFrontDoor-CDN * *. Clique em **Selecionar**.

1. Em **Permissões do segredo**, selecione **Obter** para permitir que o Front Door recupere o certificado.

1. Em **Permissões do certificado**, selecione **Obter** para permitir que o Front Door recupere o certificado.

1. Selecione **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Selecionar o certificado para o Azure Front Door a implantar
 
1. Retorne para o Standard/Premium da porta do Azure no Portal.

1. Navegue até **segredos** em *configurações* e selecione **Adicionar certificado**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Captura de tela da página de aterrissagem de segredo de porta frontal do Azure.":::

1. Na página **Adicionar certificado** , marque a caixa de seleção do certificado que você deseja adicionar à porta de recepção do Azure Standard/Premium. Deixe a seleção de versão como "mais recente" e selecione **Adicionar**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Captura de tela da página Adicionar certificado.":::

1. Depois que o certificado é provisionado com êxito, você pode usá-lo quando adicionar um novo domínio personalizado.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Captura de tela do certificado adicionada com êxito aos segredos.":::

1. Navegue para **domínios** em *configuração* e selecione **+ Adicionar** para adicionar um novo domínio personalizado. Na página **Adicionar um domínio** , escolha "Traga seu próprio certificado (BYOC)" para *https*. Para *segredo*, selecione o certificado que você deseja usar na lista suspensa. 

    > [!NOTE]
    > O certificado selecionado deve ter um nome comum (CN) igual ao domínio personalizado que está sendo adicionado.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Captura de tela de adicionar uma página de domínio personalizada com HTTPS.":::

1. Siga as etapas na tela para validar o certificado. Em seguida, associe o domínio personalizado recém-criado a um ponto de extremidade, conforme descrito em [criando um guia de domínio personalizado](how-to-add-custom-domain.md) .

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Alterar do Azure gerenciado para trazer seu próprio certificado (BYOC)

1. Você pode alterar um certificado gerenciado do Azure existente para um certificado gerenciado pelo usuário selecionando o estado do certificado para abrir a página de **detalhes do certificado** .

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Captura de tela do estado do certificado na página de aterrissagem de domínios." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Na página **detalhes do certificado** , você pode alterar da opção "Azure Managed" para "Traga seu próprio certificado (BYOC)". Em seguida, siga as mesmas etapas anteriores para escolher um certificado. Selecione **Atualizar** para alterar o certificado associado com um domínio.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Captura de tela da página de detalhes do certificado.":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [caching com o Azure front door Standard/Premium](concept-caching.md).
