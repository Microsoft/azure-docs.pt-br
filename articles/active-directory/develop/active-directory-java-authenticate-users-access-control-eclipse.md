---
title: Como usar o Controle de Acesso (Java) | Microsoft Docs
description: Aprenda a desenvolver e usar o controle de acesso com Java no Azure.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: 698403d181e1fee09bb4692290c92203ded97ba4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Como autenticar usuários da Web com o Serviço de Controle de Acesso do Azure usando o Eclipse
Este guia mostrará como usar o ACS (Serviço de Controle de Acesso) do Azure dentro do Kit de Ferramentas do Azure para Eclipse. Para obter mais informações sobre o ACS, consulte a seção [Próximas etapas](#next_steps) .

> [!NOTE]
> O Filtro de Controle dos Serviços de Acesso do Azure é uma visualização de tecnologia da comunidade. Como software de pré-lançamento, a Microsoft não dá suporte formal a ele.
> 
> 

## <a name="what-is-acs"></a>O que é o ACS?
A maioria dos desenvolvedores não são especialistas em identidade e geralmente não querem gastar tempo desenvolvendo a autenticação e mecanismos de autenticação para seus aplicativos e serviços. O ACS é um serviço do Azure que fornece uma maneira fácil de autenticar os usuários que precisam acessar seus aplicativos e serviços da web sem a necessidade de fatorar lógica de autenticação complexa em seu código.

Os seguintes recursos estão disponíveis no ACS:

* Integração com o Windows Identity Foundation (WIF).
* Suporte para IPs (provedores de identidade) populares da Web incluindo o Windows Live ID, o Google, o Yahoo e o Facebook.
* Suporte para o Serviço de Federação do Active Directory (AD FS) 2.0.
* Um serviço de gerenciamento baseado em Open Data Protocol (OData) que fornece acesso programático para configurações do ACS.
* Um Portal de Gerenciamento que permite acesso administrativo para as configurações do ACS.

Para obter mais informações sobre o ACS, consulte [Serviço de Controle de Acesso 2.0][Access Control Service 2.0].

## <a name="concepts"></a>Conceitos
O ACS do Azure baseia-se na identidade baseada em declarações - uma abordagem consistente para a criação de mecanismos de autenticação para aplicativos executados localmente ou na nuvem. A identidade baseada em declarações fornece uma maneira comum para aplicativos e serviços obterem as informações da identidade necessárias sobre os usuários de sua organização, em outras organizações e na Internet.

Para concluir as tarefas deste guia, você precisa compreender os seguintes conceitos:

**Cliente** - no contexto deste guia de instruções, esse é um navegador que está tentando obter acesso ao seu aplicativo Web.

**Aplicativo de terceira parte confiável (RP)** - um aplicativo RP é um site ou um serviço Web que terceiriza a autenticação para uma autoridade externa. No jargão de identidade, dizemos que o RP confia essa autoridade. Este guia explica como configurar seu aplicativo para relação de confiança de ACS.

**Token** - um token é uma coleção de dados de segurança que geralmente são emitidos após a autenticação bem-sucedida de um usuário. Ele contém um conjunto de *declarações*, atributos do usuário autenticado. Uma declaração pode representar um nome de usuário, um identificador de uma função à qual um usuário pertence, a idade de um usuário e assim por diante. Um token é geralmente assinado digitalmente, o que significa que pode sempre ser originado de volta ao seu emissor e seu conteúdo não pode ser violado. Um usuário obtém acesso a um aplicativo RP apresentando um token válido emitido por uma autoridade na qual o aplicativo RP confia.

**IP (Provedor de Identidade)** - um IP é uma autoridade que autentica as identidades dos usuários e emite tokens de segurança. O trabalho real de emissão de tokens é implementado por meio de um serviço especial chamado STS (Serviço de Token de Segurança). Exemplos típicos de IPs incluem o Windows Live ID, o Facebook, os repositórios de usuários de negócios (como o Active Directory) e assim por diante.
Quando o ACS está configurado para confiar em um IP, o sistema irá aceitar e validar os tokens emitidos por esse IP. O ACS pode confiar em vários IPs ao mesmo tempo, o que significa que quando o seu aplicativo confia no ACS, você pode oferecer instantaneamente seu aplicativo a todos os usuários autenticados de todos os IPs nos quais o ACS confia em seu nome.

**FP (Provedor de Federação)** - os IPs têm conhecimento direto dos usuários e os autenticam usando suas credenciais e emitem declarações sobre o que conhecem sobre eles. Um FP (provedor de federação) é um tipo diferente de autoridade: em vez de autenticar usuários diretamente, ele atua como um intermediário e agencia a autenticação entre um RP e um ou mais IPs. Tanto IPs quanto FPs emitem tokens de segurança, por isso ambos usam os STS (Serviços de Token de Segurança). O ACS é um FP.

**Mecanismo de Regras do ACS** - a lógica usada para transformar os tokens recebidos de IPs confiáveis em tokens com o objetivo de serem consumidos pelo RP é codificada no formulário de regras de transformação de declarações simples. O ACS apresenta um mecanismo de regra que se encarrega de aplicar qualquer lógica de transformação que você especifique para seu RP.

**Namespace do ACS** - um namespace é uma partição de nível superior do ACS que você usa para organizar suas configurações. Um namespace espera uma lista de IPs que você confia, os aplicativos de RPs a serem atendidos, as regras que você espera que o mecanismo de regra aplique ao processar tokens recebidos e assim por diante. Um namespace expõe vários pontos de extremidade que serão usados pelo aplicativo e pelo desenvolvedor para fazer o ACS executar sua função.

A figura a seguir mostra como a autenticação do ACS funciona com um aplicativo da Web:

![Diagrama de fluxo do ACS][acs_flow]

1. O cliente (neste caso, um navegador) solicita uma página do RP.
2. Como a solicitação ainda não foi autenticada, o RP redireciona o usuário para a autoridade que ele confia, que é o ACS. O ACS apresenta ao usuário a opção de IPs que foram especificados para essa RP. O usuário seleciona o IP apropriado.
3. O cliente vai para a página de autenticação do IP e solicita ao usuário para fazer logon.
4. Depois que o cliente é autenticado (por exemplo, a identidade que credenciais foram inseridas), o IP emite um token de segurança.
5. Depois de emitir um token de segurança, o IP redireciona o cliente para o ACS, e o cliente envia o token de segurança emitido pelo IP ao ACS.
6. O ACS valida o token de segurança emitido por IP, insere a declaração de identidade nesse token para o mecanismo de regras do ACS, calcula as declarações de identidade de saída e emite um novo token de segurança que contém essas declarações de saída.
7. O ACS redireciona o cliente para o RP. O cliente envia o novo token de segurança emitido pelo ACS ao RP. O RP valida a assinatura no token de segurança emitido pelo ACS, valida as declarações nesse token e retorna a página que foi solicitada originalmente.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir as tarefas deste guia, você precisará do seguinte:

* Um JDK (Java Developer Kit) versão 1.6 ou posterior.
* Um IDE do Eclipse para desenvolvedores do Java EE, Indigo ou posterior. Isso pode ser baixado em <http://www.eclipse.org/downloads/>. 
* Uma distribuição de um servidor web baseado em Java ou servidor de aplicativo, como o Apache Tomcat, o GlassFish, o Servidor de Aplicativo JBoss ou o Jetty.
* Uma assinatura do Azure, que pode ser adquirida em <http://www.microsoft.com/windowsazure/offers/>.
* O Kit de Ferramentas do Azure para Eclipse, versão de abril de 2014 ou posterior. Para saber mais, confira [Instalação do Kit de Ferramentas do Azure para Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Um certificado X.509 para uso com o aplicativo. Você precisará desse certificado no certificado público (.cer) e no formato Troca de Informações Pessoais (.PFX). (As opções para criação desse certificado serão descritas posteriormente neste tutorial).
* Familiaridade com emulador de computação do Azure e com as técnicas de implantação discutidas em [Criando um aplicativo Hello World para o Azure no Eclipse (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Criar um namespace do ACS
Para começar a usar o ACS (Access Control Service) no Azure, você deve criar um namespace do ACS. O namespace fornece um escopo exclusivo para resolver os recursos do ACS a partir do seu aplicativo.

1. Faça logon no [Portal de Gerenciamento do Azure][Azure Management Portal].
2. Clique em **Active Directory**. 
3. Para criar um novo namespace do Controle de Acesso, clique em **Novo**, em **Serviços de Aplicativos**, em **Controle de Acesso** e em **Criação Rápida**. 
4. Insira um nome para o namespace. O Azure verifica se o nome é exclusivo.
5. Selecione a região na qual o namespace é usado. Para obter o melhor desempenho, use a região na qual você está implantando o seu aplicativo.
6. Se você tiver mais de uma assinatura, selecione a assinatura que você deseja usar para o namespace do ACS.
7. Clique em **Criar**.

O Azure cria e ativa o namespace. Aguarde até que o status do novo namespace seja **Ativo** para continuar. 

## <a name="add-identity-providers"></a>Adicionar provedores de identidade
Nesta tarefa, você adicionará os IPs a serem usados com seu aplicativo RP para autenticação. Para fins de demonstração, esta tarefa mostra como adicionar o Windows Live como um IP, mas você poderá usar qualquer um dos IPs listados no Portal de Gerenciamento ACS.

1. No [Portal de Gerenciamento do Azure][Azure Management Portal], clique em **Active Directory**, selecione um namespace de Controle de Acesso e, em seguida, clique em **Gerenciar**. O Portal de Gerenciamento do ACS é aberto.
2. No painel de navegação esquerdo do Portal de Gerenciamento do ACS, clique em **Provedores de Identidade**.
3. O Windows Live ID é habilitado por padrão e não pode ser excluído. Para fins deste tutorial, apenas o Windows Live ID é usado. Nessa tela, no entanto, é onde você poderia adicionar outros IPs, clicando no botão **Adicionar** .

O Windows Live ID agora está habilitado como um IP para o seu namespace do ACS. Em seguida, especifique seu aplicativo web Java (a ser criado mais tarde) como um RP.

## <a name="add-a-relying-party-application"></a>Adicionar um aplicativo de terceira parte confiável
Nesta tarefa, você configura o ACS para reconhecer seu aplicativo web Java como um aplicativo RP válido.

1. No Portal de Gerenciamento do ACS, clique em **Aplicativos de terceiras partes confiáveis**.
2. Na página **Aplicativos de Terceira Parte Confiável**, clique em **Adicionar**.
3. Na página **Adicionar aplicativos de terceiras partes confiáveis** , faça o seguinte:
   
   1. Em **Nome**, digite o nome do RP. Para fins desse tutorial, digite **Aplicativo Web do Azure**.
   2. Em **Modo**, selecione **Inserir as configurações manualmente**.
   3. Em **Realm**, digite o URI ao qual o token de segurança emitido pelo ACS se aplica. Para esta tarefa, digite **http://localhost:8080/**.
      ![Realm de terceira parte confiável para uso no emulador de computação][relying_party_realm_emulator]
   4. Em **URL de Retorno,** digite a URL para a qual o ACS retorna o token de segurança. Para esta tarefa, digite **http://localhost:8080/MyACSHelloWorld/index.jsp**
      ![URL de retorno da terceira parte confiável para uso no emulador de computação][relying_party_return_url_emulator]
   5. Aceite os valores padrão no restante dos campos.
4. Clique em **Salvar**.

Você agora configurou com êxito seu aplicativo Web Java, quando executado no emulador de computação do Azure (em http://localhost:8080/), para ser um RP no namespace do ACS. Em seguida, crie as regras que o ACS usa para processar declarações para o RP.

## <a name="create-rules"></a>Criar regras
Nesta tarefa, você define as regras que orientam como as declarações são passadas dos IPs para seu RP. Para o objetivo deste guia, iremos configurar o ACS simplesmente para copiar os tipos e valores de declarações de entrada diretamente no token de saída, sem filtrá-los nem modificá-los.

1. Na página principal do Portal de Gerenciamento do ACS, clique em **Grupos de Regras**.
2. Na página **Grupos de Regras**, clique em **Grupo de Regras Padrão para o Aplicativo Web do Azure**.
3. Na página **Editar Grupo de Regras**, clique em **Gerar**.
4. Na página **Gerar Regras: Grupo de Regras Padrão para o Aplicativo Web do Azure**, verifique se Windows Live ID está selecionado e clique em **Gerar**.    
5. Na página **Editar Grupo de Regras**, clique em **Salvar**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Carregar um certificado para seu namespace do ACS
Nesta tarefa, você carrega um certificado PFX que será usado para assinar solicitações de token criadas pelo seu namespace do ACS.

1. Na página principal do Portal de Gerenciamento do ACS, clique em **Certificados e Chaves**.
2. Na página **Certificados e Chaves**, clique em **Adicionar** acima de **Autenticação de Tokens**.
3. Na página **Adicionar Certificado ou Chave de Autenticação de Token** :
   1. Na seção **Usado para**, clique em **Aplicativo de Terceira Parte Confiável** e selecione **Aplicativo Web do Azure** (que você configurou previamente com o nome do seu aplicativo de terceira parte confiável).
   2. Na seção **Tipo**, selecione **Certificado X.509**.
   3. Na seção **Certificado** , clique no botão procurar e navegue até o arquivo de certificado x.509 que você deseja usar. Esse será um arquivo .PFX. Selecione o arquivo, clique em **Abrir** e digite a senha do certificado na caixa de texto **Senha**. Observe que, para fins de teste, você deve usar um certificado autoassinado. Para criar um certificado autoassinado, use o botão **Novo** na caixa de diálogo **Biblioteca de Filtros do ACS** (descrita posteriormente) ou use o utilitário **encutil.exe** no [site do projeto][project website] do Kit de Início do Azure para Java.
   4. Verifique se **Tornar Primário** está marcado. Sua página **Adicionar Chave ou Certificado de Autenticação de Tokens** deve ser semelhante à exibida a seguir.
       ![Adicionar certificado de autenticação de tokens][add_token_signing_cert]
   5. Clique em **Salvar** para salvar suas configurações e fechar a página **Adicionar Certificado ou Chave de Autenticação de Tokens**.

Em seguida, revise as informações na página Integração de Aplicativos e copie o URI do qual você precisará para configurar seu aplicativo Web Java para usar o ACS.

## <a name="review-the-application-integration-page"></a>Revisar a página Integração de Aplicativos
Você pode encontrar todas as informações e o código necessário para configurar seu aplicativo Web Java (o aplicativo RP) para trabalhar com o ACS na página Integração de Aplicativos do Portal de Gerenciamento do ACS. Você precisará dessas informações ao configurar seu aplicativo web Java para autenticação federada.

1. No Portal de Gerenciamento ACS, clique em **Integração de aplicativos**.  
2. Na página **Integração de Aplicativos**, clique em **Páginas de Logon**.
3. Na página **Integração da Página de Logon**, clique em **Aplicativo Web do Azure**.

Na página **Integração da Página de Logon: Aplicativo Web do Azure**, a URL listada na **Opção 1: Link para uma página de logon hospedada pelo ACS** será usada em seu aplicativo Web Java. Você precisará desse valor ao adicionar a biblioteca de Filtros de Serviços do Controle de Acesso para seu aplicativo Java.

## <a name="create-a-java-web-application"></a>Criar um aplicativo web Java
1. No Eclipse, no menu, clique em **Arquivo**, clique em **Novo** e clique em **Projeto Web Dinâmico**. (Se você não vir o **Projeto Web Dinâmico** listado como um projeto disponível depois de clicar em **Arquivo**, **Novo**, faça o seguinte: clique em **Arquivo**, clique em **Novo**, clique em **Projeto**, expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**.) Para fins deste tutorial, nomeie o projeto **MyACSHelloWorld**. (Certifique-se de usar esse nome, as etapas subsequentes deste tutorial esperam que seu arquivo WAR seja nomeado MyACSHelloWorld). Sua tela será semelhante à seguinte:
   
    ![Criar um Projeto Hello World para exemplo do ACS][create_acs_hello_world]
   
    Clique em **Concluir**.
2. Na exibição do Explorador de Projeto do Eclipse, expanda **MyACSHelloWorld**. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.
3. Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo como **index.jsp**. Mantenha a pasta pai como MyACSHelloWorld/WebContent, conforme mostrado a seguir:
   
    ![Adicionar um arquivo JSP para exemplo do ACS][add_jsp_file_acs]
   
    Clique em **Avançar**.
4. Na caixa de diálogo **Selecionar Modelo JSP**, selecione **Novo Arquivo JSP (html)** e clique em **Concluir**.
5. Quando o arquivo index.jsp for aberto no Eclipse, adicione texto para exibir **Hello ACS World!** dentro do elemento existente `<body>`. Seu conteúdo `<body>` atualizado deve ser semelhante ao seguinte:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Salve o index.jsp.

## <a name="add-the-acs-filter-library-to-your-application"></a>Adicionar a Biblioteca de Filtros do ACS ao seu aplicativo
1. No explorador de Projeto do Eclipse, clique com o botão direito do mouse em **MyACSHelloWorld**, clique em **Caminho de Compilação** e em **Configurar Caminho de Compilação**.
2. Na caixa de diálogo **Caminho de Compilação do Java**, clique na guia **Bibliotecas**.
3. Clique em **Adicionar Biblioteca**.
4. Clique em **Filtro dos Serviços de Controle de Acesso do Azure (da MS Open Tech)** e clique em **Avançar**. A caixa de diálogo **Filtro do Access Control Services do Azure** é exibida.  (O campo **Localização** pode ter um caminho diferente, dependendo de onde você instalou o Eclipse, e o número da versão pode ser diferente, dependendo das atualizações do software.)
   
    ![Adicionar a biblioteca de filtros do ACS][add_acs_filter_lib]
5. Usando um navegador aberto na página **Integração da Página de Logon** do Portal de Gerenciamento, copie a URL listada no campo **Opção 1: link para uma página de logon hospedada pelo ACS** e cole-a no campo **Ponto de Extremidade de Autenticação do ACS** na caixa de diálogo do Eclipse.
6. Usando um navegador aberto na página **Editar Aplicativo de Terceira Parte Confiável** do Portal de Gerenciamento, copie a URL listada no campo **Realm** e cole-a no campo **Realm de Terceira Parte Confiável** na caixa de diálogo do Eclipse.
7. Na seção **Segurança** da caixa de diálogo Eclipse, se desejar usar um certificado existente, clique em **Procurar**, navegue até o certificado que você deseja usar, selecione-o e clique em **Abrir**. Ou, se você desejar criar um novo certificado, clique em **Novo** para exibir a caixa de diálogo **Novo Certificado**, especifique a senha, o nome do arquivo .cer e o nome do arquivo .pfx para o novo certificado.
8. Marque **Inserir o certificado no arquivo WAR**. A incorporação do certificado dessa maneira o inclui em sua implantação sem que você precise adicioná-lo manualmente como um componente. (Caso contrário, se você precisar armazenar seu certificado externamente ao arquivo WAR, você poderá adicionar o certificado como um componente da função e desmarcar **Inserir o certificado no arquivo WAR**.)
9. [Opcional] Mantenha a opção **Requer conexões HTTPS** marcada. Se você definir essa opção, precisará acessar seu aplicativo usando o protocolo HTTPS. Se você não desejar requerer conexões HTTPS, desmarque essa opção.
10. Para uma implantação no emulador de computação, suas configurações do **Filtro do ACS do Azure** serão semelhantes ao seguinte.
    
    ![Configurações do Filtro do ACS do Azure para uma implantação no emulador de computação][add_acs_filter_lib_emulator]
11. Clique em **Concluir**.
12. Clique em **Sim** quando for apresentada uma caixa de diálogo informando que será criado um arquivo web.xml.
13. Clique em **OK** para fechar o diálogo **Caminho de Compilação Java**.

## <a name="deploy-to-the-compute-emulator"></a>Implantar no emulador de computação
1. No Explorador de Projeto do Eclipse, clique com o botão direito do mouse em **MyACSHelloWorld**, clique em **Azure** e em **Pacote para Azure**.
2. Para **Nome do Projeto**, digite **MyAzureACSProject** e clique em **Avançar**.
3. Selecione um JDK e o servidor de aplicativo. (Essas etapas são abordadas detalhadamente no tutorial [Criando um aplicativo Hello World para o Azure no Eclipse (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).
4. Clique em **Concluir**.
5. Clique no botão **Executar no emulador do Azure** .
6. Depois de iniciar seu aplicativo web Java no emulador de computação, feche todas as instâncias do seu navegador (para que nenhuma sessão atual do navegador interfira em seu teste de logon do ACS).
7. Execute seu aplicativo ao abrir <http://localhost:8080/MyACSHelloWorld/> em seu navegador (ou <https://localhost:8080/MyACSHelloWorld/> se tiver selecionado **Requer conexões HTTPS**). Um logon do Windows Live ID deve ser solicitado e você deverá ser levado à URL de retorno especificada para seu aplicativo de terceira parte confiável.
8. Ao concluir a exibição do aplicativo, clique no botão **Redefinir Emulador do Azure** .

## <a name="deploy-to-azure"></a>Implantar no Azure
Para implantar no Azure, você precisará alterar o realm da terceira parte confiável e retornar a URL para seu namespace do ACS.

1. No Portal de Gerenciamento do Azure, na página **Editar Aplicativo da Terceira Parte Confiável**, modifique **Realm** para ser a URL de seu site implantado. Substitua **exemplo** pelo nome DNS especificado para sua implantação.
   
    ![Realm de terceira parte confiável para uso na produção][relying_party_realm_production]
2. Modifique a **URL de retorno** para ser a URL do seu aplicativo. Substitua **exemplo** pelo nome DNS especificado para sua implantação.
   
    ![URL de retorno da terceira parte confiável para uso na produção][relying_party_return_url_production]
3. Clique em **Salvar** para salvar o realm da terceira parte confiável atualizado e retornar as alterações da URL.
4. Mantenha a página **Integração da Página de Logon** aberta no navegador, você precisará copiá-la em breve.
5. No explorador de Projeto do Eclipse, clique com o botão direito do mouse em **MyACSHelloWorld**, clique em **Caminho de Compilação** e em **Configurar Caminho de Compilação**.
6. Clique na guia **Bibliotecas**, clique em **Filtro dos Serviços de Controle de Acesso do Azure** e em **Editar**.
7. Usando um navegador aberto na página **Integração da Página de Logon** do Portal de Gerenciamento, copie a URL listada no campo **Opção 1: link para uma página de logon hospedada pelo ACS** e cole-a no campo **Ponto de Extremidade de Autenticação do ACS** na caixa de diálogo do Eclipse.
8. Usando um navegador aberto na página **Editar Aplicativo de Terceira Parte Confiável** do Portal de Gerenciamento, copie a URL listada no campo **Realm** e cole-a no campo **Realm de Terceira Parte Confiável** na caixa de diálogo do Eclipse.
9. Na seção **Segurança** da caixa de diálogo Eclipse, se desejar usar um certificado existente, clique em **Procurar**, navegue até o certificado que você deseja usar, selecione-o e clique em **Abrir**. Ou, se você desejar criar um novo certificado, clique em **Novo** para exibir a caixa de diálogo **Novo Certificado**, especifique a senha, o nome do arquivo .cer e o nome do arquivo .pfx para o novo certificado.
10. Mantenha a opção **Incorporar o certificado no arquivo WAR** marcada, supondo que você deseja incorporar o certificado no arquivo WAR.
11. [Opcional] Mantenha a opção **Requer conexões HTTPS** marcada. Se você definir essa opção, precisará acessar seu aplicativo usando o protocolo HTTPS. Se você não desejar requerer conexões HTTPS, desmarque essa opção.
12. Para uma implantação no Azure, suas configurações do Filtro do ACS do Azure serão semelhantes ao seguinte.
    
    ![Configurações do Filtro do ACS do Azure para uma implantação de produção][add_acs_filter_lib_production]
13. Clique em **Concluir** para fechar a caixa de diálogo **Editar Biblioteca**.
14. Clique em **OK** para fechar a caixa de diálogo **Propriedades de MyACSHelloWorld**.
15. No Eclipse, clique no botão **Publicar na Nuvem do Azure** . Responda às solicitações, de maneira semelhante ao que foi feito na seção **Para implantar seu aplicativo no Azure** do tópico [Criando um aplicativo Hello World para o Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Após a implantação do seu aplicativo web, feche todas as sessões abertas do navegador e execute seu aplicativo web. Você deverá ser solicitado a entrar com as credenciais do Windows Live ID que, em seguida, serão enviadas para a URL de retorno de seu aplicativo de terceira parte confiável.

Quando você terminar de usar seu aplicativo Hello World do ACS, lembre-se de excluir a implantação (você pode aprender como excluir uma implantação no tópico [Criando um aplicativo Hello World para o Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).

## <a name="next_steps"></a>Próximas etapas
Para obter um exame da marcação linguagem SAML (Security Assertion Markup Language) retornada pelo ACS para seu aplicativo, consulte [Como exibir SAML retornado pelo Serviço de Controle de Acesso do Azure][How to view SAML returned by the Azure Access Control Service]. Para explorar melhor a funcionalidade do ACS e experimentar mais cenários sofisticados, consulte [Serviço de Controle de Acesso 2.0][Access Control Service 2.0].

Além disso, esse exemplo usou a opção **Incorporar o certificado no arquivo WAR** . Essa opção simplifica a implantação do certificado. Se, em vez disso, você desejar manter seu certificado de autenticação separado do seu arquivo WAR, você poderá usar a seguinte técnica:

1. Na seção **Segurança** da caixa de diálogo **Filtro dos Serviços de Controle de Acesso do Azure**, digite **${env.JAVA_HOME}/mycert.cer** e desmarque a opção **Incorporar o certificado no arquivo WAR**. (Ajuste mycert.cer se o nome do arquivo do certificado for diferente). Clique em **Concluir** para fechar o diálogo.
2. Copie o certificado como um componente em sua implantação: no Explorador de Projeto do Eclipse, expanda **MyAzureACSProject**, clique com o botão direito do mouse em **WorkerRole1**, clique em **Propriedades**, expanda **Função do Azure** e clique em **Componentes**.
3. Clique em **Adicionar**.
4. Na caixa de diálogo **Adicionar Componente** :
   
   1. Na seção **Importar** :
      1. Use o botão **Arquivo** para navegar para o certificado que você deseja usar. 
      2. Para **Método**, selecione **copiar**.
   2. Para **Como Nome**, clique na caixa de texto e aceite o nome padrão.
   3. Na seção **Implantar** :
      1. Para **Método**, selecione **copiar**.
      2. Para **Para o diretório**, digite **%JAVA_HOME%**.
   4. Sua caixa de diálogo **Adicionar Componente** deverá ser semelhante à seguinte.
      
       ![Adicionar componente de certificado][add_cert_component]
   5. Clique em **OK**.

Neste ponto, seu certificado seria incluído em sua implantação. Observe que, independentemente de você incorporar o certificado no arquivo WAR ou adicioná-lo como um componente à sua implantação, você precisa carregar o certificado em seu namespace, conforme descrito na seção [Carregar um certificado em seu namespace do ACS][Upload a certificate to your ACS namespace].

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

