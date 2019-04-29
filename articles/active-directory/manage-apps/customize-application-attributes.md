---
title: Personalizar mapeamentos de atributo do Azure AD | Microsoft Docs
description: Saiba quais são os mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure e como você pode modificá-los para atender às necessidades de negócios.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b63118a7c5fae49edebe4ae4976a1362781ae8cf
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759782"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizar os mapeamentos de atributos de provisionamento de usuário para aplicativos SaaS no Azure Active Directory
Microsoft Azure AD oferece suporte para provisionamento de usuário para aplicativos SaaS de terceiros, como Salesforce, G Suite e outros. Se você habilitar o provisionamento de usuário para um aplicativo de SaaS de terceiros, o portal do Azure controla seus valores de atributo por meio de mapeamentos de atributo.

Há um conjunto pré-configurado de atributos e mapeamentos de atributo entre objetos de usuário do Azure AD e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, juntamente com os usuários, como grupos.

Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Portanto, você pode alterar ou excluir mapeamentos de atributo existentes ou criar novos mapeamentos de atributo.
 
## <a name="editing-user-attribute-mappings"></a>Editar mapeamentos de atributos de usuário

Siga estas etapas para acessar o **mapeamentos** recurso de provisionamento de usuário:

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com).

1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados, incluindo os aplicativos que foram adicionados da galeria, é mostrada.

1. Selecione qualquer aplicativo para carregar seu painel de gerenciamento de aplicativo, onde você pode exibir relatórios e gerenciar as configurações do aplicativo.

1. Selecione **provisionamento** para gerenciar configurações para o aplicativo selecionado de provisionamento de conta de usuário.

1. Expandir **mapeamentos** para exibir e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino. Se o aplicativo de destino oferecer suporte a ele, esta seção permite configurar opcionalmente o provisionamento de grupos e contas de usuário.

   ![Salesforce](./media/customize-application-attributes/21.png) 

1. Selecione uma **mapeamentos** configuração para abrir o relacionados **mapeamento de atributos** tela. Alguns mapeamentos de atributo são exigidos por um aplicativo SaaS para funcionar corretamente. Para os atributos necessários, o recurso **Excluir** não está disponível.

   ![Salesforce](./media/customize-application-attributes/22.png)

   Nesta captura de tela, você pode ver que o **nome de usuário** atributo de um objeto gerenciado no Salesforce é preenchido com o **userPrincipalName** valor do Azure Active Directory objeto vinculado.

