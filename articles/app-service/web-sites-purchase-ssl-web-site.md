---
title: Comprar e configurar um certificado SSL do Azure – Serviço de Aplicativo | Microsoft Docs
description: Saiba como comprar um certificado de Serviço de Aplicativo e associá-lo ao seu aplicativo de Serviço de Aplicativo
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: 3e113639dbe4220b943d49dc610ee22b6416e12a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832665"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Comprar e configurar um certificado SSL para o Serviço de Aplicativo do Azure

Este tutorial mostra como proteger seu [aplicativo do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/) ou [aplicativo de função](https://docs.microsoft.com/azure/azure-functions/) criando (comprando) um certificado do Serviço de Aplicativo no [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) e vinculando-o a um aplicativo do Serviço de Aplicativo.

> [!TIP]
> Certificados do Serviço de Aplicativo podem ser usados para qualquer Serviço Azure ou não Azure e não se limitam aos Serviços de Aplicativos. Para fazer isso, você precisa criar uma cópia local do PFX de um Certificado do Serviço de Aplicativo para que possa usá-lo em qualquer lugar. Para obter mais informações, leia [Como criar uma cópia local do PFX de um Certificado do Serviço de Aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, é necessário ter:

- [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/)
- [Mapear um nome de domínio para seu aplicativo](app-service-web-tutorial-custom-domain.md) ou [comprar e configurá-lo no Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Iniciar o pedido de certificado

Inicie um pedido de certificado de Serviço de Aplicativo na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">página de criação de Certificado do Serviço de Aplicativo</a>.

![Criação de certificado](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Use a tabela a seguir para ajudá-lo a configurar o certificado. Ao terminar, clique em **Criar**.

| Configuração | DESCRIÇÃO |
|-|-|
| NOME | Um nome amigável para o seu certificado de Serviço de Aplicativo. |
| Nome do Host do Domínio Raiz | Se você especificar o domínio raiz aqui, você obtém um certificado que protege *os dois*: o domínio raiz e o subdomínio `www`. Para proteger apenas um subdomínio qualquer, especifique aqui o nome de domínio totalmente qualificado do subdomínio (por exemplo, `mysubdomain.contoso.com`). |
| Assinatura | O datacenter onde o aplicativo Web está hospedado. |
| Grupo de recursos | O grupo de recursos que contém o certificado. Você pode usar um novo grupo de recursos ou selecionar o mesmo grupo de recursos que seu aplicativo de Serviço de Aplicativo, por exemplo. |
| Certificado SKU | Determina o tipo de certificado para criar, se um certificado padrão ou uma [certificado curinga](https://wikipedia.org/wiki/Wildcard_certificate). |
| Termos legais | Clique para confirmar que você concorda com os termos legais. Os certificados são obtidos do GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Armazenar no Azure Key Vault

Quando o processo de compra do certificado tiver sido concluído, haverá mais algumas etapas a serem concluídas antes de começar a usar esse certificado. 

Selecione o certificado na página [Certificados do Serviço de Aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, clique em **Configuração do Certificado** > **Etapa 1: armazenar**.

![inserir imagem de pronto para armazenar no KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

O [Cofre da Chave](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. É o armazenamento de escolha para certificados de Serviço de Aplicativo.

Na página **Status do Key Vault**, clique em **Repositório do Key Vault** para criar um novo cofre ou escolher um cofre existente. Se você optar por criar um novo cofre, use a tabela a seguir para ajudá-lo a configurar o cofre e clique em Criar. veja para criar novo Key Vault no mesmo grupo de recursos e assinatura.

| Configuração | DESCRIÇÃO |
|-|-|
| NOME | Um nome exclusivo que consiste em caracteres alfanuméricos e traços. |
| Grupo de recursos | Como recomendação, selecione o mesmo grupo de recursos do seu certificado de Serviço de Aplicativo. |
| Local padrão | Selecione o mesmo local que o aplicativo de Serviço de Aplicativo. |
| Tipo de preço | Para obter mais informações, confira [Detalhes de preços do Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define os aplicativos e o acesso permitido aos recursos do cofre. Você pode configurá-lo mais tarde seguindo as etapas em [Conceder acesso de vários aplicativos a um cofre de chaves](../key-vault/key-vault-group-permissions-for-apps.md). |
| Acesso à Rede Virtual | Restringir o acesso do cofre a determinadas redes virtuais do Azure. Você pode configurá-lo mais tarde seguindo as etapas em [Configurar o Redes Virtuais e Firewall do Azure Key Vault](../key-vault/key-vault-network-security.md) |

Depois de selecionar o cofre, feche a página **Repositório do Key Vault**. A opção **Armazenar** deve mostrar uma marca de seleção verde para sucesso. Mantenha a página aberta para a próxima etapa.

## <a name="verify-domain-ownership"></a>Verificar a propriedade de domínio

Na mesma página **Configuração do Certificado** usada na última etapa, clique em **Etapa 2: verificar**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Selecione **Verificação do Serviço de Aplicativo**. Uma vez que você já mapeou o domínio ao seu aplicativo Web (veja [Pré-requisitos](#prerequisites)), ele já foi verificado. Basta clicar em **Verificar** para concluir esta etapa. Clique no botão **Atualizar** até que a mensagem **O Certificado tem Domínio Verificado** seja exibida.

> [!NOTE]
> Há suporte para quatro tipos de métodos de verificação de domínio: 
> 
> - **Serviço de Aplicativo** – a opção mais conveniente quando o domínio já está mapeado para um aplicativo do Serviço de Aplicativo na mesma assinatura. Ela tira proveito do fato de que o aplicativo de Serviço de Aplicativo já verificou a propriedade de domínio.
> - **Domínio** – verifique um [domínio do Serviço de Aplicativo que você adquiriu do Azure](manage-custom-dns-buy-domain.md). O Azure adiciona automaticamente a verificação do registro TXT para você e conclui o processo.
> - **Email** – verifique o domínio enviando um email para o administrador de domínio. As instruções são fornecidas quando você seleciona a opção.
> - **Manual** – verifique o domínio usando uma página HTML (apenas certificado **Standard**) ou um registro TXT do DNS. As instruções são fornecidas quando você seleciona a opção.

## <a name="bind-certificate-to-app"></a>Associar certificado ao aplicativo

No **[portal do Azure](https://portal.azure.com/)**, no menu à esquerda, selecione **Serviços de Aplicativos** > **\<seu_aplicativo >**.

No painel de navegação à esquerda de seu aplicativo, selecione **Configurações de SSL** > **Certificados Particulares (.pfx)** > **Importar Certificado do Serviço de Aplicativo**.

![inserir imagem de Importar Certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Selecione o certificado que você acabou de adquirir.

Agora que o certificado foi importado, você precisa associá-lo a um nome de domínio mapeado no seu aplicativo. Selecione **Associações** > **Adicionar Associação SSL**. 

![inserir imagem de Importar Certificado](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Use a tabela a seguir para ajudá-lo a configurar a associação na caixa de diálogo **Associações SSL** e, em seguida, clique em **Adicionar Associação**.

| Configuração | DESCRIÇÃO |
|-|-|
| Nome do host | O nome de domínio ao qual adicionar a associação SSL. |
| Impressão Digital do Certificado Privado | O certificado a ser associado. |
| Tipo de SSL | <ul><li>**SSL de SNI** – várias associações SSL baseadas em SNI podem ser adicionadas. Esta opção permite que vários certificados SSL protejam vários domínios no mesmo endereço IP. Navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) dão suporte ao SNI (encontre informações de suporte ao navegador mais abrangentes em [Indicação de Nome de Servidor](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**SSL baseado em IP** – apenas uma associação SSL baseada em IP pode ser adicionada. Esta opção permite apenas um certificado SSL para proteger um endereço IP público dedicado. Depois de configurar a associação, siga as etapas em [Remapear um registro para IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Verificar o acesso HTTPS

Visite seu aplicativo usando `HTTPS://<domain_name>`, em vez de `HTTP://<domain_name>`, para verificar se o certificado foi configurado corretamente.

## <a name="rekey-certificate"></a>Certificado de rechaveamento

Se você achar que seu certificado 's privado chave estiver comprometida, você pode fazer o rechaveamento da seu certificado. Selecione o certificado na [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) página e, em seguida, selecione **rechaveamento e sincronização** na navegação à esquerda.

Clique em **rechaveamento** para iniciar o processo. Esse processo pode demorar de um a 10 minutos para ser concluído.

![inserir imagem de Rechaveamento SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

A criação de uma nova chave para o certificado causará a emissão de um novo certificado pela autoridade de certificação.

Depois que a operação de rechaveamento for concluída, clique em **sincronização**. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.

> [!NOTE]
> Se você não clicar **sincronização**, serviço de aplicativo sincroniza automaticamente seu certificado dentro de 48 horas.

## <a name="renew-certificate"></a>Renovar certificado

Para ativar a renovação automática do seu certificado a qualquer momento, selecione o certificado na página [Certificados do Serviço de Aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e clique em **Configurações de Renovação Automática** no painel de navegação à esquerda.

Selecione **Ativado** e clique em **Salvar**. Os certificados poderão iniciar automaticamente renovação 60 dias antes do término se você tiver ativado a renovação automática.

![renovar o certificado automaticamente](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Para renovar manualmente o certificado, clique em **Renovação Manual**. Você pode solicitar renovar manualmente o certificado de 60 dias antes da expiração.

Depois que a operação de renovação for concluída, clique em **sincronização**. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.

> [!NOTE]
> Se você não clicar **sincronização**, serviço de aplicativo sincroniza automaticamente seu certificado dentro de 48 horas.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Impor HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Impor o TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Usar um certificado SSL no código de aplicativo no Serviço de Aplicativo do Azure](app-service-web-ssl-cert-load.md)
* [Perguntas frequentes: Certificados do Serviço de Aplicativo](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
