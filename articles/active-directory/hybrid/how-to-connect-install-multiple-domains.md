---
title: Vários domínios do Azure AD Connect
description: Este documento descreve como configurar e configurar vários domínios de nível superior com o Microsoft 365 e o Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53a0da5b5db21c9a543d39d1b252b0b4c64e2a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306354"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Suporte a Vários Domínios para Federação com o Azure AD
A documentação a seguir fornece orientação sobre como usar vários domínios e subdomínios de nível superior ao federar com domínios do Microsoft 365 ou do AD do Azure.

## <a name="multiple-top-level-domain-support"></a>Suporte para vários domínios de nível superior
A federação de vários domínios de nível superior com o Azure AD requer configuração adicional que não é necessária na federação de um único domínio de nível superior.

Quando um domínio é federado com o Azure AD, várias propriedades são definidas no domínio no Azure.  Uma delas é IssuerUri.  Essa propriedade é um URI usado pelo Azure AD para identificar o domínio ao qual o token está associado.  O URI não precisa resolver para nada, mas deve ser um URI válido.  Por padrão, o Azure AD define esse URI como o valor do identificador do serviço de federação em sua configuração local do AD FS.

> [!NOTE]
> O identificador do serviço de federação é um URI que identifica exclusivamente um serviço de federação.  O serviço de federação é uma instância do AD FS que funciona como o serviço de token de segurança.
>
>

Você pode exibir o IssuerUri usando o comando `Get-MsolDomainFederationSettings -DomainName <your domain>` do PowerShell.

