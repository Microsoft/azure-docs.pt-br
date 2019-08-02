---
title: Integração do Azure MFA Server e Active Directory - Azure Active Directory
description: Como integrar o Servidor de Autenticação Multifator do Azure ao Active Directory para sincronizar os diretórios.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: eac6cff0f0f12daaf772549f547aafd670600d61
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536983"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integração de diretórios entre o Azure MFA Server e o Active Directory

Use a seção Integração de Diretórios do Servidor do Azure MFA para se integrar com o Active Directory ou com outro diretório LDAP. Você pode configurar atributos para corresponder ao esquema de diretório e configurar a sincronização automática de usuários.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, Microsoft não oferecerá o servidor MFA para novas implantações. Novos clientes que gostariam de exigir a autenticação multifator de seus usuários devem usar a autenticação de multifator do Azure baseado em nuvem. Os clientes existentes que ativaram o servidor de MFA antes de 1 de julho será capazes de baixar a versão mais recente, as atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="settings"></a>Configurações

Por padrão, o Servidor de Autenticação Multifator do Azure (MFA) é configurado para importar ou sincronizar usuários do Active Directory.  A guia Integração de Diretório permite substituir o comportamento padrão e associar a um diretório LDAP diferente, a um diretório ADAM ou a um controlador de domínio específico do Active Directory.  Ele também permite o uso da autenticação LDAP para proxy LDAP ou associação LDAP como um destino RADIUS, pré-autenticação para autenticação IIS ou autenticação principal para o portal do usuário.  A tabela a seguir descreve as configurações individuais.

![Editar configuração de LDAP no servidor MFA](./media/howto-mfaserver-dir-ad/dirint.png)

> [!NOTE]
> Integração de diretório não é garantida para trabalhar com diretórios que não seja o Active Directory Domain Services.

| Recurso | DESCRIÇÃO |
| --- | --- |
| Usar o Active Directory |Selecione a opção Usar o Active Directory para usar o Active Directory na importação e na sincronização.  Esta é a configuração padrão. <br>Observação: Para que a integração do Active Directory funcione corretamente, ingresse o computador em um domínio e entre com uma conta de domínio. |
| Incluir domínios confiáveis |Marque a caixa de seleção **Incluir Domínios Confiáveis** para que o agente tente se conectar a domínios confiáveis pelo domínio atual, a outro domínio na floresta ou a domínios envolvidos em uma relação de confiança de floresta.  Quando não estiver importando ou sincronizando usuários de qualquer um dos domínios confiáveis, desmarque a caixa de seleção para melhorar o desempenho.  O padrão é verificado. |
| Usar configuração LDAP específica |Selecione a opção Usar LDAP para usar as configurações de LDAP especificadas na importação e na sincronização. Observação: Quando a opção Usar LDAP é selecionada, a interface do usuário altera as referências do Active Directory para o LDAP. |
| Botão Editar |O botão Editar permite que as configurações atuais de LDAP sejam modificadas. |
| Usar consultas de escopo de atributo |Indica se as consultas de escopo de atributo devem ser usadas.  As consultas de escopo de atributo permitem que as pesquisas eficientes de diretórios qualificando os registros com base no atributo de outro registro.  O Servidor de Autenticação Multifator do Azure usa as consultas de escopo de atributo para consultar eficientemente os usuários que são membros de um grupo de segurança.   <br>Observação:  Há alguns casos em que há suporte para consultas de escopo de atributo, mas elas não devem ser usadas.  Por exemplo, o Active Directory pode ter problemas com consultas de escopo de atributo quando um grupo de segurança contiver membros de mais de um domínio. Nesse caso, desmarque a caixa de seleção. |

A tabela a seguir descreve as definições de configuração de LDAP.

