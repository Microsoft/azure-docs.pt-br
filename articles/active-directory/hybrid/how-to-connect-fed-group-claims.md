---
title: Configurar declarações de grupo para aplicativos com Azure Active Directory | Microsoft Docs
description: Informações sobre como configurar declarações de grupo para uso com o Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: bef5942707c1ded22ba82bdb0d945b9fdb23fffa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349343"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Configurar declarações de grupo para aplicativos com Azure Active Directory

Azure Active Directory pode fornecer informações de associação de grupo de usuários em tokens para uso em aplicativos.  Há suporte para dois padrões principais:

- Grupos identificados por seu atributo de OID (identificador de objeto Azure Active Directory)
- Grupos identificados por atributos sAMAccountName ou GroupId para grupos e usuários sincronizados com Active Directory (AD)

> [!IMPORTANT]
> Há várias advertências a serem observadas para essa funcionalidade:
>
>- O suporte para o uso de atributos sAMAccountName e SID (identificador de segurança) sincronizados do local foi projetado para habilitar a movimentação de aplicativos existentes do AD FS e de outros provedores de identidade. Os grupos gerenciados no Azure AD não contêm os atributos necessários para emitir essas declarações.
>- Em organizações maiores, o número de grupos de que um usuário é membro pode exceder o limite que Azure Active Directory será adicionado a um token. 150 grupos para um token SAML e 200 para um JWT. Isso pode levar a resultados imprevisíveis. Se os usuários tiverem um grande número de associações de grupo, é recomendável usar a opção para restringir os grupos emitidos em declarações para os grupos relevantes para o aplicativo.  
>- Para o novo desenvolvimento de aplicativos, ou em casos em que o aplicativo pode ser configurado para ele e onde o suporte a grupos aninhados não é necessário, é recomendável que a autorização no aplicativo seja baseada em funções de aplicativo em vez de grupos.  Isso limita a quantidade de informações que precisam entrar no token, é mais seguro e separa a atribuição de usuário da configuração do aplicativo.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Declarações de grupo para aplicativos migrando do AD FS e de outros provedores de identidade

Muitos aplicativos configurados para autenticar com AD FS dependem de informações de associação de grupo na forma de atributos de grupo do Windows AD.   Esses atributos são o sAMAccountName do grupo, que pode ser qualificado por nome de domínio ou o identificador de segurança do grupo do Windows (GroupId).  Quando o aplicativo é federado com AD FS, AD FS usa a função TokenGroups para recuperar as associações de grupo para o usuário.

Um aplicativo que foi movido de AD FS precisa de declarações no mesmo formato. As declarações de grupo e função podem ser emitidas de Azure Active Directory que contêm o sAMAccountName qualificado pelo domínio ou o GroupId sincronizado de Active Directory em vez do objectID do Azure Active Directory do grupo.

Os formatos com suporte para declarações de grupo são:

- **ObjectID do grupo de Azure Active Directory** (disponível para todos os grupos)
- **sAMAccountName** (disponível para grupos sincronizados do Active Directory)
- **NetbiosDomain\sAMAccountName** (disponível para grupos sincronizados do Active Directory)
- **DNSDomainName\sAMAccountName** (disponível para grupos sincronizados do Active Directory)
- **Identificador de segurança do grupo local** (disponível para grupos sincronizados do Active Directory)

> [!NOTE]
> os atributos sAMAccountName e SID do grupo local só estão disponíveis em objetos de grupo sincronizados a partir de Active Directory.   Eles não estão disponíveis em grupos criados no Azure Active Directory ou no office365.   Os aplicativos configurados no Azure Active Directory para serem sincronizados com atributos de grupo locais os obtêm somente para grupos sincronizados.

## <a name="options-for-applications-to-consume-group-information"></a>Opções para que os aplicativos consumam informações de grupo

Os aplicativos podem chamar o ponto de extremidade de grupos do MS Graph para obter informações de grupo para o usuário autenticado. Essa chamada garante que todos os grupos dos quais um usuário é membro estejam disponíveis mesmo quando houver um grande número de grupos envolvidos.  A enumeração de grupo, em seguida, é independente das limitações de tamanho do token.

No entanto, se um aplicativo existente espera consumir informações de grupo por meio de declarações, Azure Active Directory pode ser configurada com vários formatos de declarações diferentes.  Considere as seguintes opções:

