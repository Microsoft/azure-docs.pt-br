---
title: 'Azure AD Connect: Histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões de Azure AD Connect e Azure AD Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73318d1ee14894f5d22f7c4d2e61418e3b1038c1
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636870"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Histórico de lançamento de versões
A equipe do Azure AD (Azure Active Directory) atualiza regularmente o Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi projetado para ajudar você a controlar as versões que foram lançadas e para entender quais alterações estão na versão mais recente.



Essa tabela é uma lista de tópicos relacionados:

Tópico |  Detalhes
--------- | --------- |
Etapas para atualizar do Azure AD Connect | Métodos diferentes para [atualizar de uma versão anterior para a versão mais recente](how-to-upgrade-previous-version.md) do Azure AD Connect.
Permissões necessárias | Para obter permissões necessárias para aplicar uma atualização, veja [contas e permissões](reference-connect-accounts-permissions.md#upgrade).
Baixar| [Baixar o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>O lançamento de uma nova versão do Azure AD Connect é um processo que requer várias etapas de controle de qualidade para garantir o bom funcionamento do serviço e, enquanto passamos por esse processo, o número de versão de uma nova versão, bem como o status da versão, é atualizado para refletir o estado mais recente.
Enquanto passarmos por esse processo, o número de versão será mostrado com um "X" no lugar do número da versão secundária, como em "1.3.X.0"; isso indica que as notas sobre a versão deste documento são válidas para todas as versões que começam com "1.3.". Assim que finalizarmos o processo de versão, o número da versão será atualizado para a versão lançada mais recentemente e o status da versão será atualizado para "Liberado para download e atualização automática".
Nem todas as versões do Azure AD Connect serão disponibilizadas para atualização automática. O status da versão indicará se uma versão foi disponibilizada para atualização automática ou apenas para baixar. Se a atualização automática foi habilitada no seu servidor do Azure AD Connect, então esse servidor atualizará automaticamente para a versão mais recente do Azure AD Connect que é liberado para atualização automática. Observe que nem todas as configurações do Azure AD Connect estão qualificadas para atualização automática. 

Para esclarecer o uso da atualização automática, é destinada a enviar por push todas as atualizações importantes e correções críticas para você. Isso não é necessariamente a versão mais recente, pois nem todas as versões exigirão/incluirão uma correção para um problema de segurança crítico (apenas um exemplo de muitos). Um problema como esse seria resolvido com uma nova versão fornecida por meio da atualização automática. Se não houver esses problemas, não haverá nenhuma atualização enviada usando a atualização automática e, em geral, se você estiver usando a versão de atualização automática mais recente, deverá ser bom.
No entanto, se você quiser todos os recursos e atualizações mais recentes, a melhor maneira de ver se há algum é verificar essa página e instalá-las como desejar. 

Siga este link para ler mais sobre [atualização automática](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> A partir de 1º de novembro de 2020, começaremos a implementar um processo de substituição pelo qual as versões do Azure AD Connect lançadas há mais de 18 meses serão preteridas. Vamos começar esse processo preterindo todas as versões do Azure AD Connect com a versão 1.3.20.0 (lançada em 24/4/2019) e anteriores, e continuaremos a avaliar a substituição de versões mais antigas do Azure AD Connect sempre que uma nova versão for lançada.
>
> Você precisa verificar se está executando uma versão recente do Azure AD Connect para ter uma experiência de suporte ideal. 
>
>Se você executar uma versão preterida do Azure AD Connect, talvez não tenha as correções de segurança, as melhorias de desempenho, a solução de problemas, as ferramentas de diagnóstico e aprimoramentos de serviço mais recentes e, se precisar de suporte, talvez não consiga receber o nível de serviço de que sua organização precisa.
>
>Se você tiver habilitado Azure AD Connect para sincronização, começará automaticamente a receber notificações de Integridade que avisam sobre substituições futuras quando estiver executando uma das versões mais antigas.
>
>Consulte [este artigo](./how-to-upgrade-previous-version.md) para saber mais sobre como atualizar o Azure AD Connect para a versão mais recente.
>
>Para obter informações de histórico de versão sobre versões preteridas, consulte [Azure ad Connect arquivo de histórico de lançamento de versão](reference-connect-version-history-archive.md)

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Status de liberação
07/29/2020: liberado para download

### <a name="functional-changes"></a>Alterações funcionais
Este é um lançamento de correção de bug. Não há nenhuma alteração funcional nesta versão.

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigido um problema em que o administrador não pode habilitar "logon único contínuo" se a conta de computador do AZUREADSSOACC já estiver presente no "Active Directory".
- Correção de um problema que causou um erro de preparo durante a importação Delta da API v2 para um objeto conflitante que foi reparado por meio do portal de integridade.
- Corrigido um problema na configuração de importação/exportação na qual a regra personalizada desabilitada foi importada como habilitada.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Status de liberação
07/10/2020: liberado para download

### <a name="functional-changes"></a>Alterações funcionais
Esta versão inclui uma visualização pública da funcionalidade para exportar a configuração de um servidor de Azure AD Connect existente para um. Arquivo JSON que pode ser usado ao instalar um novo servidor de Azure AD Connect para criar uma cópia do servidor original.

Uma descrição detalhada desse novo recurso pode ser encontrada neste [artigo](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Problemas corrigidos
- Corrigido um bug em que haveria um falso aviso sobre o tamanho do BD local nas compilações localizadas durante a atualização.
- Corrigido um bug em que haveria um erro falso nos eventos de aplicativo para a troca de nome de conta/nome de domínio.
- Correção de um erro em que Azure AD Connect não seria possível instalar em um controlador de domínio, fornecendo o erro "membro não encontrado".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Status de liberação
7/5/2020: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esse build de hotfix corrige um problema em que, quando apenas contêineres netos eram selecionados, domínios não selecionados estavam sendo selecionados incorretamente na interface do usuário do assistente.


>[!NOTE]
>Essa versão inclui a nova API de ponto de extremidade V2 de sincronização do Azure AD Connect.  Esse novo ponto de extremidade V2 está atualmente em visualização pública.  É necessário ter essa versão, ou posterior, para usar a nova API de ponto de extremidade V2.  No entanto, a instalação dessa versão não basta para habilitar o ponto de extremidade V2. Você continuará a usar o ponto de extremidade V1 enquanto não habilitar o ponto de extremidade V2.  Siga as etapas em [API de ponto de extremidade V2 de sincronização do Azure AD Connect (visualização pública)](how-to-connect-sync-endpoint-api-v2.md) para habilitá-la e aceitar a visualização pública.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Status de liberação
23/4/2020: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esse build de hotfix corrige um problema introduzido no build 1.5.20.0, em que um administrador de locatários com MFA não conseguia habilitar o DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Status de liberação
20/4/2020: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Esse build de hotfix corrigirá um problema no build 1.5.20.0 se você tiver clonado a regra **In from AD - Group Join** e não tiver clonado a regra **In from AD - Group Common**.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Status de liberação
9/4/2020: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos
- Esse build de hotfix corrigirá um problema com o build 1.5.18.0 se você tiver o recurso Filtro de grupo habilitado e usar mS-DS-ConsistencyGuid como indicador de origem.
- Foi corrigido um problema no módulo do PowerShell ADSyncConfig, em que a invocação do comando DSACLS usado em todos os cmdlets Permissões Set-ADSync* causavam um dos seguintes erros:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Se você tiver clonado a regra de sincronização **In from AD - Group Join** , não tiver clonado a regra de sincronização **In from AD - Group Common** e planejar fazer upgrade, conclua as seguintes etapas como parte da atualização:
> 1. Durante a atualização, desmarque a opção **Iniciar o processo de sincronização ao concluir a configuração**.
> 2. Edite a regra de sincronização de junção clonada e adicione as seguintes duas transformações:
>     - Defina o fluxo direto `objectGUID` como `sourceAnchorBinary`.
>     - Defina o fluxo de expressão `ConvertToBase64([objectGUID])` como `sourceAnchor`.     
> 3. Habilite o agendador usando `Set-ADSyncScheduler -SyncCycleEnabled $true`.



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Status de liberação
2/4/2020: Liberado para download

### <a name="functional-changes-adsyncautoupgrade"></a>Alterações nas funções de ADSyncAutoUpgrade 

- Adicionado suporte ao recurso mS-DS-ConsistencyGuid de objetos de grupo. Isso permite mover grupos entre florestas ou reconectar grupos no AD ao Azure AD quando o objectID do grupo do AD é alterado, por exemplo, quando um servidor do AD é recriado após uma calamidade. Para obter mais informações, confira [Movendo grupos entre florestas](how-to-connect-migrate-groups.md).
- O atributo mS-DS-ConsistencyGuid é definido automaticamente em todos os grupos sincronizados, e você não precisa fazer nada para habilitar esse recurso. 
- Get-ADSyncRunProfile removido porque ele não está mais em uso. 
- Foi alterado o aviso que você vê ao tentar usar uma conta de Administrador Corporativo ou de Administrador de Domínio na conta do conector do AD DS para fornecer mais contexto. 
- Adição de um novo cmdlet para remover objetos do espaço do conector; a antiga ferramenta CSDelete.exe foi removida e substituída pelo novo cmdlet Remove-ADSyncCSObject. O cmdlet Remove-ADSyncCSObject usa um CsObject como entrada. Esse objeto pode ser recuperado por meio do cmdlet Get-ADSyncCSObject.

>[!NOTE]
>A antiga ferramenta CSDelete.exe foi removida e substituída pelo novo cmdlet Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigido um bug no seletor de UO/na floresta de write-back do grupo que executava novamente o assistente do Azure AD Connect depois de desabilitar o recurso. 
- Introduzida uma nova página de erro que será exibida se os valores de registro DCOM necessários estiverem ausentes e fornecerá um novo link de ajuda. As informações também são gravadas em arquivos de log. 
- Corrigido um problema com a criação da conta de sincronização de Azure Active Directory em que a habilitação de Extensões de Diretório ou PHS podia falhar porque a conta não foi propagada em todas as réplicas de serviço antes da tentativa de uso. 
- Corrigido um bug no utilitário de compactação de erros de sincronização que não estava manipulando caracteres substitutos corretamente. 
- Corrigido um bug na atualização automática que deixava o servidor no estado suspenso do agendador. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Status de liberação
9/12/2019: Liberado para download. Não disponível pela atualização automática.
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
- Atualizamos a sincronização de hash de senha do Azure AD Domain Services para lidar adequadamente com preenchimento em hashes do Kerberos.  Isso fornecerá uma melhoria no desempenho durante a sincronização de senha do Azure AD para Azure AD Domain Services.
- Adicionamos suporte a sessões confiáveis entre o agente de autenticação e o barramento de serviço.
- Essa versão impõe o TLS 1.2 à comunicação entre o agente de autenticação e os serviços de nuvem.
- Adicionamos um cache DNS para conexões WebSocket entre o agente de autenticação e os serviços de nuvem.
- Adicionamos a capacidade de direcionar um agente específico da nuvem para testar a conectividade do agente.

### <a name="fixed-issues"></a>Problemas corrigidos
- A versão 1.4.18.0 teve um bug em que o cmdlet do PowerShell para DSSO estava usando as credenciais de logon do Windows em vez das credenciais de administrador fornecidas durante a execução do PS. Como resultado, não era possível habilitar DSSO em várias florestas por meio da interface do usuário Azure AD Connect. 
- Foi feita uma correção para habilitar o DSSO simultaneamente em toda a floresta por meio da interface do usuário Azure AD Connect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Status de liberação
08/11/2019: Liberado para download. Não disponível pela atualização automática.

>[!IMPORTANT]
>Devido a uma alteração no esquema interno nessa versão do Azure AD Connect, se você gerenciar as definições de configuração de relação de confiança do AD FS usando o MSOnline do PowerShell, precisará fazer upgrade do seu módulo do MSOnline PowerShell para a versão 1.1.183.57 ou superior

### <a name="fixed-issues"></a>Problemas corrigidos

Essa versão corrige um problema com dispositivos ingressados no Azure AD híbridos existentes. Essa versão contém uma nova regra de sincronização de dispositivo que corrige o problema.
Observe que essa alteração de regra pode causar a exclusão de dispositivos obsoletos do Azure AD. Isso não é motivo de preocupação, pois esses objetos de dispositivo não são usados pelo Azure AD durante a autorização de Acesso Condicional. Para alguns clientes, o número de dispositivos que será excluído por essa alteração de regra pode exceder o limite de exclusão. Se você vir que a exclusão de objetos de dispositivo no Azure AD excede o limite de exclusão de exportação, é aconselhável permitir que as exclusões sejam feitas. [Como permitir que as exclusões sejam feitas quando excedem o limite de exclusão](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Status de liberação
28/9/2019: Liberado para atualização automática em locatários selecionados. Indisponível para download.

Essa versão corrige um bug que fez alguns servidores serem atualizados automaticamente de uma versão anterior para 1.4.18.0 e tiveram problemas com a SSPR (redefinição de senha self-service) e o Write-back de senha.

### <a name="fixed-issues"></a>Problemas corrigidos

Em determinadas circunstâncias, os servidores que foram atualizados automaticamente para a versão 1.4.18.0 não reabilitaram a Redefinição de senha self-service e o Write-back de senha após a conclusão da atualização. Essa versão de atualização automática corrige o problema e reabilita a redefinição de senha self-service e o Write-back de senha.

Corrigimos um bug no utilitário de compactação de erros de sincronização que não estava manipulando caracteres substitutos corretamente.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Estamos investigando um incidente em que alguns clientes estão enfrentando um problema com os dispositivos ingressados no Azure AD híbridos após a atualização para a atual versão do Azure AD Connect. Aconselhamos os clientes que implantaram a junção ao Azure AD híbrido a adiar a atualização para essa versão até que a causa raiz desses problemas seja totalmente compreendida e mitigada. Mais informações serão fornecidas assim que possível.

>[!IMPORTANT]
>Nessa versão do Azure AD Connect, alguns clientes podem ver que alguns ou todos os seus dispositivos Windows desaparecem do Azure AD. Isso não é motivo de preocupação, pois essas identidades de dispositivo não são usadas pelo Azure AD durante a autorização de Acesso Condicional. Para obter mais informações, confira [Entendendo o desaparecimento de dispositivos do Azure AD Connect 1.4.xx.x](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Status de liberação
25/9/2019: Liberado somente para atualização automática.

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
- Novas ferramentas de solução de problemas ajudam a solucionar os cenários "usuário não está sincronizando", "grupo não está sincronizando" ou "membros do grupo não estão sincronizando".
- Adicione suporte para nuvens nacionais em Azure AD Connect script de solução de problemas.
- Os clientes devem ser informados de que os pontos de extremidade WMI preteridos de MIIS_Service foram removidos. Todas as operações de WMI agora devem ser feitas por meio de cmdlets do PS.
- Melhoria de segurança redefinindo a delegação restrita no objeto AZUREADSSOACC.
- Quando você adiciona/edita uma regra de sincronização, se houver algum atributo usado na regra que esteja no esquema do conector, mas não tenha sido adicionado ao conector, os atributos serão adicionados automaticamente ao conector. O mesmo acontece para o tipo de objeto que a regra afeta. Se algo for adicionado ao conector, ele será marcado para importação completa no próximo ciclo de sincronização.
- Não há mais suporte ao uso de Administrador Corporativo ou de Domínio como a conta do conector nas novas implantações do Azure AD Connect. As implantações Azure AD Connect atuais usando um administrador corporativo ou de domínio como a conta do conector não serão afetadas por esta versão.
- No Gerenciador de Sincronização, uma sincronização completa é executada na criação/edição/exclusão de uma regra. Um pop-up será exibido quando houver alteração de regra e notificará o usuário em caso de importação completa ou sincronização completa.
- Etapas de mitigação adicionadas para erros de senha à página ' conectores > Propriedades > conectividade '.
- Foi adicionado um aviso de substituição do Synchronization Service Manager na página de propriedades do conector. Esse aviso notifica o usuário de que as alterações devem ser feitas por meio do assistente do Azure AD Connect.
- Adição de novo erro em problemas com a política de senha de um usuário.
- Impedimento de configuração incorreta da filtragem de grupo por filtros de domínio e UO. A filtragem de grupo mostrará um erro quando o domínio/a UO do grupo inserido já estiver filtrado e impedirá que o usuário avance até que o problema seja resolvido.
- Os usuários não podem mais criar um conector para o Active Directory Domain Services ou para o Windows Azure Active Directory na interface do usuário do Synchronization Service Manager.
- Corrigida a acessibilidade de controles de interface do usuário personalizados no Synchronization Service Manager.
- Habilitamos seis tarefas de gerenciamento de federação para todos os métodos de entrada no Azure AD Connect.  (Anteriormente, apenas a tarefa "Atualizar certificado TLS/SSL do AD FS" estava disponível para todas as entradas.)
- Adicionado um aviso na alteração do método de entrada de federação para PHS ou PTA que informa que todos os domínios e usuários do Azure AD serão convertidos para autenticação gerenciada.
- Certificados de assinatura de token removidos da tarefa "Redefinir a confiança do Azure AD e do AD FS" e adicionada uma subtarefa separada para atualizar esses certificados.
- Adicionada uma nova tarefa de gerenciamento de federação chamada "Gerenciar certificados", com subtarefas para atualizar os certificados TLS ou de assinatura de token do farm do AD FS.
- Adicionada uma nova subtarefa de gerenciamento de federação chamada "Especificar servidor primário", que permite aos administradores especificar um novo servidor primário para o farm do AD FS.
- Adicionada uma nova tarefa de gerenciamento de federação chamada "Gerenciar servidores", que tem subtarefas para implantar um servidor do AD FS, implantar um servidor proxy de aplicativo Web e especificar o servidor primário.
- Adicionada uma nova tarefa de gerenciamento de federação chamada "Exibir configuração da federação", que exibe as configurações atuais do AD FS.  (Por causa dessa adição, as configurações do AD FS foram removidas da página "Examinar sua solução".)

### <a name="fixed-issues"></a>Problemas corrigidos
- Problema de erro de sincronização resolvido no cenário em que um objeto de usuário que assume o controle do objeto de contato correspondente tem uma autoreferência (por exemplo, o usuário é seu próprio gerente).
- Os pop-ups da ajuda agora aparecem no foco do teclado.
- Para a atualização automática, se algum aplicativo conflitante estiver sendo executado há até 6 horas, ele é encerrado e o upgrade continua.
- Limite o número de atributos que um cliente pode selecionar a 100 por objeto ao escolher extensões de diretório. Isso impedirá que o erro ocorra durante a exportação, pois o Azure tem o máximo de 100 atributos de extensão por objeto.
- Correção de um bug para tornar o script de conectividade do AD mais robusto.
- Correção de um bug para fazer Azure AD Connect instalar em um computador usando um serviço WCF de pipes nomeados existente mais robusto.
- Diagnóstico aprimorado e solução de problemas em relação a políticas de grupo que não permitem que o serviço ADSync seja iniciado quando é instalado inicialmente.
- Corrigido um bug em que o nome de exibição de um computador com Windows era gravado incorretamente.
- Corrigido um bug em que o tipo de SO de um computador Windows era gravado incorretamente.
- Corrigido um bug em que computadores sem Windows 10 estavam sincronizando inesperadamente. Observe que o efeito dessa alteração será que computadores sem Windows-10 que foram previamente sincronizados serão excluídos. Isso não afeta nenhum recurso, pois a sincronização de computadores com Windows é usada somente para ingresso no domínio híbrido do Azure AD, que funciona apenas em dispositivos Windows-10.
- Foram adicionados vários cmdlets (internos) novos ao módulo ADSync do PowerShell.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Há um problema conhecido com a atualização de Azure AD Connect de uma versão anterior para 1.3.21.0, em que o portal de Microsoft 365 não reflete a versão atualizada, mesmo que Azure AD Connect atualizada com êxito.
>
> Para resolver isso, você precisa importar o módulo **AdSync** e, em seguida, executar o `Set-ADSyncDirSyncConfiguration` cmdlet do PowerShell no servidor de Azure ad Connect.  Use as seguintes etapas:
>
>1. Abra o PowerShell no modo de administrador.
>2. Execute `Import-Module "ADSync"`.
>3. Execute `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Status de liberação 

14/5/2019: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos 

- Corrigido um aumento de vulnerabilidade de privilégio existente no build 1.3.20.0 do Microsoft Azure Active Directory Connect.  Essa vulnerabilidade, em determinadas condições, pode permitir que um invasor execute dois cmdlets do PowerShell no contexto de uma conta com privilégios e execute ações privilegiadas.  Essa atualização de segurança resolve o problema por desabilitar esses cmdlets. Para obter mais informações, confira [Atualização de segurança](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