| Recurso | DESCRIÇÃO |
| --- | --- |
| Servidor |Insira o nome do host ou o endereço IP do servidor que está executando o diretório LDAP.  Um servidor de backup também pode ser especificado separado por ponto-e-vírgula. <br>Observação: Quando o Tipo de Associação for SSL, um nome do host totalmente qualificado será necessário. |
| DN base |Insira o nome diferenciado do objeto de diretório base do qual todas as consultas de diretório são iniciadas.  Por exemplo, d=abc,dc=com. |
| Tipo de associação – Consultas |Selecione o tipo de associação apropriado a ser usado na associação do diretório LDAP à pesquisa.  Isso é usado para importações, sincronização e resolução de nome de usuário. <br><br>  Anônimo: uma associação anônima será executada.  O DN e associar senha não são usados.  Isso só funciona se o diretório LDAP permitir a associação anônima e as permissões autorizarem a consulta dos registros e atributos adequados.  <br><br> Simples: Associar DN e Associar Senha são passados como texto sem formatação para associar ao diretório LDAP.  Isso serve para testes, a fim de verificar se o servidor pode ser acessado e se a conta de associação tem o acesso apropriado. Depois que o certificado apropriado tiver sido instalado, use o SSL.  <br><br> SSL: Associar DN e Associar Senha são criptografados usando SSL para associar ao diretório LDAP.  Instale um certificado localmente confiança do diretório LDAP.  <br><br> Windows: Associar Nome de Usuário e Associar Senha são usados para se conectar com segurança a um controlador de domínio do Active Directory ou diretório ADAM.  Se Associar Nome de Usuário for deixado em branco, a conta do usuário conectado será usada na associação. |
| Tipo de associação – Autenticações |Selecione o tipo de associação apropriado para usar ao executar a autenticação de associação LDAP.  Veja as descrições de tipo de associação em Tipo de associação: consultas.  Por exemplo, isso permite que a associação anônima seja utilizada para consultas enquanto associação SSL seja usada para proteger as autenticações de associação LDAP. |
| Associar DN de ligação ou nome de usuário |Digite o nome distinto do registro do usuário para a conta a ser usada ao associar ao diretório LDAP.<br><br>O nome distinto de associação somente é usado quando o tipo de associação é simples ou SSL.  <br><br>Insira o nome de usuário da conta do Windows para usar ao associar ao diretório LDAP quando o tipo de associação for Windows.  Se for deixado em branco, a conta do usuário conectado será usada na associação. |
| Senha de associação |Insira a senha de associação para o DN ou nome de usuário de associação que está sendo usado para associação ao diretório LDAP.  Para configurar a senha do Serviço AdSync do Servidor de Autenticação Multifator, habilite a sincronização e garanta que o serviço esteja em execução no computador local.  A senha será salva em Nomes de Usuário e Senhas Armazenados no Windows na conta em que o Serviço AdSync do Servidor de Autenticação Multifator do Azure estiver em execução.  A senha também será salva na conta em que a interface de usuário do servidor Multi-Factor Auth estiver em execução e na conta em que o serviço do servidor de Autenticação Multifator estiver em execução.  <br><br>Uma vez que a senha é armazenada somente em Nomes de Usuário e Senhas Armazenados do Windows do servidor local, repita esta etapa em cada Servidor de Autenticação Multifator que precise de acesso à senha. |
| Limite de tamanho de consulta |Especifique o limite de tamanho para o número máximo de usuários que uma pesquisa de diretório retornará.  Esse limite deve corresponder à configuração no diretório LDAP.  Em pesquisas grandes, em que a paginação não tem suporte, a importação e a sincronização tentam recuperar os usuários em lotes.  Se o limite de tamanho especificado aqui for maior que o limite configurado no diretório LDAP, alguns usuários poderão ser perdidos. |
| Botão de teste |Clique em **teste** para testar a ligação com o servidor LDAP.  <br><br>Você não precisa selecionar o **usar LDAP** opção para testar a associação. Isso permite que a associação seja testada antes de usar a configuração de LDAP. |

## <a name="filters"></a>Filtros