- Ao usar a associação de grupo para fins de autorização no aplicativo, é preferível usar o ObjectID do grupo. O ObjectID do grupo é imutável e exclusivo em Azure Active Directory e está disponível para todos os grupos.
- Se estiver usando o sAMAccountName do grupo local para autorização, use nomes qualificados do domínio;  Há menos chances de nomes conflitantes. sAMAccountName pode ser exclusivo em um domínio Active Directory, mas se mais de um domínio Active Directory for sincronizado com um locatário Azure Active Directory, haverá a possibilidade de que mais de um grupo tenha o mesmo nome.
- Considere o uso de [funções de aplicativo](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) para fornecer uma camada de indireção entre a associação de grupo e o aplicativo.   O aplicativo então faz decisões de autorização interna com base em declarações de função no token.
- Se o aplicativo estiver configurado para obter atributos de grupo que são sincronizados a partir de Active Directory e um grupo não contiver esses atributos, ele não será incluído nas declarações.
- As declarações de grupo em tokens incluem grupos aninhados, exceto ao usar a opção para restringir as declarações de grupo a grupos atribuídos ao aplicativo.  Se um usuário for membro de GroupB e GroupB for um membro de GroupA, as declarações de grupo para o usuário conterão GroupA e GroupB. Quando os usuários de uma organização têm um grande número de associações de grupo, o número de grupos listados no token pode aumentar o tamanho do token.  Azure Active Directory limita o número de grupos que será emitido em um token para 150 para asserções SAML e 200 para JWT.  Se um usuário for membro de um número maior de grupos, os grupos serão omitidos e um link para o ponto de extremidade do grafo para obter informações sobre o grupo será incluído em seu lugar.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Pré-requisitos para usar atributos de grupo sincronizados do Active Directory

As declarações de associação de grupo podem ser emitidas em tokens para qualquer grupo se você usar o formato ObjectId. Para usar declarações de grupo em formatos diferentes do ObjectId do grupo, os grupos devem ser sincronizados Active Directory usando Azure AD Connect.

Há duas etapas para configurar Azure Active Directory para emitir nomes de grupo para grupos de Active Directory.

1. **Sincronizar nomes de grupo de Active Directory** Antes que Azure Active Directory possa emitir os nomes de grupo ou o SID do grupo local em declarações de grupo ou função, os atributos necessários precisam ser sincronizados do Active Directory.  Você deve estar executando Azure AD Connect versão 1.2.70 ou posterior.   As versões anteriores do Azure AD Connect do que 1.2.70 sincronizarão os objetos de grupo de Active Directory, mas não incluirão os atributos de nome de grupo necessários.  Atualize para a versão atual.

2. **Configurar o registro do aplicativo no Azure Active Directory para incluir declarações de grupo em tokens** As declarações de grupo podem ser configuradas na seção aplicativos empresariais do portal ou usando o manifesto do aplicativo na seção registros do aplicativo.  Para configurar as declarações de grupo no manifesto do aplicativo, consulte "Configurando o Azure Active Directory registro de aplicativo para atributos de grupo" abaixo.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Adicionar declarações de grupo a tokens para aplicativos SAML usando a configuração de SSO

Para configurar declarações de grupo para um aplicativo SAML de galeria ou não Galeria, abra **aplicativos empresariais**, clique no aplicativo na lista, selecione **configuração de logon único** e, em seguida, selecione **atributos de usuário & declarações**.

Clique em **Adicionar uma declaração de grupo**  