![Captura de tela que mostra os resultados depois de inserir o comando "Get-MsolDomainFederationSettings" no PowerShell.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Um problema surge quando você adiciona mais de um domínio de nível superior.  Por exemplo, digamos que você tenha configurado a federação entre o Azure AD e seu ambiente local.  Para este documento, usamos o domínio bmcontoso.com.  Agora, adicionamos um segundo domínio de nível superior, bmfabrikam.com.

![Uma captura de tela mostrando vários domínios de nível superior](./media/how-to-connect-install-multiple-domains/domains.png)

Quando você tenta converter o domínio bmfabrikam.com em federado, recebe um erro.  A razão para isso é que o Azure AD tem uma restrição que não permite que a propriedade IssuerUri tenha o mesmo valor para mais de um domínio.  

![Captura de tela que mostra um erro de Federação no PowerShell.](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parâmetro SupportMultipleDomain
Para solucionar essa restrição, você precisa adicionar um IssuerUri diferente. Isso pode ser feito usando o parâmetro `-SupportMultipleDomain`.  Esse parâmetro é usado com os seguintes cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Esse parâmetro faz o Azure AD configurar o IssuerUri para se basear no nome do domínio.  Esse IssuerUri será exclusivo nos diretórios no Azure AD.  O uso do parâmetro permite que o comando do PowerShell seja concluído com êxito.

![Captura de tela que mostra uma conclusão bem-sucedida do comando do PowerShell.](./media/how-to-connect-install-multiple-domains/convert.png)

Analisando as configurações do domínio bmfabrikam.com, dá para ver o seguinte:

![Captura de tela que mostra as configurações do domínio "bmfabrikam.com".](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` não altera os outros pontos de extremidade que ainda estão configurados para apontar para o serviço de federação em adfs.bmcontoso.com.

O `-SupportMultipleDomain` também garante que o sistema de AD FS inclua o valor de emissor adequado em tokens emitidos para o Azure AD. Esse valor é definido usando a parte do domínio do UPN dos usuários e configurando-o como o domínio no IssuerUri, ou seja, https://{upn suffix}/adfs/services/trust.

Portanto, durante a autenticação no Azure AD ou Microsoft 365, o elemento IssuerUri no token do usuário é usado para localizar o domínio no Azure AD. Se não houver correspondência, a autenticação falhará.

Por exemplo, se o UPN de um usuário for bsimon@bmcontoso.com, o elemento IssuerUri as emissões do AD FS de token será definido como `http://bmcontoso.com/adfs/services/trust`. Esse elemento corresponderá à configuração do Azure AD, e à autenticação será bem-sucedida.

Abaixo vemos a regra de declaração personalizada que implementa essa lógica:

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> Para usar a opção -SupportMultipleDomain quando tentar adicionar um novo domínio ou converter domínios já existentes, você já precisa ter sua confiança federada configurada para dar suporte a eles.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Como atualizar a relação de confiança entre o AD FS e o Azure AD
Se você não tiver configurado a confiança federada entre o AD FS e a instância do Azure AD, será preciso recriá-la.  Isso ocorre porque quando ela é originalmente configurada sem o parâmetro `-SupportMultipleDomain`, o IssuerUri é definido com o valor padrão.  Na captura de tela abaixo, veja o IssuerUri definido como `https://adfs.bmcontoso.com/adfs/services/trust`.

Se você conseguir adicionar um novo domínio no Portal do Azure AD e tentar convertê-lo usando `Convert-MsolDomaintoFederated -DomainName <your domain>`, obterá o seguinte erro.

![Captura de tela que mostra um erro de Federação no PowerShell depois de tentar converter um novo domínio com o comando "Convert-MsolDomaintoFederated".](./media/how-to-connect-install-multiple-domains/trust1.png)

Se você tentar adicionar a opção `-SupportMultipleDomain`, receberá o seguinte erro:

![Captura de tela que mostra um erro de Federação depois de adicionar a opção "-SupportMultipleDomain".](./media/how-to-connect-install-multiple-domains/trust2.png)

A simples tentativa de executar `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` no domínio original também resultará em erro.

![Erro de federação](./media/how-to-connect-install-multiple-domains/trust3.png)

Use as etapas a seguir para adicionar mais um domínio de nível superior.  Se você já tiver adicionado um domínio e não usou o parâmetro `-SupportMultipleDomain`, utilize as etapas para remover e atualizar o domínio original.  Se você ainda não tiver adicionado um domínio de nível superior, poderá começar com as etapas para adicionar um domínio usando o PowerShell do Azure AD Connect.

Use as etapas a seguir para remover a relação de confiança do Microsoft Online e atualizar seu domínio original.

1. No servidor de Federação do AD FS, abra **Gerenciamento do AD FS.**
2. À esquerda, expanda **Relações de Confiança** e **Terceira Parte Confiável**
3. À direita, exclua a entrada **Plataforma de Identidade do Microsoft Office 365** .
   ![Remover o Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Em um computador que tenha o [Módulo do Azure Active Directory para Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) instalado, execute o seguinte: `$cred=Get-Credential`.  
5. Insira o nome de usuário e a senha de um administrador global para o domínio do Azure AD com o qual você está federando.
6. No PowerShell, insira `Connect-MsolService -Credential $cred`
7. No PowerShell, insira `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Essa atualização é para o domínio original.  Usando os domínios acima, seria: `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Use as etapas a seguir para adicionar o novo domínio de nível superior usando o PowerShell

1. Em um computador que tenha o [Módulo do Azure Active Directory para Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)) instalado, execute o seguinte: `$cred=Get-Credential`.  
2. Insira o nome de usuário e a senha de um administrador global para o domínio do Azure AD com o qual você está federando
3. No PowerShell, insira `Connect-MsolService -Credential $cred`
4. No PowerShell, insira `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Use as etapas a seguir para adicionar o novo domínio de nível superior usando o Azure AD Connect.

1. Inicie o Azure AD Connect na área de trabalho ou no menu Iniciar
2. Escolha a captura de tela "adicionar um domínio do Azure AD adicional" ![ que mostra a página "tarefas adicionais" com "adicionar um domínio do Azure ad adicional" selecionado.](./media/how-to-connect-install-multiple-domains/add1.png)
3. Insira as credenciais do Azure AD e do Active Directory
4. Escolha o segundo domínio que você deseja configurar para federação.
   ![Adicionar um domínio do Azure AD](./media/how-to-connect-install-multiple-domains/add2.png)
5. Clique em Instalar

### <a name="verify-the-new-top-level-domain"></a>Verifique o novo domínio de nível superior
Usando o comando `Get-MsolDomainFederationSettings -DomainName <your domain>`do PowerShell, você pode exibir o IssuerUri atualizado.  A captura de tela abaixo mostra que as configurações de federação foram atualizadas no domínio original `http://bmcontoso.com/adfs/services/trust`

![Captura de tela que mostra as configurações de Federação atualizadas no domínio original.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

E o IssuerUri no novo domínio foi sido definido como `https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Suporte para subdomínios
Quando você adiciona um subdomínio, devido à maneira usada pelo Azure AD para tratar domínios, ele herda as configurações do pai.  Por isso, o IssuerUri precisa corresponder aos pais.

Digamos, por exemplo, que eu tenha bmcontoso.com e adicione corp.bmcontoso.com.  O IssuerUri para um usuário de corp.bmcontoso.com precisará ser **`http://bmcontoso.com/adfs/services/trust`** .  No entanto, a regra padrão implementada acima para o Azure AD irá gerar um token com um emissor como **`http://corp.bmcontoso.com/adfs/services/trust`** . , que não corresponderá ao domínio do valor obrigatório e fará com que a autenticação falhe.

### <a name="how-to-enable-support-for-subdomains"></a>Como habilitar o suporte para subdomínios
Para solucionar esse comportamento, a relação de confiança de terceira parte confiável do AD FS do Microsoft Online precisa ser atualizada.  Para isso, você precisa configurar a regra de declaração personalizada para que ela ignore os subdomínios do sufixo UPN do usuário ao construir o valor de Issuer personalizado.

A declaração a seguir fará isso:

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
O último número na expressão regular definida representa quantos domínios pai há em seu domínio raiz. Aqui usamos bmcontoso.com, então são necessários dois domínios pai. Se você tiver três domínios pai (por exemplo: corp.bmcontoso.com), então o número seria três. Eventualmente, pode ser indicado um intervalo. A quantidade máxima sempre deve corresponder ao máximo de domínios. "{2,3}" corresponderá a dois a três domínios (ou seja: bmfabrikam.com e corp.bmcontoso.com).

Use as etapas a seguir para adicionar uma declaração personalizada que dê suporte a subdomínios.

1. Abra o gerenciamento do AD FS
2. Clique com botão direito do mouse na relação de confiança de terceira parte confiável do Microsoft Online e escolha Editar regras de declaração
3. Escolha a terceira regra de declaração e substitua ![Editar declaração](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Substitua a declaração atual:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    por

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![Substituir declaração](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Clique em OK.  Clique em Aplicar.  Clique em OK.  Feche o gerenciamento do AD FS.

## <a name="next-steps"></a>Próximas etapas
Agora que você tem o Azure AD Connect instalado, é possível [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Saiba mais sobre estes recursos, que foram habilitados com a instalação: [Atualização automática](how-to-connect-install-automatic-upgrade.md), [Impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).

Saiba mais sobre estes tópicos comuns: [Agendador e como disparar a sincronização](how-to-connect-sync-feature-scheduler.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).