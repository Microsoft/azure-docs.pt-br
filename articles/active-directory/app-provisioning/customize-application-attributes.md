---
title: Tutorial – Personalizar mapeamentos de atributo do Azure Active Directory
description: Saiba quais são os mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure e como você pode modificá-los para atender às necessidades de negócios.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 738b89ee9347a25e2d24369a48e966f0bec6daf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579460"
---
# <a name="tutorial---customize-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Tutorial – Personalizar mapeamentos de atributos do provisionamento de usuário para aplicativos SaaS no Azure Active Directory

O Microsoft Azure AD dá suporte ao provisionamento de usuário para aplicativos SaaS de terceiros, como o Salesforce e o G Suite, entre outros. Se você habilitar o provisionamento de usuário para um aplicativo SaaS de terceiros, o portal do Azure controlará os valores de seus atributos por meio do mapeamento de atributos.

Antes de começar, verifique se você está familiarizado com os conceitos de gerenciamento de aplicativo e de **SSO (logon único)** . Confira os seguintes links:
- [Série de guias de início rápido sobre gerenciamento de aplicativo no Azure AD](../manage-apps/view-applications-portal.md)
- [O que é SSO (logon único)?](../manage-apps/what-is-single-sign-on.md)

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de usuário do Azure AD e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos em conjunto com os Usuários, como Grupos.

Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Sendo assim, você pode alterar ou excluir mapeamentos de atributos existentes ou criar mapeamentos.

## <a name="editing-user-attribute-mappings"></a>Editar mapeamentos de atributos de usuário

Siga estas etapas para acessar o recurso **Mapeamentos** do provisionamento de usuário:

