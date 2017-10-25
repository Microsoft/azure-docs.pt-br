---
title: "Histórico de lançamento de versão do conector | Microsoft Docs"
description: "Este tópico lista todas as versões dos Conectores do FIM (Forefront Identity Manager) e MIM (Microsoft Identity Manager)"
services: active-directory
documentationcenter: 
author: fimguy
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2017
ms.author: fimguy
ms.openlocfilehash: 98eb9b3a58737da2436eed591d69a900166c6af9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connector-version-release-history"></a>Histórico de lançamento de versão do conector
Os Conectores do FIM (Forefront Identity Manager) e MIM (Microsoft Identity Manager) são atualizados com frequência.

> [!NOTE]
> Este tópico é somente para FIM e MIM. Esses conectores não são suportados para instalação no Azure AD Connect. Os conectores liberados são pré-instalados no AADConnect durante o upgrade para o Build especificado.

Este tópico lista todas as versões dos conectores que foram lançadas.

Links relacionados:

* [Baixar os Conectores mais recentes](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Conector LDAP Genérico](active-directory-aadconnectsync-connector-genericldap.md)
* [Conector do SQL Genérico](active-directory-aadconnectsync-connector-genericsql.md)
* [Conector dos Serviços Web](http://go.microsoft.com/fwlink/?LinkID=226245)
* [Conector do PowerShell](active-directory-aadconnectsync-connector-powershell.md)
* [Conector do Lotus Domino](active-directory-aadconnectsync-connector-domino.md)


## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Problemas corrigidos:

* Serviços Web genéricos:
  * Correção de um problema que estava impedindo um projeto SOAP de ser criado quando havia dois ou mais pontos de extremidade.
* SQL genérico:
  * Na operação de importação, o GSQL não estava convertendo o tempo corretamente, quando salvo no espaço do conector. O formato de data e hora padrão para o espaço do conector do GSQL foi alterado de "aaaa-MM-dd hh:mm:ssZ'' para "aaaa-MM-dd HH:mm:ssZ'.

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Problemas corrigidos:

* Serviços Web genéricos:
  * A ferramenta Wsconfig não converteu corretamente a matriz JSON de "solicitação de exemplo" para o método de serviço REST. Isso causou problemas com a serialização dessa matriz Json para a solicitação REST.
  * A Ferramenta de Configuração do Conector do Serviço Web não dá suporte ao uso de símbolos de espaço em nomes de atributo JSON 
    * Um padrão de substituição pode ser adicionado manualmente ao arquivo WSConfigTool.exe.config, por exemplo ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Lotus Notes:
  * Quando a opção **Permitir certificadores personalizados para a organização/as unidades organizacionais** está desabilitada, o conector falha durante a exportação (Atualização) Após o fluxo de exportação, todos os atributos são exportados para o Domino mas, no momento da exportação, uma KeyNotFoundException é retornada para Sincronização. 
    * Isso acontece porque a operação de renomeação falha ao tentar alterar o DN (atributo UserName) alterando um dos atributos abaixo:  
      - Sobrenome
      - Nome
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - ou
      - altcommonname

  * Quando a opção **Permitir certificadores personalizados para a organização/as unidades organizacionais** está habilitada, mas os certificadores necessários ainda estão vazios, ocorre uma KeyNotFoundException.

### <a name="enhancements"></a>Melhorias:

* SQL genérico:
  * **Cenário: reprojetado implementado:** recurso "*"
  * **Descrição da solução:** abordagem alterada para a [manipulação de atributos de referência de vários valores](active-directory-aadconnectsync-connector-genericsql.md).


### <a name="fixed-issues"></a>Problemas corrigidos:

* Serviços Web genéricos:
  * Não será possível importar a configuração do servidor se houver um Conector WebService
  * O Conector WebService não está funcionando com vários Serviços Web

* SQL genérico:
  * Nenhum tipo de objeto é listado para o atributo de referência de valor único
  * A importação delta na estratégia de Controle de Alterações exclui o objeto quando o valor é removido da tabela de vários valores
  * OverflowException no conector GSQL com DB2 no AS/400

Lotus:
  * Adicionada a opção de habilitar/desabilitar a pesquisa de UOs antes de abrir a página GlobalParameters

## <a name="114430"></a>1.1.443.0

Lançamento: março de 2017

### <a name="enhancements"></a>Melhorias

* SQL genérico:</br>
  **Sintomas de cenário:** são uma limitação conhecida com o conector do SQL onde podemos permitir somente uma referência a um tipo de objeto e exigir uma referência cruzada com membros. </br>
  **Descrição da solução:** na etapa de processamento de referências onde a opção "*" é escolhida, todas as combinações de tipos de objeto são retornadas para o mecanismo de sincronização.

>[!Important]
- Isso cria vários espaços reservados
- Isso é necessário para garantir que o nome seja exclusivo entre tipos de objetos cruzados.


* LDAP genérico:</br>
 **Cenário:** quando apenas alguns contêineres são selecionados na partição específica e a pesquisa ainda será realizada na partição inteira. O específico será filtrado pelo serviço de sincronização, mas não pelo MA que pode causar degradação do desempenho. </br>

 **Descrição de solução:**  o código do conector GLDAP é modificado para passar por todos os contêineres e objetos de pesquisa em cada um deles, em vez de pesquisar na partição inteira.


* Lotus Domino:

  **Cenário:** o suporte para exclusão de email Domino para a remoção de pessoa durante uma exportação. </br>
  **Solução:** suporte para exclusão de email configurável para a remoção de pessoa durante uma exportação.

### <a name="fixed-issues"></a>Problemas corrigidos:
* Serviços Web genéricos:
 * Ao alterar a URL do serviço nos projetos wsconfig SAP padrão usando a ferramenta de configuração do serviço da Web, aparece o seguinte erro: não foi possível localizar uma parte do caminho

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* LDAP genérico:
 * O conector de GLDAP não verá todos os atributos no AD LDS
 * Quebra de assistente quando não é detectado nenhum atributo UPN do esquema de diretório LDAP
 * Quando o atributo de "objectclass" não está selecionado, há falha na importação Delta com erros de descoberta não presentes durante a importação completa
 * Uma página de configuração "Configurar partições e hierarquias", não exibe todos os objetos cujos tipos são iguais à partição para servidores Novel no Genérico  
LDAP MA. Eles exibem apenas objetos de partição RootDSE.


* SQL genérico:
 * Fixo para bug de atributo com multivalores de importação Delta de marca d'água de SQL não importado
 * Ao exportar valores excluídos/adicionados de atributo com multivalores, eles não são excluídos/adicionados na fonte de dados.  


* Lotus Notes:
 * Um campo específico "Nome completo" é mostrado corretamente no metaverso, no entanto, o valor do atributo se torna nulo ou vazio quando exportado para notas.
 * Fixo para o erro de certificador de duplicata
 * Quando o objeto sem dados é selecionado no conector Lotus Domino com outros objetos e, em seguida, recebemos o erro de descoberta ao executar a importação completa.
 * Às vezes, no final da execução da importação Delta no conector Lotus Domino, o serviço Microsoft.IdentityManagement.MA.LotusDomino.Service.exe retorna uma mensagem de erro de aplicativo.
 * O geral da associação do grupo funciona bem e é mantido, exceto ao executar a exportação para tentar remover um usuário da associação, apesar de exibir mensagem de êxito com uma atualização, o usuário não é removido da associação no Lotus Notes.
 * A oportunidade de escolher o modo de exportação como "Acrescentar item no final" foi adicionada na configuração de GUI do Lotus MA para acrescentar itens novos no final durante a exportação de atributos com multivalores.
 * O conector adicionará a lógica necessária para excluir o arquivo da pasta de email e o cofre de ID.
 * Exclua a associação que não estiver funcionando para membro NAB cruzado.
 * Os valores devem ser excluídos com êxito do atributo de multivalores

## <a name="111170"></a>1.1.117.0
Lançamento: março de 2016

**Novo Conector**  
Versão inicial do [Conector do SQL Genérico](active-directory-aadconnectsync-connector-genericsql.md).

**Novos recursos:**

* Conector LDAP Genérico:
  * Adicionado suporte para importação delta com o Isode.
* Conector dos Serviços Web:
  * Atualizadas as atividades csEntryChangeResult e setImportErrorCode para permitir que erros no nível de objeto sejam retornados para o mecanismo de sincronização.
  * Atualizados os modelos SAP6 e SAP6User para usar a nova funcionalidade de erro no nível de objeto.
* Conector do Lotus Domino:
  * Para exportar, é necessário um certificador por catálogo de endereços. Agora é possível usar a mesma senha para todos os certificadores, a fim de facilitar o gerenciamento.

**Problemas corrigidos:**

* Conector LDAP Genérico:
  * Para o IBM Tivoli DS, alguns atributos de referência não foram detectados corretamente.
  * Para o Open LDAP durante a importação delta, os espaços em branco no início e no final das cadeias de caracteres foram truncados.
  * Para o Novell e NetIQ, houve falha em uma exportação que movia um objeto entre UOs/contêineres e ao mesmo tempo renomeava o objeto.
* Conector dos Serviços Web:
  * Se o serviço Web tinha vários pontos de extremidade para a mesma associação, o conector não os descobriu corretamente.
* Conector do Lotus Domino:
  * Uma exportação do atributo fullName para um banco de dados de entrada de email não funcionou.
  * Uma exportação que adicionava e removia um membro de um grupo exportou apenas os membros adicionados.
  * Se um Documento do Notes for inválido (o atributo isValid for definido como false), haverá uma falha do Conector.

## <a name="older-releases"></a>Versões mais antigas
Antes de março de 2016, os Conectores foram liberados como tópicos de suporte.

**LDAP Genérico**

* [KB3078617](https://support.microsoft.com/kb/3078617) – 1.0.0597, setembro de 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) – 1.0.0549, março de 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) – 1.0.0534, janeiro de 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) – 1.0.0419, setembro de 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) – 4.3.1082, março de 2014

**Serviços Web**

* [KB3008178](https://support.microsoft.com/kb/3008178) – 1.0.0419, setembro de 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) – 1.0.0419, setembro de 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) – 1.0.0597, setembro de 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) – 1.0.0549, março de 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) – 5.3.0712, agosto de 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) – 5.3.1003, fevereiro de 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) – 5.3.0721, outubro de 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) – 5.3.0534, agosto de 2013

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
