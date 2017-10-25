---
title: Conector Lotus Domino | Microsoft Docs
description: Este artigo descreve como configurar o conector Lotus Domino da Microsoft.
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 328e2039bbaa8988dc35b46712fd0de6f8bcbee8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lotus-domino-connector-technical-reference"></a>Referência técnica do conector Lotus Domino
Este artigo descreve o conector Lotus Domino. O artigo se aplica aos seguintes produtos:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * É necessário usar o hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível para download no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Visão geral do conector Lotus Domino
O conector Lotus Domino permite integrar o serviço de sincronização ao servidor do Lotus Domino da IBM.

Partindo de um ponto de vista detalhado, os seguintes recursos têm suporte na versão atual do conector:

| Recurso | Suporte |
| --- | --- |
| Fonte de dados conectada |Servidor:  <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Cliente:<li>Lotus Domino 8.5.x</li><li>Lotus Notes 9.x</li> |
| Cenários |<li>Gerenciamento de ciclo de vida do objeto</li><li>Gerenciamento de grupos</li><li>Gerenciamento de senha</li> |
| Operações |<li>Importação completa e Delta</li><li>Exportação</li><li>Definir e alterar a senha em senha HTTP</li> |
| Esquema |<li>Pessoa (Roaming do usuário, Contato (pessoas sem certificado))</li><li>Agrupar</li><li>Recurso (Recurso, Reunião online, Sala)</li><li>Banco de dados Entrada de Email</li><li>Descoberta dinâmica de atributos para objetos com suporte</li> |

O conector Lotus Domino usa o cliente Lotus Notes para se comunicar com o servidor Lotus Domino. Como consequência dessa dependência, um cliente Lotus Notes compatível deve ser instalado no servidor de sincronização. A comunicação entre o cliente e o servidor é implementada por meio da interface de interoperabilidade do .NET do Lotus Notes (Interop.domino.dll). Essa interface facilita a comunicação entre o cliente Lotus Notes e a plataforma Microsoft.NET, além de oferecer suporte ao acesso a documentos e exibições do Lotus Domino. Na importação delta, também é possível que a interface nativa C++ seja usada (dependendo do método de importação delta selecionado).

### <a name="prerequisites"></a>Pré-requisitos
Antes de usar o Conector, verifique se você tem os seguintes pré-requisitos no servidor de sincronização:

* Microsoft .NET 4.5.2 Framework ou posterior
* O cliente Lotus Notes deve estar instalado no servidor de sincronização
* O conector Lotus Domino requer que o banco de dados do esquema LDAP do Lotus Domino padrão (schema.nsf) esteja presente no servidor do diretório Domino. Se não estiver, você poderá instalá-lo executando ou reiniciando o serviço LDAP no servidor Domino.

### <a name="connected-data-source-permissions"></a>Permissões da fonte de dados conectada
Para executar qualquer uma das tarefas com suporte no conector Lotus Domino, você deve ser um membro dos seguintes grupos:

* Administradores com acesso completo
* Administradores
* Administradores de banco de dados

A tabela a seguir lista as permissões que são necessárias para cada operação:

| Operação | Direitos de acesso |
| --- | --- |
| Importar |<li>Ler documentos públicos</li><li> Administrador com acesso completo (como membro do grupo Administradores de acesso completo, você tem automaticamente o acesso efetivo na ACL).</li> |
| Exportar e definir senha |Acesso efetivo:  <li>Criar documentos</li><li>Excluir documentos</li><li>Ler documentos públicos</li><li>Gravar documentos públicos</li><li>Replicar ou copiar documentos</li>Para operações de exportação, você também precisa das seguintes funções: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>Operações diretas e AdminP
As operações vão diretamente para o diretório Domino ou passam pelo processo AdminP. As tabelas a seguir listam todos os objetos com suporte, operações e, se aplicável, o método de implementação relacionado:

**Catálogo de endereços principal**

| Objeto | Criação | Atualização | Exclusão |
| --- | --- | --- | --- |
| Pessoa |AdminP |Direta |AdminP |
| Agrupar |AdminP |Direta |AdminP |
| MailInDB |Direta |Direta |Direta |
| Recurso |AdminP |Direta |AdminP |

**Catálogo de endereços secundário**

| Objeto | Criação | Atualização | Exclusão |
| --- | --- | --- | --- |
| Pessoa |N/D |Direta |Direta |
| Agrupar |Direta |Direta |Direta |
| MailInDB |Direta |Direta |Direta |
| Recurso |N/D |N/D |N/D |

Quando um recurso é criado, um documento do Notes é criado. Da mesma forma, quando um recurso é excluído, o documento é excluído.

### <a name="ports-and-protocols"></a>Portas e protocolos
O cliente IBM Lotus Notes e os servidores Domino se comunicam usando a NRPC (Chamada de Procedimento Remoto do Notes), em que NRPC deve usar TCP/IP. O número da porta padrão é 1352, mas pode ser alterado pelo administrador do Domino.

