---
title: "Como os aplicativos são adicionados ao Active Directory do Azure."
description: "Este artigo descreve como os aplicativos são adicionados a uma instância do Active Directory do Azure."
services: active-directory
documentationcenter: 
author: shoatman
manager: kbrint
editor: 
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: shoatman
ms.custom: aaddev
ms.openlocfilehash: 6ffcfcb7ed071a12b0b3495ad534fd00f6d6ad99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que os aplicativos são adicionados ao AD do Azure
Uma das coisas inicialmente confusas ao exibir uma lista de aplicativos na sua instância do Active Directory do Azure é compreender de onde vieram os aplicativos e por que eles existem.  Este artigo fornece uma visão geral de alto nível de como os aplicativos são representados no diretório, além de contexto que irá ajudá-lo a compreender como um aplicativo passou a estar em seu diretório.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Que serviços do AD do Azure fornece aos aplicativos?
Os aplicativos são adicionados ao AD do Azure para aproveitar um ou mais serviços que ele fornece.  Esses serviços incluem:

* Autenticação e autorização de aplicativo
* Autenticação e autorização de usuário
* SSO (logon único) usando federação ou sua senha
* Provisionamento do usuário e sincronização
* Controle de acesso baseado em função; use o diretório para definir funções de aplicativo para executar verificações de autorização baseadas em função em um aplicativo.
* Serviços de autorização oAuth (usados pelo Office 365 e outros aplicativos Microsoft para autorizar o acesso a APIs/recursos).
* Publicação de aplicativo e proxy; publicar um aplicativo na Internet a partir de uma rede privada

## <a name="how-are-applications-represented-in-the-directory"></a>Como os aplicativos são representados no diretório?
Aplicativos são representados no AD do Azure usando dois objetos: um objeto de aplicativo e um objeto de entidade de serviço.  Há um objeto de aplicativo registrado no diretório "home"/"proprietário" ou "publicação", além de um ou mais objetos de entidade de serviço representando o aplicativo em cada diretório em que ele atua.  

O objeto de aplicativo descreve o aplicativo ao AD do Azure (o serviço multilocatário) e pode incluir qualquer um dos seguintes itens: (*Observação*: esso não é uma lista completa.)

* Nome, logotipo e publicador
* Segredos (chaves simétricas e/ou assimétricas usadas para autenticar o aplicativo)
* Dependências de API (oAuth)
* APIs/recursos/escopos publicados (oAuth)
* Funções de aplicativo (RBAC)
* Metadados SSO e configuração (SSO)
* Configuração e metadados de provisionamento de usuário
* Configuração e metadados de proxy

A entidade de serviço é um registro do aplicativo em cada diretório, onde o aplicativo atua incluindo seu diretório base.  A entidade de serviço:

* Faz referência a um objeto de aplicativo por meio da propriedade de id do aplicativo
* Registra atribuições de função de aplicativo de usuários e grupos locais
* Registra as permissões de administrador e usuário local concedidas ao aplicativo
  * Por exemplo: permissão para que o aplicativo acesse o email de um usuário específico
* Registra políticas locais, incluindo a política de acesso condicional
* Registra configurações alternativas locais para um aplicativo
  * Declara regras de transformação
  * Mapeamentos de atributos (provisionamento do usuário)
  * Funções de aplicativo específicas de locatário (se o aplicativo dá suporte a funções personalizadas)
  * Nome/logotipo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Um diagrama de objetos de aplicativo e entidades de serviço entre diretórios
![Um diagrama que ilustra como objetos de aplicativo e entidades de serviço coexistem com instâncias do AD do Azure.][apps_service_principals_directory]

Como você pode ver no diagrama acima.  A Microsoft mantém dois diretórios internamente (à esquerda), que utiliza para publicar aplicativos.

* Um para Microsoft Apps (diretório de serviços da Microsoft)
* Um para aplicativos de terceiros pré-integrados (diretório de galeria de aplicativos)

É exigido que editores/fornecedores de aplicativos que se integram com o AD do Azure tenham um diretório de publicação.  (algum diretório SAAS).

Os aplicativos que você adiciona por conta própria incluem:

* Aplicativos desenvolvidos por você (integrados ao AAD)
* Aplicativos conectados por você para logon único
* Aplicativos publicados por você usando o proxy de aplicativo do AD do Azure.

### <a name="a-couple-of-notes-and-exceptions"></a>Algumas observações e exceções
* Nem todas as entidades de serviço apontam de volta para objetos de aplicativo.  Hein? Quando o AD do Azure foi originalmente criado, os serviços fornecidos aos aplicativos eram muito mais limitados e a entidade de serviço foi suficiente para estabelecer uma identidade de aplicativo.  A entidade de serviço original era mais próxima, em termos de formato, da conta de serviço do Active Directory do Windows Server.  Por esse motivo, ainda é possível criar entidades de serviço usando o PowerShell do AD do Azure sem primeiro criar um objeto de aplicativo.  A Graph API requer um objeto de aplicativo antes de criar uma entidade de serviço.
* Nem todas as informações descritas acima estão expostas programaticamente.  Os itens a seguir estão disponíveis apenas na interface do usuário:
  * Declara regras de transformação
  * Mapeamentos de atributos (provisionamento do usuário)