Filtros permitem que você defina os critérios para qualificar os registros ao executar uma pesquisa de diretório.  Definindo o filtro, você pode definir o escopo de objetos que você deseja sincronizar.  

![Configurar a filtragem de diretório no servidor MFA](./media/howto-mfaserver-dir-ad/dirint2.png)

A Autenticação Multifator do Azure tem estas três opções de filtro:

* **Filtro de contêiner** : especifique os critérios de filtro usados para qualificar os registros de contêiner ao executar uma pesquisa de diretório.  Para o Active Directory e o ADAM, (| ( objectClass=organizationalUnit)(objectClass=container)) normalmente é usado.  Para outros diretórios LDAP, use os critérios de filtro que qualificam cada tipo de objeto de contêiner dependendo do esquema de diretório.  <br>Observação:  Se for deixado em branco, ((objectClass=organizationalUnit)(objectClass=container)) será usado por padrão.
* **Filtro de grupo de segurança** : especifique os critérios de filtro usados para qualificar os registros do grupo de segurança ao executar uma pesquisa de diretório.  Para o Active Directory e o ADAM, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) normalmente é usado.  Para outros diretórios LDAP, os critérios de filtro que qualificam cada tipo de objeto do grupo de segurança devem ser usados dependendo do esquema de diretório.  <br>Observação:  Se for deixado em branco, (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) será usado por padrão.
* **Filtro de usuário** : especifique os critérios de filtro usados para qualificar os registros de usuário ao executar uma pesquisa de diretório.  Para o Active Directory e o ADAM, (& (objectClass=user)(objectCategory=person)) normalmente é usado.  Para outros diretórios LDAP, use (objectClass = inetOrgPerson) ou algo semelhante dependendo do esquema de diretório. <br>Observação:  Se for deixado em branco, (&(objectCategory=person)(objectClass=user)) será usado por padrão.

## <a name="attributes"></a>Atributos

Você pode personalizar atributos como necessário para um diretório específico.  Isso permite que você adicione atributos personalizados e ajuste a sincronização somente para os atributos de que você precisa. Use o nome do atributo conforme definido no esquema de diretório para o valor de cada campo de atributo. A tabela a seguir fornece informações adicionais sobre cada recurso.

Os atributos podem ser inseridos manualmente e não precisam coincidir com um atributo na lista de atributos.

![Personalizar os atributos da integração de diretório no servidor MFA](./media/howto-mfaserver-dir-ad/dirint3.png)

