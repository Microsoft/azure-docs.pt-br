---
title: "Adicionar um certificado SSL ao seu aplicativo de serviço de aplicativo do Azure | Microsoft Docs"
description: "Saiba como adicionar um certificado SSL ao seu aplicativo de serviço de aplicativo."
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
ms.openlocfilehash: 191dd7240ad15b4936a72bc27a2d0162350f3afb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Comprar e configurar um certificado SSL para seu Serviço de Aplicativo do Azure

Neste tutorial, você irá proteger seu aplicativo web com a compra de um certificado SSL para o  **[serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** armazená-los em segurança no [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)e associá-lo a um domínio personalizado.

## <a name="step-1---log-in-to-azure"></a>Etapa 1 - Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Etapa 2: Fazer um pedido de certificado SSL

Você pode fazer um pedido de certificado SSL, criando um novo [certificado de serviço de aplicativo](https://portal.azure.com/#create/Microsoft.SSL) no **portal do Azure**.

![Criação de certificado](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Insira um amigável **nome** para o SSL de certificado e insira o **nome de domínio**

> [!NOTE]
> Essa é uma das partes mais importantes do processo de compra. Digite corretamente o nome do host (domínio personalizado) que você deseja proteger com esse certificado. **NÃO** acrescente WWW ao nome do host. 
>

Selecione seu **assinatura**, **grupo de recursos**, e **SKU de certificado**

> [!WARNING]
> Certificados de serviço de aplicativo só podem ser usados por outros serviços de aplicativo dentro da mesma assinatura.  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Etapa 3: Armazenar o certificado no Cofre da Chave do Azure

> [!NOTE]
> O [Cofre da Chave](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem.
>

Após a conclusão da compra do Certificado SSL, você precisará abrir a folha Recursos dos [Certificados de Serviço de Aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders).

![inserir imagem de pronto para armazenar no KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Você perceberá que o status do certificado é **"Emissão Pendente"** , pois há mais algumas etapas que precisam ser concluídas antes de começar a usar esses certificados.

Clique em **"Configuração do Certificado"** na folha Propriedades do Certificado e clique em **"Etapa 1: Armazenar"** para armazenar esse certificado no Cofre de Chaves do Azure.

Na folha **"Status do Cofre de Chaves"**, clique em **"Repositório do Cofre de Chaves"** para escolher um Cofre de Chaves existente a fim de armazenar esse certificado **OU "Criar Novo Cofre de Chaves"** para criar um novo Cofre de Chaves no mesmo grupo de recursos e na mesma assinatura.

> [!NOTE]
> O cofre da chave do Azure tem encargos mínimo para armazenar o certificado.
> Para obter mais informações, consulte  **[detalhes de preços do Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Depois de selecionar o repositório de Cofre de chave para armazenar esse certificado, o **armazenar** opção tiverem êxito.

![Inserir imagem de sucesso de repositório em KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Etapa 4: Verificar a propriedade do domínio

> [!NOTE]
> Há três tipos de verificação de domínio de certificados de serviço de aplicativo com suporte: verificação do domínio, email, Manual. Eles são explicados em mais detalhes no [seção avançada](#advanced).

Da mesma **configuração do certificado** folha que você usou na etapa 3, clique em **etapa 2: Verifique se**.

**Verificação de domínio** é o processo mais conveniente **somente se** que  **[adquirido seu domínio personalizado do serviço de aplicativo do Azure.](custom-dns-web-site-buydomains-web-app.md)**
Clique no botão **"Verificar"** para concluir esta etapa.

![Inserir imagem de verificação de domínio](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Depois de clicar em **Verify**, use o **atualização** botão até que o **Verify** opção deve mostrar o êxito.

![Inserir imagem de verificar o êxito na KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Etapa 5: Atribuir o certificado ao Aplicativo do Serviço de Aplicativo

> [!NOTE]
> Antes de executar as etapas nesta seção, você precisa ter associado um nome de domínio personalizado ao seu aplicativo. Para saber mais, confira **[Configurando um nome de domínio personalizado para um aplicativo Web.](app-service-web-tutorial-custom-domain.md)**
>

No  **[portal do Azure](https://portal.azure.com/)**, clique a opção **Serviço de Aplicativo** à esquerda da página.

Clique no nome do aplicativo ao qual você deseja atribuir a esse certificado.

Em **Configurações**, clique em **Certificados SSL**.

Clique em **Importar Certificado do Serviço de Aplicativo** e selecione o certificado que você acabou de adquirir.

![inserir imagem de Importar Certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

No **associações ssl** seção clique **adicionar associações**e use os menus suspensos para selecionar o nome de domínio a ser protegido com SSL e o certificado a usar. Você também pode selecionar se deseja usar **[SNI (Indicação de Nome de Servidor)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou SSL baseado em IP.

![inserir imagem de Associações SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Clique em **Adicionar Associação** para salvar as alterações e habilitar o SSL.

> [!NOTE]
> Se você selecionou **SSL com base em IP** e seu domínio personalizado foi configurado pelo uso de um registro A, você deverá executar as seguintes etapas adicionais. Eles são explicados em mais detalhes no [seção avançada](#Advanced).

Neste ponto, você poderá visitar o seu aplicativo usando `HTTPS://` em vez de `HTTP://` para verificar se o certificado foi configurado corretamente.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Etapa 6 – Tarefas de gerenciamento

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Avançado

### <a name="verifying-domain-ownership"></a>Verificando a propriedade de domínio

Há dois outros tipos de suporte para certificados de serviço do aplicativo de verificação de domínio: Mail e Verificação Manual.

#### <a name="mail-verification"></a>Verificação por email

Um email de verificação já foi enviado para os endereços de email associados a esse domínio personalizado.
Para concluir a etapa de verificação por email, abra o email e clique no link de verificação.

![Inserir imagem de verificação de email](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Se você precisar reenviar o email de verificação, clique no botão **Reenviar Email**.

#### <a name="manual-verification"></a>Verificação manual

> [!IMPORTANT]
> Verificação da página da Web em HTML (funciona somente com SKU de Certificado Padrão)
>

1. Crie um arquivo HTML chamado **"starfield.html"**

1. O conteúdo desse arquivo deve ser o nome exato do Token de verificação de domínio. (Você pode copiar o token na folha Status de Verificação de Domínio)

1. Carregue esse arquivo na raiz do servidor Web que hospeda seu domínio`/.well-known/pki-validation/starfield.html`

1. Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação. Pode demorar alguns minutos até a verificação ser concluída.

> [!TIP]
> Verifique se um terminal usando `curl -G http://<domain>/.well-known/pki-validation/starfield.html` a resposta deve conter o `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Verificação de registro TXT do DNS

1. Usando o gerenciador de DNS, crie um registro TXT no subdomínio `@` com um valor igual ao Token de Verificação de Domínio.
1. Clique em **"Atualizar"** para atualizar o Status do certificado após a verificação.

> [!TIP]
> Você precisa criar um registro TXT em `@.<domain>` com o valor `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Atribuir o certificado ao Aplicativo do Serviço de Aplicativo

Se você selecionou **SSL baseado em IP** e seu domínio personalizado foi configurado pelo uso de um registro A, você deverá executar as seguintes etapas adicionais:

Depois de ter configurado uma associação de SSL baseada em IP, um endereço IP dedicado é atribuído ao seu aplicativo. Encontre esse endereço IP na página **Domínio personalizado** nas configurações do aplicativo, logo acima da seção **Nomes de host**. Ele é listado como **endereço IP externo**

![inserir imagem de IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Observe que esse endereço IP será diferente do endereço IP virtual usado anteriormente para configurar o registro A de seu domínio. Se você estiver configurado para usar SSL baseado em SNI ou não estão configurados para usar SSL, nenhum endereço será listado para essa entrada.

Usando as ferramentas fornecidas pelo registro de nomes de domínio, modifique o registro A de seu nome de domínio personalizado para redirecionar para o endereço IP da etapa anterior.

## <a name="rekey-and-sync-the-certificate"></a>Criar novas chaves e sincronizar o certificado

Se você precisar rechaveamento seu certificado, selecione **rechaveamento e sincronizar** opção **propriedades do certificado** folha.

Clique no botão **"Criar Nova Chave"** para iniciar o processo. Esse processo pode demorar de um a 10 minutos para ser concluído.

![inserir imagem de Nova Chave SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

A criação de uma nova chave para o certificado causará a emissão de um novo certificado pela autoridade de certificação.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar uma rede de fornecimento de conteúdo](app-service-web-tutorial-content-delivery-network.md)