1. Selecione uma existente **mapeamento de atributos** para abrir o **Editar atributo** tela. Aqui você pode editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino.

   ![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo
Com mapeamentos de atributo, você controla como os atributos são preenchidos em um aplicativo SaaS de terceiro. Há quatro tipos diferentes de mapeamento com suporte:

* **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto vinculado no AD do Azure.
* **Constante** – o atributo de destino é preenchido com uma cadeia de caracteres específica que você especificou.
* **Expressão** – o atributo de destino é preenchido com base no resultado de uma expressão de script. 
  Para saber mais, consulte [Escrever expressões para mapeamentos de atributo no Azure Active Directory](functions-for-customizing-application-data.md).
* **Nenhum** – o atributo de destino é deixado inalterado. No entanto, se o atributo de destino estiver vazio, ele é preenchido com o valor padrão que você especificar.

Além desses quatro tipos básicos, mapeamentos de atributo personalizados suportam o conceito de um recurso opcional **padrão** atribuição de valor. A atribuição de valor padrão garante que um atributo de destino é preenchido com um valor se não houver um valor no AD do Azure ou no objeto de destino. A configuração mais comum é deixar isso em branco.


### <a name="understanding-attribute-mapping-properties"></a>Noções básicas de propriedades de mapeamento de atributo

Na seção anterior, você já foram introduzidos para a propriedade tipo de mapeamento de atributo.
Juntamente com essa propriedade, mapeamentos de atributo também dão suporte os seguintes atributos:

- **Atributo de origem** – o atributo de usuário do sistema de origem (exemplo: Azure Active Directory).
- **Atributo de destino** – o atributo de usuário no sistema de destino (exemplo: ServiceNow).
- **Corresponder objetos utilizando esse atributo** – indica se este mapeamento deve ser usado para identificar exclusivamente os usuários entre os sistemas de origem e destino. Normalmente, ele é definido no atributo userPrincipalName ou email no Azure AD, que normalmente é mapeado para um campo de nome de usuário em um aplicativo de destino.
- **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver vários, eles são avaliados na ordem definida por este campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.
- **Aplicar esse mapeamento**
    - **Sempre** – aplicar esse mapeamento na criação de usuário e ações de atualização.
    - **Somente durante a criação** -aplicar esse mapeamento somente nas ações de criação de usuário.


## <a name="editing-group-attribute-mappings"></a>Editar mapeamentos de atributos de grupo

Um número selecionado de aplicativos, como ServiceNow, Box e G Suite, suporte à capacidade de provisionar objetos de grupo e objetos de usuário. Objetos de grupo podem conter propriedades de grupo, como nomes de exibição e aliases de email, juntamente com os membros do grupo.

![ServiceNow](./media/customize-application-attributes/24.png)

Provisionamento de grupo pode ser opcionalmente habilitado ou desabilitado, selecionando o mapeamento de grupo em **mapeamentos**e definindo **habilitado** para a opção desejada no **demapeamentodeatributo** tela.

Os atributos provisionados como parte dos objetos de Grupo podem ser personalizados da mesma maneira como os objetos de Usuário, como descrito anteriormente. 

>[!TIP]
>O provisionamento dos objetos de grupo (propriedades e membros) é um conceito distinto de [atribuir grupos](assign-user-or-group-access-portal.md) para um aplicativo. É possível atribuir um grupo a um aplicativo, mas somente provisionar os objetos de usuário contidos no grupo. O provisionamento de objetos de grupo completos não é necessário para usar grupos em atribuições.


## <a name="editing-the-list-of-supported-attributes"></a>Editando a lista de atributos com suporte

Os atributos de usuário que têm suporte para um determinado aplicativo são pré-configurados. Gerenciamento de usuário do aplicativo para a maioria das APIs não oferecem suporte a descoberta de esquema. Portanto, o serviço de provisionamento do Azure AD não é capaz de gerar dinamicamente a lista de atributos com suporte por meio de chamadas para o aplicativo. 

No entanto, alguns aplicativos oferecem suporte a atributos personalizados, e o serviço de provisionamento do Azure AD pode ler e gravar aos atributos personalizados. Para inserir suas definições no portal do Azure, selecione a **Mostrar opções avançadas** caixa de seleção na parte inferior a **mapeamento de atributos** tela e, em seguida, selecione **Editar lista de atributos para** seu aplicativo.

Os aplicativos e sistemas que dão suporte à personalização da lista de atributos incluem:

* Salesforce
* ServiceNow
* Workday
* Active Directory do Azure ([atributos do Azure AD Graph API padrão](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e há suporte para extensões de diretório personalizado)
* Aplicativos que dão suporte ao [SCIM 2.0](https://tools.ietf.org/html/rfc7643), onde os atributos definidos no [esquema principal](https://tools.ietf.org/html/rfc7643) precisam ser adicionados

>[!NOTE]
>Editar a lista de atributos com suporte é recomendado apenas para os administradores que personalizaram o esquema de seus aplicativos e sistemas e que tenham conhecimento de antemão de como seus atributos personalizados foram definidos. Às vezes, isso requer familiaridade com as APIs e as ferramentas dos desenvolvedores fornecidas por um aplicativo ou sistema. 

Ao editar a lista de atributos com suporte, as seguintes propriedades são fornecidas:

* **Nome** - O nome do sistema do atributo, conforme definido no esquema do objeto de destino. 
* **Tipo** -o tipo de dados o atributo armazena, conforme definido no esquema do objeto de destino, que pode ser um dos seguintes tipos:
   * *Binário* - O atributo contém dados binários.
   * *Booliano* - O atributo contém um valor Verdadeiro ou Falso.
   * *DateTime* - O atributo contém uma cadeia de caracteres de data.
   * *Número inteiro* - O atributo contém um número inteiro.
   * *Referência* - O atributo contém uma ID que faz referência a um valor armazenado em outra tabela no aplicativo de destino.
   * *Cadeia de caracteres* - O atributo contém uma cadeia de caracteres de texto. 
* **Chave primária?** – Se o atributo é definido como um campo de chave primária no esquema do objeto de destino.
* **Obrigatório?** – Se o atributo é necessário para ser preenchido no sistema ou aplicativo de destino.
* **Vários valores?** – Se o atributo dá suporte a vários valores.
* **Diferenciar maiúsculas e minúsculas?** -Se os valores de atributos são avaliados de maneira diferencia maiusculas de minúsculas.
* **Expressão API** -não usar, a menos que instruído a fazer isso pela documentação de um conector de provisionamento específico (como o Workday).
* **Referenciado atributo do objeto** - se é um atributo de tipo de referência, em seguida, esse menu permite que você selecione a tabela e o atributo no aplicativo de destino que contém o valor associado ao atributo. Por exemplo, se você tiver um atributo chamado "Departamento" cujo valor armazenado faz referência a um objeto em uma tabela separada de "Departamentos", selecione "Departments.Name". As tabelas de referência e os campos ID primária tem suportados para um determinado aplicativo são pré-configurados e atualmente não podem ser editados usando o portal do Azure, mas podem ser editados usando o [API do Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Para adicionar um novo atributo, role até o final da lista de atributos com suporte, preencha os campos acima usando as entradas fornecidas e selecione **Adicionar Atributo**. Selecione **Salvar** quando terminar de adicionar atributos. Em seguida, será necessário recarregar a **provisionamento** guia para os novos atributos fiquem disponíveis no editor de mapeamento de atributo.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restaurando os atributos padrão e mapeamentos de atributos

Se você precisar recomeçar e redefinir os mapeamentos existentes de volta para seu estado padrão, você pode selecionar o **restaurar os mapeamentos padrão** caixa de seleção e salvar a configuração. Isso define todos os mapeamentos como se o aplicativo acabou de ser adicionado ao seu locatário do Azure AD da Galeria de aplicativos. 

Selecionar esta opção efetivamente forçará uma ressincronização de todos os usuários enquanto o serviço de provisionamento está em execução. 

>[!IMPORTANT]
>É altamente recomendável **status de provisionamento** ser definido como **Off** antes de invocar essa opção.


## <a name="what-you-should-know"></a>O que você deve saber

* O Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, apenas os objetos que precisam de atualização são processados durante um ciclo de sincronização. 

* A atualização de mapeamentos de atributo tem impacto no desempenho de um ciclo de sincronização. Uma atualização a uma configuração de mapeamento de atributo exige que todos os objetos gerenciados sejam reavaliados. 

* Uma prática recomendada é manter o número de alterações consecutivas aos seus mapeamentos de atributos no mínimo.


## <a name="next-steps"></a>Próximas etapas

* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
* [Escrevendo expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](use-scim-to-provision-users-and-groups.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)


