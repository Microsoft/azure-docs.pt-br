---
title: "Azure Active Directory B2C: Uso da personalização de idiomas | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: uso da personalização de idiomas

>[!NOTE] 
>Esse recurso está em uma versão prévia.  Recomendamos que você use um locatário de teste ao usar esse recurso.  Não pretendemos quaisquer alterações significativas da visualização para a versão de lançamento, mas reservamo-nos o direito de fazer essas alterações para melhorar o recurso.  Depois que você teve a oportunidade de experimentar o recurso, forneça comentários sobre suas experiências e como podemos melhorá-lo.  Você pode fornecer comentários por meio do portal do Azure com a ferramenta de face de smiley no canto superior direito.   Se houver a necessidade comercial de ativação usando esse recurso durante a fase de visualização, informe-nos seus cenários e para fornecermos a orientação e assistência corretas.  Você pode em contato conosco em [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com).

A personalização de idiomas permite que você altere seu percurso do usuário para um idioma diferente para atender às suas necessidades de cliente.  Podemos fornecer traduções para 36 idiomas (consulte [Informações adicionais](#additional-information)).  Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas de acordo com suas necessidades.  

## <a name="how-does-language-customization-work"></a>Como funciona a personalização de idiomas?
A personalização de idiomas permite que você selecione em quais idiomas o seu percurso do usuário está disponível.  Quando o recurso está habilitado, você pode fornecer o parâmetro de cadeia de caracteres de consulta, ui_locales, do seu aplicativo.  Quando você chama o Azure AD B2C, nós traduzimos a página para a localidade que você indicou.  Usar o tipo de configuração lhe dá total controle sobre os idiomas em seu percurso do usuário e ignora as configurações de idioma do navegador do cliente. Como alternativa, você talvez não precise desse nível de controle sobre quais idiomas o seu cliente vê.  Se você não fornecer um parâmetro ui_locales, a experiência do cliente será determinada pelas configurações do navegador dele.  Você ainda pode controlar para quais idiomas seu percurso do usuário está convertido adicionando-os como um idioma com suporte.  Se o navegador do cliente está definido para mostrar um idioma que você não deseja oferecer suporte, o idioma selecionado como padrão da cultura com suporte é mostrado.

1. **Idiomas especificados por local de IU** – depois que você habilita a personalização de idiomas, seu percurso do usuário é convertido para o idioma especificado aqui
2. **Idioma solicitado pelo navegador** - se nenhuma localidade de IU foi especificada, ele traduzirá para o idioma solicitado pelo navegador, **se ele estiver incluído nos idiomas com suporte**
3. **Idioma padrão de política** – se o navegador não especificar um idioma ou especificar um que não tenha suporte, ele traduzirá para o idioma padrão da política

>[!NOTE]
>Se você estiver usando atributos personalizados de usuário, você precisa fornecer suas próprias traduções. Confira "[Como personalizar suas cadeias de caracteres](#customize-your-strings)" para obter mais detalhes.
>

## <a name="support-uilocales-requested-languages"></a>Suporte aos idiomas ui_locales solicitados 
Ao habilitar a "Personalização de idiomas" em uma política, agora você pode controlar o idioma do percurso do usuário adicionando o parâmetro ui_locales.
1. [Siga estas etapas para navegar até a folha de recursos do B2C no portal do Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navegue até uma política que você deseja habilitar para traduções.
3. Clique em **Personalização de idiomas**.
4. Leia o aviso atentamente.  Uma vez habilitada, você não pode desativar a "personalização de idiomas".
5. Ative o recurso e clique em **OK**.
6. Salve a política no canto superior esquerdo da folha **Editar política**.

## <a name="select-which-languages-your-user-journey-supports"></a>Selecione quais idiomas oferecem suporte a seu percurso do usuário 
Crie uma lista de idiomas permitidos para seu percurso do usuário a ser traduzido quando o parâmetro ui_locales não é fornecido.
1. Verifique se a sua política tem "Personalização de idiomas" habilitada nas instruções anteriores.
2. A partir da folha **Editar política**, selecione **Personalização de idiomas**.
3. Você será direcionado para a folha **idiomas com suporte**.  Aqui, você pode selecionar **Adicionar idioma**.
4. Selecione todos os idiomas que você gostaria de ter suporte.  

>[!NOTE]
>Se um parâmetro ui_locales não foi fornecido, a página será traduzida como para o idioma do navegador do cliente apenas se o idioma estiver na lista
>

5. Clique em **OK** na parte inferior
6. Feche a folha **Personalização de idiomas** e **salve** a sua política.

## <a name="customize-your-strings"></a>Como personalizar suas cadeias de caracteres
A "personalização de idiomas" permite que você personalize qualquer cadeia de caracteres em seu percurso do usuário.
1. Verifique se a sua política tem "Personalização de idiomas" habilitada nas instruções anteriores.
2. A partir da folha **Editar política**, selecione **Personalização de idiomas**.
3. No menu de navegação à esquerda, selecione **Baixar conteúdo**.
4. Selecione a página que você deseja editar.
5. No menu suspenso, selecione o idioma que você deseja editar.
6. Clique em **Download**. 

Essas etapas fornecem um arquivo JSON que você pode usar para começar a editar suas cadeias de caracteres.

### <a name="changing-any-string-on-the-page"></a>Alteração de qualquer cadeia de caracteres na página
1. Abra o arquivo JSON baixado das instruções anteriores em um editor de JSON.
2. Localize o elemento que você deseja alterar.  Você pode encontrar o `StringId` da cadeia de caracteres que você está procurando, ou procurar o `Value` que você deseja alterar.
3. Atualize o atributo `Value` com o que você deseja exibir.
4. Salve o arquivo e carregue suas alterações.

### <a name="changing-extension-attributes"></a>Alteração dos atributos de extensão
Se você deseja alterar a cadeia de caracteres para um atributo do usuário personalizado, ou adicionar uma ao JSON, faça isto no seguinte formato:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>Se você precisar substituir uma cadeia de caracteres, certifique-se de que o valor `Override` esteja definido para `true`.  Se o valor não mudou, a entrada será ignorada. 
>

Substitua `<ExtensionAttribute>` pelo nome do seu atributo de usuário personalizado.  
Substitua `<ExtensionAttributeValue>` por uma cadeia de caracteres nova a ser exibida.

### <a name="using-localizedcollections"></a>Uso do LocalizedCollections
Se você deseja fornecer uma lista de conjunto de valores de respostas, você precisa criar um `LocalizedCollections`.  Um `LocalizedCollections` é uma matriz de pares de `Name` e `Value`.  O `Name` é o que é exibido e o `Value` é o que é retornado na declaração.  Para adicionar um `LocalizedCollections`, ele tem o seguinte formato:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": false,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```
>[!IMPORTANT]
>Se você precisar substituir uma cadeia de caracteres, certifique-se de que o valor `Override` esteja definido para `true`.  Se o valor não mudou, a entrada será ignorada. 
>

* `ElementId` é o atributo do usuário, para o qual `LocalizedCollections` é uma resposta
* `Name` é o valor mostrado ao usuário
* `Value` é retornado na declaração quando essa opção está selecionada

### <a name="upload-your-changes"></a>Carregamento das suas alterações
1. Depois de concluir as alterações para o arquivo JSON, navegue de volta para seu locatário B2C.
2. A partir da folha **Editar política**, selecione **Personalização de idiomas**.
3. No menu de navegação à esquerda, selecione **Carregar conteúdo**.
4. Selecione a página na qual você deseja carregar suas alterações.
5. Se você quiser carregar um idioma para o qual você forneceu anteriormente um JSON, é necessário excluir a entrada anterior.  Você pode excluí-los clicando o `...` à direita deste idioma e selecionar **Excluir**.
6. Clique em **Adicionar** no canto superior esquerdo.
7. Selecione o idioma do arquivo JSON.
8. Clique o botão da pasta à direita e navegue até o arquivo JSON.
9. Clique o botão **Carregar** na parte inferior da folha.
10. Volte para a folha **Editar política** e clique em **Salvar**.

## <a name="additional-information"></a>Informações adicionais
### <a name="recommendations-for-language-customization"></a>Recomendações para "Personalização de idiomas"
Recomendamos colocar somente nas entradas dos recursos de idiomas para cadeias de caracteres que você deseja substituir explicitamente.  Podemos impor um limite de tamanho para o arquivo que é compilado sem todas as suas traduções de JSON.  Se os arquivos ficarem muito grandes, eles afetarão o desempenho do seu percurso do usuário.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>Rótulos de personalização de IU da página são removidos após a "personalização de idiomas" estar habilitada
Quando você habilita a "personalização de idiomas", as edições anteriores dos rótulos usando as personalizações da IU da página são removidas, exceto os atributos de usuário personalizados.  Essa alteração é feita para evitar conflitos onde você pode editar suas cadeias de caracteres.  Você pode continuar a alterar os rótulos e outras cadeias de caracteres, carregando recursos de idiomas na "personalização de idiomas".
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>A Microsoft está comprometida em fornecer as traduções mais atualizadas
Vamos aprimorar continuamente as traduções e mantê-las em conformidade para você.  Identificaremos as alterações na terminologia global e faremos as atualizações que funcionarão diretamente no seu percurso do usuário.
### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda
Não há suporte para idiomas da direita para a esquerda, se você precisar desse recurso, vote para ele nos [Comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traduções de provedor de identidade social
Fornecemos o parâmetro OIDC ui_locales para logons sociais, mas eles não são respeitados por alguns provedores de identidade social, incluindo o Facebook e o Google. 
### <a name="browser-behavior"></a>Comportamento do navegador
Ambos o Chrome e o Firefox solicitam para o idioma definido e, caso seja um idioma com suporte, ele será exibido antes do padrão.  Atualmente, o Edge não solicita um idioma e exibe diretamente o idioma padrão.
### <a name="known-issues"></a>Problemas conhecidos
* O carregamento de recursos de idiomas para a página MFA em uma política de Editar perfil não está disponível no momento.
* Não são gerados `LocalizedCollections` para valores quando são solicitados pelo tipo de resposta
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>E se eu desejar um idioma que não tem suporte?
Planejamos fornecer uma extensão desse recurso, que permite carregar um recurso JSON para "idiomas personalizados".  O recurso permite que você especifique o código de idioma e o nome para qualquer idioma e forneça *todas as* as traduções para aquele idioma.  Se você precisar desse recurso, envie-nos o cenário para [ aadb2cpreview@microsoft.com ](mailto:aadb2cpreview@microsoft.com).  

### <a name="what-languages-are-supported"></a>Quais são os idiomas com suporte?

| idioma              | Código de idioma |
|-----------------------|---------------|
| Bangla                | bn            |
| Tcheco                 | cs            |
| Dinamarquês                | da            |
| Alemão                | de            |
| Grego                 | el            |
| Inglês               | en            |
| Espanhol               | es            |
| Finlandês               | fi            |
| Francês                | fr            |
| Guzerate              | gu            |
| Híndi                 | hi            |
| Croata              | hr            |
| Húngaro             | hu            |
| Italiano               | it            |
| Japonês              | ja            |
| Kannada               | kn            |
| Coreano                | ko            |
| Malaiala             | ml            |
| Marati               | mr            |
| Malaio                 | ms            |
| Norueguês Bokmal      | nb            |
| Holandês                 | nl            |
| Punjabi               | pa            |
| Polonês                | pl            |
| Português - Brasil   | pt-br         |
| Português - Portugal | pt-pt         |
| Romeno              | ro            |
| Russo               | ru            |
| Eslovaco                | sk            |
| Sueco               | sv            |
| Tâmil                 | ta            |
| Télugo                | te            |
| Tailandês                  | th            |
| Turco               | tr            |
| Chinês - Simplificado  | zh-hans       |
| Chinês - Tradicional | zh-hant       |