### <a name="not-supported"></a>Sem suporte
As operações a seguir não têm suporte da versão atual do conector Lotus Domino:

* Mover caixa de correio entre servidores.

## <a name="create-a-new-connector"></a>Criar um novo conector
### <a name="client-software-installation-and-configuration"></a>Instalação e configuração do software cliente
O Lotus Notes deve ser instalado no servidor **antes** de o conector ser instalado.

Ao fazer a instalação, tenha a certeza de fazer uma **Instalação de usuário único**. O padrão **Instalação de vários usuários** não funciona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Na página de recursos, instale apenas os recursos do Lotus Notes necessários e o **Logon Único do Cliente**. O Logon Único é necessário para que o conector possa fazer logon no servidor Domino.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Observação:** inicie o Lotus Notes uma vez com um usuário que esteja localizado no mesmo servidor da conta que você usa como a conta de serviço do conector. Além disso, certifique-se de fechar o cliente do Lotus Notes no servidor. Ele não pode estar em execução ao mesmo tempo em que o Conector tenta se conectar ao servidor do Domino.

### <a name="create-connector"></a>Criar o conector
Para criar um conector Lotus Domino, em **Serviço de Sincronização**, selecione **Agente de Gerenciamento** e **Criar**. Escolha o conector **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Se sua versão do serviço de sincronização oferecer a capacidade de configurar **Arquitetura**, verifique se o conector está definido para seu valor padrão para ser executado em **Processo**.