* Para obter informações mais detalhadas sobre a entidade de serviço e objetos de aplicativo, consulte a documentação de referência de Graph API REST do AD do Azure.  *Dica*: a documentação da Graph API do AD do Azure é o mais parecida possível com uma referência a esquema do AD do Azure que está disponível no momento.  
  * [Aplicativo](https://msdn.microsoft.com/library/azure/dn151677.aspx)
  * [Entidade de serviço](https://msdn.microsoft.com/library/azure/dn194452.aspx)

## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Como os aplicativos são adicionados à minha instância do AD do Azure?
Há muitas maneiras pelas quais um aplicativo pode ser adicionado ao AD do Azure:

* Adicionar um aplicativo a partir de [Galeria de aplicativos do Active Directory do Azure](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Inscreva-se em um Aplicativo de Terceiros integrado ao Azure Active Directory (por exemplo: [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
  * Durante a inscrição/entrada será solicitado aos usuários que concedam permissão ao aplicativo para acessar seus perfis, além de outras permissões.  A primeira pessoa a dar consentimento faz com que uma entidade de serviço que representa o aplicativo seja adicionada ao diretório.
* Inscrever-se/entrar nos serviços online da Microsoft, como o [Office 365](http://products.office.com/)
  * Quando você se inscrever no Office 365 ou iniciar uma avaliação, uma ou mais entidades de serviço serão criadas no diretório que representa os vários serviços usados para fornecer toda a funcionalidade associada ao Office 365.
  * Alguns serviços do Office 365 como o SharePoint criam entidades de serviço em uma base contínua para permitir a comunicação segura entre componentes, incluindo fluxos de trabalho.
* Para adicionar um aplicativo que você está desenvolvendo no Portal de Gerenciamento do Azure, consulte: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Adicione um aplicativo que você está desenvolvendo com o Visual Studio, consulte:
  * [Métodos de autenticação do ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
  * [Serviços conectados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Adicionar um aplicativo para usar o [Proxy de aplicativo do AD do Azure](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Conectar a um aplicativo para logon único usando SAML ou SSO de senha
* Muitas outras, incluindo várias experiências de desenvolvedor no Azure e/ou experiências no Gerenciador de APIs entre centros de desenvolvedores

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicativos à minha instância do AD do Azure?
Somente os administradores globais podem:

* Adicionar aplicativos da Galeria de Aplicativos do AD do Azure (aplicativos de terceiros pré-integrados)
* Publicar um aplicativo usando o proxy de aplicativo do AD do Azure

Todos os usuários em seu diretório têm direitos para adicionar aplicativos que estão desenvolvendo e critério sobre com quais aplicativos eles compartilham/dão acesso aos seus dados organizacionais.  *Lembre-se que inscrição/entrada pelo usuário em um aplicativo e concessão de permissões podem resultar na criação de uma entidade de serviço.*

Isso pode inicialmente soar preocupante, mas mantenha o seguinte em mente:

* Aplicativos foram capazes de aproveitar o Windows Server Active Directory para autenticação do usuário por muitos anos sem exigir que o aplicativo fosse registrado/gravado no diretório.  Agora a organização terá visibilidade aprimorada de exatamente quantos aplicativos estão usando o diretório e para que finalidade.
* Não é necessário que o processo de publicação/registro do aplicativo seja realizado pelo administrador.  Com os serviços de Federação do Active Directory, era provável que um administrador precisasse adicionar um aplicativo como uma terceira parte confiável em nome dos desenvolvedores.  Agora, os desenvolvedores podem usar o autoatendimento.
* Usuários se inscrevendo/entrando em aplicativos usando as contas de suas organizações para fins comerciais é algo positivo.  Se eles subsequentemente deixarem a organização, perderão o acesso à sua conta no aplicativo que estavam utilizando.
* Ter um registro de quais dados foram compartilhados com qual o aplicativo é algo bom.  Os dados estão mais transportáveis do que nunca e é útil ter um registro claro de quem compartilhou quais dados com quais aplicativos.
* Aplicativos que usam o AD do Azure para oAuth decidem exatamente quais permissões que os usuários são capazes de conceder a aplicativos e quais permissões exigem o consentimento de um administrador.  Nem é preciso dizer que apenas administradores podem consentir com permissões mais significativas e em escopos maiores.
* Os usuários adicionando e permitindo que os aplicativos acessem seus dados são eventos auditados, de modo que você pode exibir os relatórios de auditoria no Portal de Gerenciamento do Azure para determinar como um aplicativo foi adicionado ao diretório.

**Observação:** *a própria Microsoft esteve operando por muitos meses usando a configuração padrão.*

Com tudo isso, é possível impedir que os usuários em seu diretório adicionem aplicativos e exerçam critérios sobre quais informações compartilham com aplicativos, modificando a configuração Diretório no Portal de Gerenciamento do Azure.  A configuração a seguir pode ser acessada dentro do Portal de Gerenciamento do Azure na guia "Configurar" do diretório.

![Uma captura de tela da interface do usuário para definir as configurações do aplicativo integrado][app_settings]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como adicionar aplicativos ao AD do Azure e como configurar serviços para aplicativos.

* Desenvolvedores: [Saiba como integrar um aplicativo ao AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Desenvolvedores: [Examinar o código de exemplo para aplicativos integrados ao Azure Active Directory no GitHub](https://github.com/AzureADSamples)
* Desenvolvedores e profissionais de TI: [Leia a documentação da API REST para a Graph API do Active Directory do Azure](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Profissionais de TI: [Saiba como usar aplicativos pré-integrados do Active Directory do Azure a partir da Galeria de Aplicativos](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Profissionais de TI: [Localize tutoriais para a configuração de determinados aplicativos pré-integrados](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Profissionais de TI: [Saiba como publicar um aplicativo usando o Proxy do aplicativo do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Consulte também
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](../active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:../media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
