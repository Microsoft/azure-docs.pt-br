---
title: 'Azure AD Connect: conceitos de design | Microsoft Docs'
description: "Este tópico detalha determinadas áreas de design da implementação"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: f23443d438c95a784f655fb9a5f20dfcf37be189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: conceitos de design
O objetivo deste tópico é descrever as áreas que devem ser consideradas durante o design de implementação do Azure AD Connect. Este tópico é um aprofundamento em determinadas áreas e esses conceitos também são descritos brevemente em outros tópicos.

## <a name="sourceanchor"></a>sourceAnchor
O atributo sourceAnchor é definido como *um atributo imutável durante o tempo de vida de um objeto*. Ele identifica de maneira exclusiva um objeto como sendo o mesmo objeto local e no AD do Azure. O atributo também é chamado de **immutableId** e os dois nomes são usados como sinônimos.

A palavra imutável, ou seja, "que não pode ser alterado", é importante neste tópico. Como o valor desse atributo não pode ser alterado depois de ser definido, é importante escolher um design que dê suporte ao seu cenário.

O atributo é usado para os seguintes cenários:

* Quando um novo servidor de mecanismo de sincronização é compilado ou recompilado após um cenário de recuperação de desastre, esse atributo vincula objetos existentes no Azure AD a objetos locais.
* Se você for de uma identidade somente em nuvem para um modelo de identidade sincronizado, esse atributo permitirá que os objetos efetuem o "hard match" dos objetos existentes no Azure AD em relação aos objetos locais.
* Se você usar federação, esse atributo, junto com **userPrincipalName** , é usado na declaração para identificar exclusivamente um usuário.

Este tópico trata somente de sourceAnchor no que diz respeito aos usuários. As mesmas regras se aplicam a todos os tipos de objeto, mas somente para usuários para os quais esse problema normalmente é uma preocupação.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionando um bom atributo sourceAnchor
O valor do atributo deve seguir as regras a seguir:

* Ter menos de 60 caracteres
  * Caracteres diferentes de a-z, A-Z ou 0-9 são codificados e contados como 3 caracteres
* Não conter nenhum caractere especial: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Ser globalmente exclusivo
* Ser uma cadeia de caracteres, um inteiro ou um binário
* Não ser baseado no nome do usuário, já que isso muda
* Não diferenciar maiúsculas de minúsculas e evitar valores que podem variar maiúsculas e minúsculas
* Ser atribuído quando o objeto é criado

Se o sourceAnchor selecionado não for do tipo de cadeia de caracteres, o Azure AD Connect usará Base64Encode no valor do atributo para assegurar que nenhum caractere especial será exibido. Se você usar outro servidor de federação em vez do ADFS, verifique se o servidor também pode usar Base64Encode para o atributo.

O atributo sourceAnchor diferencia letras maiúsculas de minúsculas. Um valor "DaviBarros" não é igual a "davibarros". Porém, você não deve ter dois objetos diferentes com apenas uma diferença de uso de maiúsculas.

Se você tiver uma única floresta local, o atributo que você deve usar é **objectGUID**. Esse também é o atributo usado quando você usar configurações expressas no Azure AD Connect, além de ser o atributo usado pelo DirSync.

Se você tem várias florestas e não move usuários entre florestas e domínios, **objectGUID** é um bom atributo para usar, mesmo nesse caso.

Se você mover os usuários entre domínios e florestas, deve encontrar um atributo que não será alterado ou que não pode ser movido com os usuários durante a movimentação. Uma abordagem recomendada é apresentar um atributo sintético. Um atributo que contenha algo parecido com um GUID seria adequado. Durante a criação do objeto, um novo GUID é criado e carimbado no usuário. Uma regra de sincronização personalizada pode ser criada no servidor de mecanismo de sincronização para criar esse valor baseado no **objectGUID** e atualizar o atributo selecionado no ADDS. Ao mover o objeto, não se esqueça também de copiar o conteúdo do valor.