### <a name="connectivity"></a>Conectividade
Na página Conectividade, é preciso especificar o nome do servidor Lotus Domino e informar as credenciais de logon.  
![Conectividade](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

A propriedade Domino Server oferece suporte a dois formatos de nome de servidor:

* ServerName
* ServerName/DirectoryName

O **ServerName/DirectoryName** é o formato preferencial para esse atributo, pois ele fornece uma resposta mais rápida quando o conector contata o servidor Domino.

O arquivo UserID fornecido é armazenado no banco de dados de configuração do serviço de sincronização.

Na **Importação Delta** , você tem estas opções:

* **Nenhum**. O conector não faz importações delta.
* **Adicionar/Atualizar**. O conector vai adicionar importação delta e atualizar as operações. Para excluir, é necessária uma operação **Importação Completa** . Essa operação está usando a interoperabilidade .Net.
* **Adicionar/Atualizar/Excluir**. O conector adiciona, atualiza e exclui operações de importação delta. Essa operação está usando as interfaces C++ nativas.

Em **Opções do Esquema** , você tem as seguintes opções:

* **Esquema Padrão**. O conector detecta o esquema do servidor Domino. Essa seleção é a opção padrão.
* **Esquema DSML**. Usado somente se o servidor Domino não expuser o esquema. Desse modo, você pode criar um arquivo DSML com o esquema e importá-lo. Para obter mais informações sobre o DSML, confira [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Quando você clica em Avançar, os parâmetros de configuração de senha UserID são verificados.

### <a name="global-parameters"></a>Parâmetros Globais
Na página Parâmetros Globais, defina o fuso horário, bem como a opção de operação de importação e exportação.  
![Parâmetros Globais](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

O parâmetro **Fuso Horário do Servidor Domino** define o local do seu servidor Domino.

Essa opção de configuração é necessária para oferecer suporte às operações **importação delta** , pois ela permite que o serviço de sincronização determine as alterações entre as duas últimas importações.

>[!Note]
A partir da atualização de março de 2017 a tela de parâmetros globais inclui a opção de excluir o banco de dados de email do usuário durante a exclusão do usuário.

![Excluir a caixa de correio de um usuário](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>Configurações de importação, método
O recurso **Executar Importação Completa por** possui as seguintes opções:

* Pesquisar
* Exibição (Recomendado)

**Pesquisa** está usando indexação no Domino, mas é comum que os índices não sejam atualizados em tempo real e os dados retornados do servidor nem sempre estejam corretos. Em um sistema com muitas alterações, essa opção geralmente não funciona bem e fornece exclusões falsas em algumas situações. No entanto, a **pesquisa** é mais rápida que a **exibição**.

**Exibição** é a opção recomendada, pois ela fornece o estado correto dos dados. Ela é ligeiramente mais lenta que a pesquisa.

#### <a name="creation-of-virtual-contact-objects"></a>Criação de objetos Contato Virtual
O recurso **Habilitar criação do \_objeto Contact** tem as seguintes opções:

* Nenhum
* Valores de Não Referência
* Valores de Referência e Não Referência

No Domino, os atributos de referência podem conter muitos formatos diferentes para fazer referência a outros objetos. Para poder representar variações diferentes, o conector implementa objetos \_Contact, também conhecidos como VC **(Contatos Virtuais)**. Esses objetos são criados para que possam ingressar em objetos existentes de MV ou projetados como novos objetos. Dessa forma, as referências de atributo podem ser preservadas.

Ao habilitar essa configuração e, se o conteúdo de um atributo de referência não estiver em um formato DN, um objeto \_Contact será criado. Por exemplo, um atributo de membro de um grupo pode conter endereços SMTP. Também é possível ter shortName e outros atributos presentes nos atributos de referência. Para esse cenário, selecione **Valores de Não Referência**. Essa é a configuração mais comum para implementações do Domino.

Quando o Lotus Domino é configurado para ter catálogos de endereços separados com diferentes nomes distintos representando o mesmo objeto, também é possível criar objetos \_Contact para todos os valores de referência que são encontrados em um catálogo de endereços. Para esse cenário, selecione a opção **Valores de Referência e Não Referência** .

Se você tiver vários valores no atributo **FullName** no Domino, convém habilitar a criação de Contatos Virtuais para que as referências possam ser resolvidas. Por exemplo, esse atributo pode ter vários valores após um casamento ou divórcio. Marque a caixa de seleção **Habilitar... FullName tem vários valores** para esse cenário.

Com a união dos atributos corretos, os objetos \_Contact podem ser unidos ao objeto MV.

Esses objetos têm VC=\_Contact adicionado à respectiva DN.

#### <a name="import-settings-conflict-object"></a>Configurações de importação, objeto de conflito
**Excluir Objeto de Conflito**

Em uma ampla implementação do Domino, é possível que vários objetos tenham a mesma DN devido a problemas de replicação. Nesses casos, o conector verá dois objetos com UniversalIDs diferentes, mas mesma DN. Esse conflito faria com que um objeto transitório fosse criado no espaço do conector. O conector pode ignorar os objetos que foram selecionados no Domino como vítimas da replicação. A recomendação é manter essa caixa de seleção marcada.

#### <a name="export-settings"></a>Exportar configurações
Se a opção **Usar AdminP para atualizar referências** estiver desmarcada, a exportação de atributos de referência, como membro, será uma chamada direta e não usará o processo AdminP. Use essa opção somente quando AdminP não tiver sido configurado para manter a integridade referencial.

#### <a name="routing-information"></a>Informações de roteamento
No Domino, é possível que um atributo de referência tenha informações de roteamento inseridas como um sufixo à DN. Por exemplo, o atributo de membro em um grupo pode conter **CN=example/organization@ABC**. O sufixo @ABC é a informação de roteamento. As informações de roteamento são usadas pelo Domino para enviar emails ao sistema Domino correto, o que pode ser um sistema em uma organização diferente. No campo Informações de Roteamento, você pode especificar os sufixos de roteamento usados na organização, no escopo do conector. Se um desses valores for encontrado como um sufixo em um atributo de referência, as informações de roteamento serão removidas da referência. Se o sufixo de roteamento em um valor de referência não puder ser correspondido com um desses valores especificados, um objeto \_Contact será criado. Esses objetos \_Contact são criados com **RO=@<RoutingSuffix>** inserido na DN. Para esses objetos \_Contact, os atributos a seguir também são adicionados para permitir a união a um objeto real se necessário: \_routingName, \_contactName, \_displayName e UniversalID.

#### <a name="additional-address-books"></a>Catálogos de endereços adicionais
Se não tiver o **directory assistance** instalado, que fornece o nome dos catálogos de endereços secundários, você poderá inserir manualmente esses catálogos de endereços.

#### <a name="multivalued-transformation"></a>Transformação com vários valores
Muitos atributos no Lotus Domino têm vários valores. Os atributos do metaverso correspondentes normalmente são de valor único. Ao configurar a opção de operação Importação e Exportação, você permite que o conector ajude com a tradução necessária dos atributos afetados.

**Exportarar**  
A opção de operação Exportar é compatível com dois modos:

* Acrescentar item
* Substituir item

**Substituir Item** : quando você seleciona essa opção, o conector sempre remove os valores atuais do atributo no Domino e os substitui pelos valores fornecidos. O atributo com valor fornecido pode ter um valor único ou vários valores.

Exemplo: o atributo Assistente de um objeto de pessoa tem os seguintes valores:

* CN=Vinicius Monte/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Paulo Araújo/OU=Contoso/O=Americas,NAB=names.nsf

Se um novo Assistente chamado **Pedro Gonçalves** for atribuído a esse objeto de pessoa, o resultado será:

* CN=Pedro Gonçalves/OU=Contoso/O=Americas,NAB=names.nsf

**Acrescentar Item** : quando você seleciona essa opção, o conector mantém os valores existentes no atributo no Domino e insere novos valores no topo da lista de dados.

Exemplo: o atributo Assistente de um objeto de pessoa tem os seguintes valores:

* CN=Vinicius Monte/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Paulo Araújo/OU=Contoso/O=Americas,NAB=names.nsf

Se um novo Assistente chamado **Pedro Gonçalves** for atribuído a esse objeto de pessoa, o resultado será:

* CN=Pedro Gonçalves/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Vinicius Monte/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Paulo Araújo/OU=Contoso/O=Americas,NAB=names.nsf

**Importarar**  
A opção de operação Importar é compatível com dois modos:

* Padrão
* Múltiplos Valores para Valor Único

**Padrão** : quando você seleciona a opção Padrão, todos os valores de todos os atributos são importados.

**Múltiplos Valores para Valor Único** : quando você seleciona essa opção, um atributo com múltiplos valores é convertido em um atributo de valor único. Se houver mais de um valor, o valor na parte superior (normalmente, esse também é o valor mais recente) será usado.

Exemplo: o atributo Assistente de um objeto de pessoa tem os seguintes valores:

* CN=Pedro Gonçalves/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Vinicius Monte/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Paulo Araújo/OU=Contoso/O=Americas,NAB=names.nsf

A atualização mais recente para esse atributo é **Pedro Gonçalves**. Como a opção de operação Importar é definida como Múltiplos Valores para Valor Único, o conector importa apenas **Pedro Gonçalves** no espaço do conector.

A lógica para converter atributos de múltiplos valores em atributos de valor único não se aplica ao atributo de membro de grupo nem ao atributo de nome completo de pessoa.

Também é possível configurar as regras de transformação de importação e exportação para atributos de múltiplos valores por atributo, como uma exceção à regra global. Para configurar essa opção, insira [objecttype].[attributename] nas caixas de texto **importar lista de atributos de exclusão** e **exportar lista de atributos de exclusão**. Por exemplo, se você digitar Person.Assistant e o sinalizador global estiver definido para importar todos os valores, apenas o primeiro valor será importado para o assistente.

#### <a name="certifiers"></a>Certificadores
Todas as Organizações/Unidades Organizacionais são listadas pelo conector. Para poder exportar objetos de indivíduo para o catálogo de endereços principal, é necessário um certificador com a respectiva senha.

Se todos os certificadores tiverem a mesma senha, a **Senha para todos os Certificadores** poderá ser usada. Assim, você pode inserir a senha aqui e especificar apenas o arquivo de certificador.

Se você apenas importar, não será necessário especificar qualquer certificador.

### <a name="configure-provisioning-hierarchy"></a>Configurar a hierarquia de provisionamento
Ao configurar o conector Lotus Domino, pule essa página de caixa de diálogo. O conector Lotus Domino não é compatível com o provisionamento de hierarquia.  
![Hierarquia de provisionamento](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar partições e hierarquias
Ao configurar partições e hierarquias, você deve selecionar o catálogo de endereços principal chamado  NAB=names.nsf. Além do catálogo de endereços principal, é possível escolher catálogos de endereços secundários, se estes existirem.  
![Partições](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Selecionar atributos
Ao configurar atributos, você deve selecionar todos os atributos que tenham o prefixo **\_MMS\_**. Esses atributos são necessários quando você provisiona novos objetos para o Lotus Domino.

![Atributos](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gerenciamento de ciclo de vida do objeto
Esta seção apresenta uma visão geral dos diferentes objetos no Domino.

### <a name="person-objects"></a>Objetos de pessoa
O objeto de pessoa representa usuários na Organização e nas Unidades Organizacionais. Além dos atributos padrão, o administrador do Domino pode adicionar atributos personalizados a um objeto Person. No mínimo, um objeto Person deve incluir todos os atributos obrigatórios. Para obter uma lista completa de atributos obrigatórios, confira [Propriedades do Lotus Notes](#lotus-notes-properties). Para registrar um objeto de pessoa, os seguintes pré-requisitos devem ser atendidos:

* O catálogo de endereços (names.nsf) deve ter sido definido e deve ser o catálogo de endereços principal.
* Você deve ter a ID do certificador da O/UO e a senha para registrar um usuário específico na Organização/Unidade Organizacional.
* É preciso definir um conjunto específico de propriedades do Lotus Notes para um objeto de pessoa. Essas propriedades são usadas para provisionar o objeto de pessoa. Para obter mais informações, confira a seção chamada [Propriedades do Lotus Notes](#lotus-notes-properties) mais adiante neste documento.
* A senha HTTP inicial para uma pessoa é um atributo e definido durante o provisionamento.
* O objeto de pessoa deve ser um destes três tipos com suporte:
  1. Usuário Normal que tenha um arquivo de email e um arquivo de id de usuário
  2. Usuário móvel (um Usuário Normal que inclua todos os arquivos de banco de dados móvel)
  3. Contatos (usuário sem arquivo de id)

As pessoas (exceto contatos) ainda podem ser agrupadas em Usuários dos EUA e Usuários Internacionais, conforme definido pelo valor da propriedade \_MMS\_IDRegType. Essas pessoas usam o cliente Notes para acessar servidores Lotus Domino que têm uma ID do Notes e um documento Person. Se estiverem usando o email do Notes, eles também terão um arquivo de email. O usuário deve ser registrado para se tornar ativo. Para obter mais informações, consulte:

* [Setting up Notes users](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [User Registration](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [Managing users](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [Renaming users](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Todas essas operações são executadas no Lotus Domino e importadas no serviço de sincronização.

### <a name="resources-and-rooms"></a>Recursos e salas
Um Recurso é outro tipo de banco de dados do Lotus Domino. Os Recursos podem ser salas de conferência com vários tipos de equipamento, como projetores. Há subtipos de recurso que têm suporte do conector Lotus Domino, que são definidos com base no atributo Tipo de Recurso:

| Tipo de recurso | Atributo Tipo de Recurso |
| --- | --- |
| Sala |1 |
| Recurso (Outros) |2 |
| Reunião Online |3 |

Para que o tipo de objeto Recurso funcione, os seguintes itens são obrigatórios:

* O banco de dados Reserva de Recursos já deve existir no servidor Domino conectado
* O site já deve estar definido para o Recurso

O banco de dados Reserva de Recursos contém três tipos de documento:

* Perfil do Site
* Recurso
* Reserva

Para obter mais informações sobre a configuração do banco de dados Reserva de Recursos, confira [Configurando o banco de dados Reservas de Recursos](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Criar, atualizar e excluir recursos**  
As operações Criar, Atualizar e Excluir são executadas pelo conector Lotus Domino no banco de dados Reserva de Recursos. Os recursos são criados como documentos em Names.nsf (isto é, o catálogo de endereços principal). Para obter mais informações sobre como editar e excluir Recursos, confira [Editando e excluindo documentos do Recurso](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Operação Importar e Exportar para Recursos**  
Os Recursos podem ser importados e exportados no serviço de sincronização, assim como qualquer outro tipo de objeto. Escolha o tipo de objeto como Recurso durante a configuração. Para que a operação de exportação seja bem-sucedida, você deve ter detalhes do tipo Recurso, Banco de Dados de Conferência e Nome do site.

### <a name="mail-in-databases"></a>Bancos de dados de Entrada de Email
O banco de dados de Entrada de Email é um banco de dados que foi desenvolvido para receber emails. Trata-se de uma caixa de correio do Lotus Domino que não está associada a qualquer conta de usuário específica do Lotus Domino (isto é, não tem seu próprio arquivo de ID e senha). Um banco de dados de Entrada de Email tem um UserID exclusivo (“nome curto”) associado a ele e seu próprio endereço de email.

Se houver a necessidade de uma caixa de correio separada com seu próprio endereço de email que possa ser compartilhada entre usuários diferentes (por exemplo: group@contoso.com), um banco de dados de entrada de email será criado. O acesso a essa caixa de correio é controlado por meio de sua ACL (Lista de Controle de Acesso), que contém os nomes dos usuários do Notes que têm permissão para abrir a caixa de correio.

Para obter uma lista dos atributos necessários, veja a seção [Atributos obrigatórios](#mandatory-attributes) , mais adiante neste artigo.

Quando um banco de dados é desenvolvido para receber um email, um documento de banco de dados de Entrada de Email é criado no Lotus Domino. Este documento deve existir no diretório Domino de cada servidor que armazena uma cópia do banco de dados. Para obter uma descrição detalhada sobre como criar um documento de banco de dados de entrada de email, confira [Creating a Mail-In Database document](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html)(Criando um documento de banco de dados de entrada de email).

Para criar um banco de dados de Entrada de Email, o banco de dados já deve existir (deve ter sido criado pelo Admin do Lotus) no servidor Domino.

### <a name="group-management"></a>Gerenciamento de grupos
Você pode obter uma visão geral detalhada do gerenciamento de grupos do Lotus Domino nos seguintes recursos:

* [Using groups](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [Creating a group](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [Creating and modifying groups](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [Managing groups](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [Renaming a group](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gerenciamento de senha
Para um usuário registrado do Lotus Domino, existem dois tipos de senha:

1. Senha do usuário (armazenada no arquivo User.id)
2. Senha Internet/HTTP

O conector Lotus Domino oferece suporte a operações apenas com senha HTTP.

Para executar o gerenciamento de senha, você deve habilitá-lo para o conector no Designer do Agente de Gerenciamento. Para habilitar o gerenciamento de senha, escolha **Habilitar gerenciamento de senha** na página da caixa de diálogo **Configurar Extensões**.  
![Configurar Extensões](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

O conector Lotus Domino oferece suporte às seguintes operações em Senha da Internet:

* Definir Senha: defina uma nova senha de HTTP/Internet no usuário, no Domino. Por padrão, a conta também é desbloqueada. O sinalizador de desbloqueio é exposto na interface do WMI do Mecanismo de Sincronização.
* Alterar Senha: nesse cenário, um usuário poderá alterar a senha ou será solicitado a alterar a senha após um tempo especificado. Para que essa operação ocorra, ambas (a senha antiga e nova) são obrigatórias. Depois de alterada, a nova senha é atualizada no Lotus Domino.

Para obter mais informações, consulte:

* [Using the Internet lockout feature](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [Managing Internet passwords](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informações de referência
Esta seção lista as descrições de atributo e os requisitos de atributo para o conector Lotus Domino.

### <a name="lotus-notes-properties"></a>Propriedades do Lotus Notes
Quando você provisiona objetos Person para seu diretório Lotus Domino, seus objetos devem ter um conjunto de propriedades específico com valores específicos populados. Esses valores só serão necessários para as operações Create.

A tabela a seguir lista essas propriedades e fornece uma descrição delas.

| Propriedade | Descrição |
| --- | --- |
| \_MMS_AltFullName |O nome completo alternativo do usuário. |
| \_MMS_AltFullNameLanguage |O idioma a ser usado para especificar o nome completo alternativo do usuário. |
| \_MMS_CertDaysToExpire |O número de dias a partir da data atual para expiração do certificado. Se não for especificado, a data padrão será de dois anos a partir da data atual. |
| \_MMS_Certifier |A propriedade que contém o nome da hierarquia organizacional do certificador. Por exemplo: OU=OrganizationUnit,O=Org,C=Country. |
| \_MMS_IDPath |Se a propriedade estiver vazia, nenhum arquivo de identificação do usuário será criado localmente no Servidor de Sincronização. Se a propriedade contiver um nome de arquivo, um arquivo de ID de usuário será criado na pasta de metadados. A propriedade também pode conter um caminho completo. |
| \__MMS_IDRegType |As pessoas podem ser classificadas como contatos, Usuários dos EUA e Usuários internacionais. A tabela a seguir lista os valores possíveis:  <li>0 - Contato</li><li>1 - Usuário dos EUA</li><li>2 - Usuário internacional</li> |
| \_MMS_IDStoreType |Propriedade necessária para os usuários dos EUA e internacionais. A propriedade contém um valor inteiro que especifica se a identificação do usuário é armazenada como um anexo no catálogo de endereços do Notes ou no arquivo de email da pessoa. Se o arquivo ID do Usuário for um anexo no catálogo de endereços, como opção, ele poderá ser criado como um arquivo com \_MMS_IDPath. <li>Vazio — arquivo de ID do repositório no Cofre de ID, nenhum arquivo de identificação (usado para Contatos).</li><li> 1 - Anexo no catálogo de endereços do Notes. A propriedade \_MMS_Password deve ser definida para os arquivos de identificação de usuário anexados</li><li>2 - ID do repositório no arquivo de email da pessoa. O \_MMS_UseAdminP deve ser definido como falso para permitir que o arquivo de email seja criado durante o registro de Pessoa. A propriedade \_MMS_Password deve ser definida para os arquivos de identificação de usuário.</li> |
| \_MMS_MailQuotaSizeLimit |O número de megabytes que são permitidos para o banco de dados do arquivo de email. |
| \_MMS_MailQuotaWarningThreshold |O número de megabytes que são permitidos para o banco de dados do arquivo de email antes de um aviso ser emitido. |
| \_MMS_MailTemplateName |O arquivo de modelo de email que é usado para criar o arquivo de email do usuário. Se um modelo for especificado, o arquivo de email será criado usando o modelo especificado. Se nenhum modelo for especificado, o arquivo de modelo padrão será usado para criar o arquivo. |
| \_MMS_OU |A propriedade opcional que é o nome da UO sob o certificador. Essa propriedade deve estar vazia para os contatos. |
| \_MMS_Password |A propriedade necessária para os usuários. A propriedade contém a senha para o arquivo de identificação do objeto. |
| \_MMS_UseAdminP |A propriedade deve ser definida como true se o arquivo de email deve ser criado pelo processo AdminP no servidor Domino (assíncrono para o processo de exportação). Se a propriedade for definida como falsa, o arquivo de email será criado com o usuário Domino (síncrono no processo de exportação). |

Para um usuário com um arquivo de identificação associado, a propriedade \_MMS_Password deve conter um valor. Para acesso de email por meio do cliente Lotus Notes, as propriedades MailServer e MailFile de um usuário devem conter um valor.

Para acessar o email por meio de um navegador da Web, as seguintes propriedades deverão conter valores:

* MailFile - propriedade necessária que contém o caminho no servidor Lotus Domino, onde o arquivo de email está armazenado.
* MailServer - propriedade necessária que contém o nome do servidor Lotus Domino. Esse valor é o nome a ser usado ao criar o arquivo de email Lotus no servidor Domino.
* HTTPPassword - propriedade opcional que contém a senha de acesso da Web para o objeto.

Para acessar o servidor Domino sem o recurso de email, a propriedade HTTPPassword deve conter um valor. A propriedade MailFile e a propriedade MailServer podem estar vazias.

Com \_MMS_ IDStoreType = 2 (ID do repositório no arquivo de email), a propriedade MailSystem de NotesRegistrationclass é definida como REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Atributos obrigatórios
O conector Lotus Domino é compatível basicamente com esses tipos de objeto (tipos de documento):

* Agrupar
* Banco de dados Entrada de Email
* Pessoa
* Contato (Indivíduo sem certificador)
* Recurso

Esta seção lista os atributos que são obrigatórios para cada tipo de objeto com suporte para exportar para um servidor Domino.

| Tipo de objeto | Atributos obrigatórios |
| --- | --- |
| Agrupar |<li>ListName</li> |
| Banco de dados de Entrada de Email |<li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| Pessoa |<li>Sobrenome</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\__MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| Contato (Indivíduo sem certificador) |<li>\__MMS_IDRegType</li> |
| Recurso |<li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>Site</li><li>displayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>Problemas e perguntas comuns
### <a name="schema-detection-does-not-work"></a>Detecção de esquema não funciona
Para ser capaz de detectar o esquema, é necessário que o arquivo schema.nsf esteja presente no servidor Domino. Esse arquivo aparecerá somente se o LDAP estiver instalado no servidor. Se o esquema não for detectável, verifique o seguinte:

* O arquivo schema.nsf está presente na pasta raiz do servidor Domino
* O usuário tem permissões para ver o arquivo schema.nsf.
* Force a reinicialização do servidor LDAP. Abra o **Console do Lotus Domino** e use o comando **Tell LDAP ReloadSchema** para recarregar o esquema.

### <a name="not-all-secondary-address-books-are-visible"></a>Nem todos os catálogos de endereços secundários estão visíveis
O conector Domino depende do recurso **Directory Assistance** para poder encontrar os catálogos de endereços secundários. Se os catálogos de endereços secundários estiverem ausentes, verifique se o [Directory Assistance](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) foi habilitado e configurado no servidor Domino.

### <a name="custom-attributes-in-domino"></a>Atributos personalizados no Domino
Há várias maneiras no Domino de estender o esquema, de modo que ele parece um atributo personalizado consumível pelo conector.

**Abordagem 1: Estender o esquema do Lotus Domino**

1. Crie uma cópia do Modelo do Diretório Domino {PUBNAMES.NTF} seguindo [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (você não deve personalizar o modelo do diretório padrão IBM Lotus Domino):
2. Abra o modelo do diretório Cópia do Domino {CONTOSO.NTF} que foi criado no Domino Designer e siga estas etapas:
   * Clique em Elementos Compartilhados e expanda os Subformulários
   * Clique duas vezes no subformulário ${ObjectName}InheritableSchema (em que {ObjectName} é o nome da classe do objeto estrutural padrão, por exemplo: Person).
   * Nome do atributo que você deseja adicionar ao esquema {MyPersonAtrribute} e correspondente a esse atributo. Crie um campo ao escolher o menu **Criar** e depois **Campo** no menu.
   * No campo adicionado, defina suas propriedades escolhendo seu Tipo, Estilo, Tamanho, Fonte e outros parâmetros relacionados na janela Propriedades do campo.
   * Mantenha o valor Padrão do atributo igual ao nome fornecido para esse atributo (por exemplo, se o nome do atributo for MyPersonAttribute, mantenha o valor padrão com o mesmo nome).
   * Salve o subformulário ${ObjectName}InheritableSchema com os valores atualizados.
3. Substitua o Modelo do Diretório Domino {PUBNAMES.NTF} pelo novo modelo personalizado {CONTOSO.NTF} seguindo [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Feche o Admin. do Domino e abra o Console do Domino para reiniciar o serviço LDAP e recarregar o esquema LDAP:
   * No Console do Domino, insira o comando sob o texto **Comando do Domino** arquivado para reiniciar o serviço LDAP – [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
   * Para recarregar o esquema LDAP, use o comando Tell LDAP - Tell LDAP ReloadSchema
5. Abra o Admin. do Domino e escolha a guia Pessoas e Grupos para ver se o atributo adicionado é refletido em Adicionar Pessoa do Domino.
6. Abra Schema.nsf na guia **Arquivos** e veja se o atributo adicionado é refletido na classe do objeto LDAP dominoPerson.

**Abordagem 2: Criar um auxClass com atributo personalizado e associar à classe do objeto**

1. Crie uma cópia do Modelo do Diretório Domino {PUBNAMES.NTF} seguindo [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (Nunca personalize o Modelo do Diretório padrão IBM Lotus Domino):
2. Abra o modelo do diretório Cópia do Domino {CONTOSO.NTF} que foi criado no Domino Designer.
3. No painel esquerdo, selecione o Código Compartilhado e, em seguida, os subformulários.
4. Clique em Novo Subformulário
5. Siga as etapas abaixo para especificar as propriedades do novo subformulário:
   * Com o novo subformulário aberto, escolha Design - Propriedades de Subformulário
   * Ao lado da propriedade Nome, digite um nome para a classe de objeto auxiliar; por exemplo, TestSubform.
   * Mantenha marcada a propriedade Opções “Incluir na caixa de diálogo Subformulário de Inserção...”
   * Desmarque a propriedade Opções "Renderizar HTML de transferência no Notes".
   * Mantenha as outras propriedades como estão e feche a caixa Propriedades do Subformulário.
   * Salve e feche o novo subformulário.
6. Siga o procedimento abaixo para adicionar um campo e definir a classe de objeto auxiliar:
   * Abra o subformulário que você criou.
   * Escolha Criar - Campo.
   * Ao lado de Nome na guia Básico da caixa de diálogo Campo, especifique qualquer nome, por exemplo: {MyPersonTestAttribute}.
   * No campo adicionado, defina suas propriedades escolhendo seu Tipo, Estilo, Tamanho, Fonte e outras propriedades relacionadas.
   * Mantenha o valor Padrão do atributo igual ao nome fornecido para esse atributo (por exemplo, se o nome do atributo for MyPersonTestAttribute, mantenha o valor padrão com o mesmo nome).
   * Salve o subformulário com valores atualizados e siga este procedimento:
     * No painel esquerdo, selecione o Código Compartilhado e, em seguida, Subformulários.
     * Selecione o novo subformulário e escolha Design - Propriedades de Design.
     * Clique na terceira guia da esquerda e selecione **Propagar essa proibição de alteração de design**.
7. Abra o subformulário ${ObjectName}ExtensibleSchema, (em que {ObjectName} é o nome da classe de objeto estrutural padrão, por exemplo, Person).
8. Insira Recurso e escolha o Subformulário (que você criou, por exemplo, TestSubform) e salve o subformulário ${ObjectName}ExtensibleSchema.
9. Substitua o Modelo do Diretório Domino {PUBNAMES.NTF} pelo novo modelo personalizado {CONTOSO.NTF} seguindo [estas etapas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Feche o Admin. do Domino e abra o Console do Domino para reiniciar o serviço LDAP e recarregar o esquema LDAP:
    * No Console do Domino, insira o comando sob o texto **Comando do Domino** arquivado para reiniciar o serviço LDAP – [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    * Para recarregar o esquema LDAP, use o comando Tell LDAP **Tell LDAP ReloadSchema**.
11. Abra o Admin. do Domino e escolha a guia Pessoas e Grupos para ver se o atributo adicionado é refletido em Adicionar Pessoa do Domino (na guia Outros).
12. Abra Schema.nsf na guia **Arquivos** e veja se o atributo adicionado é refletido sob a classe de objeto auxiliar TestSubform LDAP.

**Abordagem 3: Adicionar o atributo personalizado à classe ExtensibleObject**

1. Abra o arquivo {Schema.nsf} colocado no diretório raiz
2. Escolha as Classes de Objeto LDAP no menu esquerdo em **Todos os Documentos do Esquema** e clique no botão **Adicionar classe de objeto**:
3. Forneça o Nome LDAP no formulário de {zzzExtensibleSchema} (onde zzz é o nome da classe de objeto estrutural padrão, por exemplo, Person). Por exemplo, para estender o esquema para a classe de objeto Person, forneça {PersonExtensibleSchema} do nome LDAP.
4. Forneça o nome da classe Superior Object, para o qual você deseja estender o esquema. Por exemplo, para estender o esquema para a classe de objeto Person, forneça o nome de classe Superior Object {dominoPerson}:
5. Forneça uma OID válida correspondente para a classe de objeto.
6. Selecione Estender/atributos personalizados nos campos Obrigatório ou Tipos de Atributo Opcionais de acordo com o requisito:
7. Depois de adicionar os atributos necessários à ExtensibleObjectClass, clique em **Salvar e Fechar**.
8. Uma ExtensibleObjectClass é criada para a classe de objeto padrão respectiva com atributos estendidos.

## <a name="troubleshooting"></a>Solucionar problemas
* Para saber mais sobre como habilitar o registro em log para solucionar problemas do conector, confira [How to Enable ETW Tracing for Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).
