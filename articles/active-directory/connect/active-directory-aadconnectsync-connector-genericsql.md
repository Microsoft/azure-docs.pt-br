---
title: "Conector do SQL Genérico | Microsoft Docs"
description: "Este artigo descreve como configurar o conector SQL genérico da Microsoft."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: fd8ccef3-6605-47ba-9219-e0c74ffc0ec9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: a84096ba53a308855beedd76d9dec827c025cd57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="generic-sql-connector-technical-reference"></a>Referência técnica do conector SQL genérico
Este artigo descreve o conector SQL genérico. O artigo se aplica aos seguintes produtos:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * É necessário usar o hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2 o conector está disponível para download do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Para ver esse conector em ação, consulte o artigo [Generic SQL Connector step-by-step](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Visão geral do conector SQL genérico
O conector SQL genérico permite que você integre o serviço de sincronização com um sistema de banco de dados que oferece conectividade ODBC.  

Partindo de um ponto de vista detalhado, os seguintes recursos têm suporte na versão atual do conector:

| Recurso | Suporte |
| --- | --- |
| Fonte de dados conectada |O conector é compatível com todos os drivers ODBC de 64 bits. Ele foi testado com o seguinte:  <li>Microsoft SQL Server & SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 & 11g</li><li>MySQL 5.x</li> |
| Cenários |<li>Gerenciamento de ciclo de vida do objeto</li><li>Gerenciamento de senha</li> |
| Operações |<li>Importação completa e importação delta, exportação</li><li>Para exportar: Adicionar, Excluir, Atualizar e Substituir</li><li>Definir senha, alterar senha</li> |
| Esquema |<li>Detecção dinâmica de objetos e atributos</li> |

### <a name="prerequisites"></a>Pré-requisitos
Para usar o conector, verifique se você tem os seguintes itens no servidor de sincronização:

* Microsoft .NET 4.5.2 Framework ou posterior
* Drivers de cliente ODBC de 64 bits

### <a name="permissions-in-connected-data-source"></a>Permissões na fonte de dados conectada
Para criar ou executar qualquer uma das tarefas com suporte no conector SQL genérico, você deve ter:

* db_datareader
* db_datawriter

### <a name="ports-and-protocols"></a>Portas e protocolos
Para as portas necessárias ao funcionamento do driver ODBC, confira a documentação do fornecedor do banco de dados.

## <a name="create-a-new-connector"></a>Criar um novo conector
Para criar um conector SQL genérico, em **Serviço de Sincronização** selecione **Agente de Gerenciamento** e **Criar**. Selecione o **Conector SQL genérico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Conectividade
O conector usa um arquivo DSN ODBC para conectividade. Crie o arquivo DSN usando **Fontes de Dados ODBC** encontrado no menu Iniciar em **Ferramentas Administrativas**. A ferramenta administrativa, cria um **DSN de Arquivo** para que ele possa ser fornecido ao conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

A tela Conectividade é a primeira quando você cria um novo conector SQL genérico. Primeiro, forneça as seguintes informações:

* Caminho do arquivo DSN
* Autenticação
  * Nome de usuário
  * Senha

O banco de dados deve dar suporte a um dos seguintes métodos de autenticação:

* **Autenticação do Windows**: o banco de dados de autenticação usa as credenciais do Windows para verificar o usuário. O nome de usuário/senha especificados são usados para autenticar com o banco de dados. Essa conta precisa de permissões para o banco de dados.
* **Autenticação do SQL**: o banco de dados de autenticação usa o nome de usuário/senha definido na tela Conectividade para se conectar ao banco de dados. Se você armazenar a nome de usuário/senha no arquivo DSN, as credenciais fornecidas na tela Conectividade terão precedência.
* **Autenticação do banco de dados SQL do Azure**: para mais informações, veja [Conectar ao banco de dados SQL usando a autenticação do Azure Active Directory](../../sql-database/sql-database-aad-authentication.md).

**DN é Âncora**: se você selecionar esta opção, o DN também será usado como atributo de âncora. Ele pode ser usado para uma implementação simples, mas também tem as seguintes limitações:

* O conector dá suporte a apenas um tipo de objeto. Portanto, qualquer atributo de referência só pode fazer referência ao mesmo tipo de objeto.

**Tipo de Exportação: Substituir Objeto**: durante a exportação, quando apenas alguns atributos tiverem sido alteradas, o objeto inteiro com todos os atributos será exportado e substituirá o objeto existente.

### <a name="schema-1-detect-object-types"></a>Esquema 1 (Detectar tipos de objeto)
Nesta página você configurará como o conector localizará tipos de objeto diferentes no banco de dados.

Cada tipo de objeto é apresentado como uma partição e configurado ainda mais em **Configurar Partições e Hierarquias**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Método de detecção do Tipo de objeto**: o conector dá suporte a esses métodos de detecção do tipo de objeto.

* **Valor Fixo**: forneça a lista de tipos de objeto como uma lista separada por vírgulas. Por exemplo: `User,Group,Department`.  
  ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
* **Tabela/exibição/procedimento armazenado**: forneça o nome da tabela/exibição/procedimento armazenado e, em seguida, o nome da coluna que fornece a lista de tipos de objeto. Se você usar um procedimento armazenado, forneça também parâmetros para ele no formato **[Nome]:[Direção]:[Valor]**. Forneça cada parâmetro em uma linha separada (use Ctrl + Enter para obter uma nova linha).  
  ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
* **Consulta SQL**: essa opção permite que você forneça uma consulta SQL que retorna uma única coluna com tipos de objeto, por exemplo, `SELECT [Column Name] FROM TABLENAME`. A coluna retornada deve ser do tipo cadeia de caracteres (varchar).

### <a name="schema-2-detect-attribute-types"></a>Esquema 2 (Detectar tipos de atributo)
Nesta página, você configurará como os nomes e tipos de atributos serão detectados. As opções de configuração são listadas para cada tipo de objeto detectado na página anterior.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Método de detecção do tipo de atributo**: o conector dá suporte a esses métodos de detecção do tipo de atributo com cada tipo de objeto detectado na tela Esquema 1.

* **Tabela/exibição/procedimento armazenado**: forneça o nome da tabela/exibição/procedimento armazenado que deve ser usado para localizar nomes de atributo. Se você usar um procedimento armazenado, forneça também parâmetros para ele no formato **[Nome]:[Direção]:[Valor]**. Forneça cada parâmetro em uma linha separada (use Ctrl + Enter para obter uma nova linha). Para detectar nomes de atributo em um atributo de valores múltiplos, forneça uma lista separada por vírgulas de tabelas ou exibições. Não haverá suporte para cenários de valores múltiplos quando a tabela pai e filho tiverem os mesmos nomes de coluna.
* **Consulta SQL**: essa opção permite que você forneça uma consulta SQL que retorna uma única coluna com nomes de atributo, por exemplo, `SELECT [Column Name] FROM TABLENAME`. A coluna retornada deve ser do tipo cadeia de caracteres (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Esquema 3 (Definir âncora e DN)
Esta página permite que você configure a âncora e o atributo DN para cada tipo de objeto detectado. Você pode selecionar vários atributos para tornar a âncora exclusiva.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

* Atributos de valores múltiplos e boolianos não são listados.
* O mesmo atributo não pode usar DN e âncora, a menos que **DN é Âncora** seja selecionado na página Conectividade.
* Se **DN é Âncora** estiver selecionado na página Conectividade, esta página só solicitará o atributo DN. Esse atributo será usado também como o atributo de âncora.

  ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Esquema 4 (Definir tipo de atributo, referência e direção)
Essa página permite que você configure o tipo de atributo, como inteiro, binário ou Booliano, e a direção para cada atributo. Todos os atributos da página **Esquema 2** estão listados como atributos de valores múltiplos.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

* **DataType**: usado para mapear o tipo de atributo para os tipos conhecidos do mecanismo de sincronização. O padrão é usar o mesmo tipo, conforme detectado no esquema SQL, mas DateTime e referência não são facilmente detectáveis. Para esses você precisa especificar **DateTime** ou **Referência**.
* **Direção**: você pode definir a direção do atributo para Importar, Exportar ou ImportExport. ImportExport é o padrão.

![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Observações:

* Se um tipo de atributo não for detectável pelo conector, ele usará o tipo de dados String.
* **Tabelas aninhadas** podem ser consideradas tabelas de banco de dados de uma coluna. O Oracle armazena as linhas de uma tabela aninhada sem nenhuma ordem específica. No entanto, quando você recupera a tabela aninhada em uma variável de PL/SQL, as linhas recebem subscritos consecutivos, começando em 1. Que lhe dá acesso do tipo matriz a linhas individuais.
* **VARRYS** não têm suporte no conector.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Esquema 5 (Definir partição para atributos de referência)
Nesta página você configura todos os atributos de referência a cuja partição (tipo de objeto) um atributo está se referindo.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Se você usar **DN é âncora**, deverá usar o mesmo tipo de objeto do qual está fazendo referência. Não é possível referenciar outro tipo de objeto.

>[!NOTE]
A partir da atualização de março de 2017 há uma opção para "*" e quando essa opção é escolhida todos os tipos de membro possíveis serão importados.

![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/any-option.png)

>[!IMPORTANT]
 Desde maio de 2017 o "*" também conhecido como **qualquer opção** foi alterado para dar suporte ao fluxo de importação e exportação. Se você quiser usar essa opção, a exibição/tabela de vários valores deve ter um atributo que contém o tipo de objeto.

![](./media/active-directory-aadconnectsync-connector-genericsql/any-02.png)

 </br> Se "*" for selecionado, o nome da coluna com o tipo de objeto também deverá ser especificado.</br> ![](./media/active-directory-aadconnectsync-connector-genericsql/any-03.png)

Após a importação você verá algo semelhante à imagem a seguir:

  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/after-import.png)



### <a name="global-parameters"></a>Parâmetros Globais
A página Parâmetros Globais é usada para configurar importação Delta, formato de data/hora e método de senha.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)



O conector SQL genérico dá suporte aos seguintes métodos de importação Delta:

* **Gatilho**: consulte [Gerar exibições de Delta usando gatilhos](https://technet.microsoft.com/library/cc708665.aspx).
* **Marca d'água**: uma abordagem genérica que pode ser usada com qualquer banco de dados. A consulta de marca-d'água será preenchida com base no fornecedor do banco de dados. Uma coluna de marca-d'água deve estar presente em cada tabela/exibição usada. Essa coluna deve controlar inserções e atualizações para as tabelas e suas tabelas dependentes (de valores múltiplos ou filho). Os relógios entre o serviço de sincronização e o servidor de banco de dados devem ser sincronizados. Caso contrário, algumas entradas na importação delta podem ser omitidas.  
  Limitação:
  * A estratégia de marca-d'água não dá suporte a exclusão de objetos.
* **Instantâneo**(funciona somente com o Microsoft SQL Server) [Gerando exibições de Delta usando instantâneos](https://technet.microsoft.com/library/cc720640.aspx)
* **Acompanhamento de alterações**(funciona somente com o Microsoft SQL Server) [About Acompanhamento de alterações](https://msdn.microsoft.com/library/bb933875.aspx)  
  Limitações:
  * Atributo de âncora e DN devem ser parte da chave primária para o objeto selecionado na tabela.
  * Consulta SQL não tem suporte durante importação e exportação com acompanhamento de alterações.

**Parâmetros adicionais**: especifique o fuso horário do servidor de banco de dados que indica o local em que o servidor de banco de dados está localizado. Esse valor é usado para dar suporte aos vários formatos de atributos de data e hora.

O conector sempre armazena data e data e hora no formato UTC. Para converter corretamente datas e horas, o fuso horário do servidor de banco de dados e o formato usado devem ser especificados. O formato deve ser expresso no formato .Net.

Durante a exportação, todos os atributos de data e hora devem ser fornecidos ao conector no formato de hora UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuração de senha**: o conector fornece recursos de sincronização de senha e dá suporte a definição e alteração de senha.

O conector fornece dois métodos para dar suporte à sincronização de senha:

* **Procedimento armazenado**: esse método requer dois procedimentos armazenados para dar suporte a definição e alteração de senha. Digite todos os parâmetros para adicionar e alterar a operação de senha em **Definir procedimento armazenado de senha** e **Alterar parâmetros de procedimento armazenado de senha** como nos exemplos abaixo.
  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
* **Extensão de senha**: este método requer DLL de extensão de senha (você precisa fornecer o nome DLL de extensão que está implementando a interface [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ). O assembly de extensão de senha deve ser colocado na pasta de extensão para que o conector possa carregar a DLL no tempo de execução.
  ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Você também deve habilitar o Gerenciamento de senhas na página **Configurar Extensão** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar Partições e Hierarquias
Na página de partições e hierarquias, selecione todos os tipos de objeto. Cada tipo de objeto está em sua própria partição.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Você também pode substituir os valores definidos na página **Conectividade** ou **Parâmetros Globais**.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurar Âncoras
Esta página é somente leitura, pois a âncora já foi definida. O atributo de âncora selecionado sempre é acrescentado com o tipo de objeto para garantir que ele permaneça exclusivo em todos os tipos de objeto.

![ancoras](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurar parâmetros da etapa de execução
Essas etapas são configuradas em perfis de execução do conector. Essas configurações fazem o trabalho real de importar e exportar dados.

### <a name="full-and-delta-import"></a>Importação completa e Delta
O conector SQL genérico oferece suporte a importação completa e Delta usando estes métodos genérico:

* Tabela
* Visualizar
* Procedimento armazenado
* Consulta SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabela/Exibição**  
Para importar atributos de valores múltiplos de um objeto, você precisa fornecer o nome de tabela/exibição separados por vírgulas em **Nome de tabela/exibições de valores múltiplos** e as respectivas condições de junção em **Condição de junção** com a tabela principal.

Exemplo: você deseja importar o objeto de funcionário e todos os seus atributos de valores múltiplos. Há duas tabelas chamadas Funcionário (tabela principal) e Departamento (valores múltiplos).
Faça o seguinte:

* Digite **Funcionário** em **Tabela/Exibição/SP**.
* Digite Departamento em **Nome da tabela/exibições de valores múltiplos**.
* Digite a condição de junção entre Funcionário e Departamento em **Condição de Junção**, por exemplo, `Employee.DEPTID=Department.DepartmentID`.
  ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procedimentos armazenados**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

* Se você tem muitos dados, é recomendável implementar a paginação com os procedimentos armazenados.
* Para o procedimento armazenado der suporte à paginação, forneça o Índice inicial e o final. Consulte: [Paginação eficiente em grandes quantidades de dados](https://msdn.microsoft.com/library/bb445504.aspx).
* @StartIndex e @EndIndex são substituídos em tempo de execução pelo respectivo valor do tamanho de página configurado na página **Configurar Etapa**. Por exemplo, quando o conector recupera a primeira página e o tamanho da página é definido como 500, nessa situação @StartIndex é 1 e @EndIndex é 500. Esses valores aumentam quando o conector recupera páginas subsequentes e altera o valor de @StartIndex e @EndIndex.
* Para executar o procedimento armazenado com parâmetros, forneça os parâmetros no formato `[Name]:[Direction]:[Value]` . Forneça cada parâmetro em uma linha separada (use Ctrl + Enter para obter uma nova linha).
* O conector do SQL genérico também dá suporte à operação de importação de Servidores Vinculados no Microsoft SQL Server. Se informações precisarem ser recuperadas de uma Tabela no servidor Vinculado, a Tabela deverá ser fornecida no formato: `[ServerName].[Database].[Schema].[TableName]`
* O conector SQL genérico só dá suporte a objetos com estrutura semelhante (nome de alias e tipo de dados) entre informações de etapas de execução e detecção de esquema. Se o objeto selecionado do esquema e as informações fornecidas na etapa de execução forem diferentes, o conector do SQL será não poderá dar suporte a esse tipo de cenário.

**Consulta SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

* Consultas com vários conjuntos de resultados não têm suporte.
* A consulta SQL dá suporte a paginação e fornece o índice inicial e final como uma variável para dar suporte à paginação.

### <a name="delta-import"></a>Importação de delta
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

A configuração de Importação de Delta exige alguma configuração adicional, em comparação com a Importação Completa.

* Se você escolher a abordagem de instantâneo ou gatilho para acompanhar as alterações delta, forneça banco de dados de uma tabela de histórico ou instantâneo na caixa **Nome de banco de dados de Instantâneo ou tabela de Histórico** .
* Você também precisa fornecer a condição de junção entre a tabela de histórico e tabela principal, por exemplo, `Employee.ID=History.EmployeeID`
* Para controlar a transação na tabela principal da tabela de histórico, você deve fornecer o nome da coluna que contém as informações da operação (Adicionar/Atualizar/Excluir).
* Se você escolher a Marca d'água para acompanhar as alterações delta, forneça o nome da coluna que contém as informações de operação em **Nome de Coluna de Marca d'água**.
* A coluna **alterar atributo de tipo** é necessária para o tipo de alteração. Essa coluna mapeia uma alteração que ocorre na tabela primária ou tabela de valores múltiplos para um tipo de alteração na exibição de delta. Esta coluna pode conter o tipo de alteração Modify_Attribute para a alteração de nível de atributo ou um tipo de alteração Adicionar, Modificar ou Excluir, para um tipo de alteração de nível de objeto. Se for algo diferente do valor padrão de Adicionar, Modificar ou Excluir, você poderá definir esses valores usando essa opção.

### <a name="export"></a>Exportação
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

O conector SQL genérico oferece suporte à exportação usando quatro métodos:

* Tabela
* Visualizar
* Procedimento armazenado
* Consulta SQL

**Tabela/Exibição**  
se você escolher a opção de Tabela/Exibição, o conector gerará as respectivas consultas para fazer a Exportação.

**Procedimentos armazenados**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Se você escolher a opção de Procedimento Armazenado, a Exportação exigirá três procedimentos armazenados diferentes para executar operações Inserir/Atualizar/Excluir.

* **Adicionar nome do SP**: esse SP será executado se houver objetos para inserção no conector, na respectiva tabela.
* **Atualizar nome do SP**: esse SP será executado se houver objetos para atualização no conector, na respectiva tabela.
* **Excluir nome do SP**: esse SP será executado se houver objetos para exclusão no conector, na respectiva tabela.
* Atributo selecionado do esquema usado como um valor de parâmetro para o procedimento armazenado. Por exemplo, `EmployeeName: INPUT: @EmployeeName` (NomeFuncionário está selecionado no esquema do conector, e o conector substitui o respectivo valor durante a exportação)
* Para executar o procedimento armazenado com parâmetros, forneça os parâmetros no formato `[Name]:[Direction]:[Value]` . Forneça cada parâmetro em uma linha separada (use Ctrl + Enter para obter uma nova linha).

**SQL query**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Se você escolher a opção de consulta SQL, a Exportação exigirá três consultas diferentes para executar operações Inserir/Atualizar/Excluir.

* **Consulta Insert**: essa consulta será executada se houver objetos para inserção no conector, na respectiva tabela.
* **Consulta Update**: esta consulta será executada se houver objetos para atualização no conector, na respectiva tabela.
* **Consulta Delete**: esta consulta será executada se houver objetos para exclusão no conector, na respectiva tabela.
* Atributo selecionado do esquema, usado como um valor de parâmetro na consulta, por exemplo, `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Solucionar problemas
* Para saber mais sobre como habilitar o registro em log para solucionar problemas do conector, confira [How to Enable ETW Tracing for Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).