Outra solução é escolher um atributo existente que você sabe que não será alterado. Os atributos usados normalmente incluem **employeeID**. Se você considerar um atributo que contenha letras, verifique se não há nenhuma possibilidade da letra (letras maiúsculas ou letras minúsculas) alterar o valor do atributo. Atributos inválidos que não devem ser usados incluem os atributos com o nome do usuário. Em um casamento ou divórcio, o nome deve ser alterado, o que não é permitido para esse atributo. Isso também é um dos motivos pelos quais os atributos como **userPrincipalName**, **mail** e **targetAddress** não são nem mesmo possíveis de se selecionar no assistente de instalação do Azure AD Connect. Esses atributos também contêm o caractere "@", que não é permitido no sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alterando o atributo sourceAnchor
O valor do atributo sourceAnchor não pode ser alterado após o objeto ser criado no AD do Azure e a identidade ser sincronizada.

Por esse motivo, as seguintes restrições se aplicam ao Azure AD Connect:

* O atributo sourceAnchor somente pode ser definido durante a instalação inicial. Se você executar o assistente de instalação novamente, essa opção será somente leitura. Se você precisar alterar essa configuração, desinstale e reinstale.
* Se você instalar outro servidor do Azure AD Connect, você deverá selecionar o mesmo atributo sourceAnchor usado anteriormente. Se você usava o DirSync anteriormente e mudou para o Azure AD Connect, será preciso usar **objectGUID** , já que ele é o atributo usado pelo DirSync.
* Se o valor de sourceAnchor for alterado após o objeto ser exportado para o Azure AD, a sincronização do Azure AD Connect gerará um erro e não permitirá nenhuma outra alteração no objeto antes de o problema ser corrigido e o sourceAnchor ser alterado de volta no diretório de origem.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Usando o msDS-ConsistencyGuid como sourceAnchor
Por padrão, o Azure AD Connect (versão 1.1.486.0 e anteriores) usa o objectGUID como o atributo sourceAnchor. O ObjectGUID é gerado pelo sistema. Não é possível especificar seu valor ao criar objetos do AD locais. Conforme explicado na seção [sourceAnchor](#sourceanchor), há cenários em que você precisa especificar o valor sourceAnchor. Se os cenários forem aplicáveis a você, use um atributo do AD configurável (por exemplo, msDS-ConsistencyGuid) como o atributo sourceAnchor.

O Azure AD Connect (versão 1.1.524.0 e posterior) agora facilita o uso de msDS-ConsistencyGuid como o atributo sourceAnchor. Ao usar esse recurso, o Azure AD Connect configura automaticamente as regras de sincronização para:

1. Use msDS-ConsistencyGuid como o atributo sourceAnchor para objetos do Usuário. O ObjectGUID é usado para outros tipos de objeto.

2. Para qualquer objeto do Usuário do AD local determinado cujo atributo msDS-ConsistencyGuid não esteja preenchido, o Azure AD Connect grava seu valor objectGUID de volta para o atributo msDS-ConsistencyGuid no Active Directory local. Depois que o atributo msDS-ConsistencyGuid é preenchido, o Azure AD Connect exporta o objeto para o Azure AD.

>[!NOTE]
> Uma vez que o objeto AD local é importado para o Azure AD Connect (ou seja, importado para o Espaço do AD Connector e projetado no Metaverso), você não pode mais alterar seu valor sourceAnchor. Para especificar o valor de sourceAnchor para um determinado objeto do AD local, configure seu atributo msDS-ConsistencyGuid antes de importá-lo para o Azure AD Connect.

### <a name="permission-required"></a>Permissão necessária
Para esse recurso funcionar, a conta do AD DS usada para sincronizar com o Active Directory local deve receber permissão de gravação para o atributo msDS-ConsistencyGuid no Active Directory local.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Como habilitar o recurso ConsistencyGuid – nova instalação
Você pode habilitar o uso de ConsistencyGuid como sourceAnchor durante uma nova instalação. Esta seção aborda tanto a instalação Expressa quanto a Personalizada em detalhes.

  >[!NOTE]
  > Somente as versões mais recentes do Azure AD Connect (1.1.524.0 e posteriores) dão suporte ao uso de ConsistencyGuid como sourceAnchor durante a nova instalação.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Como habilitar o recurso ConsistencyGuid
No momento, o recurso pode ser habilitado somente durante a nova instalação do Azure AD Connect.

#### <a name="express-installation"></a>Instalação Expressa
Ao instalar o Azure AD Connect com o modo Expresso, o assistente do Azure AD Connect determina automaticamente o atributo do AD mais apropriado a ser usado como o atributo sourceAnchor usando a seguinte lógica:

* Primeiro, o assistente do Azure AD Connect consulta seu locatário do Azure AD para recuperar o atributo do AD usado como o atributo sourceAnchor na instalação anterior do Azure AD Connect (caso haja algum). Se essas informações estiverem disponíveis, o Azure AD Connect usará o mesmo atributo do AD.

  >[!NOTE]
  > Somente as versões mais recentes do Azure AD Connect (1.1.524.0 e posteriores) armazenam informações no seu locatário do Azure AD sobre o atributo sourceAnchor usado durante a instalação. Versões mais antigas do Azure AD Connect não fazem isso.

* Se as informações sobre o atributo sourceAnchor usado não estiverem disponíveis, o assistente verificará o estado do atributo msDS-ConsistencyGuid em seu Active Directory local. Se o atributo não estiver configurado em nenhum objeto no diretório, o assistente usará o msDS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo estiver configurado em um ou mais objetos no diretório, o assistente concluirá que o atributo está sendo usado por outros aplicativos e não é adequado como atributo sourceAnchor...

* Nesse caso, o assistente fará o fallback usando o objectGUID como o atributo sourceAnchor.

* Depois da definição do atributo sourceAnchor, o assistente armazena as informações em seu locatário do Azure AD. As informações serão usadas pela instalação futura do Azure AD Connect.

Quando a instalação do Expresso estiver concluída, o assistente o informará de qual atributo foi selecionado como o atributo SourceAnchor.

![O assistente informa qual atributo do AD foi escolhido como sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalação personalizada
Ao instalar o Azure AD Connect com o modo Personalizado, o assistente do Azure AD Connect oferece duas opções ao configurar o atributo sourceAnchor:

![Instalação personalizada – configuração do sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Configuração | Descrição |
| --- | --- |
| Permitir que o Azure gerencie a âncora de origem para mim | Selecione essa opção se desejar que o Azure AD escolha o atributo para você. Se você selecionar essa opção, o Assistente do Azure AD Connect aplicará a mesma [lógica de seleção de atributo sourceAnchor usada durante a instalação do Expresso](#express-installation). De modo semelhante à instalação do Expresso, o assistente informará qual atributo foi selecionado como atributo Source Anchor após a conclusão da instalação Personalizada. |
| Um atributo específico | Selecione esta opção se você quiser especificar um atributo existente do AD como o atributo sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Como habilitar o recurso ConsistencyGuid – implantação existente
Se você tiver uma implantação existente do Azure AD Connect, que esteja usando o objectGUID como o atributo de Âncora de Origem, você poderá mudar para ela, usando o ConsistencyGuid em vez disso.

>[!NOTE]
> Somente as versões mais recentes do Azure AD Connect (1.1.552.0 e posteriores) dão suporte à comutação de ObjectGuid para ConsistencyGuid como o atributo de Âncora de Origem.

Para trocar de objectGUID para ConsistencyGuid como o atributo de Âncora de Origem:

1. Inicie o assistente do Azure AD Connect e clique em **Configurar** para acessar a tela Tarefas.

2. Selecione a opção de tarefa **Configurar Âncora de Origem** e clique em **Avançar**.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Insira suas credenciais de administrador do Azure AD e clique em **Avançar**.

4. O assistente do Azure AD Connect analisará o estado do atributo msDS-ConsistencyGuid em seu Active Directory local. Se o atributo não estiver configurado em nenhum objeto no diretório, o Azure AD Connect concluirá que nenhum outro aplicativo está usando o atributo atualmente e é seguro usá-lo como o atributo de Âncora de Origem. Clique em **Avançar** para continuar.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. Na tela **Pronto para configurar**, clique em **Configurar** para alterar a configuração.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. Quando a configuração for concluída, o assistente indicará que o msDS-ConsistencyGuid agora está sendo usado como o atributo de Âncora de Origem.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

Durante a análise (etapa 4), se o atributo estiver configurado em um ou mais objetos no diretório, o assistente concluirá que o atributo está sendo usado por outro aplicativo e retornará um erro, conforme ilustrado no diagrama a seguir. Se você tiver certeza de que o atributo não é usado pelos aplicativos existentes, precisará contatar o Suporte para obter informações sobre como suprimir o erro.

![Habilitar ConsistencyGuid para implantação existente – erro](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Impacto sobre o AD FS ou a configuração de federação de terceiros
Se você estiver usando o Azure AD Connect para gerenciar a implantação do AD FS local, o Azure AD Connect atualizará automaticamente as regras de declaração para usar o mesmo atributo do AD como sourceAnchor. Isso garante que a declaração ImmutableID gerada pelo ADFS seja consistente com os valores de sourceAnchor exportados para o Azure AD.

Se você estiver gerenciando o AD FS fora do Azure AD Connect ou usando servidores de federação de terceiros para autenticação, atualize manualmente as regras de declaração para que a declaração ImmutableID seja consistente com os valores de sourceAnchor exportados para o Azure AD, conforme descrito na seção do artigo [Modificar regras de declaração do AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). O assistente retorna o seguinte aviso após a conclusão da instalação:

![Configuração da federação de terceiros](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Adicionando novos diretórios à implantação existente
Suponha que você tenha implantado o Azure AD Connect com o recurso ConsistencyGuid habilitado e agora deseje adicionar outro diretório à implantação. Quando você tenta adicionar o diretório, o assistente do Azure AD Connect verifica o estado do atributo mSDS-ConsistencyGuid no diretório. Se o atributo estiver configurado em um ou mais objetos no diretório, o assistente concluirá que o atributo está sendo usado por outros aplicativos e retornará um erro, conforme ilustrado no diagrama a seguir. Se você tiver certeza de que o atributo não é usado pelos aplicativos existentes, precisará contatar o Suporte para obter informações sobre como suprimir o erro.

![Adicionando novos diretórios à implantação existente](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Entrar no Azure AD
Ao integrar seu diretório local ao Azure AD, é importante compreender como as configurações de sincronização podem afetar a maneira de autenticar o usuário. O Azure AD usa userPrincipalName (UPN) para autenticar o usuário. No entanto, ao sincronizar os usuários, você deve escolher o atributo a ser usado para o valor de userPrincipalName cuidadosamente.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolher o atributo para userPrincipalName
Ao selecionar o atributo para fornecer o valor de UPN a ser usado no Azure, garanta que

* Os valores de atributo estão de acordo com a sintaxe UPN (RFC 822), ou seja, eles devem estar no formato username@domain
* O sufixo nos valores corresponde a um dos domínios personalizados verificados no Azure AD

Em configurações expressas, a opção suposta para o atributo é userPrincipalName. Se o atributo userPrincipalName não contém o valor que você deseja que os usuários usem para entrar no Azure, escolha **Instalação Personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado de domínio personalizado e UPN
É importante garantir que haja um domínio verificado para o sufixo UPN.

John é um usuário em contoso.com. Você deseja que Júlio use o UPN local john@contoso.com para entrar no Azure depois de ter sincronizado os usuários para o diretório contoso.onmicrosoft.com do Azure AD. Para fazer isso, você precisa adicionar e verificar contoso.com como um domínio personalizado no Azure AD antes de iniciar a sincronização dos usuários. Se o sufixo de Pedro, por exemplo, contoso.com, não corresponder a um domínio verificado no Azure AD, o Azure AD substituirá o sufixo UPN por contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domínios locais não roteáveis e UPN para Azure AD
Algumas organizações têm domínios não roteáveis, como contoso.local ou domínios de rótulo único simples, como contoso. Não é possível verificar um domínio não roteável no Azure AD. O Azure AD Connect pode sincronizar apenas um domínio verificado no Azure AD. Quando você cria um diretório do Azure AD, ele cria um domínio roteável que torna-se o domínio padrão do Azure AD, por exemplo, contoso.onmicrosoft.com. Portanto, é necessário verificar se outros domínios roteáveis nesse cenário, caso você não deseje sincronizar com o domínio padrão onmicrosoft.com.

Leia [Adicionar seu nome de domínio personalizado ao Azure Active Directory](../active-directory-add-domain.md) para obter mais informações sobre como verificar domínios.

O Azure AD Connect detecta se você está executando em um ambiente de domínio não roteável e avisa corretamente para não prosseguir com configurações expressas. Se você está operando em um domínio não roteável, é provável que o UPN dos usuários também tenha sufixos não roteáveis. Por exemplo, se você estiver executando em contoso.local, o Azure AD Connect vai sugerir o uso de configurações personalizadas em vez de usar as configurações expressas. Usando as configurações personalizadas, você pode especificar o atributo que deve ser usado como o UPN para entrar no Azure depois que os usuários são sincronizados com o Azure AD.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
