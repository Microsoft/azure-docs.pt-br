---
title: Tutorial - Configurar HTTPS em um domínio personalizado da CDN do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como habilitar ou desabilitar o HTTPS no ponto de extremidade da CDN do Azure com um domínio personalizado.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/17/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: f22273a28d5e4207712bdba71ef788629d51916e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321664"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Configurar HTTPS em um domínio personalizado da CDN do Azure

Este tutorial mostra como habilitar o protocolo HTTPS para um domínio personalizado que está associado um ponto de extremidade da CDN do Azure. Usando o protocolo HTTPS em seu domínio personalizado (por exemplo, https:\//www.contoso.com), você garante que seus dados confidenciais são fornecidos com segurança por meio da criptografia TLS/SSL quando ele são enviada pela Internet. Quando seu navegador da Web é conectado a um site da Web via HTTPS, ele valida o certificado de segurança do site da Web e verifica se ele é emitido por uma autoridade de certificação legítima. Esse processo oferece segurança e protege seus aplicativos Web contra ataques.

A CDN do Azure dá suporte a HTTPS em um nome do host do ponto de extremidade da CDN por padrão. Por exemplo, se você criar um ponto de extremidade da CDN (como https:\//contoso.azureedge.net), o HTTPS será habilitado automaticamente.  

Alguns dos principais atributos do recurso HTTPS são:

- Sem custo adicional: não há nenhum custo para a aquisição ou renovação do certificado e nenhum custo para tráfego HTTPS. Você paga apenas pelo GB de saída da CDN.

- Habilitação simples: o provisionamento com um clique está disponível no [portal do Azure](https://portal.azure.com). Você também pode usar a API REST ou outras ferramentas de desenvolvedor para habilitar o recurso.

- Gerenciamento de certificado completo disponível: todos os certificados de aquisição e gerenciamento são manipulados para você. Os certificados são provisionados e renovados automaticamente antes da expiração, o que remove os riscos de interrupção do serviço devido à expiração de um certificado.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Habilite o protocolo HTTPS em seu domínio personalizado.
> - Usar um certificado gerenciado por CDN 
> - Usar o seu próprio certificado
> - Validar o domínio
> - Desabilite o protocolo HTTPS em seu domínio personalizado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Antes de concluir as etapas deste tutorial, crie primeiro um perfil CDN e pelo menos um ponto de extremidade da CDN. Para saber mais, confira [Início Rápido: Criar um ponto de extremidade e um perfil de CDN do Azure](cdn-create-new-endpoint.md).

Além disso, você deve associar um domínio personalizado da CDN do Azure no ponto de extremidade da CDN. Para obter mais informações, confira [Tutorial: Adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure](cdn-map-content-to-custom-domain.md) 

> [!IMPORTANT]
> Os certificados gerenciados pela CDN não estão disponíveis para os domínios raiz ou apex. Se seu domínio personalizado da CDN do Azure for um domínio raiz ou apex, você deverá usar o recurso Traga seu próprio certificado. 
>

---

## <a name="ssl-certificates"></a>Certificados SSL
Para habilitar o protocolo HTTPS para fornecer com segurança o conteúdo em um domínio personalizado de CDN do Azure, você deve usar um certificado SSL. Você pode optar por usar um certificado que é gerenciado pela CDN do Azure ou usar seu próprio certificado.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opção 1 (padrão): habilitar HTTPS com um certificado gerenciado por CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Quando você usa um certificado gerenciado de CDN, o recurso HTTPS pode ser ativado com apenas alguns cliques. A CDN do Azure lida completamente com tarefas de gerenciamento de certificado, como aquisição e renovação. Depois que você habilitar o recurso, o processo será iniciado imediatamente. Se o domínio personalizado já estiver mapeado para o ponto de extremidade de CDN, nenhuma ação adicional será necessária. A CDN do Azure processará as etapas e concluirá a solicitação automaticamente. No entanto, se o domínio personalizado for mapeado em outro lugar, você deverá usar o email para validar sua propriedade de domínio.

Para habilitar HTTPS em um domínio personalizado, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), navegue até o perfil **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Akamai**, **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.

2. Na lista de pontos de extremidade da CDN, selecione o ponto de extremidade que contém seu domínio personalizado.

    ![Lista de pontos de extremidade](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    A página **Ponto de extremidade** é exibida.

3. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain.png)

    A página de **Domínio personalizado** é exibida.

4. Em Tipo de gerenciamento de certificado, selecione **CDN gerenciado**.

5. Selecione **Ativado** para habilitar HTTPS.

    ![Status de HTTPS de domínio personalizado](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. Prossiga para [Validar o domínio](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[Opção 2: habilitar HTTPS com seu próprio certificado](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Este recurso está disponível apenas com os perfis de **CDN do Azure da Microsoft** e de **CDN do Azure do Verizon**. 
>
 
Você pode usar seu próprio certificado para habilitar o recurs HTTPS. Esse processo é feito por meio de uma integração com o Azure Key Vault, que permite que você armazene os certificados com segurança. A CDN do Azure usa esse mecanismo seguro para obter o certificado e requer algumas etapas adicionais. Quando você cria o certificado SSL, você deve criá-lo com uma autoridade de certificação (CA) com permissão. Caso contrário, se você usar uma autoridade de certificação sem permissão, sua solicitação será rejeitada. Confira uma lista das autoridades de certificação permitidas em [Autoridades de certificação permitidas para habilitar HTTPS personalizado na CDN do Azure](cdn-troubleshoot-allowed-ca.md). Para o **CDN do Azure da Verizon**, qualquer CA válida será aceita. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparar a conta e o certificado do Azure Key Vault
 
1. Azure Key Vault: é necessário ter uma conta do Azure Key Vault em execução sob a mesma assinatura que o perfil da CDN do Azure e os pontos de extremidade da CDN desejados para habilitar o HTTPS personalizado. Crie uma conta do Azure Key Vault caso você não tenha uma.
 
2. Certificados do Azure Key Vault: se já tiver um certificado, poderá carregá-lo diretamente na sua conta do Azure Key Vault ou criar um certificado diretamente pelo Azure Key Vault por meio de uma das CAs de parceiros às quais o Azure Key Vault se integra. 

### <a name="register-azure-cdn"></a>Registrar a CDN do Azure

Registre a CDN do Azure como um aplicativo em seu Azure Active Directory por meio do PowerShell.

1. Se for necessário, instale o [Azure PowerShell](/powershell/azure/install-az-ps) em seu computador local.

2. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Registrar a CDN do Azure no PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Conceder à CDN do Azure acesso ao seu cofre de chaves
 
Conceda à CDN do Azure permissão para acessar os certificados (segredos) em sua conta do Azure Key Vault.

1. Em sua conta do Key Vault, em CONFIGURAÇÕES, selecione **Políticas de acesso** e depois **Adicionar novo** para criar uma nova política.

    ![Criar nova política de acesso](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. Em **Selecionar entidade de segurança**, procure por **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** e escolha **Microsoft.Azure.Cdn**. Clique em **Selecionar**.

    ![Configurações de política de acesso](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Em **Permissões do segredo**, selecione **Obter** para permitir que a CDN execute essas permissões para obter e listar os certificados. 

4. Selecione **OK**. 

    A CDN do Azure pode acessar este cofre de chaves e os certificados (segredos) que estão armazenados nesse cofre.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Selecionar o certificado para a CDN do Azure implantar
 
1. Retorne ao portal da CDN do Azure e selecione o perfil e o ponto de extremidade de CDN desejados para habilitar o HTTPS personalizado. 

2. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    A página de **Domínio personalizado** é exibida.

3. Em Tipo de gerenciamento de certificado, selecione **Usar meu próprio certificado**. 

    ![Configurar seu certificado](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Selecione um cofre de chaves, o certificado (segredo) e a versão do certificado.

    A CDN do Azure lista as seguintes informações: 
    - As contas do Key Vault para sua ID de assinatura. 
    - Os certificados (segredos) no cofre de chaves selecionado. 
    - As versões de certificado disponíveis. 
 
5. Selecione **Ativado** para habilitar HTTPS.
  
6. Quando você usa seu próprio certificado, a validação de domínio não é necessária. Avance para a [Aguardar a propagação](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Validar o domínio

Se você já tiver um domínio personalizado em uso que é mapeado para o ponto de extremidade personalizado com um registro CNAME, ou se estiver usando seu próprio certificado, vá para  
[O domínio personalizado é mapeado para o ponto de extremidade da CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Caso contrário, se a entrada de registro CNAME para o ponto de extremidade não existe ou contém o subdomínio cdnverify, vá para [Domínio personalizado não está mapeado para o ponto de extremidade da CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>O domínio personalizado é mapeado para o ponto de extremidade da CDN por meio de um registro CNAME

Quando tiver adicionado um domínio personalizado ao seu ponto de extremidade, você criou um registro CNAME na tabela de DNS do seu registrador de domínio para mapear para o nome de host do ponto de extremidade da CDN. Se esse registro CNAME ainda existir e não contiver o subdomínio cdnverify, a autoridade de certificação (CA) DigiCert o usará para validar automaticamente a propriedade do seu domínio personalizado. 

Se você estiver usando seu próprio certificado, a validação de domínio não será necessária.

O registro CNAME deve estar no formato a seguir, em que *Nome* é o nome de domínio personalizado e *Valor* é o nome de host do ponto de extremidade da CDN:

| Nome            | Tipo  | Valor                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Para obter mais informações sobre os registros CNAME, consulte [criar o registro de DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Se o registro CNAME estiver no formato correto, o DigiCert verificará automaticamente seu nome de domínio personalizado e criará um certificado dedicado ao seu nome de domínio. O DigitCert não enviará um email de verificação, e você não precisará aprovar sua solicitação. O certificado é válido por um ano e será renovado automaticamente antes de expirar. Avance para a [Aguardar a propagação](#wait-for-propagation). 

A validação automática geralmente demora algumas horas. Se o domínio não for validado em 24 horas, abra um tíquete de suporte.

>[!NOTE]
>Caso você tenha um registro de CAA (Autorização de Autoridade de Certificação) com o provedor DNS, ele deverá incluir o DigiCert como uma AC válida. Um registro de CAA permite que os proprietários do domínio especifiquem com seus provedores DNS quais ACs estão autorizadas a emitir certificados para seus domínios. Se uma AC receber um pedido de um certificado para um domínio que tem um registro de CAA e a AC não estiver listada como um emissor autorizado, ela será proibida de emitir o certificado para esse domínio ou subdomínio. Para obter informações sobre como gerenciar registros CAA, consulte [Gerenciar registros CAA](https://support.dnsimple.com/articles/manage-caa-record/). Para uma ferramenta de registro CAA, consulte [Ajuda do registro CAA](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>O domínio personalizado não é mapeado para o ponto de extremidade da CDN

>[!NOTE]
>Se você estiver usando a **CDN do Azure do Akamai**, seu domínio personalizado deve ser mapeado ao ponto de extremidade da CDN com um registro CNAME, conforme mencionado acima.  Esse recurso está atualmente em nossa lista de pendências. 

Se a entrada do registro CNAME do ponto de extremidade não existir mais ou contiver o subdomínio cdnverify, siga o restante das instruções nesta etapa.

O DigiCert envia um email de verificação para os seguintes endereços de email. Verifique se é possível aprovar diretamente por meio de um dos seguintes endereços:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

Você deverá receber um email em poucos minutos, semelhante ao exemplo a seguir, solicitando que você aprove a solicitação. Se estiver usando um filtro de spam, adicione verification@digicert.com à sua lista de permissões. Se você não receber um email em até 24 horas, contate o suporte da Microsoft.
    
![Email de validação de domínio](./media/cdn-custom-ssl/domain-validation-email.png)

Quando você clicar no link de aprovação, será direcionado para o seguinte formulário de aprovação online: 
    
![Formulário de validação de domínio](./media/cdn-custom-ssl/domain-validation-form.png)

Siga as instruções do formulário; você tem duas opções de verificação:

- Você pode aprovar todos os pedidos futuros feitos por meio da mesma conta para o mesmo domínio raiz; por exemplo, contoso.com. Essa abordagem é recomendada se você pretende adicionar domínios personalizados adicionais para o mesmo domínio raiz.

- Você pode aprovar apenas o nome do host específico usado nesta solicitação. Uma aprovação adicional é necessária para as solicitações posteriores.

Após a aprovação, o DigiCert conclui a criação do certificado para seu nome de domínio personalizado. O certificado é válido por um ano e será renovado automaticamente antes de expirar.

## <a name="wait-for-propagation"></a>Aguardar a propagação

Depois da validação do nome de domínio, é necessário de 6 a 8 horas para a ativação do recurso HTTPS de domínio personalizado. Quando o processo for concluído, o status HTTPS Personalizado no portal do Azure será definido como **Habilitado** e as quatro etapas da operação da caixa de diálogo do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado agora está pronto para usar o HTTPS.

![Habilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você habilita o HTTPS. Depois de habilitar o HTTPS, quatro etapas de operação são exibidas na caixa de diálogo do domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais da subetapa são exibidos na etapa conforme ela avança. Nem todas essas subetapas ocorrerão. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Etapa da operação | Detalhes da subetapa da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| | Sua solicitação HTTPS está sendo enviada. |
| | Sua solicitação HTTPS foi enviada com êxito. |
| 2 Validação de domínio | O domínio é validado automaticamente se CNAME for mapeado para o ponto de extremidade da CDN. Caso contrário, uma solicitação de verificação será enviada ao email listado no registro do seu domínio (WHOIS inscrito). Verifique o domínio assim que possível. |
| | Sua propriedade do domínio foi validada com êxito. |
| | A solicitação de validação da propriedade do domínio expirou (provavelmente, o cliente não respondeu dentro de 6 dias). O HTTPS não será habilitado no domínio. * |
| | A solicitação de validação da propriedade do domínio foi rejeitada pelo cliente. O HTTPS não será habilitado no domínio. * |
| 3 Provisionamento de certificado | No momento, a autoridade de certificação está emitindo o certificado necessário para habilitar o HTTPS em seu domínio. |
| | O certificado foi emitido e está sendo implantado na rede CDN. Isso pode levar até 6 horas. |
| | O certificado foi implantado com êxito na rede CDN. |
| 4 Concluído | O HTTPS foi habilitado com êxito em seu domínio. |

\* Essa mensagem não é exibida, a menos que tenha ocorrido um erro. 

Se ocorrer um erro antes que a solicitação seja enviada, a seguinte mensagem de erro será exibida:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Limpar recursos - desabilitar HTTPS

Nas etapas anteriores, você habilitou o protocolo HTTPS em seu domínio personalizado. Se você não deseja mais usar seu domínio personalizado com HTTPS, você pode desabilitar o HTTPS, executando as seguintes etapas:

### <a name="disable-the-https-feature"></a>Desabilitar o recurso HTTPS 

1. No [Portal do Azure](https://portal.azure.com), navegue até o seu perfil **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.

2. Na lista de pontos de extremidade, clique no ponto de extremidade que contém seu domínio personalizado.

3. Clique no domínio personalizado no qual você deseja desabilitar o HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Clique em **Desativado** para desabilitar o HTTPS e, em seguida, clique em **Aplicar**.

    ![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Aguardar a propagação

Depois que o recurso HTTPS do domínio personalizado for desabilitado, poderá levar até 6-8 horas para que ele entre em vigor. Quando o processo for concluído, o status HTTPS Personalizado no portal do Azure será definido como **Desabilitado** e as três etapas da operação da caixa de diálogo do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado não pode mais usar o HTTPS.

![Desabilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você desabilita o HTTPS. Depois de desabilitar o HTTPS, três etapas de operação são exibidas na caixa de diálogo Domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais são exibidos na etapa. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Andamento da operação | Detalhes da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| 2 Desprovisionamento de certificado | Excluindo o certificado |
| 3 Concluído | Certificado excluído |

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. *Quem é o provedor de certificado e que tipo de certificado é usado?*

    Para a **CDN do Azure da Verizon** e **CDN do Azure da Microsoft**, um certificado dedicado/único fornecido pela Digicert é usado para seu domínio personalizado. 

2. *Você usa TLS/SSL de SNI ou baseado em IP?*

    A **CDN do Azure da Verizon** e a **CDN Standard do Azure da Microsoft** usam TLS/SSL de SNI.

3. *E se eu não receber o email de verificação de domínio do DigiCert?*

    Se você tiver uma entrada CNAME para seu domínio personalizado que aponte diretamente para seu nome do host do ponto de extremidade (e você não estiver usando o nome de subdomínio cdnverify), você não receberá um email de verificação de domínio. A validação ocorre automaticamente. Caso contrário, se você não tem uma entrada CNAME e você ainda não recebeu um email em até 24 horas, entre em contato com o suporte da Microsoft.

4. *Usar um certificado SAN é menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue os mesmos padrões de criptografia e segurança de um certificado dedicado. Todos os certificados SSL emitidos usam SHA-256 para uma maior segurança do servidor.

5. *É necessário ter um registro de Autorização de Autoridade de Certificação em meu provedor DNS?*

    Não, um registro de Autorização de Autoridade de Certificação não é necessário no momento. No entanto, caso você tenha um, ele deverá incluir o DigiCert como uma AC válida.

6. *Em 20 de junho de 2018, a CDN do Azure da Verizon passou a usar um certificado dedicado com o SNI TLS/SSL por padrão. O que acontece com meus domínios personalizados existentes usando o certificado SAN (Nomes alternativos de entidade) e TLS/SSL baseado em IP?*

    Os domínios existentes migrarão gradualmente para um único certificado nos próximos meses, se a Microsoft analisar que somente as solicitações do cliente de SNI serão feitas ao seu aplicativo. Se a Microsoft detectar solicitações de cliente que não são de SNI feitas em seu aplicativo, seus domínios permanecerão no certificado SAN com TLS/SSL baseado em IP. Em qualquer caso, não haverá interrupção ao seu serviço ou suporte às suas solicitações de cliente, independentemente se essas solicitações forem de SNI ou não.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> - Habilite o protocolo HTTPS em seu domínio personalizado.
> - Usar um certificado gerenciado por CDN 
> - Usar o seu próprio certificado
> - Valide o domínio.
> - Desabilite o protocolo HTTPS em seu domínio personalizado.

Avance para o próximo tutorial para aprender como configurar a colocação de cache em um domínio personalizado da CDN.

> [!div class="nextstepaction"]
> [Tutorial: definir regras de cache da CDN do Azure](cdn-caching-rules-tutorial.md)

