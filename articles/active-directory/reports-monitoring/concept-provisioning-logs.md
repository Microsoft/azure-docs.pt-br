---
title: Visão geral do provisionamento de logs no portal do Azure (visualização) | Microsoft Docs
description: Obtenha uma introdução ao provisionamento de relatórios de log no Azure Active Directory por meio do portal do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8c4876faf9ebc2619309aa0095a8ffe1e9e93d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500539"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Visão geral do provisionamento de logs no portal do Azure (versão prévia)

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- Atividade: 
    - **Entradas**: informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - [Logs de auditoria](concept-audit-logs.md): informações de atividade do sistema sobre gerenciamento de usuários e grupos, aplicativos gerenciados e atividades de diretório.
    - **Logs de provisionamento**: atividade do sistema sobre usuários, grupos e funções que são provisionadas pelo serviço de provisionamento do Azure AD. 

- Segurança: 
    - **Entradas arriscadas**: uma [entrada arriscada](../identity-protection/overview-identity-protection.md) é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco**: um [usuário arriscado](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este tópico fornece uma visão geral dos logs de provisionamento. Os logs fornecem respostas a perguntas como: 

* Quais grupos foram criados com êxito no ServiceNow?
* Quais usuários foram removidos com êxito da Adobe?
* Quais usuários do workday foram criados com êxito no Active Directory? 

## <a name="prerequisites"></a>Pré-requisitos

Esses usuários podem acessar os dados em logs de provisionamento:

* Proprietários de aplicativos (logs para seus próprios aplicativos)
* Usuários nas funções administrador de segurança, leitor de segurança, leitor de relatório, operador de segurança, administrador de aplicativos e administrador de aplicativos de nuvem
* Usuários em uma função personalizada com a [permissão provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Administradores globais


Para que você exiba o relatório de atividade de provisionamento, seu locatário deve ter uma licença de Azure AD Premium associada a ele. Para atualizar sua edição do Azure AD, consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Maneiras de interagir com os logs de provisionamento 
Os clientes podem interagir com os logs de provisionamento de quatro maneiras:

- Acessar os logs do portal do Azure, conforme descrito na próxima seção.
- Streaming dos logs de provisionamento em [Azure monitor](../app-provisioning/application-provisioning-log-analytics.md). Esse método permite a retenção de dados estendidos e a criação de painéis, alertas e consultas personalizados.
- Consultando a [API de Microsoft Graph](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) para os logs de provisionamento.
- Baixar os logs de provisionamento como um arquivo CSV ou JSON.

## <a name="access-the-logs-from-the-azure-portal"></a>Acessar os logs do portal do Azure
Você pode acessar os logs de provisionamento selecionando **logs de provisionamento** na seção **monitoramento** do painel de **Azure Active Directory** na [portal do Azure](https://portal.azure.com). Pode levar até duas horas para que alguns registros de provisionamento apareçam no Portal.

![Captura de tela que mostra seleções para acessar os logs de provisionamento.](./media/concept-provisioning-logs/access-provisioning-logs.png "Provisionando logs")


Um log de provisionamento tem uma exibição de lista padrão que mostra:

- A identidade
- A ação
- O sistema de origem
- O sistema de destino
- O status
- A data


![Captura de tela que mostra as colunas padrão em um log de provisionamento.](./media/concept-provisioning-logs/default-columns.png "Colunas padrão")

Você pode personalizar o modo de exibição de lista selecionando **colunas** na barra de ferramentas.

![Captura de tela que mostra o botão para personalizar colunas.](./media/concept-provisioning-logs/column-chooser.png "Seletor de coluna")

Essa área permite que você exiba campos adicionais ou remova os campos já exibidos.

![Captura de tela que mostra as colunas disponíveis com algumas selecionadas.](./media/concept-provisioning-logs/available-columns.png "Colunas disponíveis")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Captura de tela que mostra informações detalhadas.](./media/concept-provisioning-logs/steps.png "Filtrar")


## <a name="filter-provisioning-activities"></a>Filtrar atividades de provisionamento

Você pode filtrar os dados de provisionamento. Alguns valores de filtro são preenchidos dinamicamente com base no seu locatário. Se, por exemplo, você não tiver nenhum evento "criar" em seu locatário, não haverá uma opção **criar** filtro.

Na exibição padrão, você pode selecionar os seguintes filtros:

- **Identidade**
- **Data**
- **Status**
- **Ação**


![Captura de tela que mostra os valores de filtro.](./media/concept-provisioning-logs/default-filter.png "Filtrar")

O filtro de **identidade** permite que você especifique o nome ou a identidade sobre a qual você se preocupa. Essa identidade pode ser um usuário, grupo, função ou outro objeto. 

Você pode Pesquisar pelo nome ou ID do objeto. A ID varia de acordo com o cenário. Por exemplo, quando você estiver provisionando um objeto do Azure AD para o Salesforce, a ID de origem será a ID de objeto do usuário no Azure AD. A ID de destino é a ID do usuário no Salesforce. Quando você estiver Provisionando do WORKDAY para Active Directory, a ID de origem será a ID de funcionário do workday Worker. 

> [!NOTE]
> O nome do usuário talvez nem sempre esteja presente na coluna de **identidade** . Sempre haverá uma ID. 


O filtro **Data** permite definir um período de tempo para os dados retornados. Os valores possíveis são:

- 1 mês
- 7 dias
- 30 dias
- 24 horas
- Intervalo de tempo personalizado

Ao selecionar um período de tempo personalizado, você pode configurar uma data de início e uma data de término.

O filtro **Status** permite que você selecione:

- **Todos**
- **Êxito**
- **Falha**
- **Ignorado**

O filtro de **ação** permite filtrar essas ações:

- **Criar** 
- **Atualizar**
- **Excluir**
- **Desabilitar**
- **Outras**

Além dos filtros da exibição padrão, você pode definir os filtros a seguir.

![Captura de tela que mostra os campos que você pode adicionar como filtros.](./media/concept-provisioning-logs/add-filter.png "Escolha um campo")

- **ID do trabalho**: uma ID de trabalho exclusiva é associada a cada aplicativo para o qual você habilitou o provisionamento.   

- **ID do ciclo**: a ID do ciclo identifica exclusivamente o ciclo de provisionamento. Você pode compartilhar essa ID com o suporte ao produto para pesquisar o ciclo em que esse evento ocorreu.

- **ID da alteração**: a ID da alteração é um identificador exclusivo para o evento de provisionamento. Você pode compartilhar essa ID com o suporte do produto para pesquisar o evento de provisionamento.   

- **Sistema de origem**: você pode especificar de onde a identidade está sendo provisionada. Por exemplo, quando você estiver provisionando um objeto do Azure AD para o ServiceNow, o sistema de origem será o Azure AD. 

- **Sistema de destino**: você pode especificar para onde a identidade está sendo provisionada. Por exemplo, quando você estiver provisionando um objeto do Azure AD para o ServiceNow, o sistema de destino será ServiceNow. 

- **Aplicativo**: você pode mostrar somente os registros de aplicativos com um nome de exibição que contém uma cadeia de caracteres específica.

## <a name="provisioning-details"></a>Detalhes de provisionamento 

Ao selecionar um item no modo de exibição de lista de provisionamento, você obtém mais detalhes sobre esse item. Os detalhes são agrupados nas guias a seguir.

![Captura de tela que mostra quatro guias que contêm detalhes de provisionamento.](./media/concept-provisioning-logs/provisioning-tabs.png "Guias")

- **Etapas**: descreve as etapas executadas para provisionar um objeto. O provisionamento de um objeto pode consistir em quatro etapas:
  
  1. Importe o objeto.
  1. Determine se o objeto está no escopo.
  1. Corresponder ao objeto entre a origem e o destino.
  1. Provisionar o objeto (criar, atualizar, excluir ou desabilitar).

  ![Captura de tela mostra as etapas de provisionamento na guia etapas.](./media/concept-provisioning-logs/steps.png "Filtrar")

- **Solução de problemas & recomendações**: fornece o código de erro e o motivo. As informações de erro estarão disponíveis somente se ocorrer uma falha.

- **Propriedades modificadas**: mostra o valor antigo e o novo valor. Se não houver nenhum valor antigo, essa coluna ficará em branco.

- **Resumo**: fornece uma visão geral do que aconteceu e identificadores para o objeto nos sistemas de origem e de destino.

## <a name="download-logs-as-csv-or-json"></a>Baixar logs como CSV ou JSON

Você pode baixar os logs de provisionamento para uso posterior acessando os logs no portal do Azure e selecionando **baixar**. O arquivo será filtrado com base nos critérios de filtro que você selecionou. Torne os filtros o mais específicos possível para reduzir o tamanho e o tempo do download. 

O download do CSV inclui três arquivos:

* **ProvisioningLogs**: baixa todos os logs, exceto as etapas de provisionamento e as propriedades modificadas.
* **ProvisioningLogs_ProvisioningSteps**: contém as etapas de provisionamento e a ID de alteração. Você pode usar a ID de alteração para ingressar no evento com os outros dois arquivos.
* **ProvisioningLogs_ModifiedProperties**: contém os atributos que foram alterados e a ID de alteração. Você pode usar a ID de alteração para ingressar no evento com os outros dois arquivos.

#### <a name="open-the-json-file"></a>Abrir o arquivo JSON
Para abrir o arquivo JSON, use um editor de texto como [Microsoft Visual Studio código](https://aka.ms/vscode). Visual Studio Code torna o arquivo mais fácil de ler fornecendo realce de sintaxe. Você também pode abrir o arquivo JSON usando navegadores em um formato não editável, como [o Microsoft Edge](https://aka.ms/msedge). 

#### <a name="prettify-the-json-file"></a>Melhorar o arquivo JSON
O arquivo JSON é baixado no formato reduzidos para reduzir o tamanho do download. Esse formato pode tornar a carga difícil de ler. Confira duas opções para melhorar o arquivo:

- Use [Visual Studio Code para formatar o JSON](https://code.visualstudio.com/docs/languages/json#_formatting).

- Use o PowerShell para formatar o JSON. Esse script produzirá o JSON em um formato que inclui tabulações e espaços: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analisar o arquivo JSON

Aqui estão alguns comandos de exemplo para trabalhar com o arquivo JSON usando o PowerShell. Você pode usar qualquer linguagem de programação com a qual esteja familiarizado.  

Primeiro, [Leia o arquivo JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) executando este comando:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Agora você pode analisar os dados de acordo com seu cenário. Aqui estão alguns exemplos: 

- Saída de todas as IDs de trabalho no arquivo JSON:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Saída de todas as IDs de alteração para eventos em que a ação foi "criar":

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>O que você deve saber

Aqui estão algumas dicas e considerações para o provisionamento de relatórios:

- O portal do Azure repositórios relataram dados de provisionamento por 30 dias se você tiver uma edição Premium e 7 dias se tiver uma edição gratuita. Você pode publicar os logs de provisionamento para [log Analytics](../app-provisioning/application-provisioning-log-analytics.md) para retenção além de 30 dias. 

- Você pode usar o atributo ID de alteração como um identificador exclusivo. Isso é útil quando você está interagindo com o suporte a produtos, por exemplo.

- Você pode ver eventos ignorados para usuários que não estão no escopo. Isso é esperado, especialmente quando o escopo de sincronização é definido como todos os usuários e grupos. O serviço avaliará todos os objetos no locatário, mesmo aqueles que estão fora do escopo. 

- Os logs de provisionamento não estão disponíveis no momento na nuvem governamental. Se você não puder acessar os logs de provisionamento, use os logs de auditoria como uma solução alternativa temporária. 

- Os logs de provisionamento não mostram importações de função (aplica-se a AWS, Salesforce e zendesk). Você pode encontrar os logs para importações de função nos logs de auditoria. 

## <a name="error-codes"></a>Códigos do Erro

Use a tabela a seguir para entender melhor como resolver erros encontrados nos logs de provisionamento. Para quaisquer códigos de erro ausentes, forneça comentários usando o link na parte inferior desta página. 

|Código do erro|Descrição|
|---|---|
|Conflito, EntryConflict|Corrija os valores de atributo conflitantes no Azure AD ou no aplicativo. Ou então, examine a configuração de atributo correspondente se a conta de usuário conflitante deveria ser correspondida e assumida. Examine a [documentação](../app-provisioning/customize-application-attributes.md) para obter mais informações sobre como configurar atributos correspondentes.|
|TooManyRequests|O aplicativo de destino rejeitou essa tentativa de atualizar o usuário porque ele está sobrecarregado e recebendo muitas solicitações. Não há nada a fazer. Essa tentativa será desativada automaticamente. A Microsoft também foi notificada desse problema.|
|InternalServerError |O aplicativo de destino retornou um erro inesperado. Um problema de serviço com o aplicativo de destino pode estar impedindo que isso funcione. Essa tentativa será desativada automaticamente em 40 minutos.|
|InsufficientRights, MethodNotAllowed, não permitido, não autorizado| O Azure AD foi autenticado com o aplicativo de destino, mas não foi autorizado a executar a atualização. Examine as instruções que o aplicativo de destino forneceu, juntamente com o respectivo [tutorial](../saas-apps/tutorial-list.md)de aplicativo.|
|UnprocessableEntity|O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo de destino pode não estar correta ou um problema de serviço com o aplicativo de destino pode estar impedindo que isso funcione.|
|WebExceptionProtocolError |Ocorreu um erro de protocolo HTTP ao conectar-se ao aplicativo de destino. Não há nada a fazer. Essa tentativa será desativada automaticamente em 40 minutos.|
|InvalidAnchor|Um usuário que foi criado ou correspondido anteriormente pelo serviço de provisionamento não existe mais. Verifique se o usuário existe. Para forçar uma nova correspondência de todos os usuários, use a API Microsoft Graph para [reiniciar o trabalho](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). <br><br>Reiniciar o provisionamento irá disparar um ciclo inicial, o que pode levar tempo para ser concluído. Reiniciar o provisionamento também exclui o cache que o serviço de provisionamento usa para operar. Isso significa que todos os usuários e grupos no locatário terão que ser avaliados novamente e determinados eventos de provisionamento poderão ser descartados.|
|NotImplemented | O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo pode não estar correta ou um problema de serviço com o aplicativo de destino pode estar impedindo que isso funcione. Examine as instruções que o aplicativo de destino forneceu, juntamente com o respectivo [tutorial](../saas-apps/tutorial-list.md)de aplicativo. |
|MandatoryFieldsMissing, MissingValues |Não foi possível criar o usuário porque os valores necessários estão ausentes. Corrija os valores de atributo ausentes no registro de origem ou examine a configuração de atributo correspondente para garantir que os campos obrigatórios não sejam omitidos. [Saiba mais](../app-provisioning/customize-application-attributes.md) sobre como configurar atributos correspondentes.|
|SchemaAttributeNotFound |Não foi possível executar a operação porque foi especificado um atributo que não existe no aplicativo de destino. Consulte a [documentação](../app-provisioning/customize-application-attributes.md) sobre personalização de atributo e verifique se a configuração está correta.|
|InternalError |Ocorreu um erro de serviço interno no serviço de provisionamento do Azure AD. Não há nada a fazer. Essa tentativa será repetida automaticamente em 40 minutos.|
|InvalidDomain |Não foi possível executar a operação porque um valor de atributo contém um nome de domínio inválido. Atualize o nome de domínio no usuário ou adicione-o à lista de permitidos no aplicativo de destino. |
|Tempo limite |A operação não pôde ser concluída porque o aplicativo de destino demorou muito tempo para responder. Não há nada a fazer. Essa tentativa será repetida automaticamente em 40 minutos.|
|LicenseLimitExceeded|Não foi possível criar o usuário no aplicativo de destino porque não há licenças disponíveis para esse usuário. Adquira mais licenças para o aplicativo de destino. Ou então, examine as atribuições de usuário e a configuração de mapeamento de atributo para garantir que os usuários corretos sejam atribuídos com os atributos corretos.|
|DuplicateTargetEntries  |A operação não pôde ser concluída porque mais de um usuário no aplicativo de destino foi encontrado com os atributos correspondentes configurados. Remova o usuário duplicado do aplicativo de destino ou [Reconfigure os mapeamentos de atributo](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Não foi possível concluir a operação porque mais de um usuário foi encontrado com os atributos correspondentes configurados. Remova o usuário duplicado ou [Reconfigure os mapeamentos de atributo](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Quando cada usuário é avaliado, o sistema tenta importar o usuário do sistema de origem. Esse erro geralmente ocorre quando o usuário que está sendo importado não tem a propriedade correspondente definida em seus mapeamentos de atributo. Sem um valor presente no objeto de usuário para o atributo correspondente, o sistema não pode avaliar o escopo, a correspondência ou a exportação de alterações. Observe que a presença desse erro não indica que o usuário está no escopo, porque você ainda não avaliou o escopo para o usuário.|
|EntrySynchronizationSkipped | O serviço de provisionamento consultou com êxito o sistema de origem e identificou o usuário. Nenhuma ação adicional foi realizada no usuário e elas foram ignoradas. O usuário pode estar fora do escopo ou talvez o usuário já existia no sistema de destino sem nenhuma alteração adicional necessária.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Uma solicitação GET para recuperar um usuário ou grupo recebeu vários usuários ou grupos na resposta. O sistema espera receber apenas um usuário ou grupo na resposta. [Por exemplo](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group), se você fizer uma solicitação get para recuperar um grupo e fornecer um filtro para excluir Membros e seu sistema para o ponto de extremidade do scim (gerenciamento de identidade entre domínios) retornar os membros, você receberá esse erro.|

## <a name="next-steps"></a>Próximas etapas

* [Verificar o status do provisionamento do usuário](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problema na configuração do provisionamento do usuário para um aplicativo de galeria do Azure AD](../app-provisioning/application-provisioning-config-problem.md)
* [API do Graph para provisionamento de logs](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)