1. Entre no [Portal do Azure Active Directory](https://aad.portal.azure.com).
1. Selecione **Aplicativos empresariais** no painel esquerdo. É mostrada uma lista com todos os aplicativos configurados, incluindo aplicativos que foram adicionados da galeria.
1. Selecione qualquer aplicativo para carregar o painel de gerenciamento dele, no qual é possível ver relatórios e gerenciar configurações do aplicativo.
1. Selecione **Provisionamento** para gerenciar as configurações de provisionamento de conta do usuário no aplicativo selecionado.
1. Expanda **Mapeamentos** para ver e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino. Quando o aplicativo de destino tem suporte, esta seção oferece a opção de configurar o provisionamento de grupos e de contas de usuário.

   ![Usar Mapeamentos para exibir e editar atributos de usuário](./media/customize-application-attributes/21.png)

1. Selecione uma configuração de **Mapeamentos** para abrir a tela **Mapeamento de Atributos** relacionada. Alguns mapeamentos de atributos são obrigatórios para que o aplicativo SaaS funcione corretamente. Para os atributos necessários, o recurso **Excluir** não está disponível.

   ![Usar o Mapeamento de Atributos para configurar mapeamentos de atributos para aplicativos](./media/customize-application-attributes/22.png)

   Nesta captura de tela, você pode ver que o atributo **Nome de usuário** de um objeto gerenciado no Salesforce é preenchido com o valor **userPrincipalName** do Objeto do Azure Active Directory vinculado.

1. Selecione um **Mapeamento de Atributos** existente para abrir a tela **Editar Atributo**. Aqui, é possível editar e exibir os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino.

   ![Usar Editar Atributo para editar atributos de usuário](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo

Com mapeamentos de atributo, você controla como os atributos são preenchidos em um aplicativo SaaS de terceiro.
Há quatro tipos diferentes de mapeamento com suporte:

- **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto vinculado no AD do Azure.
- **Constante** – o atributo de destino é populado com uma cadeia de caracteres especificada por você.
- **Expressão** – o atributo de destino é preenchido com base no resultado de uma expressão de script.
  Para saber mais, consulte [Escrever expressões para mapeamentos de atributo no Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Nenhum** – o atributo de destino é deixado inalterado. No entanto, se estiver vazio, o atributo de destino será preenchido com o Valor padrão que você especificar.

Em conjunto com esses quatro tipos básicos, os mapeamentos de atributos personalizados dão suporte ao conceito de atribuição de um valor **padrão** opcional. A atribuição de valor padrão assegura que o atributo de destino seja preenchido com um valor quando não há um no Azure AD nem no objeto de destino. A configuração mais comum é deixar isso em branco.

### <a name="understanding-attribute-mapping-properties"></a>Noções básicas de propriedades de mapeamento de atributo

Na seção anterior, você foi apresentado à propriedade do tipo de mapeamento de atributos.
Além dessa propriedade, os mapeamentos de atributos também dão suporte aos seguintes atributos:

- **Atributo de origem** – o atributo de usuário do sistema de origem (exemplo: Azure Active Directory).
- **Atributo de destino** – o atributo de usuário no sistema de destino (exemplo: ServiceNow).
- **Valor padrão se nulo (opcional)** – o valor que será passado para o sistema de destino se o atributo de origem for nulo. Ele será provisionado apenas quando um usuário for criado. O "valor padrão quando nulo" não será provisionado ao atualizar um usuário existente. Se, por exemplo, quiser provisionar todos os usuários existentes no sistema de destino com um Cargo específico (quando ele for nulo no sistema de origem), você poderá usar a seguinte [expressão](../app-provisioning/functions-for-customizing-application-data.md): Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Substitua "DefaultValue" pelo que você gostaria de provisionar quando o valor for nulo no sistema de origem. 
- **Corresponder objetos usando este atributo** – se esse mapeamento deve ser usado para identificar com exclusividade usuários entre os sistemas de origem e destino. Normalmente, isso é definido no atributo userPrincipalName ou email no Azure AD, que costuma ser mapeado para um campo de nome de usuário em um aplicativo de destino.
- **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver vários, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado. Embora você possa definir tantos atributos correspondentes quantos desejar, considere se os atributos que está usando como correspondentes são verdadeiramente exclusivos e se eles precisam ser atributos correspondentes. Normalmente, os clientes têm 1 ou 2 atributos correspondentes em sua configuração. 
- **Aplicar esse mapeamento**
  - **Sempre** – aplicar esse mapeamento nas ações de atualização e de criação de usuário.
  - **Somente durante a criação** – aplicar esse mapeamento somente a ações de criação de usuário.

## <a name="matching-users-in-the-source-and-target--systems"></a>Usuários correspondentes nos sistemas de origem e destino
O serviço de provisionamento do Azure AD pode ser implantado em cenários "green field" (em que os usuários não existem no sistema de destino) e em cenários "brownfield" (em que os usuários já existem no sistema de destino). Para dar suporte aos dois cenários, o serviço de provisionamento usa o conceito de atributos correspondentes. Os atributos correspondentes permitem que você determine como identificar de maneira exclusiva um usuário na origem e faça a correspondência com o usuário no destino. Como parte do planejamento de sua implantação, defina o atributo que pode ser usado para identificar de maneira exclusiva um usuário nos sistemas de origem e destino. Aspectos a considerar:

- **Atributos correspondentes devem ser exclusivos:** os clientes costumam usar atributos como userPrincipalName, email ou a ID do objeto como o atributo correspondente.
- **Vários atributos podem ser usados como atributos correspondentes:** você pode definir vários atributos a serem avaliados ao fazer a correspondência entre os usuários, bem como a ordem em que eles são avaliados (precedência de correspondência na interface do usuário). Se, por exemplo, você definir três atributos como correspondentes e um usuário for considerado correspondente com exclusividade após avaliar os dois primeiros atributos, o serviço não avaliará o terceiro. O serviço vai avaliar os atributos correspondentes na ordem especificada e interromper a avaliação quando uma correspondência for encontrada.  
- **O valor na origem e no destino não precisa corresponder exatamente:** o valor no destino pode ser uma função simples do valor na origem. Sendo assim, é possível ter o atributo emailAddress na origem e userPrincipalName no destino, bem como fazer a correspondência com uma função do atributo emailAddress que substitui alguns caracteres por um valor constante.  
- **Não há suporte para correspondência baseada em uma combinação de atributos:** a maioria dos aplicativos não dá suporte a consultas baseadas em duas propriedades. Portanto, não é possível fazer a correspondência com base em uma combinação de atributos. É possível avaliar propriedades únicas em sequência.
- **Todos os usuários precisam ter um valor para pelo menos um atributo correspondente:** se você definir um atributo correspondente, todos os usuários precisarão ter um valor para esse atributo no sistema de origem. Se, por exemplo, você definir userPrincipalName como o atributo correspondente, todos os usuários precisarão ter um userPrincipalName. Se você definir vários atributos correspondentes (por exemplo, extensionAttribute1 e email), nem todos os usuários precisarão ter o mesmo atributo correspondente. Um usuário poderia ter extensionAttribute1 e não ter email, enquanto outro usuário poderia ter email e não ter extensionAttribute1. 
- **O aplicativo de destino deve dar suporte à filtragem no atributo correspondente:** os desenvolvedores de aplicativos permitem filtrar um subconjunto de atributos em sua API de usuário ou de grupo. Para aplicativos na galeria, garantimos que o mapeamento de atributos padrão use um atributo no qual a API do aplicativo de destino dá suporte à filtragem. Ao alterar o atributo correspondente padrão do aplicativo de destino, verifique a documentação da API de terceiros para garantir que seja possível filtrar com base nele.  

## <a name="editing-group-attribute-mappings"></a>Editar mapeamentos de atributos de grupo

Um número seleto de aplicativos, como o ServiceNow, o Box e o G Suite, dão suporte ao provisionamento de objetos de Grupo e de objetos de Usuário. Objetos de Grupo podem conter propriedades do grupo, como nomes de exibição e aliases de email, bem como os membros do grupo.

![Exemplo mostra o ServiceNow com objetos de Grupo e Usuário provisionados](./media/customize-application-attributes/24.png)

O provisionamento de grupo pode, opcionalmente, ser habilitado ou desabilitado selecionando o mapeamento de grupo em **Mapeamentos** e configurando **Habilitado** como a opção desejada na tela **Mapeamento de Atributos**.

Os atributos provisionados como parte dos objetos de Grupo podem ser personalizados da mesma maneira como os objetos de Usuário, como descrito anteriormente. 

> [!TIP]
> O provisionamento dos objetos de grupo (propriedades e membros) é um conceito distinto de [atribuir grupos](../manage-apps/assign-user-or-group-access-portal.md) para um aplicativo. É possível atribuir um grupo a um aplicativo, mas somente provisionar os objetos de usuário contidos no grupo. O provisionamento de objetos de grupo completos não é necessário para usar grupos em atribuições.

## <a name="editing-the-list-of-supported-attributes"></a>Editando a lista de atributos com suporte

Os atributos de usuário que têm suporte para um determinado aplicativo são pré-configurados. As APIs de gerenciamento do usuário da maioria dos aplicativos não têm suporte para a descoberta de esquema. Portanto, o serviço de provisionamento do Azure AD não é capaz de gerar dinamicamente a lista de atributos com suporte fazendo chamadas para o aplicativo.

No entanto, alguns aplicativos dão suporte a atributos personalizados e o serviço de provisionamento do Azure AD pode ler e gravar em atributos personalizados. Para inserir as definições deles no portal do Azure, marque a caixa de seleção **Mostrar opções avançadas** na parte inferior da tela **Mapeamento de Atributos** e, em seguida, selecione **Editar a Lista de Atributos** para seu aplicativo.

Os aplicativos e sistemas que dão suporte à personalização da lista de atributos incluem:

- Salesforce
- ServiceNow
- Workday para o Active Directory/Workday para o Azure Active Directory
- SuccessFactors para o Active Directory/SuccessFactors para o Azure Active Directory
- Azure Active Directory ([os atributos padrão da API do Graph do Azure AD](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) e as extensões de diretório personalizadas são compatíveis). Saiba mais sobre [como criar extensões](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning-sync-attributes-for-mapping#create-an-extension-attribute-on-a-cloud-only-user) e [limitações conhecidas](https://docs.microsoft.com/azure/active-directory/app-provisioning/known-issues). 
- Aplicativos compatíveis com [SCIM 2.0](https://tools.ietf.org/html/rfc7643)
- O write-back do Azure Active Directory para Workday ou SuccessFactors dá suporte para atualizar metadados relevantes para atributos com suporte (XPATH e JSONPath), mas não dá suporte para adicionar novos atributos do Workday ou do SuccessFactors além daqueles incluídos no esquema padrão


> [!NOTE]
> Recomendamos editar a lista de atributos compatíveis somente para administradores que personalizaram o esquema de aplicativos e sistemas, bem como têm um conhecimento direto sobre de que modo os atributos personalizados foram definidos ou a hipótese de um atributo de origem não ser exibido de modo automático na interface do usuário do portal do Azure. Às vezes, isso requer familiaridade com as APIs e as ferramentas dos desenvolvedores fornecidas por um aplicativo ou sistema. A capacidade de editar a lista de atributos compatíveis está bloqueada por padrão, mas os clientes podem habilitar a funcionalidade navegando até a seguinte URL: https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true. Em seguida, acesse seu aplicativo para ver a lista de atributos, conforme descrito [acima](#editing-the-list-of-supported-attributes). 

Ao editar a lista de atributos com suporte, as seguintes propriedades são fornecidas:

- **Nome** - O nome do sistema do atributo, conforme definido no esquema do objeto de destino.
- **Tipo** – o tipo de dados que o atributo armazena, conforme definido no esquema do objeto de destino, que pode ser um dos seguintes:
  - *Binário* - O atributo contém dados binários.
  - *Booliano* - O atributo contém um valor Verdadeiro ou Falso.
  - *DateTime* - O atributo contém uma cadeia de caracteres de data.
  - *Número inteiro* - O atributo contém um número inteiro.
  - *Referência* - O atributo contém uma ID que faz referência a um valor armazenado em outra tabela no aplicativo de destino.
  - *Cadeia de caracteres* - O atributo contém uma cadeia de caracteres de texto.
- **Chave primária?** – Se o atributo é definido como um campo de chave primária no esquema do objeto de destino.
- **Obrigatório?** – Se o atributo precisa ser preenchido no aplicativo ou no sistema de destino.
- **Vários valores?** – Se o atributo dá suporte para vários valores.
- **Diferenciar maiúsculas e minúsculas?** – Se os valores dos atributos são avaliados com diferenciação de maiúsculas e minúsculas.
- **Expressão API** – não usar, a menos que instruído a fazer isso pela documentação de um conector de provisionamento específico (como o Workday).
- **Atributo do Objeto Referenciado** – se for um atributo de Tipo de referência, esse menu permitirá que você selecione a tabela e o atributo no aplicativo de destino que contém o valor associado ao atributo. Por exemplo, se você tiver um atributo chamado "Departamento" cujo valor armazenado faz referência a um objeto em uma tabela separada de "Departamentos", selecione "Departments.Name". As tabelas de referência e os campos de ID primários com suporte para um determinado aplicativo são pré-configurados e, atualmente, não podem ser editados usando o portal do Azure, mas podem ser editados usando a [API do Microsoft Graph](/graph/api/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisionar um atributo de extensão personalizado para um aplicativo em conformidade com SCIM
O RFC do SCIM define um esquema de usuário e grupo principal, permitindo também que as extensões do esquema atendam às necessidades de seu aplicativo. Para adicionar um atributo personalizado a um aplicativo SCIM:
   1. Entre no [portal do Azure Active Directory](https://aad.portal.azure.com), selecione **Aplicativos Empresariais**, selecione seu aplicativo e, em seguida, selecione **Provisionamento**.
   2. Em **Mapeamentos**, selecione o objeto (usuário ou grupo) para o qual deseja adicionar um atributo personalizado.
   3. Na parte inferior da página, selecione **Mostrar opções avançadas**.
   4. Selecione **Editar lista de atributos do AppName**.
   5. Na parte inferior da lista de atributos, insira informações sobre o atributo personalizado nos campos fornecidos. Em seguida, selecione **Adicionar Atributo**.

Para aplicativos SCIM, o nome do atributo deve seguir o padrão mostrado no exemplo abaixo. O "CustomExtensionName" e o "CustomAttribute" podem ser personalizados de acordo com os requisitos do seu aplicativo, por exemplo: urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 

Essas instruções são aplicáveis somente a aplicativos habilitados para o SCIM. Aplicativos como o ServiceNow e o Salesforce não são integrados ao Azure AD usando o SCIM e, portanto, não exigem esse namespace específico ao adicionar um atributo personalizado.

Atributos personalizados não podem ser atributos de referência, nem atributos com vários valores e com tipo complexo. Atualmente, atributos de extensão com vários valores e com tipo complexo personalizados têm suporte apenas nos aplicativos na galeria.  
 
**Exemplo de representação de um usuário com um atributo de extensão:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Provisionar uma função para um aplicativo SCIM
Use as etapas abaixo para provisionar funções para um usuário em seu aplicativo. Observe que a descrição abaixo é específica para aplicativos SCIM personalizados. Para aplicativos da galeria, como o Salesforce e o ServiceNow, use os mapeamentos de funções predefinidos. Os marcadores abaixo descrevem como transformar o atributo AppRoleAssignments no formato esperado pelo aplicativo.

- O mapeamento de um appRoleAssignment no Azure AD para uma função em seu aplicativo requer que você transforme o atributo usando uma [expressão](../app-provisioning/functions-for-customizing-application-data.md). O atributo appRoleAssignment **não deve ser mapeado diretamente** para um atributo de função sem usar uma expressão para analisar os detalhes da função. 

- **SingleAppRoleAssignment** 
  - **Quando usar:** use a expressão SingleAppRoleAssignment para provisionar uma só função para um usuário e para especificar a função primária. 
  - **Como configurar:** use as etapas descritas acima para navegar até a página de mapeamentos de atributo e use a expressão SingleAppRoleAssignment para mapear para o atributo de funções. Há três atributos de função entre os quais escolher: (roles[primary eq "True"].display, roles[primary eq "True].type e roles[primary eq "True"].value). Você pode optar por incluir qualquer um dos atributos de função (ou todos eles) em seus mapeamentos. Se quiser incluir mais de um, basta adicionar um novo mapeamento e incluí-lo como o atributo de destino.  
  
  ![Adicionar SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Aspectos a considerar**
    - Certifique-se de que não sejam atribuídas várias funções a um usuário. Não podemos garantir qual função será provisionada.
    
  - **Solicitação de exemplo (POST)** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
  - **Exemplo de saída (PATCH)** 
    
   ```
   "Operations": [
   {
   "op": "Add",
   "path": "roles",
   "value": [
   {
   "value": "{\"id\":\"06b07648-ecfe-589f-9d2f-6325724a46ee\",\"value\":\"25\",\"displayName\":\"Role1234\"}"
   }
   ]
   ```  
Os formatos de solicitação no PATCH e no POST são diferentes. Para garantir que o POST e o PATCH sejam enviados no mesmo formato, você pode usar o sinalizador de recurso descrito [aqui](./application-provisioning-config-problem-scim-compatibility.md#flags-to-alter-the-scim-behavior). 

- **AppRoleAssignmentsComplex** 
  - **Quando usar:** use a expressão AppRoleAssignmentsComplex para provisionar várias funções para um usuário. 
  - **Como configurar:** edite a lista de atributos com suporte conforme descrito acima para incluir um novo atributo para as funções: 
  
    ![Adicionar funções](./media/customize-application-attributes/add-roles.png)<br>

    Em seguida, use a expressão AppRoleAssignmentsComplex para mapear para o atributo de função personalizado, conforme mostrado na imagem abaixo:

    ![Adicionar AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Aspectos a considerar**
    - Todas as funções serão provisionadas como primary = false.
    - O POST contém o tipo da função. A solicitação PATCH não contém o tipo. Estamos trabalhando para enviar o tipo em solicitações POST e PATCH.
    
  - **Saída de exemplo** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Provisionar um atributo com vários valores
Determinados atributos, como phoneNumbers e emails, são atributos com vários valores, pois pode ser necessário especificar diferentes tipos de números de telefone ou emails. Use a expressão abaixo para atributos com vários valores. Ela permite que você especifique o tipo de atributo e o mapeie para o atributo de usuário com valor correspondente no Azure AD. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restaurando os atributos padrão e mapeamentos de atributos

Caso precise recomeçar e redefinir os mapeamentos existentes para o estado padrão, marque a caixa de seleção **Restaurar os mapeamentos padrão** e salve a configuração. Isso define todos os mapeamentos e filtros de escopo como se o aplicativo tivesse acabado de ser adicionado ao seu locatário do Azure AD da galeria de aplicativos.

A seleção dessa opção efetivamente forçará uma ressincronização de todos os usuários enquanto o serviço de provisionamento estiver em execução.

> [!IMPORTANT]
> É altamente recomendável que o **Status de provisionamento** seja definido como **Desativado** antes de invocar essa opção.

## <a name="what-you-should-know"></a>O que você deve saber

- O Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, apenas os objetos que precisam de atualização são processados durante um ciclo de sincronização.
- A atualização de mapeamentos de atributo tem impacto no desempenho de um ciclo de sincronização. Uma atualização a uma configuração de mapeamento de atributo exige que todos os objetos gerenciados sejam reavaliados.
- Uma melhor prática recomendada é manter o número de alterações consecutivas em seus mapeamentos de atributos ao mínimo.
- Atualmente, não há suporte para a adição de um atributo de foto a ser provisionado para um aplicativo, pois não é possível especificar o formato para sincronizar a foto. Solicite o recurso no [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory)
- O atributo IsSoftDeleted geralmente faz parte dos mapeamentos padrão de um aplicativo. IsSoftdeleted pode ser verdadeiro em um de quatro cenários (o usuário está fora do escopo por não estar atribuído ao aplicativo, o usuário está fora do escopo por não se encaixar no filtro de escopo, o usuário foi excluído de maneira temporária no Azure AD ou a propriedade AccountEnabled está definida como falsa no usuário). Não é recomendável remover o atributo IsSoftDeleted dos mapeamentos de atributos.
- O serviço de provisionamento do Azure AD não dá suporte ao provisionamento de valores nulos.
- A chave primária, normalmente "ID", não deve ser incluída como um atributo de destino em seus mapeamentos de atributo. 
- O atributo de função normalmente precisa ser mapeado usando uma expressão em vez de um mapeamento direto. Confira a seção acima para obter mais detalhes sobre o mapeamento de funções. 
- Embora você possa desabilitar os grupos dos seus mapeamentos, não há suporte para a desabilitação de usuários. 

## <a name="next-steps"></a>Próximas etapas

- [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
- [Escrevendo expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
- [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](use-scim-to-provision-users-and-groups.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