| Recurso | DESCRIÇÃO |
| --- | --- |
| Identificador exclusivo |Digite o nome do atributo que serve como o identificador exclusivo do contêiner, do grupo de segurança e dos registros de usuário.  No Active Directory, isso geralmente é objectGUID. Outras implementações LDAP podem usar entryUUID ou algo semelhante.  O padrão é objectGUID. |
| Tipo de identificador exclusivo |Selecione o tipo de atributo de identificador exclusivo.  No Active Directory, o atributo objectGUID é do tipo GUID. Outras implementações LDAP podem usar o tipo Cadeia de Caracteres ou Matriz de Bytes ASCII.  O padrão é GUID. <br><br>É importante definir esse tipo corretamente, já que os itens de sincronização são referenciados por seu identificador exclusivo. O tipo de identificador exclusivo é usado para localizar o objeto diretamente no diretório.  Definir esse tipo como Cadeia de Caracteres quando o diretório realmente armazena o valor como uma matriz de bytes de caracteres ASCII e impede que a sincronização funcione corretamente. |
| Nome distinto |Digite o nome do atributo que contém o nome distinto para cada registro.  No Active Directory, isso geralmente é distinguishedName. Outras implementações LDAP podem usar entryDN ou algo semelhante.  O padrão é distinguishedName. <br><br>Se um atributo contendo apenas o nome diferenciado não existir, o atributo de caminho de anúncios pode ser usado.  A parte do caminho "LDAP://\<servidor\>/" é removida automaticamente, deixando apenas o nome distinto do objeto. |
| Nome do contêiner |Insira o nome do atributo que contém o nome em um registro de contêiner.  O valor desse atributo é exibido na hierarquia do contêiner ao importar do Active Directory ou adicionar itens de sincronização.  O padrão é o nome. <br><br>Se contêineres diferentes usarem atributos diferentes para seus nomes, use ponto-e-vírgula para separar vários atributos de nome de contêiner.  O primeiro atributo de nome de contêiner encontrado em um objeto de contêiner é usado para exibir seu nome. |
| Nome do grupo de segurança |Insira o nome do atributo que contém o nome em um registro de grupo de segurança.  O valor desse atributo é exibido na lista Grupo de segurança ao importar do Active Directory ou adicionar itens de sincronização.  O padrão é o nome. |
| Nome de Usuário |Digite o nome do atributo que contém o nome de usuário em um registro de usuário.  O valor deste atributo é usado como o nome de usuário do servidor de Autenticação Multifator.  Um segundo atributo pode ser especificado como um backup do primeiro.  O segundo atributo só será usado se o primeiro atributo não contiver um valor para o usuário.  Os padrões são userPrincipalName e sAMAccountName. |
| Nome |Insira o nome do atributo que contém o nome em um registro de usuário.  O padrão é givenName. |
| Sobrenome |Insira o nome do atributo que contém o sobrenome em um registro de usuário.  O padrão é sn. |
| Endereço de email |Insira o nome do atributo que contém o endereço de email em um registro de usuário.  O endereço de email é usado para enviar boas-vindas e emails de atualização para o usuário.  O padrão é email. |
| Grupo de usuários |Insira o nome do atributo que contém o grupo de usuários em um registro de usuário.  O grupo de usuários pode ser usado para filtrar usuários no agente e em relatórios no Portal de Gerenciamento do servidor Multi-Factor Authentication. |
| DESCRIÇÃO |Insira o nome do atributo que contém a descrição em um registro de usuário.  A descrição é usada somente para pesquisa.  O padrão é descrição. |
| Idioma to telefonema |Insira o nome do atributo que contém o nome curto do idioma a ser usado para chamadas de voz para o usuário. |
| Idioma da mensagem de texto |Insira o nome do atributo que contém o nome curto do idioma a ser usado para mensagens de texto SMS para o usuário. |
| Idioma do aplicativo móvel |Insira o nome do atributo que contém o nome curto do idioma a ser usado para mensagens de texto do aplicativo Telefone do usuário. |
| Idioma do token OATH |Insira o nome do atributo que contém o nome curto do idioma a ser usado para mensagens de texto de token OATH para o usuário. |
| Telefone comercial |Insira o nome do atributo que contém o número de telefone comercial em um registro de usuário.  O padrão é telephoneNumber. |
| Telefone residencial |Insira o nome do atributo que contém o número de telefone residencial em um registro de usuário.  O padrão é homePhone. |
| Pager |Digite o nome do atributo que contém o número de pager em um registro de usuário.  O padrão é pager. |
| Telefone celular |Insira o nome do atributo que contém o número de telefone celular em um registro de usuário.  O padrão é celular. |
| Fax |Insira o nome do atributo que contém o número de fax em um registro de usuário.  O padrão é facsimileTelephoneNumber. |
| Telefone IP |Insira o nome do atributo que contém o número de telefone IP em um registro de usuário.  O padrão é ipPhone. |
| Personalizado |Insira o nome do atributo que contém um número de telefone personalizado em um registro de usuário.  O padrão é em branco. |
| Extensão |Insira o nome do atributo que contém o ramal do número de telefone em um registro de usuário.  O valor do campo de ramal só será usado como ramal do número de telefone principal.  O padrão é em branco. <br><br>Se o atributo Ramal não for especificado, os ramais poderão ser incluídos como parte do atributo de telefone. Nesse caso, preceda a extensão com um 'x' para que ele é analisado corretamente.  Por exemplo, 3123-4567 x890 resultaria em 3123-4567 como o número de telefone e 890 como o ramal. |
| Botão Restaurar Padrões |Clique em **Restaurar Padrões** para retornar todos os atributos ao valor padrão.  Os padrões devem funcionar corretamente com o esquema do Active Directory ou do ADAM normal. |

