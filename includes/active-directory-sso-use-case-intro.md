As organizações vêm utilizando mais aplicativos de [SaaS (Software como serviço)](https://azure.microsoft.com/overview/what-is-saas/) para produtividade, uma vez que a tecnologia e as ferramentas de nuvem estão se tornando mais amplamente disponíveis. À medida que cresce o número de aplicativos de SaaS, torna-se um desafio para os administradores gerenciar contas e direitos de acesso e para os usuários lembrar de suas senhas diferentes. Gerenciar esses aplicativos individualmente cria mais trabalho e é menos seguro.

* Funcionários que precisam controlar muitas senhas tendem a usar métodos menos seguros para se lembrar delas, anotando as senhas ou usando as mesmas senhas em várias contas.
* Quando chega um novo funcionário ou um funcionário deixa a organização, todas as suas contas devem ser provisionadas ou desconfiguradas individualmente.
* Além disso, os funcionários podem começar a usar aplicativos de SaaS em seu trabalho sem passar pela TI, o que significa que estão criando suas próprias contas em sistemas que os administradores de TI ainda aprovaram e não estão monitorando.  

Uma solução para todos esses desafios é o SSO (logon único). É a maneira mais simples de gerenciar vários aplicativos e fornecer aos usuários uma experiência consistente de logon. O Active Directory do Azure (Azure AD) fornece uma robusta solução de SSO e tem muitos aplicativos pré-integrados disponíveis, com tutoriais para os administradores configurarem um novo aplicativo e iniciarem o provisionamento de usuários rapidamente.

## <a name="how-does-azure-active-directory-integrate-apps"></a>Como o Active Directory do Azure integra aplicativos?
O Azure AD permite que você integre seus aplicativos e contas provisionadas. Isso pode ser feito por meio de uma de duas abordagens.

* Se o aplicativo for pré-integrado à Galeria de aplicativos, você pode usar esse portal para instalar aplicativos e configurar suas configurações para permitir o SSO. Para qualquer aplicativo da Galeria, você pode começar seguindo as instruções simples apresentadas na Galeria de aplicativos e no portal do Azure para habilitar o logon único.
* Se o aplicativo não estiver na Galeria, você ainda pode configurar a maioria dos aplicativos no Azure AD como um aplicativo personalizado. Isso requer um pouco mais experiência técnica para ser configurado. Você pode adicionar qualquer aplicativo que ofereça suporte ao SAML 2.0 como um aplicativo federado ou qualquer aplicativo que tenha uma página de entrada baseada em HTML como um aplicativo do SSO de senha.

No caso em que usuários criam suas próprias contas para aplicativos de SaaS que não são gerenciados pela IT, a ferramenta [Cloud App Discovery](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) oferece uma solução. Essa ferramenta monitora o tráfego da Web para identificar quais aplicativos estão sendo usados em toda a organização e o número de pessoas que usam cada um deles. A TI pode usar essas informações para saber quais aplicativos os usuários preferem e decidir o que integrar ao Azure AD para SSO.  

Ao integrar um aplicativo ao Azure AD, você pode mapear as identidades do aplicativo estabelecido pelos usuários para suas respectivas identidades Azure AD.  

