---
title: Tutorial – Configurar HTTPS em um domínio personalizado para o Azure Front Door Service | Microsoft Docs
description: Neste tutorial, você aprenderá como habilitar e desabilitar o HTTPS na sua configuração do Azure Front Door Service para um domínio personalizado.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: sharadag
ms.openlocfilehash: 48733a8c2a554fc62c7731b6c0fb4ef5b8d45159
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450185"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Tutorial: Configurar HTTPS em um domínio personalizado do Front Door

Este tutorial mostra como habilitar o protocolo HTTPS para um domínio personalizado que está associado ao seu Front Door na seção hosts de front-end. Usando o protocolo HTTPS em seu domínio personalizado (por exemplo, https:\//www.contoso.com), você garante que seus dados confidenciais são fornecidos com segurança por meio da criptografia TLS/SSL quando ele são enviada pela Internet. Quando seu navegador da Web é conectado a um site da Web via HTTPS, ele valida o certificado de segurança do site da Web e verifica se ele é emitido por uma autoridade de certificação legítima. Esse processo oferece segurança e protege seus aplicativos Web contra ataques.

O Azure Front Door Service dá suporte a HTTPS em um nome do host padrão do Front Door, por padrão. Por exemplo, se você criar um Front Door (como https:\//contoso.azurefd.net), o HTTPS será habilitado automaticamente para solicitações feitas a https://contoso.azurefd.net. No entanto, depois de integrar o domínio personalizado 'www.contoso.com', será necessário habilitar adicionalmente o HTTPS para esse host de front-end.   

Alguns dos principais atributos do recurso HTTPS são:

- Sem custo adicional: não há nenhum custo para a aquisição ou renovação do certificado e nenhum custo para tráfego HTTPS. 

- Habilitação simples: o provisionamento com um clique está disponível no [portal do Azure](https://portal.azure.com). Você também pode usar a API REST ou outras ferramentas de desenvolvedor para habilitar o recurso.

- Gerenciamento de certificado completo disponível: todos os certificados de aquisição e gerenciamento são manipulados para você. Os certificados são provisionados e renovados automaticamente antes da expiração, o que remove os riscos de interrupção do serviço devido à expiração de um certificado.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Habilite o protocolo HTTPS em seu domínio personalizado.
> - Usar um certificado gerenciado por AFD 
> - Usar seu próprio certificado, ou seja, um certificado SSL personalizado
> - Validar o domínio
> - Desabilitar o protocolo HTTPS em seu domínio personalizado


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir as etapas deste tutorial, crie primeiro um Front Door e integre pelo menos um domínio personalizado. Para obter mais informações, confira [Tutorial: Adicionar um domínio personalizado ao seu Front Door](front-door-custom-domain.md).

## <a name="ssl-certificates"></a>Certificados SSL

Para habilitar o protocolo HTTPS para fornecer com segurança o conteúdo em um domínio personalizado do Front Door, você deve usar um certificado SSL. Você pode optar por usar um certificado gerenciado pelo Azure Front Door Service ou usar seu próprio certificado.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Opção 1 (padrão): usar um certificado gerenciado pelo Front Door

Quando você usa um certificado gerenciado pelo Azure Front Door Service, o recurso HTTPS pode ser ativado com apenas alguns cliques. O Azure Front Door Service lida completamente com tarefas de gerenciamento de certificado, como aquisição e renovação. Depois que você habilitar o recurso, o processo será iniciado imediatamente. Se o domínio personalizado já estiver mapeado para o host de front-end padrão do Front Door (`{hostname}.azurefd.net`), nenhuma ação adicional será necessária. O Front Door processará as etapas e concluirá a solicitação automaticamente. No entanto, se o domínio personalizado for mapeado em outro lugar, você deverá usar o email para validar sua propriedade de domínio.

Para habilitar HTTPS em um domínio personalizado, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), navegue até seu perfil do **Front Door**.

2. Na lista de hosts de front-end, selecione o domínio personalizado para o qual deseja habilitar o HTTPS, contendo seu domínio personalizado.

3. Na seção **HTTPS do domínio personalizado**, clique em **Habilitado** e selecione **gerenciado por Front Door** como a origem do certificado.

4. Clique em Salvar.

5. Prossiga para [Validar o domínio](#validate-the-domain).


### <a name="option-2-use-your-own-certificate"></a>Opção 2: Usar o seu próprio certificado

Você pode usar seu próprio certificado para habilitar o recurs HTTPS. Esse processo é feito por meio de uma integração com o Azure Key Vault, que permite que você armazene os certificados com segurança. O Azure Front Door Service usa esse mecanismo seguro para obter o certificado e requer algumas etapas adicionais. Quando você cria o certificado SSL, você deve criá-lo com uma autoridade de certificação (CA) com permissão. Caso contrário, se você usar uma autoridade de certificação sem permissão, sua solicitação será rejeitada. Para obter uma lista das autoridades de certificação permitidas, consulte [Autoridades de certificação permitidas para habilitar HTTPS personalizado no Azure Front Door Service](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparar a conta e o certificado do Azure Key Vault
 
1. Azure Key Vault: é necessário ter uma conta do Azure Key Vault em execução na mesma assinatura que o Front Door para o qual você deseja habilitar o HTTPS personalizado. Crie uma conta do Azure Key Vault caso não tenha uma.

> [!WARNING]
> No momento, o Azure Front Door Service oferece suporte apenas a contas do Key Vault na mesma assinatura que a configuração do Front Door. Se você escolher um Key Vault com uma assinatura diferente do Front Door, ocorrerá uma falha.

2. Certificados do Azure Key Vault: se já tiver um certificado, poderá carregá-lo diretamente na sua conta do Azure Key Vault ou criar um certificado diretamente pelo Azure Key Vault por meio de uma das CAs de parceiros às quais o Azure Key Vault se integra.

#### <a name="register-azure-front-door-service"></a>Registrar o Azure Front Door Service

Registre a entidade de serviço para o Azure Front Door Service como aplicativo no seu Azure Active Directory, via PowerShell.

1. Se for necessário, instale o [Azure PowerShell](/powershell/azure/install-az-ps) no PowerShell em seu computador local.

2. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-service-access-to-your-key-vault"></a>Conceder acesso ao seu cofre de chaves para o Azure Front Door Service
 
Conceda permissão ao Azure Front Door Service para acessar os certificados em Segredos na sua conta do Azure Key Vault.

1. Em sua conta do Key Vault, em CONFIGURAÇÕES, selecione **Políticas de acesso** e depois **Adicionar novo** para criar uma nova política.

2. Em **Selecionar entidade de segurança**, pesquise por **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** e escolha **Microsoft.Azure.Frontdoor**. Clique em **Selecionar**.


3. Em **Permissões do segredo**, selecione **Obter** para permitir que o Front Door execute essas permissões para obter e listar os certificados. 

4. Selecione **OK**. 

    Agora, o Azure Front Door Service pode acessar este cofre de chaves e os certificados (segredos) que estão armazenados nele.
 
#### <a name="select-the-certificate-for-azure-front-door-service-to-deploy"></a>Selecionar o certificado para o Azure Front Door Service implantar
 
1. Retorne ao seu Front Door no portal. 

2. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    A página de **Domínio personalizado** é exibida.

3. Em Tipo de gerenciamento de certificado, selecione **Usar meu próprio certificado**. 

4. O Azure Front Door Service exige que a assinatura da conta do Key Vault seja igual à do Front Door. Selecione um cofre de chaves, o certificado (segredo) e a versão do certificado.

    O Azure Front Door Service contém as informações a seguir: 
    - As contas do Key Vault para sua ID de assinatura. 
    - Os certificados (segredos) no cofre de chaves selecionado. 
    - As versões de certificado disponíveis. 
 
5. Quando você usa seu próprio certificado, a validação de domínio não é necessária. Avance para a [Aguardar a propagação](#wait-for-propagation).

## <a name="validate-the-domain"></a>Validar o domínio

Se você já tiver um domínio personalizado em uso que é mapeado para o ponto de extremidade personalizado com um registro CNAME, ou se estiver usando seu próprio certificado, vá para  
[O domínio personalizado está mapeado para o Front Door](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Caso contrário, se a entrada de registro CNAME para o domínio não existir mais ou contiver o subdomínio afdverify, vá para [Domínio personalizado não está mapeado para o Front Door](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>O domínio personalizado está mapeado para o Front Door por registro CNAME

Quando adiciona um domínio personalizado aos hosts de front-end do Front Door, você cria um registro CNAME na tabela DNS do seu registrador de domínios para mapeá-lo para o nome do host .azurefd.net padrão do Front Door. Se esse registro CNAME ainda existir e não contiver o subdomínio afdverify, a autoridade de certificação DigiCert vai utilizá-lo para validar automaticamente a propriedade do seu domínio personalizado. 

Se você estiver usando seu próprio certificado, a validação de domínio não será necessária.

O registro CNAME deve estar no formato a seguir, em que *Nome* é o nome de domínio personalizado e *Valor* é o nome de host .azurefd.net padrão do Front Door:

| NOME            | Tipo  | Valor                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Para obter mais informações sobre os registros CNAME, consulte [criar o registro de DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Se o registro CNAME estiver no formato correto, o DigiCert verificará automaticamente seu nome de domínio personalizado e criará um certificado dedicado ao seu nome de domínio. O DigitCert não enviará um email de verificação, e você não precisará aprovar sua solicitação. O certificado é válido por um ano e será renovado automaticamente antes de expirar. Avance para a [Aguardar a propagação](#wait-for-propagation). 

A validação automática geralmente demora alguns minutos. Se você não vir seu domínio validado em uma hora, abra um tíquete de suporte.

>[!NOTE]
>Caso você tenha um registro de CAA (Autorização de Autoridade de Certificação) com o provedor DNS, ele deverá incluir o DigiCert como uma AC válida. Um registro de CAA permite que os proprietários do domínio especifiquem com seus provedores DNS quais ACs estão autorizadas a emitir certificados para seus domínios. Se uma AC receber um pedido de um certificado para um domínio que tem um registro de CAA e a AC não estiver listada como um emissor autorizado, ela será proibida de emitir o certificado para esse domínio ou subdomínio. Para obter informações sobre como gerenciar registros CAA, consulte [Gerenciar registros CAA](https://support.dnsimple.com/articles/manage-caa-record/). Para uma ferramenta de registro CAA, consulte [Ajuda do registro CAA](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>O domínio personalizado não está mapeado para o Front Door

Se a entrada do registro CNAME do ponto de extremidade não existir mais ou contiver o subdomínio afdverify, siga o restante das instruções nesta etapa.

Depois de habilitar o HTTPS em seu domínio personalizado, a autoridade de certificação (CA) do DigiCert valida a propriedade de seu domínio entrando em contato com o inscrito, de acordo com as informações do inscrito [WHOIS](http://whois.domaintools.com/) do domínio. O contato é feito pelo endereço de email (por padrão) ou pelo número de telefone listado no registro WHOIS. Conclua a validação completa de domínio antes de ativar o HTTPS no seu domínio personalizado. Você tem seis dias úteis para aprovar o domínio. As solicitações que não foram aprovadas em até seis dias úteis são canceladas automaticamente. 

![Registro WHOIS](./media/front-door-custom-domain-https/whois-record.png)

O DigiCert também envia um email de verificação para endereços de email adicionais. Se as informações do inscrito WHOIS forem particulares, verifique se é possível aprovar diretamente por meio de um dos seguintes endereços:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

Você deverá receber um email em poucos minutos, semelhante ao exemplo a seguir, solicitando que você aprove a solicitação. Se estiver usando um filtro de spam, adicione admin@digicert.com à sua lista de permissões. Se você não receber um email em até 24 horas, contate o suporte da Microsoft.

Quando você clicar no link de aprovação, será direcionado para um formulário de aprovação online. Siga as instruções do formulário; você tem duas opções de verificação:

- Você pode aprovar todos os pedidos futuros feitos por meio da mesma conta para o mesmo domínio raiz; por exemplo, contoso.com. Essa abordagem é recomendada se você pretende adicionar domínios personalizados adicionais para o mesmo domínio raiz.

- Você pode aprovar apenas o nome do host específico usado nesta solicitação. Uma aprovação adicional é necessária para as solicitações posteriores.

Após a aprovação, o DigiCert conclui a criação do certificado para seu nome de domínio personalizado. O certificado é válido por um ano e será renovado automaticamente antes de expirar.

## <a name="wait-for-propagation"></a>Aguardar a propagação

Depois da validação do nome de domínio, é necessário de 6 a 8 horas para a ativação do recurso HTTPS de domínio personalizado. Quando o processo for concluído, o status HTTPS Personalizado no portal do Azure será definido como **Habilitado** e as quatro etapas da operação da caixa de diálogo do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado agora está pronto para usar o HTTPS.

### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você habilita o HTTPS. Depois de habilitar o HTTPS, quatro etapas de operação são exibidas na caixa de diálogo do domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais da subetapa são exibidos na etapa conforme ela avança. Nem todas essas subetapas ocorrerão. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Etapa da operação | Detalhes da subetapa da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| | Sua solicitação HTTPS está sendo enviada. |
| | Sua solicitação HTTPS foi enviada com êxito. |
| 2 Validação de domínio | O domínio será validado automaticamente se o CNAME estiver mapeado para o host de front-end .azurefd.net padrão do Front Door. Caso contrário, uma solicitação de verificação será enviada ao email listado no registro do seu domínio (WHOIS inscrito). Verifique o domínio assim que possível. |
| | Sua propriedade do domínio foi validada com êxito. |
| | A solicitação de validação da propriedade do domínio expirou (provavelmente, o cliente não respondeu dentro de 6 dias). O HTTPS não será habilitado no domínio. * |
| | A solicitação de validação da propriedade do domínio foi rejeitada pelo cliente. O HTTPS não será habilitado no domínio. * |
| 3 Provisionamento de certificado | No momento, a autoridade de certificação está emitindo o certificado necessário para habilitar o HTTPS em seu domínio. |
| | O certificado foi emitido e está sendo implantado para o Front Door. Esse processo poderia demorar até 1 hora. |
| | O certificado foi implantado com êxito no Front Door. |
| 4 Concluído | O HTTPS foi habilitado com êxito em seu domínio. |

\* Essa mensagem não é exibida, a menos que tenha ocorrido um erro. 

Se ocorrer um erro antes que a solicitação seja enviada, a seguinte mensagem de erro será exibida:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Limpar recursos - desabilitar HTTPS

Nas etapas anteriores, você habilitou o protocolo HTTPS em seu domínio personalizado. Se você não deseja mais usar seu domínio personalizado com HTTPS, você pode desabilitar o HTTPS, executando as seguintes etapas:

### <a name="disable-the-https-feature"></a>Desabilitar o recurso HTTPS 

1. No [portal do Azure](https://portal.azure.com), navegue até a configuração do **Azure Front Door Service**.

2. Na lista de hosts de front-end, clique no domínio personalizado no qual deseja desabilitar o HTTPS.

3. Clique em **Desabilitado** para desabilitar o HTTPS e, em seguida, clique em **Salvar**.

### <a name="wait-for-propagation"></a>Aguardar a propagação

Depois que o recurso HTTPS do domínio personalizado for desabilitado, poderá levar até 6-8 horas para que ele entre em vigor. Quando o processo for concluído, o status HTTPS Personalizado no portal do Azure será definido como **Desabilitado** e as três etapas da operação da caixa de diálogo do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado não pode mais usar o HTTPS.

#### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você desabilita o HTTPS. Depois de desabilitar o HTTPS, três etapas de operação são exibidas na caixa de diálogo Domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais são exibidos na etapa. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Andamento da operação | Detalhes da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| 2 Desprovisionamento de certificado | Excluindo o certificado |
| 3 Concluído | Certificado excluído |

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. *Quem é o provedor de certificado e que tipo de certificado é usado?*

    Um certificado dedicado/único, fornecido pela DigiCert, é usado para seu domínio personalizado. 

2. *Você usa TLS/SSL de SNI ou baseado em IP?*

    O Azure Front Door Service usa TLS/SSL de SNI.

3. *E se eu não receber o email de verificação de domínio do DigiCert?*

    Se você tiver uma entrada CNAME para seu domínio personalizado que aponte diretamente para o nome do host do ponto de extremidade (e não estiver usando o nome de subdomínio afdverify), você não receberá um email de verificação de domínio. A validação ocorre automaticamente. Caso contrário, se você não tem uma entrada CNAME e você ainda não recebeu um email em até 24 horas, entre em contato com o suporte da Microsoft.

4. *Usar um certificado SAN é menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue os mesmos padrões de criptografia e segurança de um certificado dedicado. Todos os certificados SSL emitidos usam SHA-256 para uma maior segurança do servidor.

5. *É necessário ter um registro de Autorização de Autoridade de Certificação em meu provedor DNS?*

    Não, um registro de Autorização de Autoridade de Certificação não é necessário no momento. No entanto, caso você tenha um, ele deverá incluir o DigiCert como uma AC válida.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