Para editar atributos, clique em **editar** na guia atributos.  Isso abre uma janela onde você pode editar os atributos. Selecione o **...** ao lado de qualquer atributo para abrir uma janela onde você pode escolher quais atributos a serem exibidos.

![Editar mapeamento de atributo de diretório no servidor MFA](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Sincronização

A sincronização mantém o banco de dados do usuário do Azure MFA sincronizado com os usuários no Active Directory ou outro diretório LDAP. O processo é semelhante à importação manual de usuários do Active Directory, mas faz sondagens para processar alterações do grupo de segurança e de usuário do Active Directory.  Ela também desativa ou remove usuários que foram removidos de um contêiner, o grupo de segurança ou o Active Directory.

O serviço ADSync do Multi-Factor Auth é um serviço do Windows que executa a sondagem periódica do Active Directory.  Isso não é deve ser confundido com o Azure AD Sync ou o Azure AD Connect.  O Multi-Factor ADSync, embora criado sobre uma base de código semelhante, é específico para o Servidor de Autenticação Multifator do Azure.  Ele é instalado em um estado Parado e iniciado pelo serviço do servidor Multi-Factor Auth quando configurado para executar.  Se você tiver uma configuração de vários servidores Multi-Factor Auth Server, o ADSync do Multi-Factor Auth somente pode ser executado em um único servidor.

O serviço ADSync do multi-Factor Auth usa a extensão do servidor DirSync LDAP fornecida pela Microsoft para sondar alterações com eficiência.  O chamador de controle DirSync deve ter os direitos "directory get changes” e o controle estendido DS-Replication-Get-Changes.  Por padrão, esses direitos são atribuídos às contas de administrador e LocalSystem nos controladores de domínio.  Por padrão, o serviço AdSync do Multi-Factor Auth está configurado para ser executado como LocalSystem.  Portanto, é mais simples de executar o serviço em um controlador de domínio.  Se você configurar o serviço para sempre executar uma sincronização completa, ele poderá ser executado como uma conta com menos permissões.  Isso é menos eficiente, mas requer menos privilégios de conta.

Se o diretório LDAP der suporte e se estiver configurado para DirSync, a sondagem de alterações do grupo de segurança e de usuário funcionam da mesma forma como acontece com o Active Directory.  Se o diretório LDAP não der suporte para o controle DirSync, uma sincronização completa será executada durante cada ciclo.

![Sincronização de objetos de diretório para o servidor MFA](./media/howto-mfaserver-dir-ad/dirint5.png)

A tabela a seguir contém informações adicionais sobre cada uma das configurações de sincronização do guia.

| Recurso | DESCRIÇÃO |
| --- | --- |
| Habilitar a sincronização com o Active Directory |Quando marcado, o serviço do servidor de Autenticação Multifator sonda periodicamente o Active Directory quanto a alterações. <br><br>Observação: Pelo menos um Item de Sincronização precisa ser adicionado e o recurso Sincronizar Agora precisa ser executado antes que o serviço Servidor de Autenticação Multifator inicie o processamento das alterações. |
| Sincronizar cada |Especifique o intervalo de tempo que o serviço de servidor Multi-Factor Authentication aguardará entre a sondagem e o processamento de alterações. <br><br> Observação: O intervalo especificado é o tempo entre o início de cada ciclo.  Se o tempo de processamento das alterações exceder o intervalo, o serviço fará a sondagem novamente imediatamente. |
| Remover os usuários não está mais no Active Directory |Quando marcado, o serviço do servidor Multi-Factor Auth processa marcas de exclusão de usuários excluídos do Active Directory e removerá o usuário do servidor Multi-Factor Auth associado. |
| Sempre executar uma sincronização completa |Quando marcado, o serviço do servidor Multi-Factor Auth sempre executará uma sincronização completa.  Quando desmarcado, o serviço do servidor Multi-Factor Auth Server executará uma sincronização incremental consultando apenas os usuários que foram alterados.  O padrão é desmarcado. <br><br>Quando desmarcada, o servidor Azure MFA executa a sincronização incremental somente quando o diretório suporta o controle DirSync e a associação de conta para o diretório tem permissões para executar consultas incrementais de DirSync.  Se a conta não tiver as permissões apropriadas ou vários domínios estiverem envolvidos na sincronização, o Servidor Azure MFA executa uma sincronização completa. |
| Exigir a aprovação do administrador quando mais de X usuários forem desabilitados ou removidos |Os itens de sincronização podem ser configurados para desabilitar ou remover os usuários que não são mais membros de contêiner ou grupo de segurança do item.  Como salvaguarda, a aprovação de administrador pode ser exigida quando o número de usuários para desabilitar ou remover exceder um limite.  Quando marcada, a opção de aprovação é exigida para o limite especificado.  O padrão é 5 e o intervalo é de 1 a 999. <br><br> A aprovação é facilitada com o envio inicial de uma notificação por email aos administradores. A notificação de email fornece instruções para revisar e aprovar a desabilitação e a remoção de usuários.  Quando a interface de usuário do servidor Multi-Factor Authentication for iniciada, ela irá solicitar aprovação. |

O botão **Sincronizar Agora** permite que você execute uma sincronização completa dos itens de sincronização especificados.  Uma sincronização completa é necessária sempre que itens de sincronização são adicionados, modificados, removidos ou reordenados.  Também é necessária antes que o serviço AdSync do Multi-Factor Auth fique operacional, pois ele define o ponto de partida a partir do qual o serviço irá sondar alterações incrementais.  Se as alterações foram feitas aos itens de sincronização, mas ainda não foi executada uma sincronização completa, você será solicitado a sincronizar agora.

O botão **Remover** permite que o administrador exclua um ou mais itens de sincronização da lista de item de sincronização do servidor Multi-Factor Auth.

> [!WARNING]
> Depois que um registro de item de sincronização tiver sido removido, ele não pode ser recuperado. Você precisará adicionar o registro de item de sincronização novamente se tiver sido excluído por engano.

Os itens de sincronização foram removidos do servidor Multi-Factor Auth.  O serviço do servidor Multi-Factor Auth não processará mais os itens de sincronização.

Os botões Mover para cima e Mover para baixo permitem que o administrador altere a ordem dos itens de sincronização.  A ordem é importante, pois um mesmo usuário pode ser membro de mais de um item de sincronização (por exemplo, um contêiner e um grupo de segurança).  As configurações aplicadas ao usuário durante a sincronização virão do primeiro item de sincronização na lista a qual o usuário estiver associado.  Portanto, os itens de sincronização devem ser colocados em ordem de prioridade.

> [!TIP]
> Uma sincronização completa deve ser executada após a remoção de itens de sincronização.  Uma sincronização completa deve ser executada após a ordenação dos itens de sincronização.  Clique em **Sincronizar Agora** para executar uma sincronização completa.

## <a name="multi-factor-authentication-servers"></a>Servidores de autenticação multifatores

Servidores adicionais de autenticação multifator podem ser configurados para servir como um backup proxy RADIUS, proxy LDAP, ou para autenticação do IIS. A configuração da Sincronização será compartilhada entre todos os agentes. No entanto, apenas um desses agentes pode ter o serviço em execução no servidor de autenticação multifator. Esta guia permite que você selecione o servidor de autenticação multifator que deve ser habilitado para sincronização.

![Relacionados a servidores de autenticação multifator](./media/howto-mfaserver-dir-ad/dirint6.png)