![Captura de tela que mostra a página "declarações de atributos de usuário &" com a "adicionar uma declaração de grupo" selecionada.](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Use os botões de opção para selecionar quais grupos devem ser incluídos no token

![Captura de tela que mostra a janela "declarações de grupo" com "grupos de segurança" selecionado.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seleção | Descrição |
|----------|-------------|
| **Todos os grupos** | Emite grupos de segurança e listas de distribuição e funções.  |
| **Grupos de segurança** | Emite grupos de segurança dos quais o usuário é membro na declaração de grupos |
| **Funções de diretório** | Se o usuário tiver funções de diretório atribuídas, elas serão emitidas como uma declaração ' wids ' (a declaração de grupos não será emitida) |
| **Grupos atribuídos ao aplicativo** | Emite apenas os grupos atribuídos explicitamente ao aplicativo e o usuário é um membro de |

Por exemplo, para emitir todos os grupos de segurança dos quais o usuário é membro, selecione grupos de segurança

![Captura de tela que mostra a janela "declarações de grupo" com "grupos de segurança" selecionado e o menu suspenso "atributo de origem" aberto.](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Para emitir grupos usando Active Directory atributos sincronizados de Active Directory em vez de objectIDs do Azure AD, selecione o formato necessário na lista suspensa. Somente os grupos sincronizados do Active Directory serão incluídos nas declarações.

![Captura de tela que mostra o menu suspenso "atributo de origem" aberto.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Para emitir somente grupos atribuídos ao aplicativo, selecione **grupos atribuídos ao aplicativo**

![Captura de tela que mostra a janela "declarações de grupo" com "grupos atribuídos ao aplicativo" selecionado.](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Os grupos atribuídos ao aplicativo serão incluídos no token.  Outros grupos dos quais o usuário é membro serão omitidos.  Com essa opção, os grupos aninhados não são incluídos e o usuário deve ser um membro direto do grupo atribuído ao aplicativo.

Para alterar os grupos atribuídos ao aplicativo, selecione o aplicativo na lista **aplicativos empresariais** e, em seguida, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

Consulte o documento [atribuir um usuário ou grupo a um aplicativo empresarial](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) para obter detalhes de como gerenciar a atribuição de grupos a aplicativos.

### <a name="advanced-options"></a>Opções Avançadas

A maneira como as declarações de grupo são emitidas pode ser modificada pelas configurações em opções avançadas

Personalizar o nome da declaração de Grupo: se selecionado, um tipo de declaração diferente pode ser especificado para declarações de grupo.   Insira o tipo de declaração no campo nome e o namespace opcional para a declaração no campo namespace.

![Captura de tela que mostra a seção "opções avançadas" com "personalizar o nome da declaração de grupo" selecionado e os valores de "nome" e "namespace" inseridos.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Alguns aplicativos exigem que as informações de associação de grupo apareçam na declaração ' role '. Opcionalmente, você pode emitir os grupos do usuário como funções, marcando a caixa ' emitir grupos de declarações de função '.

![Captura de tela que mostra a seção "opções avançadas" com "personalizar o nome da declaração de grupo" e "emitir grupos como declarações de função" selecionado.](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se a opção para emitir dados de grupo como funções for usada, somente os grupos aparecerão na declaração de função.  Qualquer função de aplicativo à qual o usuário está atribuído não aparecerá na declaração de função.

### <a name="edit-the-group-claims-configuration"></a>Editar a configuração de declarações de grupo

Depois que uma configuração de declaração de grupo tiver sido adicionada aos atributos de usuário & configuração de declarações, a opção de adicionar uma declaração de grupo ficará esmaecida.  Para alterar a configuração de declaração de grupo, clique na declaração de grupo na lista de **declarações adicionais** .

![interface do usuário de declarações](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configurar o registro de aplicativo do Azure AD para atributos de grupo

As declarações de grupo também podem ser configuradas na seção [declarações opcionais](../../active-directory/develop/active-directory-optional-claims.md) do [manifesto do aplicativo](../../active-directory/develop/reference-app-manifest.md).

1. No portal->Azure Active Directory-> registros do aplicativo->selecione o manifesto do aplicativo >

2. Habilitar declarações de associação de grupo alterando o groupMembershipClaim

Os valores válidos são:

| Seleção | Descrição |
|----------|-------------|
| **Os** | Emite grupos de segurança, listas de distribuição e funções |
| **“SecurityGroup”** | Emite grupos de segurança dos quais o usuário é membro na declaração de grupos |
| **“DirectoryRole”** | Se o usuário tiver funções de diretório atribuídas, elas serão emitidas como uma declaração ' wids ' (a declaração de grupos não será emitida) |
| **ApplicationGroup** | Emite apenas os grupos atribuídos explicitamente ao aplicativo e o usuário é um membro de |
| **None** | Nenhum grupo é retornado. (Não diferencia maiúsculas de minúsculas, isso não funciona bem e pode ser definido diretamente no manifesto do aplicativo.) |

   Por exemplo:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Por padrão, as ObjectIDs de grupo serão emitidas no valor da declaração de grupo.  Para modificar o valor da declaração e conter os atributos do grupo local ou para alterar o tipo de declaração para função, use a configuração OptionalClaims da seguinte maneira:

3. Definir declarações opcionais de configuração de nome de grupo.

   Se você quiser que os grupos no token contenham os atributos do grupo do AD local, especifique qual tipo de token a declaração opcional deve ser aplicada na seção de declarações opcionais.  Vários tipos de token podem ser listados:

   - idToken para o token de ID de OIDC
   - accessToken para o token de acesso do OAuth/OIDC
   - Saml2Token para tokens SAML.

   > [!NOTE]
   > O tipo Saml2Token aplica-se aos tokens de formato SAML 1.1 e SAML 2.0

   Para cada tipo de token relevante, modifique a declaração de grupos para usar a seção OptionalClaims no manifesto. O esquema OptionalClaims é o seguinte:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Esquema de declarações opcional | Valor |
   |----------|-------------|
   | **name:** | Precisa ser "grupos" |
   | **source:** | Não usado. Omitir ou especificar null |
   | **essential:** | Não usado. Omitir ou especificar false |
   | **additionalProperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Somente um de "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" é necessário em um adicional.  Se mais de um estiver presente, o primeiro será usado e os outros serão ignorados.

   Alguns aplicativos exigem informações de grupo sobre o usuário na declaração de função.  Para alterar o tipo de declaração para de uma declaração de grupo para uma declaração de função, adicione "emit_as_roles" a propriedades adicionais.  Os valores de grupo serão emitidos na declaração de função.

   > [!NOTE]
   > Se "emit_as_roles" for usado, as funções de aplicativo configuradas que o usuário recebeu não aparecerão na declaração de função

### <a name="examples"></a>Exemplos

Emitir grupos como nomes de grupo nos tokens de acesso OAuth no formato dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Para emitir nomes de grupo a serem retornados no formato netbiosDomain\samAccountName como a declaração de funções nos tokens de ID SAML e OIDC:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Próximas etapas

- [Adicionar autorização usando grupos & declarações de grupos a um aplicativo Web ASP.NET Core (exemplo de código)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Atribuir um usuário ou um grupo a um aplicativo empresarial](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Configurar declarações de função](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
