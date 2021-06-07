---
title: 'Tutorial: Configurar HTTPS em um domínio personalizado da CDN do Azure'
description: Neste tutorial, você aprenderá como habilitar ou desabilitar o HTTPS no ponto de extremidade da CDN do Azure com um domínio personalizado.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 6f77bac93b7bb5e3319409c01e328c73cd08a9a0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058945"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Configurar HTTPS em um domínio personalizado da CDN do Azure

Este tutorial mostra como habilitar o protocolo HTTPS para um domínio personalizado que está associado um ponto de extremidade da CDN do Azure. 

O protocolo HTTPS em seu domínio personalizado (por exemplo, https:\//www.contoso.com) garante que seus dados confidenciais sejam entregues com segurança via TLS/SSL. Quando o navegador da Web estiver conectado via HTTPS, o navegador validará o certificado do site. O navegador verifica se ele é emitido por uma autoridade de certificação legítima. Esse processo oferece segurança e protege seus aplicativos Web contra ataques.

A CDN do Azure dá suporte a HTTPS em um nome do host do ponto de extremidade da CDN por padrão. Por exemplo, se você criar um ponto de extremidade da CDN (como https:\//contoso.azureedge.net), o HTTPS será habilitado automaticamente.  

Alguns dos principais atributos do recurso HTTPS são:

- Sem custo adicional: Não há nenhum custo para a aquisição ou renovação do certificado e nenhum custo para tráfego HTTPS. Você paga apenas pelo GB de saída da CDN.

- Habilitação simples: o provisionamento com um clique está disponível no [portal do Azure](https://portal.azure.com). Você também pode usar a API REST ou outras ferramentas de desenvolvedor para habilitar o recurso.

- Gerenciamento de certificado completo disponível: 
    * todos os certificados de aquisição e gerenciamento são manipulados para você. 
    * Certificados são automaticamente provisionados e renovados antes da expiração.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Habilite o protocolo HTTPS em seu domínio personalizado.
> - Usar um certificado gerenciado por CDN 
> - Usar o seu próprio certificado
> - Validar o domínio
> - Desabilite o protocolo HTTPS em seu domínio personalizado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Para concluir as etapas deste tutorial, crie um perfil CDN e no mínimo um ponto de extremidade CDN. Para saber mais, confira [Início Rápido: Criar um ponto de extremidade e um perfil de CDN do Azure](cdn-create-new-endpoint.md).

Associe um domínio personalizado da CDN do Azure no ponto de extremidade da CDN. Para saber mais, confira [Tutorial: Adicionar um domínio personalizado ao ponto de extremidade da CDN do Azure](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> Os certificados gerenciados pela CDN não estão disponíveis para os domínios raiz ou apex. Se seu domínio personalizado da CDN do Azure for um domínio raiz ou apex, você deverá usar o recurso Traga seu próprio certificado. 
>

---

## <a name="tlsssl-certificates"></a>Certificados TLS/SSL

Para habilitar o HTTPS em um domínio personalizado da CDN do Azure, use um certificado TLS/SSL. Você pode optar por usar um certificado que é gerenciado pela CDN do Azure ou usar um certificado próprio.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opção 1 (padrão): habilitar HTTPS com um certificado gerenciado por CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

A CDN do Azure lida com tarefas de gerenciamento de certificado, como aquisição e renovação. Depois que você habilitar o recurso, o processo será iniciado imediatamente. 

Se o domínio personalizado já estiver mapeado para o ponto de extremidade de CDN, nenhuma ação adicional será necessária. A CDN do Azure processará as etapas e concluirá a solicitação automaticamente.

Se o domínio personalizado for mapeado em outro lugar, use o email para validar sua propriedade de domínio.

Para habilitar HTTPS em um domínio personalizado, siga estas etapas:

1. Vá para o [portal do Azure](https://portal.azure.com) para localizar um certificado gerenciado pela CDN do Azure. Pesquise e selecione **perfis de CDN**. 

2. Escolha o seu perfil:
    * **CDN Standard do Azure da Microsoft**
    * **CDN Standard do Azure da Akamai**
    * **CDN Standard do Azure da Verizon**
    * **CDN Premium do Azure da Verizon**

3. Na lista de pontos de extremidade da CDN, selecione o ponto de extremidade que contém seu domínio personalizado.

    ![Lista de pontos de extremidade](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    A página **Ponto de extremidade** é exibida.

4. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    ![A captura de tela mostra a página Domínio personalizado com a opção Usar meu certificado.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    A página de **Domínio personalizado** é exibida.

5. Em Tipo de gerenciamento de certificado, selecione **CDN gerenciado**.

6. Selecione **Ativado** para habilitar HTTPS.

    ![Status de HTTPS de domínio personalizado](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Prossiga para [Validar o domínio](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Opção 2: habilitar HTTPS com seu próprio certificado](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Este recurso está disponível apenas com os perfis de **CDN do Azure da Microsoft** e de **CDN do Azure do Verizon**. 
>
 
Você pode usar seu próprio certificado para habilitar o recurs HTTPS. Esse processo é feito por meio de uma integração com o Azure Key Vault, que permite que você armazene os certificados com segurança. A CDN do Azure usa esse mecanismo seguro para obter o certificado e requer algumas etapas adicionais. Quando você cria o certificado TLS/SSL, você deve criá-lo com uma autoridade de certificação (AC) com permissão. Caso contrário, se você usar uma autoridade de certificação sem permissão, sua solicitação será rejeitada. Para obter uma lista das autoridades de certificação permitidas, consulte [Autoridades de certificação permitidas para habilitar HTTPS personalizado na CDN do Azure](cdn-troubleshoot-allowed-ca.md). Para a **CDN do Azure da Verizon**, qualquer AC válida será aceita. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparar a conta e o certificado do Azure Key Vault
 
1. Azure Key Vault: é necessário ter uma conta do Azure Key Vault em execução sob a mesma assinatura que o perfil da CDN do Azure e os pontos de extremidade da CDN desejados para habilitar o HTTPS personalizado. Crie uma conta do Azure Key Vault caso você não tenha uma.
 
2. Certificados do Azure Key Vault: Se você tiver um certificado, carregue-o diretamente em sua conta de Azure Key Vault. Se você não tiver um certificado, crie um diretamente por meio do Azure Key Vault.

### <a name="register-azure-cdn"></a>Registrar a CDN do Azure

Registre a CDN do Azure como um aplicativo em seu Azure Active Directory por meio do PowerShell.

1. Se for necessário, instale o [Azure PowerShell](/powershell/azure/install-az-ps) em seu computador local.

2. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** é a entidade de serviço para **Microsoft.AzureFrontDoor-Cdn**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Conceder à CDN do Azure acesso ao seu cofre de chaves
 
Conceda à CDN do Azure permissão para acessar os certificados (segredos) em sua conta do Azure Key Vault.

1. No cofre de chaves na seção **Configurações**, selecione **Políticas de acesso**. No painel direito, selecione **+ Adicionar Política de Acesso**:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Criar uma política de acesso ao cofre de chaves para a CDN" border="true":::

2. Na página **Adicionar política de acesso**, selecione **Nenhum selecionado** ao lado de **Selecionar entidade de segurança**. Na página **Entidade de segurança**, insira **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**. Selecione **Microsoft.AzureFrontdoor-Cdn**.  Escolha **Selecionar**:

2. Em **Selecionar entidade de segurança**, pesquise por **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** e escolha **Microsoft.AzureFrontDoor-Cdn**. Escolha **Selecionar**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Selecionar a entidade de serviço da CDN do Azure" border="true":::
    
3. Selecione **Permissões de certificado**. Selecione as caixas de seleção para **Obter** e **Listar** para conceder à CDN as permissões para obter e listar os certificados.

4. Selecione **Permissões secretas**. Selecione as caixas de seleção para **Obter** e **Listar** para conceder à CDN as permissões para obter e listar os segredos:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Selecionar permissões para a CDN ao cofre de chaves" border="true":::

5. Selecione **Adicionar**. 

> [!NOTE]
> A CDN do Azure pode acessar este cofre de chaves e os certificados (segredos) que estão armazenados nesse cofre. Qualquer instância CDN criada nessa assinatura terá acesso aos certificados nesse cofre de chaves. 

 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Selecionar o certificado para a CDN do Azure implantar
 
1. Retorne ao portal da CDN do Azure e selecione o perfil e o ponto de extremidade de CDN desejados para habilitar o HTTPS personalizado. 

2. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    A página de **Domínio personalizado** é exibida.

3. Em Tipo de gerenciamento de certificado, selecione **Usar meu próprio certificado**. 

    :::image type="content" source="./media/cdn-custom-ssl/cdn-configure-your-certificate.png" alt-text="Captura de tela de como configurar o certificado para o ponto de extremidade da CDN.":::

4. Selecione um cofre de chaves, um certificado/um segredo e a versão do certificado/do segredo.

    A CDN do Azure lista as seguintes informações: 
    - As contas do Key Vault para sua ID de assinatura. 
    - Os certificados/os segredos no cofre de chaves selecionado. 
    - As versões disponíveis do certificado/do segredo.
 
    > [!NOTE]
    > Para que o certificado seja alternado automaticamente para a última versão quando uma versão mais recente do certificado estiver disponível no Key Vault, defina a versão do certificado/do segredo como 'Última'. Se uma versão específica for selecionada, você precisará selecionar novamente a nova versão manualmente para a rotação do certificado. Leva até 24 horas para que a nova versão do certificado/do segredo seja implantada. 
   
5. Selecione **Ativado** para habilitar HTTPS.
  
6. Quando você usa seu certificado, a validação de domínio não é necessária. Prossiga para [Aguardar a propagação](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Validar o domínio

Se você já tiver um domínio personalizado em uso que é mapeado para o ponto de extremidade personalizado com um registro CNAME ou se estiver usando um certificado próprio, prossiga para [Domínio personalizado mapeado para o ponto de extremidade da CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). 

Caso contrário, se a entrada de registro CNAME para o ponto de extremidade não existe ou contém o subdomínio cdnverify, prossiga para [Domínio personalizado não mapeado para o ponto de extremidade da CDN](#custom-domain-isnt-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>O domínio personalizado é mapeado para o ponto de extremidade da CDN por meio de um registro CNAME

Quando tiver adicionado um domínio personalizado ao seu ponto de extremidade, você criou um registro CNAME no registrador de domínios DNS mapeado para o nome de host do ponto de extremidade da CDN. 

Se esse registro CNAME ainda existir e não contiver o subdomínio cdnverify, a AC (autoridade de certificação) DigiCert o usará para validar automaticamente a propriedade do seu domínio personalizado. 

Se você estiver usando um certificado próprio, a validação de domínio não será necessária.

O registro CNAME deve estar no seguinte formato:

* *Nome* é o nome de domínio personalizado.
* *Valor* é o nome do host do ponto de extremidade da CDN.

| Nome            | Tipo  | Valor                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Para obter mais informações sobre os registros CNAME, consulte [criar o registro de DNS CNAME](./cdn-map-content-to-custom-domain.md).

Se o registro CNAME estiver no formato correto, o DigiCert verificará automaticamente seu nome de domínio personalizado e criará um certificado para o seu domínio. O DigitCert não enviará um email de verificação, e você não precisará aprovar sua solicitação. O certificado é válido por um ano e será renovado automaticamente antes de expirar. Prossiga para [Aguardar a propagação](#wait-for-propagation). 

A validação automática geralmente demora algumas horas. Se o domínio não for validado em 24 horas, abra um tíquete de suporte.

>[!NOTE]
>Caso você tenha um registro de CAA (Autorização de Autoridade de Certificação) com o provedor DNS, ele deverá incluir o DigiCert como uma AC válida. Um registro de CAA permite que os proprietários do domínio especifiquem com seus provedores DNS quais ACs estão autorizadas a emitir certificados para seus domínios. Se uma AC receber um pedido de um certificado para um domínio que tem um registro de CAA e a AC não estiver listada como um emissor autorizado, ela será proibida de emitir o certificado para esse domínio ou subdomínio. Para obter informações sobre como gerenciar registros CAA, consulte [Gerenciar registros CAA](https://support.dnsimple.com/articles/manage-caa-record/). Para uma ferramenta de registro CAA, consulte [Ajuda do registro CAA](https://sslmate.com/caa/).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>O domínio personalizado não é mapeado para o ponto de extremidade da CDN

>[!NOTE]
>Se você estiver usando a **CDN do Azure do Akamai**, o CNAME a seguir deverá ser configurado para habilitar a validação de domínio automatizada. "_acme-challenge.&lt;nome de host do domínio personalizado&gt; -> CNAME -> &lt;nome de host do domínio personalizado&gt;.ak-acme-challenge.azureedge.net"

Se a entrada do registro CNAME do ponto de extremidade não existir mais ou contiver o subdomínio cdnverify, siga o restante das instruções nesta etapa.

O DigiCert envia um email de verificação para os seguintes endereços de email. Verifique se é possível aprovar diretamente por meio de um dos seguintes endereços:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Você deverá receber um email em poucos minutos solicitando que você aprove a solicitação. Caso você esteja usando um filtro de spam, adicione verification@digicert.com à lista de permitidos. Se você não receber um email em até 24 horas, contate o suporte da Microsoft.
    
![Email de validação de domínio](./media/cdn-custom-ssl/domain-validation-email.png)

Quando você selecionar o link de aprovação, será direcionado para o seguinte formulário de aprovação online: 
    
![Formulário de validação de domínio](./media/cdn-custom-ssl/domain-validation-form.png)

Siga as instruções do formulário; você tem duas opções de verificação:

- Você pode aprovar todos os pedidos futuros feitos por meio da mesma conta para o mesmo domínio raiz; por exemplo, contoso.com. Essa abordagem é recomendada se você pretende adicionar domínios personalizados adicionais para o mesmo domínio raiz.

- Você pode aprovar apenas o nome do host específico usado nesta solicitação. Será necessário obter outra aprovação para solicitações posteriores.

Após a aprovação, o DigiCert conclui a criação do certificado para seu nome de domínio personalizado. O certificado é válido por um ano e será renovado automaticamente antes de expirar.

## <a name="wait-for-propagation"></a>Aguardar a propagação

Depois da validação do nome de domínio, é necessário de 6 a 8 horas para a ativação do recurso HTTPS de domínio personalizado. Depois da conclusão do processo, o status HTTPS personalizado no portal do Azure é definido para **Habilitado**. As quatro etapas de operação na caixa de diálogo de domínio personalizado são marcadas como concluídas. Seu domínio personalizado agora está pronto para usar o HTTPS.

![Habilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você habilita o HTTPS. Depois de habilitar o HTTPS, quatro etapas de operação são exibidas na caixa de diálogo do domínio personalizado. À medida que cada etapa fica ativa, outros detalhes da subetapa são exibidos na etapa conforme ela avança. Nem todas essas subetapas ocorrerão. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Etapa da operação | Detalhes da subetapa da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| | Sua solicitação HTTPS está sendo enviada. |
| | Sua solicitação HTTPS foi enviada com êxito. |
| 2 Validação de domínio | O domínio será validado automaticamente se CNAME for mapeado para o ponto de extremidade da CDN. Caso contrário, uma solicitação de verificação será enviada ao email listado no registro do seu domínio (WHOIS inscrito).|
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

Nesta seção, você aprenderá a desabilitar o HTTPS para o domínio personalizado.

### <a name="disable-the-https-feature"></a>Desabilitar o recurso HTTPS 

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **Perfis de CDN**. 

2. Escolha o seu perfil **CDN do Azure Standard da Microsoft**, **CDN do Azure Standard do Verizon** ou **CDN do Azure Premium da Verizon**.

3. Na lista de pontos de extremidade, escolha o ponto de extremidade que contém seu domínio personalizado.

4. Escolha o domínio personalizado no qual você deseja desabilitar o HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Escolha **Desativado** para desabilitar o HTTPS e, em seguida, selecione **Aplicar**.

    ![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Aguardar a propagação

Depois que o recurso HTTPS do domínio personalizado for desabilitado, poderá levar até 6-8 horas para que ele entre em vigor. Depois da conclusão do processo, o status HTTPS personalizado no portal do Azure é definido para **Desabilitado**. As três etapas de operação na caixa de diálogo de domínio personalizado são marcadas como concluídas. Seu domínio personalizado não pode mais usar o HTTPS.

![Desabilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você desabilita o HTTPS. Depois de desabilitar o HTTPS, três etapas de operação são exibidas na caixa de diálogo de domínio personalizado. Quando uma etapa fica ativa, detalhes são exibidos sob a etapa. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Andamento da operação | Detalhes da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| 2 Desprovisionamento de certificado | Excluindo o certificado |
| 3 Concluído | Certificado excluído |

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. *Quem é o provedor de certificado e que tipo de certificado é usado?*

    Um certificado dedicado, fornecido pela DigiCert, é usado para seu domínio personalizado:
    
    * **CDN do Azure da Verizon**
    * **CDN do Azure da Microsoft**

2. *Você usa TLS/SSL de SNI ou baseado em IP?*

    A **CDN do Azure da Verizon** e a **CDN Standard do Azure da Microsoft** usam TLS/SSL de SNI.

3. *E se eu não receber o email de verificação de domínio do DigiCert?*

    Se você não estiver usando o subdomínio *cdnverify* e a entrada CNAME for para o nome do host do ponto de extremidade, você não receberá nenhum email de verificação de domínio.
     
    A validação ocorre automaticamente. Caso contrário, se você não tem uma entrada CNAME e você ainda não recebeu um email em até 24 horas, entre em contato com o suporte da Microsoft.

4. *Usar um certificado SAN é menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue os mesmos padrões de criptografia e segurança de um certificado dedicado. Todos os certificados TLS/SSL emitidos usam SHA-256 para uma maior segurança do servidor.

5. *É necessário ter um registro de Autorização de Autoridade de Certificação em meu provedor DNS?*

    Nenhum registro de Autorização de Autoridade de Certificação é necessário no momento. No entanto, caso você tenha um, ele deverá incluir o DigiCert como uma AC válida.

6. *Em 20 de junho de 2018, a CDN do Azure da Verizon passou a usar um certificado dedicado com o SNI TLS/SSL por padrão. O que acontece com meus domínios personalizados existentes usando o certificado SAN (Nomes alternativos de entidade) e TLS/SSL baseado em IP?*

    Os domínios existentes migrarão gradualmente para um único certificado nos próximos meses, se a Microsoft analisar que somente as solicitações do cliente de SNI serão feitas ao seu aplicativo. 
    
    Se os clientes não SNI forem detectados, seus domínios permanecerão no certificado SAN com TLS/SSL baseado em IP. As solicitações para seu serviço ou clientes não SNI não são afetadas.

7. *Como funcionam as renovações de certificado com o modelo Traga Seu Próprio Certificado?*

    Para garantir que um certificado mais recente seja implantado na infraestrutura do PoP, carregue seu novo certificado no Azure Key Vault. Em suas configurações de TLS na CDN do Azure, escolha a versão mais recente do certificado e selecione salvar. Em seguida, a CDN do Azure propagará o novo certificado atualizado. 

8. *É necessário reabilitar o HTTPS após a reinicialização do ponto de extremidade?*

    Sim. Se você está usando a **CDN do Azure da Akamai**, se o ponto de extremidade parar e reiniciar, você deve reabilitar a configuração HTTPS se a configuração estava ativa antes.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> - Habilite o protocolo HTTPS em seu domínio personalizado.
> - Usar um certificado gerenciado por CDN 
> - Usar o seu próprio certificado
> - Valide o domínio.
> - Desabilite o protocolo HTTPS em seu domínio personalizado.

Avance para o próximo tutorial para aprender como configurar a colocação de cache em um domínio personalizado da CDN.

> [!div class="nextstepaction"]
> [Tutorial: definir regras de cache da CDN do Azure](cdn-caching-rules-tutorial.md)
