---
title: Guia de início rápido - configurar a criptografia de SSL de ponta a ponta no Gateway de aplicativo do Azure - portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para criar um Gateway de aplicativo do Azure com criptografia SSL de ponta a ponta.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831939"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurar o SSL de ponta a ponta usando o Gateway de aplicativo com o portal

Este artigo mostra como usar o portal do Azure para configurar a criptografia de SSL de ponta a ponta com um gateway de aplicativo v1 SKU.  

> [!NOTE]
> SKU do Gateway de aplicativo v2 requer certificados raiz confiáveis para habilitar configuração de ponta a ponta. Suporte do portal para adicionar certificados raiz confiáveis ainda não está disponível. Portanto, no caso de SKU do V2, consulte [configurar o SSL de ponta a ponta usando o PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar o SSL de ponta a ponta com um gateway de aplicativo, um certificado é necessário para o gateway e são necessários certificados para os servidores de back-end. O certificado de gateway é usado para derivar uma chave simétrica conforme a especificação do protocolo SSL. A chave simétrica, em seguida, é usada para criptografar e descriptografar o tráfego enviado para o gateway. Para criptografia SSL de ponta a ponta, o back-end deve estar na lista de permissões do gateway de aplicativo. Para fazer isso, carregue o certificado público dos servidores back-end, também conhecidos como certificados de autenticação, para o gateway de aplicativo. Adicionar o certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

Para obter mais informações, consulte [SSL o SSL de ponta a ponta e de término](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Criar um novo gateway de aplicativo com SSL de ponta a ponta

Para criar um novo gateway de aplicativo com a criptografia SSL de ponta a ponta, você precisará primeiro habilitar terminação SSL ao criar um novo gateway de aplicativo. Isso habilitará a criptografia SSL para a comunicação entre o gateway de aplicativo do cliente. Em seguida, você precisará lista branca certificados para servidores de back-end nas configurações de HTTP para habilitar a criptografia SSL para a comunicação entre os gateway e o back-end de servidores de aplicativos, realização, portanto, a criptografia SSL de ponta a ponta.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Habilite a terminação SSL ao criar um novo gateway de aplicativo

Consulte este artigo para entender como [habilitar terminação SSL ao criar um novo gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Lista branca certificados para servidores de back-end

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.

2. Selecione **configurações de HTTP** no menu à esquerda. Azure criou automaticamente uma configuração de HTTP padrão, **appGatewayBackendHttpSettings**, quando você criou o gateway de aplicativo. 

3. Selecione **appGatewayBackendHttpSettings**.

4. Sob **protocolo**, selecione **HTTPS**. Um painel para **certificados de autenticação de back-end** será exibida. 

5. Sob **certificados de autenticação de back-end**, escolha **criar novo**.

6. Insira adequado **nome**.

7. Carregue o certificado usando o **certificado CER carregue** caixa.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > O certificado fornecido nesta etapa deve ser a chave pública do certificado .pfx presente no back-end. Exporte o certificado (não o certificado raiz) instalado no servidor de back-end no formato CER (Claim, Evidence and Reasoning) e use-o nesta etapa. Esta etapa coloca o back-end na lista de permissões do gateway de aplicativo.

8. Clique em **Salvar**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Habilitar o SSL de ponta a ponta para o gateway de aplicativo existente

Para configurar um gateway de aplicativo existente com a criptografia SSL de ponta a ponta, você precisará primeiro terminação de SSL habilitar no ouvinte. Isso habilitará a criptografia SSL para a comunicação entre o gateway de aplicativo do cliente. Em seguida, você precisará lista branca certificados para servidores de back-end nas configurações de HTTP para habilitar a criptografia SSL para a comunicação entre os gateway e o back-end de servidores de aplicativos, realização, portanto, a criptografia SSL de ponta a ponta.

Você precisará usar um ouvinte com o protocolo HTTPS e certificado para habilitar a terminação SSL. Você não pode alterar o protocolo de um ouvinte existente. Portanto, você pode escolher usar um ouvinte existente com o certificado e o protocolo HTTPS, ou criar um novo ouvinte. Caso escolha a primeira opção, você pode ignorar o mencionadas abaixo as etapas para **terminação de habilitar o SSL no gateway de aplicativo existente** e passar diretamente para **lista branca certificados para servidores de back-end** seção. Se você escolher a última opção, execute estas etapas. 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Habilite a terminação SSL no gateway de aplicativo existente

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.

2. Selecione **ouvintes** no menu à esquerda.

3. Escolher entre **básicas** e **multissite** ouvinte de acordo com suas necessidades.

4. Sob **protocolo**, selecione **HTTPS**. Um painel para **certificado** será exibida.

5. Carregue o certificado PFX que você pretende usar para a terminação SSL entre o gateway de aplicativo do cliente.

   > [!NOTE]
   > Para fins de teste, você pode usar um certificado autoassinado. Você não deve usar um certificado autoassinado para cargas de trabalho de produção. Saiba como [criar um certificado autoassinado](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adicionar outras configurações necessárias para o **ouvinte** , de acordo com suas necessidades.

7. Selecione **OK** para salvar.

### <a name="whitelist-certificates-for-backend-servers"></a>Lista branca certificados para servidores de back-end

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.

2. Selecione **configurações de HTTP** no menu à esquerda. Você pode colocar certificados em um HTTP de back-end existente definindo ou criar uma nova configuração de HTTP. No abaixo da etapa, faremos um certificado da lista de permissões para a configuração de HTTP padrão, **appGatewayBackendHttpSettings**.

3. Selecione **appGatewayBackendHttpSettings**.

4. Sob **protocolo**, selecione **HTTPS**. Um painel para **certificados de autenticação de back-end** será exibida. 

5. Sob **certificados de autenticação de back-end**, escolha **criar novo**.

6. Insira adequado **nome**.

7. Carregue o certificado usando o **certificado CER carregue** caixa.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > O certificado fornecido nesta etapa deve ser a chave pública do certificado .pfx presente no back-end. Exporte o certificado (não o certificado raiz) instalado no servidor de back-end no formato CER (Claim, Evidence and Reasoning) e use-o nesta etapa. Esta etapa coloca o back-end na lista de permissões do gateway de aplicativo.

8. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
