---
title: 'Azure AD Connect: Histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões do Azure AD Connect e do Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b13b23e59595acf8c637a2ef58c8098256920bea
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654052"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Histórico de lançamento de versões
A equipe do Azure AD (Azure Active Directory) atualiza regularmente o Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.


Este artigo foi projetado para ajudar você a controlar as versões que foram lançadas e para entender quais alterações estão na versão mais recente.

Essa tabela é uma lista de tópicos relacionados:

Tópico |  Detalhes
--------- | --------- |
Etapas para atualizar do Azure AD Connect | Métodos diferentes para [atualizar de uma versão anterior para a versão mais recente](how-to-upgrade-previous-version.md) do Azure AD Connect.
Permissões necessárias | Para obter permissões necessárias para aplicar uma atualização, veja [contas e permissões](reference-connect-accounts-permissions.md#upgrade).

Download | [Baixar o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Lançando uma nova versão do Azure AD Connect é um processo que requer várias etapa de controle de qualidade para garantir que a funcionalidade de operação do serviço, e enquanto percorrermos todo esse processo será atualizado o número de versão de uma nova versão, bem como o status de liberação para refletir o estado mais recente.
Enquanto percorrermos todo esse processo, o número de versão da versão será mostrado com um "X" na posição número versão secundária, como em "1.3.X.0" – Isso indica que as notas de versão neste documento são válidas para todas as versões começando com "1.3". Assim tiver finalizado o processo de lançamento o número de versão de lançamento será atualizado para a versão lançada mais recentemente e o status de liberação será atualizado para "Liberado para download e atualização automática".
Nem todas as versões do Azure AD Connect serão disponibilizadas para atualização automática. O status da versão indicará se uma versão foi disponibilizada para atualização automática ou apenas para baixar. Se a atualização automática foi habilitada no seu servidor do Azure AD Connect, então esse servidor atualizará automaticamente para a versão mais recente do Azure AD Connect que é liberado para atualização automática. Observe que nem todas as configurações do Azure AD Connect estão qualificadas para atualização automática. Siga este link para ler mais sobre [atualização automática](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)

## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Há um problema conhecido com a atualização do Azure AD Connect de uma versão anterior para 1.3.21.0 onde o portal do O365 não reflete a versão atualizada, mesmo que o Azure AD Connect foi atualizado com êxito.
>
> Para resolver o problema, você precisará importar os **AdSync** módulo e, então, execute o`Set-ADSyncDirSyncConfiguration` cmdlet do powershell no servidor do Azure AD Connect.  Você pode usar as seguintes etapas:
>
>1. Abra o Powershell no modo de administrador
>2. Execute `Import-Module "ADSync"`
>3. Execute `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`
 


### <a name="release-status"></a>Status de liberação 

05/14/2019: Liberado para download


### <a name="fixed-issues"></a>Problemas corrigidos 

- Correção de uma vulnerabilidade de elevação de privilégio que existe no Microsoft Azure Active Directory Connect compilação 1.3.20.0.  Essa vulnerabilidade, sob determinadas condições, pode permitir que um invasor execute dois cmdlets do powershell no contexto de uma conta privilegiada e executam ações privilegiadas.  Essa atualização de segurança resolve o problema desabilitando esses cmdlets. Para obter mais informações, consulte [atualização de segurança](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Status de liberação 

04/24/2019: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 

- Adicionar suporte para o domínio de atualização 
- O recurso pastas públicas do Exchange Mail fica GA 
- Melhorar o Assistente para tratamento de erros para falhas de serviço 
- Link de aviso foi adicionado para antiga interface do usuário na página de propriedades do conector. 
- O recurso do Unified write-back de grupos agora está GA 
- Mensagem de erro aprimorada SSPR quando o controlador de domínio não tem um controle LDAP 
- Diagnósticos adicionais para erros de registro DCOM durante a instalação  
- Aprimorado o rastreamento de erros de RPC PHS 
- Permitir credenciais EA de um domínio filho 
- Permitir que o nome do banco de dados a ser inserido durante a instalação (nome padrão ADSync)
- Atualizar para o ADAL 3.19.8 para pegar uma correção de WS-Trust para Ping e adicionar suporte para novas instâncias do Azure 
- Modificar grupo regras de sincronização flua samAccountName, DomainNetbios e DomainFQDN para a nuvem – necessário para declarações 
- Modificou o tratamento de regra de sincronização padrão – Leia mais [aqui](how-to-connect-fix-default-rules.md).
- Adicionado um novo agente em execução como um serviço do windows. Esse agente, denominado "Administrador de agente", permite que um diagnóstico mais detalhado remoto do servidor para ajudar a Microsoft Engineers solucionar problemas quando você abrir um caso de suporte do Azure AD Connect. Esse agente não está instalado e habilitado por padrão.  Para obter mais informações sobre como instalar e habilitar o agente, consulte [o que é o agente do Azure AD Connect Admin?](whatis-aadc-admin-agent.md). 
- Atualizado o contrato de licença de usuário final (EULA) 
- Adicionado suporte a atualização automática para implantações que usam o AD FS como seu tipo de logon.  Isso também removeu o requisito de atualizar o AD FS Azure AD terceira parte confiável como parte do processo de atualização. 
- Adicionada uma tarefa de gerenciamento de confiança do AD do Azure que fornece duas opções: analisar/atualizar a relação de confiança e redefinir a relação de confiança. 
- Alterado o comportamento de confiança de terceira parte confiável do AD FS Azure AD para que ele sempre usa a opção - SupportMultipleDomain (inclui a relação de confiança e domínio do Azure AD atualizações). 
- Alterado o comportamento de farm instalar novo AD FS para que ele requer um certificado. pfx, removendo a opção de usar um certificado previamente instalado.
- Atualizado o fluxo de trabalho instalar novo AD FS farm para que ele só permite a implantação 1 AD FS e 1 do servidor WAP.  Todos os servidores adicionais serão feitos após a instalação inicial. 

### <a name="fixed-issues"></a>Problemas corrigidos 


- Correção do SQL se reconectar a lógica do serviço ADSync 
- Correção para permitir a instalação limpa usando um SQL AOA DB vazio 
- Corrija o script de permissões do PS para refinar as permissões de GWB 
- Corrigir erros do VSS com o LocalDB  
- Corrigir mensagem enganosa de erro quando o tipo de objeto não está no escopo 
- Corrigido um problema em que a instalação do PowerShell do Azure AD em um servidor pode causar um conflito de assembly com o Azure AD Connect. 
- Corrigido o bug PHS no servidor de preparo quando as credenciais do conector são atualizadas na interface de usuário antiga. 
- Corrigidos alguns vazamentos de memória 
- Diversas correções de Autoupgrade 
- Diversas correções para o processamento não confirmados de importação e exportação 
- Corrigido um bug com uma barra invertida no domínio e UO de tratamento de filtragem 
- Corrigido um problema em que o serviço ADSync leva mais de 2 minutos para parar e causa um problema no momento da atualização. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Status de liberação

18/12/2018: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta compilação atualiza os conectores não padrão (por exemplo, conector LDAP genérico e conector SQL genérico) fornecidos com o Azure AD Connect. Para obter mais informações sobre conectores aplicáveis, consulte a versão 1.1.911.0 no [Histórico de lançamento de Versão do Conector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Status de liberação
11/12/2018: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos
Este build do hotfix permite que o usuário selecione um domínio de destino, na floresta especificada, para o contêiner RegisteredDevices ao habilitar o write-back de dispositivo.  Nas versões anteriores, as quais contêm a nova funcionalidade Opções do dispositivo (1.1.819.0 – 1.2.68.0), a localização do contêiner RegisteredDevices era limitada à raiz da floresta e não permitia domínios filhos.  Essa limitação só se manifestou nas novas implantações – atualizações in-loco não foram afetadas.  

Se algum build com a funcionalidade Opções de Dispositivo atualizada tiver sido implantado em um novo servidor e o write-back de dispositivo tiver sido habilitado, você precisará especificar a localização do contêiner manualmente se você não o quiser na raiz da floresta.  Para fazer isso, você precisa desabilitar o write-back de dispositivo e reabilitá-lo, o que permitirá que você especifique a localização do contêiner na página "Floresta de write-back".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Status de liberação 

30/11/2018:  Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Este build de hotfix corrige um conflito em que um erro de autenticação pode ocorrer devido à presença independente do módulo MSOnline da Galeria do PowerShell no servidor de sincronização.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Status de liberação 

19/11/2018:  Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

Esta compilação de hotfix corrige uma regressão na compilação anterior em que o Write-back de senha falha ao usar um Controlador de Domínio ADDS no Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Status de liberação 

25/10/2018: liberado para download

 
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 


- Alterada a funcionalidade de write-back de atributo para garantir que o correio de voz hospedado funcione conforme o esperado.  Em determinados cenários, o Azure AD substituía o atributo msExchUcVoicemailSettings durante o write-back por um valor nulo.  Agora, o Azure AD não limpará mais o valor local desse atributo se o valor na nuvem não estiver definido.
- Adicionados diagnósticos ao assistente do Azure AD Connect para investigar e identificar problemas de conectividade com o Azure AD. Esses mesmos diagnósticos também podem ser executados diretamente por meio do PowerShell usando o cmdlet Test- AdSyncAzureServiceConnectivity. 
- Adicionados diagnósticos ao assistente do Azure AD Connect para investigar e identificar problemas de conectividade com o AD. Esses mesmos diagnósticos também podem ser executados diretamente por meio do PowerShell usando a função Start-ConnectivityValidation no módulo ADConnectivityTools do PowerShell.  Para obter mais informações, confira [O que é o módulo ADConnectivityTool do PowerShell?](how-to-connect-adconnectivitytools.md)
- Adicionada uma pré-verificação da versão do esquema do AD para junção híbrida do Azure Active Directory e write-back do dispositivo 
- Alterada a pesquisa de atributos da página de Extensão do Diretório para não diferenciar maiúsculas e minúsculas.
-   Adicionado suporte completo para TLS 1.2. Esta versão tem suporte para que todos os outros protocolos sejam desabilitados e apenas o TLS 1.2 seja habilitado no computador em que o Azure AD Connect está instalado.  Para obter mais informações, confira [Imposição do TLS 1.2 para o Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Problemas corrigidos   

- Corrigido um bug em que a atualização do Azure AD Connect falhava quando o Always On SQL estava sendo usado. 
- Corrigido um bug para analisar corretamente nomes de UO que contêm uma barra invertida. 
- Corrigido um problema em que a autenticação de passagem era desabilitada para instalações limpas no modo de preparo. 
- Corrigido um bug que impedia que o módulo do PowerShell fosse carregado ao executar ferramentas de solução de problemas 
- Corrigido um bug que impedia que os clientes usassem valores numéricos no primeiro caractere de um nome de host. 
- Corrigido um bug em que o Azure AD Connect permitia partições inválidas e seleção de contêineres 
- Corrigida a mensagem de erro "Senha Inválida" quando o SSO da área de trabalho está habilitado. 
- Várias correções de bugs para o Gerenciamento de Confiança do AD FS  
- Ao configurar o write-back de dispositivo – corrigida a verificação de esquema para procurar a classe de objeto msDs-DeviceContainer (introduzida no WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

7/9/2018: liberado para download, não será liberado para atualização automática 

### <a name="fixed-issues"></a>Problemas corrigidos  

A atualização do Azure AD Connect falha se a Disponibilidade AlwaysOn do SQL for configurada para o BD do ADSync. Esse hotfix corrige esse problema e permite que a atualização seja executada com êxito. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Status de liberação

21/8/2018: Liberado para download e atualização automática. 

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- A integração do Ping Federate no Azure AD Connect agora está disponível para disponibilidade geral. [Saiba mais sobre como federar o Azure AD com o Ping Federate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- O Azure AD Connect agora cria o backup da confiança do AD do Azure no AD FS toda vez que uma atualização é feita e a armazena em um arquivo separado para facilitar a restauração, se necessário. [Saiba mais sobre a nova funcionalidade e o Azure AD confiam gerenciamento no Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Novo conjunto de ferramentas de solução de problemas ajuda a solucionar problemas de alteração do endereço de e-mail primário e ocultação de conta da lista de endereços global
- O Azure AD Connect foi atualizado para incluir o mais recente cliente nativo do SQL Server 2012
- Quando você alternar entrada do usuário para a sincronização de Hash de senha ou autenticação de passagem na tarefa "Alterar entrada do usuário", a caixa de seleção de logon único contínuo é habilitada por padrão.
- Adicionado suporte para o Windows Server Essentials 2019
- O agente do Azure AD Connect Health foi atualizado para a versão mais recente 3.1.7.0
- Durante uma atualização, se o instalador detectar alterações nas regras de sincronização padrão, o administrador será avisado antes de sobrescrever as regras modificadas. Isso permitirá que o usuário tome ações corretivas e retome mais tarde. Comportamento antigo: se houvesse uma regra pronta para uso modificada, a atualização manual substituiria essas regras sem dar nenhum aviso ao usuário e o agendador de sincronização seria desabilitado sem informar o usuário. Novo comportamento: o usuário será avisado antes de sobrescrever as regras de sincronização prontas para uso modificadas. O usuário terá a opção de interromper o processo de atualização e retomar mais tarde depois de tomar a ação corretiva.
- Fornecer um tratamento melhor de um problema de conformidade FIPS, fornecendo uma mensagem de erro para geração de hash MD5 em um ambiente compatível com FIPS e um link para a documentação que fornece uma solução alternativa para esse problema.
- Atualização de interface do usuário para melhorar as tarefas de federação no assistente, que agora estão em um subgrupo separado para federação. 
- Agora, todas as tarefas adicionais de Federação são agrupadas em um único submenu para facilidade de uso.
- Um novo módulo Posh de ADSyncConfig (AdSyncConfig.psm1) renovado com novas funções de permissões do AD movidas do antigo ADSyncPrep.psm1 (que pode estar preterido em breve)

### <a name="fixed-issues"></a>Problemas corrigidos 

- Corrigido um bug em que o AAD Connect server mostraria alto uso da CPU após a atualização para o .NET 4.7.2
- Corrigido um bug que gerava intermitentemente uma mensagem de erro para um problema de deadlock SQL resolvido automaticamente
- Corrigidos vários problemas de acessibilidade para o Editor de regras de sincronização e o Sync Service Manager  
- Corrigido um erro no qual o Azure AD Connect não pode obter informações de configuração do registro
- Corrigido um erro que criava problemas quando o usuário ia para frente/para trás no assistente
- Correção de bug para evitar um erro acontecendo devido ao incorreto vários threads, tratamento no Assistente
- Quando a página Filtragem de Sincronização de Grupo encontra um erro LDAP ao resolver grupos de segurança, o Azure AD Connect agora retorna a exceção com fidelidade total.  A causa raiz da exceção de referência ainda é desconhecida e será resolvida por um erro diferente.
-  Corrigido um erro no qual as permissões para chaves STK e NGC (atributo ms-DS-KeyCredentialLink em objetos Usuário / Dispositivo para WHfB) não estavam definidas corretamente.     
- Corrigido um bug em que 'Set-ADSyncRestrictedPermissions' não foi chamado corretamente
-  Adicionando suporte para a permissão que concede no Write-back de grupo no Assistente de instalação do AADConnect
- Ao alterar o método de entrada da sincronização de Hash de senha para o AD FS, sincronização de Hash de senha não foi desabilitada.
- Verificação adicionada para endereços IPv6 na configuração do AD FS
- Atualizada a mensagem de notificação para informar que existe uma configuração existente.
- O writeback do dispositivo não detecta o contêiner em uma floresta não confiável. Isso foi atualizado para fornecer uma mensagem de erro melhor e um link para a documentação apropriada
- Desmarcar uma OU e, em seguida, a sincronização / write-back correspondente a essa OU fornece um erro de sincronização genérico. Isso foi alterado para criar uma mensagem de erro mais compreensível.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Status de liberação

14/5/2018: Liberado para atualização automática e download.

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

Novos recursos e aprimoramentos

- Essa versão inclui a versão prévia pública da integração do PingFederate no Azure AD Connect. Com essa versão, os clientes podem configurar seu ambiente do Azure Active Directory de maneira fácil e confiável para aproveitar o PingFederate como seu provedor de federação. Para saber mais sobre como usar esse novo recurso, consulte nossa [documentação online](plan-connect-user-signin.md#federation-with-pingfederate). 
- Utilitário de solução de problemas do assistente do Azure AD Connect atualizado, no qual agora analisa mais cenários de erro, como caixas de correio vinculadas e grupos dinâmicos do AD. Leia mais sobre o utilitário de solução de problemas [aqui](tshoot-connect-objectsync.md).
- A configuração de write-back do dispositivo agora é gerenciada exclusivamente dentro do Assistente Azure AD Connect.
- Um novo Módulo do PowerShell chamado ADSyncTools.psm1 foi adicionado e pode ser usado para solucionar problemas de conectividade do SQL e vários outros utilitários de solução de problemas. Leia mais sobre o módulo ADSyncTools [aqui](tshoot-connect-tshoot-sql-connectivity.md). 
- Foi adicionada uma nova tarefa “Configurar opções de dispositivo”. Você pode usar a tarefa para configurar as duas operações a seguir: 
  - **Ingresso no Azure AD Híbrido**: Se seu ambiente tiver um espaço local do AD e você também quiser se beneficiar dos recursos fornecidos pelo Azure Active Directory, será possível implementar dispositivos adicionados ao Azure AD híbrido. Esses dispositivos são adicionados ao Active Directory local e ao Azure Active Directory.
  - **Write-back de dispositivo**: Write-back de dispositivo é usado para habilitar o acesso condicional com base em dispositivos para o AD FS (2012 R2 ou superior) dispositivos protegidos

    >[!NOTE] 
    > - A opção para habilitar o write-back de dispositivo em Personalizar opções de sincronização ficará esmaecida. 
    > -  O módulo do PowerShell do ADPrep foi preterido com esta versão.



### <a name="fixed-issues"></a>Problemas corrigidos 

- Essa versão atualiza a instalação do SQL Server Express para o SQL Server 2012 SP4, que, entre outras coisas, fornece correções para diversas vulnerabilidades de segurança.  Acesse [aqui](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) para obter mais informações sobre o SQL Server 2012 SP4.
- Processamento da regra de sincronização: regras de sincronização de saída do ingresso sem Condição de ingresso devem ser desaplicadas caso a regra de sincronização pai não seja mais aplicável
- Diversas correções de acessibilidade foram aplicadas à interface do usuário do Synchronization Service Manager e ao Editor de Regras de Sincronização
- Assistente do Azure AD Connect: erro ao criar a conta do AD Connector quando do Azure AD Connect está em um grupo de trabalho
- Assistente do Azure AD Connect: na página de entrada do Azure AD, é exibida a caixa de seleção de verificação sempre que houver qualquer incompatibilidade em domínios do AD e domínios verificados do Azure AD
- Correção para definir o estado da atualização automática do PowerShell corretamente em certos casos, após a tentativa desse tipo de atualização.
- Assistente do Azure AD Connect: atualização da telemetria para capturar informações ausentes anteriormente
- Assistente do Azure AD Connect: as seguintes alterações foram feitas ao usar a tarefa **Alterar entrada do usuário** para alternar do AD FS para a Autenticação de Passagem:
    - O Agente de Autenticação de Passagem é instalado no servidor do Azure AD Connect e o recurso Autenticação de Passagem é habilitado antes de convertermos os domínios de federados para gerenciados.
    - Os usuários não são mais convertidos de federados para gerenciados. Somente os domínios são convertidos.
- Assistente do Azure AD Connect: a expressão regular (regex) de vários domínios do AD FS não está correta quando o UPN do usuário tem a atualização caractere especial ' de regex para dar suporte a caracteres especiais
- Assistente do Azure AD Connect: remoção da mensagem falsa de “Configurar atributo de âncora de origem” quando não houver alteração 
- Assistente do Azure AD Connect: suporte de AD FS para o cenário de federação dupla
- Assistente do Azure AD Connect: declarações do AD FS não são atualizadas para domínios adicionados ao converter um domínio gerenciado para federado
- Assistente do Azure AD Connect: durante a detecção de pacotes instalados, encontramos produtos obsoletos relacionados a Dirsync/Azure AD Sync/Azure AD Connect. Agora tentaremos desinstalar os produtos obsoletos.
- Assistente do Azure AD Connect: correção do erro de mapeamento de mensagem quando ocorre falha na instalação do agente de autenticação de passagem
- Assistente do Azure AD Connect: remoção do contêiner de “Configuração” da página de Domínio/filtragem de UO
- Instalação do mecanismo de sincronização: remoção da lógica herdada desnecessária que falha ocasionalmente no msi de instalação do mecanismo de sincronização
- Assistente do Azure AD Connect: correção do texto pop-up de ajuda na página de recursos opcionais para sincronização de hash de senha
- Tempo de execução do mecanismo de sincronização: correção do cenário em que um objeto do CS tem uma importação excluída e regras de sincronização tentam reprovisionar o objeto.
- Tempo de execução do mecanismo de sincronização: adição de link de ajuda para guia de solução de problemas de conectividade online para o log de eventos de um erro de importação
- Tempo de execução do mecanismo de sincronização: uso de memória reduzido do agendador de sincronização ao enumerar os conectores
- Assistente do Azure AD Connect: correção de problema de resolução de uma conta de serviço de sincronização personalizada que não tem nenhum privilégios de leitura no AD
- Assistente do Azure AD Connect: melhoria de registro em log do domínio e seleções de filtragem de UO
- Assistente do Azure AD Connect: adição de declarações padrão do AD FS para confiança de federação criada para o cenário MFA
- Assistente do Azure AD Connect: Serviços de Federação do Active Directory (AD FS) implantam o WAP: a adição do servidor falha em usar o novo certificado
- Assistente do Azure AD Connect: exceção de DSSO quando onPremCredentials não são inicializados para um domínio 
- Preferencialmente, faz o fluxo do atributo distinguishedName do AD do objeto de usuário ativo.
- Correção de um bug superficial em que a precedência da primeira regra de sincronização OOB foi definida como 99 em vez de 100



## <a name="117510"></a>1.1.751.0
Status 12/04/2018: liberado apenas para download

>[!NOTE]
>Essa versão é um hotfix para o Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
Corrigido um problema em que a descoberta de instância automática do Azure para os locatários da China estavam falhando ocasionalmente.  

### <a name="ad-fs-management"></a>Gerenciamento dos AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos

Houve um problema na lógica de nova tentativa de configuração que resultaria em um ArgumentException declarando “um item com a mesma chave já foi adicionado”.  Isso faria com que todas as operações de nova tentativa falhassem.

## <a name="117500"></a>1.1.750.0
Status 22/03/2018: liberado para atualização automática e download.
>[!NOTE]
>Quando a atualização para essa nova versão for concluída, ela ativará automaticamente uma sincronização completa e importação completa para o conector do Azure Active Directory e uma sincronização completa para o conector AD. Como isso pode demorar algum tempo, dependendo do tamanho do ambiente Azure AD Connect, verifique se as medidas necessárias foram tomadas para dar suporte a isso ou aguarde a atualização até encontrar um momento conveniente para fazer isso.

>[!NOTE]
>"A funcionalidade AutoUpgrade foi desabilitada incorretamente para alguns locatários que implantaram builds posteriores a 1.1.524.0. Para garantir que sua instância do Azure AD Connect seja ainda elegível para AutoUpgrade, execute o seguinte cmdlet do PowerShell: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos

* Cmdlet Set-ADSyncAutoUpgrade bloquearia anteriormente no upgrade automático se o estado de atualização automática estivesse definido como suspenso. Essa funcionalidade foi alterada para não bloquear a AutoUpgrade de builds futuros.
* Alterada na página **Entrada do Usuário** a opção "Sincronização de Senha" para "Sincronização de Hash de Senha".  O Azure AD Connect sincroniza hashes de senha, não senhas, para que alinhe-se com o que realmente está ocorrendo.  Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Status: liberado para selecionar clientes

>[!NOTE]
>Quando a atualização para essa nova versão for concluída, ela ativará automaticamente uma sincronização completa e importação completa para o conector do Azure Active Directory e uma sincronização completa para o conector AD. Como isso pode demorar algum tempo, dependendo do tamanho do ambiente Azure AD Connect, verifique se as medidas necessárias foram tomadas para dar suporte a isso ou aguarde a atualização até encontrar um momento conveniente para fazer isso.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Correção da janela de tempo nas tarefas em segundo plano da página de Filtragem de Partições ao comutar para a próxima página.

* Corrigido um bug que causou violação de Acesso durante a ação personalizada do ConfigDB.

* Corrigido um bug para recuperar do tempo limite de conexão do SQL.

* Corrigido um bug onde os certificados com curingas SAN falharam no teste de pré-requisito.

* Corrigido um bug que causa falha do miiserver.exe durante uma exportação do conector do Azure Active Directory.

* Corrigido um bug em que a tentativa de senha incorreta foi registrada no DC durante a execução do assistente do Azure AD Connect para alterar a configuração.


#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Adicionar Configurações de Privacidade para o GDPR (Regulamento Geral sobre a Proteção de Dados).  Para obter mais informações, confira o artigo [aqui](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetria de aplicativos - o administrador pode ativar/desativar essa classe de dados, segundo a sua vontade

* Dados de Integridade do Azure Active Directory - o administrador deve visitar o portal de integridade para controlar suas configurações de integridade.
   Quando a política de atendimento for alterada, os agentes farão a leitura e irão impor a política.

* Adicionadas ações de configuração de write-back de dispositivo e uma barra de progresso para inicialização de página

* Diagnósticos Gerais aprimorados com relatório HTML e coleta de dados completa em um relatório HTML/ZIP-Text

* Aperfeiçoada a confiabilidade da atualização automática e adicionada a telemetria adicional para garantir que a integridade do servidor possa ser determinada

* Restringir permissões disponíveis para contas privilegiadas na conta do AD Connector

  * Para novas instalações, o assistente restringirá as permissões que as contas privilegiadas tenham na conta MSOL, após criar a conta MSOL.

As alterações irão considerar o seguinte:
1. Instalações expressas
2. Instalações personalizadas com conta de criação automática
3. Alterado o instalador para que ele não exija privilégio de SA na instalação limpa do Azure AD Connect

* Adicionado um novo utilitário para solucionar problemas de sincronização para um objeto específico. Está disponível na opção 'Solucionar problemas de sincronização de objetos' da Tarefa adicional para solucionar problemas do assistente do Azure AD Connect. Atualmente, o utilitário verifica o seguinte:

  * UserPrincipalName incompatível entre o objeto de usuário sincronizado e a conta de usuário no Locatário do Azure Active Directory.
  * Se o objeto for filtrado da sincronização devido à filtragem de domínio
  * Se o objeto for filtrado da sincronização devido à filtragem da OU (unidade organizacional)

* Adicionado um novo utilitário para sincronizar o hash de senha atual armazenado no Active Directory local para uma conta de usuário específica.

O utilitário não exige uma alteração de senha. Está disponível na opção 'Solucionar problemas de sincronização de hash de senha' da Tarefa adicional para solucionar problemas do assistente do Azure AD Connect.






## <a name="116540"></a>1.1.654.0
Status: 12 de dezembro de 2017

>[!NOTE]
>Essa versão é um hotfix relacionado à segurança para o Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Um aprimoramento foi adicionado ao Azure AD Connect versão 1.1.654.0 (e posterior) para garantir que as alterações de permissão recomendadas descritas na seção [Bloquear acesso à conta do AD DS](#lock) sejam aplicadas automaticamente quando o Azure AD Connect criar a conta do AD DS. 

- Ao configurar o Azure AD Connect, o administrador de instalação pode fornecer uma conta existente do AD DS ou permitir que o Azure AD Connect crie automaticamente a conta. As alterações de permissão são aplicadas automaticamente para a conta do AD DS criada pelo Azure AD Connect durante a instalação. Elas não são aplicadas à conta existente do AD DS fornecida pelo administrador de instalação.
- Para clientes que tenham feito um upgrade de uma versão anterior do Azure AD Connect para 1.1.654.0 (ou posterior), as alterações de permissão não serão retroativamente aplicadas às contas de AD DS existentes criadas antes da atualização. Elas serão aplicadas somente a novas contas do AD DS criadas após a atualização. Isso ocorre quando você estiver adicionando novas florestas do AD para ser sincronizada com o Azure AD.

>[!NOTE]
>Essa versão remove somente a vulnerabilidade para novas instalações do Azure AD Connect onde a conta de serviço é criada pelo processo de instalação. Para as instalações existentes, ou em casos em que você fornece a conta por conta própria, você deve garantir que essa vulnerabilidade não existe.

#### <a name="lock"></a> Bloquear o acesso à conta do AD DS
Bloqueie o acesso à conta do AD DS implementando as seguintes alterações de permissão no AD local:  

*   Desabilite herança no objeto especificado
*   Remova todas as ACEs no objeto especificado, exceto as ACEs específicas ao SELF. Queremos manter as permissões padrão intactas quando se trata do SELF.
*   Atribua essas permissões específicas:

Tipo     | Nome                          | Access               | Aplica-se A
---------|-------------------------------|----------------------|--------------|
Allow    | SYSTEM                        | Controle Total         | Este objeto  |
Allow    | Administradores Corporativos             | Controle Total         | Este objeto  |
Allow    | Administradores de Domínio                 | Controle Total         | Este objeto  |
Allow    | Administradores                | Controle Total         | Este objeto  |
Allow    | Controladores de Domínio Corporativo | Listar Conteúdo        | Este objeto  |
Allow    | Controladores de Domínio Corporativo | Ler Todas as Propriedades  | Este objeto  |
Allow    | Controladores de Domínio Corporativo | Permissões de Leitura     | Este objeto  |
Allow    | Usuários Autenticados           | Listar Conteúdo        | Este objeto  |
Allow    | Usuários Autenticados           | Ler Todas as Propriedades  | Este objeto  |
Allow    | Usuários Autenticados           | Permissões de Leitura     | Este objeto  |

Para reforçar as configurações para a conta do AD DS você pode executar [este script do PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). O script do PowerShell atribuirá as permissões mencionadas acima para a conta do AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Script do PowerShell para reforçar uma conta de serviço já existente

Para usar o script do PowerShell, para aplicar essas configurações, para uma conta do AD DS pré-existente, fornecida pela sua organização ou criada por uma instalação anterior do Azure AD Connect, baixe o script no link fornecido acima.

##### <a name="usage"></a>Uso:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Where 

**$ObjectDN** = A conta do Active Directory cujas permissões precisam ser reforçadas.

**$Credential** = A credencial de administrador que tem os privilégios necessários para restringir as permissões na conta do $ObjectDN. Normalmente, esses privilégios são mantidos pelo administrador do domínio ou da empresa. Use o nome de domínio totalmente qualificado da conta de administrador para evitar falhas de pesquisa de conta. Exemplo: contoso.com\admin.

>[!NOTE] 
>$credential.UserName deve estar no formato de FQDN\username. Exemplo: contoso.com\admin 

##### <a name="example"></a>Exemplo:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Essa vulnerabilidade foi usada para obter acesso não autorizado?

Para verificar se essa vulnerabilidade foi usada para comprometer a sua configuração do Azure AD Connect verifique a última data de redefinição de senha da sua conta de serviço.  Se o carimbo de hora for inesperado, uma investigação adicional deve ser realizada, por meio do log de eventos, para aquele evento de redefinição de senha.

Para obter mais informações, consulte [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Status: 27 de outubro de 2017

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Problema corrigido
* Corrigido um problema de compatibilidade entre o Azure AD Connect e o agente do Azure AD Connect Health (para sincronização). Esse problema afeta os clientes que estão executando a atualização in-loco do Azure AD Connect para a versão 1.1.647.0, mas, atualmente, têm o Agente de Integridade versão 3.0.127.0. Após a atualização, o Agente de Integridade não poderá mais enviar dados de integridade do Azure AD Connect Synchronization Service ao Azure AD Health Service. Com essa correção, o Agente de Integridade versão 3.0.129.0 será instalado durante a atualização in-loco do Azure AD Connect. O Agente de Integridade versão 3.0.129.0 não tem problema de compatibilidade com o Azure AD Connect versão 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Status: 19 de outubro de 2017

> [!IMPORTANT]
> Há um problema de compatibilidade conhecido entre o Azure AD Connect versão 1.1.647.0 e Azure AD Connect Health Agent (para sincronização) versão 3.0.127.0. Esse problema impede que o Health Agent envie dados de integridade sobre o Serviço de Sincronização do Azure AD Connect (incluindo erros de sincronização de objeto e dados do histórico de execução) para o Serviço do Azure AD Health. Antes de atualizar manualmente a implantação do Azure AD Connect versão 1.1.647.0, verifique a versão atual do Azure AD Connect Health Agent instalada no servidor do Azure AD Connect. Você pode fazer isso acessando o *Painel de controle → Adicionar/Remover Programas* e procure o aplicativo *Microsoft Azure AD Connect Health Agent para Sincronização*. Se sua versão for 3.0.127.0, é recomendável que você aguarde a próxima versão do Azure AD Connect estar disponível antes da atualização. Se a versão do Health Agent não for 3.0.127.0, não há problema em prosseguir com a atualização manual no local. Observe que esse problema não afeta a atualização swing ou os clientes que estão executando uma nova instalação do Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema com a tarefa *Alterar entrada do usuário* no assistente do Azure AD Connect:

  * O problema ocorre quando você tem uma implantação existente do Azure AD Connect com a Sincronização de Senha **habilitada** e você está tentando definir o método de entrada do usuário como *Autenticação de Passagem*. Antes de a alteração ser aplicada, o assistente mostra incorretamente o prompt “*Desabilitar a Sincronização de Senha*”. No entanto, a Sincronização de Senha permanece habilitada depois que a alteração é aplicada. Com essa correção, o assistente não mostra mais o prompt.

  * Por design, o assistente não desabilita a Sincronização de Senha quando você atualiza o método de entrada do usuário usando a tarefa *Alterar a entrada do usuário*. Isso é para evitar a interrupção para os clientes que desejam manter a Sincronização de Senha, mesmo que estejam habilitando a Autenticação de Passagem ou a federação como seu método de entrada do usuário primário.
  
  * Se desejar desabilitar a Sincronização de Senha depois de atualizar o método de entrada do usuário, você deverá executar a tarefa *Personalizar a Configuração da Sincronização* no assistente. Quando você navega até a página *Recursos opcionais*, desmarque a opção *Sincronização de Senha*.
  
  * Observe que o mesmo problema também ocorrerá se você tentar habilitar/desabilitar o Logon Único Contínuo. Especificamente, você tem uma implantação existente do Azure AD Connect com a Sincronização de Senha habilitada e o método de entrada do usuário já está configurado como *Autenticação de Passagem*. Usando a tarefa *Alterar entrada de usuário*, você tenta marcar/desmarcar a opção *Habilitar o Logon Único Contínuo* enquanto o método de entrada do usuário continua configurado como “Autenticação de Passagem”. Antes de a alteração ser aplicada, o assistente mostra incorretamente o prompt “*Desabilitar a Sincronização de Senha*”. No entanto, a Sincronização de Senha permanece habilitada depois que a alteração é aplicada. Com essa correção, o assistente não mostra mais o prompt.

* Corrigido um problema com a tarefa *Alterar entrada do usuário* no assistente do Azure AD Connect:

  * O problema ocorre quando você tem uma implantação existente do Azure AD Connect com a Sincronização de Senha **desabilitada** e você está tentando definir o método de entrada do usuário como *Autenticação de Passagem*. Quando a alteração é aplicada, o assistente habilita a Autenticação de Passagem e a Sincronização de Senha. Com essa correção, o assistente não habilita mais a Sincronização de Senha.

  * Anteriormente, a Sincronização de Senha era um pré-requisito para habilitar a Autenticação de Passagem. Quando você define o método de entrada do usuário como *Autenticação de Passagem*, o assistente habilita a Autenticação de Passagem e a Sincronização de Senha. Recentemente, a Sincronização de Senha foi removida como um pré-requisito. Como parte do Azure AD Connect versão 1.1.557.0, foi feita uma alteração para o Azure AD Connect não habilitar a Sincronização de Senha ao definir o método de entrada do usuário como *Autenticação de Passagem*. No entanto, a alteração foi aplicada apenas na instalação do Azure AD Connect. Com essa correção, a mesma alteração também é aplicada à tarefa *Alterar entrada do usuário*.
  
  * Observe que o mesmo problema também ocorrerá se você tentar habilitar/desabilitar o Logon Único Contínuo. Especificamente, você tem uma implantação existente do Azure AD Connect com a Sincronização de Senha desabilitada e o método de entrada do usuário já está configurado como *Autenticação de Passagem*. Usando a tarefa *Alterar entrada de usuário*, você tenta marcar/desmarcar a opção *Habilitar o Logon Único Contínuo* enquanto o método de entrada do usuário continua configurado como “Autenticação de Passagem”. Quando a alteração é aplicada, o assistente habilita a Sincronização de Senha. Com essa correção, o assistente não habilita mais a Sincronização de Senha. 

* Corrigido um problema que causava a falha na atualização do Azure AD Connect com o erro “*Não é possível atualizar o Serviço de Sincronização*”. Além disso, o Serviço de Sincronização não pode mais iniciar com o erro de evento "*O serviço não pôde ser iniciado porque a versão do bando de dados é mais nova do que a versão dos binários instalados*". O problema ocorre quando o administrador executando a atualização não tem privilégios de administrador do sistema para o SQL Server que está sendo usado pelo Azure AD Connect. Com essa correção, o Azure AD Connect requer apenas que o administrador tenha o privilégio db_owner no banco de dados ADSync durante a atualização.

* Corrigido um problema de atualização do Azure AD Connect que afetava os clientes que habilitaram o [Logon Único Contínuo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Após o Azure AD Connect ser atualizado, o Logon Único Contínuo aparece incorretamente como desabilitado no assistente do Azure AD Connect, embora o recurso permaneça habilitado e totalmente funcional. Com essa correção, o recurso agora aparece corretamente como habilitado no assistente.

* Corrigido um problema que fazia com que o assistente do Azure AD Connect sempre mostrasse o prompt “*Configurar a Âncora de Origem*” na página *Pronto para Configurar*, mesmo se não tivesse sido feita nenhuma alteração relacionada à Âncora de Origem.

* Ao executar a atualização local manual do Azure AD Connect, o cliente deve fornecer as credenciais de Administrador Global do locatário do Azure AD correspondente. Anteriormente, a atualização podia continuar mesmo que as credenciais de Administrador Global pertencessem a um locatário do Azure AD diferente. Embora a atualização pareça ser concluída com êxito, algumas configurações não são mantidas corretamente com a atualização. Com essa alteração, o assistente impede que a atualização continue se as credenciais fornecidas não coincidem com o locatário do Azure AD.

* Removida a lógica redundante que reiniciava desnecessariamente o serviço do Azure AD Connect Health no início da atualização manual.


#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Adicionada lógica para simplificar as etapas necessárias para configurar o Azure AD Connect com o Microsoft Germany Cloud. Anteriormente, era necessário atualizar as chaves do Registro específicas no servidor do Azure AD Connect para funcionar corretamente com o Microsoft Germany Cloud, conforme descrito neste artigo. Agora, o Azure AD Connect pode detectar automaticamente se seu locatário está no Microsoft Germany Cloud com base nas credenciais de administrador global fornecidas na instalação.

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect
> [!NOTE]
> Observação: o serviço de sincronização tem uma interface WMI que permite que você desenvolva seu próprio agendador personalizado. Esta interface agora está preterida e será removida de versões futuras do Azure AD Connect enviadas após 30 de junho de 2018. Os clientes que desejam personalizar o agendamento de sincronização devem usar o [Agendador integrado (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Problemas corrigidos
* Quando o assistente do Azure AD Connect cria a conta do AD Connector necessária para sincronizar as alterações do Active Directory Local, ele não atribui corretamente à conta a permissão necessária para ler objetos PublicFolder. Esse problema afeta a Instalação Expressa e a Instalação Personalizada. Essa alteração corrige o problema.

* Corrigido um problema que fazia com que a página de solução de problemas do assistente do Azure AD Connect não fosse renderizada corretamente para os administradores executando do Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Ao solucionar problemas de Sincronização de Senha usando a página de solução de problemas do assistente do Azure AD Connect, a página de solução de problemas agora retorna o status específico do domínio.

* Anteriormente, se você tentasse habilitar a Sincronização de Hash de Senha, o Azure AD Connect não verificava se a conta do Azure AD tem as permissões necessárias para sincronizar hashes de senha do AD local. Agora, o assistente do Azure AD Connect verificará e avisará você se a conta do AD Connector não tiver permissões suficientes.

### <a name="ad-fs-management"></a>Gerenciamento dos AD FS
#### <a name="fixed-issue"></a>Problema corrigido
* Corrigido um problema relacionado ao uso do recurso [ms-DS-ConsistencyGuid como Âncora de Origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor). Esse problema afeta os clientes que configuraram a *federação com o AD FS* como o método de entrada do usuário. Quando você executa a tarefa *Configurar Âncora de Origem* no assistente, o Azure AD Connect muda para usar *ms-DS-ConsistencyGuid como o atributo de origem para immutableId. Como parte dessa alteração, o Azure AD Connect tenta atualizar as regras de declaração para ImmutableId no AD FS. No entanto, essa etapa falhava porque o Azure AD Connect não tinha as credenciais de administrador necessárias para configurar o AD FS. Com essa correção, o Azure AD Connect agora solicita que você insira as credenciais de administrador para o AD FS ao executar a tarefa *Configurar Âncora de Origem*.



## <a name="116140"></a>1.1.614.0
Status: 5 de setembro de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problemas conhecidos
* Há um problema conhecido que está causando a falha da atualização do Azure AD Connect com o erro “*Não é possível atualizar o Serviço de Sincronização*”. Além disso, o Serviço de Sincronização não pode mais iniciar com o erro de evento "*O serviço não pôde ser iniciado porque a versão do bando de dados é mais nova do que a versão dos binários instalados*". O problema ocorre quando o administrador executando a atualização não tem privilégios de administrador do sistema para o SQL Server que está sendo usado pelo Azure AD Connect. Permissões de DBO não são suficientes.

* Há um problema conhecido com a atualização do Azure AD Connect que está afetando os clientes que habilitaram o [Logon Único Contínuo](how-to-connect-sso.md). Após a atualização do Azure AD Connect, o recurso é exibido como desabilitado no assistente, mesmo que na realidade o recurso permaneça habilitado. Uma correção para esse problema será fornecida em futuras versões. Os clientes que se preocupam com esse problema de exibição podem corrigi-lo manualmente habilitando o Logon Único Contínuo no assistente.

#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema que impedia que o Azure AD Connect atualizasse as regras de declarações no AD FS local, habilitando o recurso [ms-DS-ConsistencyGuid as Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor). O problema ocorre se você tenta habilitar o recurso para uma implantação existente do Azure AD Connect com AD FS configurado como o método de entrada. O problema ocorre porque o assistente não solicita as credenciais do ADFS antes de tentar atualizar as regras de declarações no AD FS.
* Corrigido um problema que causava falha na instalação do Azure AD Connect se a floresta do AD local estava com o NTLM desabilitado. O problema era devido ao assistente do Azure AD Connect não fornecer credenciais totalmente qualificadas ao criar os contextos de segurança necessários para a autenticação Kerberos. Isso faz com que a autenticação Kerberos falhe e que o assistente do Azure AD Connect volte a usar o NTLM.

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema em que a nova regra de sincronização não pode ser criada se o atributo Tag não está populado.
* Corrigido um problema que fazia com que o Azure AD Connect se conectasse ao AD local para a sincronização de senha usando NTLM, mesmo que o Kerberos estivesse disponível. Esse problema ocorria se a topologia do AD local tinha um ou mais controladores de domínio restaurados de um backup.
* Corrigido um problema que fazia com que as etapas de sincronização completa ocorressem desnecessariamente após a atualização. Em geral, a execução das etapas de sincronização completa é necessária após a atualização se houver alterações em regras de sincronização para uso imediato. O problema ocorreu devido a um erro na lógica de detecção de alteração que detectava incorretamente uma alteração ao encontrar a expressão de regra de sincronização com caracteres de nova linha. Os caracteres de nova linha são inseridos na expressão de regra de sincronização para melhorar a legibilidade.
* Corrigido um problema que pode fazer com que o servidor do Azure AD Connect não funcione corretamente após a Atualização Automática. Esse problema afeta os servidores do Azure AD Connect com a versão 1.1.443.0 (ou anterior). Para obter detalhes sobre o problema, consulte o artigo [O Azure AD Connect não está funcionando corretamente após uma atualização automática](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Corrigido um problema que pode fazer com que a atualização automática seja repetida a cada 5 minutos quando erros são encontrados. Com a correção, a Atualização Automática faz novas tentativas com retirada exponencial quando são encontrados erros.
* Corrigido um problema em que o evento de sincronização de senha 611 é mostrado incorretamente nos logs de eventos de aplicativo do Windows como **informativo** em vez de **erro**. O evento 611 é gerado sempre que a sincronização de senha encontra um problema. 
* Corrigido um problema no assistente do Azure AD Connect que permite que o recurso de write-back de grupo seja habilitado sem selecionar uma unidade organizacional necessária para write-back de grupo.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Adicionada uma tarefa de solução de problemas para o assistente do Azure AD Connect em Tarefas Adicionais. Os clientes podem aproveitar essa tarefa para solucionar problemas relacionados à sincronização de senha e coletar diagnósticos em geral. No futuro, a tarefa de Solução de Problemas será estendida para incluir outros problemas relacionados à sincronização de diretório.
* O Azure AD Connect agora dá suporte a um novo modo de instalação chamado **Usar Banco de Dados Existente**. Esse modo de instalação permite que os clientes instalem o Azure AD Connect, que especifica um banco de dados ADSync. Para obter mais informações sobre esse recurso, consulte o artigo [Usar um banco de dados existente](how-to-connect-install-existing-database.md).
* Para maior segurança, o Azure AD Connect agora assume como padrão o uso do TLS 1.2 para se conectar ao Azure AD para sincronização de diretório. Anteriormente, o padrão era o TLS 1.0.
* Quando o Agente de Sincronização de Senha do Azure AD Connect é iniciado, ele tenta se conectar a um ponto de extremidade bem conhecido do Azure AD para sincronização de senha. Após uma conexão bem-sucedida, ele é redirecionado para um ponto de extremidade específico da região. Anteriormente, o Agente de Sincronização de Senha armazenava em cache o ponto de extremidade específico da região até que ele fosse reiniciado. Agora, o agente limpa o cache e faz uma nova tentativa com o ponto de extremidade conhecido quando encontra um problema de conexão com o ponto de extremidade específico da região. Essa alteração assegura que a sincronização de senha possa fazer failover para um ponto de extremidade específico da região diferente quando o ponto de extremidade específico da região em cache não está mais disponível.
* Para sincronizar as alterações de uma floresta do AD local, uma conta do AD DS é necessária. Você pode (i) criar a conta do AD DS você mesmo e fornecer a credencial dela para o Azure AD Connect ou (ii) fornecer uma credencial de admin corporativo e permitir que o Azure AD Connect crie a conta do AD DS para você. Anteriormente, (i) era a opção padrão no assistente do Azure AD Connect. Agora, (ii) é a opção padrão.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Adicionado suporte para a Nuvem do Microsoft Azure Governamental e Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Gerenciamento dos AD FS
#### <a name="fixed-issues"></a>Problemas corrigidos
* O cmdlet Initialize-ADSyncNGCKeysWriteBack no módulo do PowerShell de preparação do AD estava aplicando ACLs incorretamente ao contêiner do registro de dispositivo incorretamente em lista de acesso e, portanto, herdaria somente as permissões existentes.  Isso foi atualizado para que a conta de serviço de sincronização tenha as permissões corretas.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* A tarefa Verificar Logon do ADFS do AAD Connect foi atualizada para que ele verifique logons em relação ao Microsoft Online e não apenas a recuperação de token do ADFS.
* Ao configurar um novo farm de AD FS usando o AAD Connect, a página solicitando credenciais do AD FS foi movida para que isso agora ocorra antes que seja solicitado ao usuário que forneça servidores ADFS e WAP.  Isso permite que o AAD Connect verifique se a conta especificada tem as permissões corretas.
* Durante a atualização do AAD Connect, não teremos mais falha em uma atualização se a relação de confiança do AAD do ADFS falhar em atualizar.  Se isso acontecer, será mostrada uma mensagem de aviso apropriada ao usuário e ele deverá prosseguir para redefinir a relação de confiança por meio da tarefa adicional do AAD Connect.

### <a name="seamless-single-sign-on"></a>Logon Único Contínuo
#### <a name="fixed-issues"></a>Problemas corrigidos
* Corrigido um problema que fazia com que o assistente do Azure AD Connect retornasse um erro se você tentasse habilitar o [Logon Único Contínuo](how-to-connect-sso.md). A mensagem de erro é *"Falha na configuração do Agente de Autenticação do Microsoft Azure AD Connect".* Esse problema afetava clientes existentes que tinham atualizado manualmente para a versão prévia dos Agentes de Autenticação para [Autenticação de Passagem](how-to-connect-sso.md) com base nas etapas descritas neste [artigo](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Status: 23 de julho de 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema corrigido

* Foi corrigido um problema que fazia com que a regra de sincronização integrada "Out to AD – User ImmutableId" fosse removida:

  * O problema ocorre quando o Azure AD Connect é atualizado ou quando a opção de tarefa *Atualizar configuração de sincronização* no assistente do Azure AD Connect é usada para atualizar a configuração da sincronização do Azure AD Connect.
  
  * Essa regra de sincronização é aplicável aos clientes que habilitaram o [ms-DS-ConsistencyGuid como recurso de âncora de origem](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Esse recurso foi introduzido na versão 1.1.524.0 e posteriores. Quando a regra de sincronização é removida, o Azure AD Connect não consegue mais preencher o atributo ms-DS-ConsistencyGuid do AD local com o valor do atributo ObjectGuid. Isso não impede que novos usuários sejam provisionados no Azure AD.
  
  * A correção garante que a regra de sincronização não seja mais removida durante a atualização ou durante a alteração de configuração, desde que o recurso esteja habilitado. Para clientes existentes que tenham sido afetados por esse problema, a correção também garantirá que a regra de sincronização seja adicionada novamente após a atualização para esta versão do Azure AD Connect.

* Foi corrigido um problema que fazia com que as regras de sincronização integradas tivessem o valor de precedência menores que 100:

  * Em geral, os valores de precedência de 0 a 99 são reservados para regras de sincronização personalizadas. Durante a atualização, os valores de precedência para regras de sincronização integradas são atualizados para acomodar as alterações de regra de sincronização. Devido a esse problema, as regras de sincronização integradas podem ser atribuídas com valores de precedência que sejam menores que 100.
  
  * A correção impede que o problema ocorra durante a atualização. No entanto, ela não restaura os valores de precedência dos clientes existentes que tenham sido afetados pelo problema. Uma correção adicional será fornecida no futuro para ajudar com a restauração.

* Foi corrigido um problema em que a [tela Filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering), no assistente do Azure AD Connect, mostrava a opção *Sincronizar todos os domínios e UOs* como selecionada, mesmo que a filtragem baseada em UO estivesse habilitada.

*   Foi corrigido um problema que fazia com que a [tela Configurar partições de diretório](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager retornasse um erro se o botão *Atualizar* fosse clicado. A mensagem de erro é *"Foi encontrado um erro ao atualizar domínios: não é possível converter objeto do tipo 'System.Collections.ArrayList' no tipo 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject".* O erro ocorre quando um novo domínio do AD é adicionado a uma floresta existente do AD e você tenta atualizar o Azure AD Connect usando o botão Atualizar.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* O [recurso de Atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandido para dar suporte aos clientes com as seguintes configurações:
  * Você habilitou o recurso write-back de dispositivo.
  * Você habilitou o recurso write-back de grupo.
  * A instalação não é uma configuração Express ou uma atualização de DirSync.
  * Você tem mais de 100 mil objetos no metaverso.
  * Você está se conectando a mais de uma floresta. A instalação expressa se conecta somente a uma floresta.
  * A conta do Conector AD não é mais a conta MSOL_ padrão.
  * O servidor está definido como em modo de preparo.
  * Você habilitou o recurso write-back de usuário.
  
  >[!NOTE]
  >A expansão de escopo do recurso de Atualização automática afeta os clientes com o Azure AD Connect build 1.1.105.0 e posteriores. Se você não quiser que seu servidor do Azure AD Connect seja atualizado automaticamente, será necessário executar o seguinte cmdlet em seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre habilitar/desabilitar a Atualização automática, veja o artigo [Azure AD Connect: atualização automática](how-to-connect-install-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Status: não será liberada. As alterações deste build estão incluídas na versão 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema corrigido

* Foi corrigido um problema que fazia com que a regra de sincronização integrada "Out to AD – User ImmutableId" fosse removida quando a configuração de filtragem baseada em UO era atualizada. Essa regra de sincronização é exigida pelo [ms-DS-ConsistencyGuid como recurso de âncora de origem](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Foi corrigido um problema em que a [tela Filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering), no assistente do Azure AD Connect, mostrava a opção *Sincronizar todos os domínios e UOs* como selecionada, mesmo que a filtragem baseada em UO estivesse habilitada.

*   Foi corrigido um problema que fazia com que a [tela Configurar partições de diretório](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) no Synchronization Service Manager retornasse um erro se o botão *Atualizar* fosse clicado. A mensagem de erro é *"Foi encontrado um erro ao atualizar domínios: não é possível converter objeto do tipo 'System.Collections.ArrayList' no tipo 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject".* O erro ocorre quando um novo domínio do AD é adicionado a uma floresta existente do AD e você tenta atualizar o Azure AD Connect usando o botão Atualizar.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* O [recurso de Atualização automática](how-to-connect-install-automatic-upgrade.md) foi expandido para dar suporte aos clientes com as seguintes configurações:
  * Você habilitou o recurso write-back de dispositivo.
  * Você habilitou o recurso write-back de grupo.
  * A instalação não é uma configuração Express ou uma atualização de DirSync.
  * Você tem mais de 100 mil objetos no metaverso.
  * Você está se conectando a mais de uma floresta. A instalação expressa se conecta somente a uma floresta.
  * A conta do Conector AD não é mais a conta MSOL_ padrão.
  * O servidor está definido como em modo de preparo.
  * Você habilitou o recurso write-back de usuário.
  
  >[!NOTE]
  >A expansão de escopo do recurso de Atualização automática afeta os clientes com o Azure AD Connect build 1.1.105.0 e posteriores. Se você não quiser que seu servidor do Azure AD Connect seja atualizado automaticamente, será necessário executar o seguinte cmdlet em seu servidor do Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Para obter mais informações sobre habilitar/desabilitar a Atualização automática, veja o artigo [Azure AD Connect: atualização automática](how-to-connect-install-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Status: Julho de 2017

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problema corrigido
* Foi corrigido um problema com o cmdlet Initialize-ADSyncDomainJoinedComputerSync que fazia com que o domínio verificado configurado no objeto de ponto de conexão de serviço existente fosse alterado, mesmo que ele ainda fosse um domínio válido. Esse problema ocorre quando seu locatário do Azure AD tem mais de um domínio verificado, os quais podem ser usados para configurar o ponto de conexão de serviço.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* O Write-back de Senha já está disponível para versão prévia com a nuvem do Microsoft Azure Governamental e com o Microsoft Cloud Alemanha. Para obter mais informações sobre o suporte do Azure AD Connect para as diferentes instâncias de serviço, veja o artigo [Azure AD Connect: considerações especiais para instâncias](reference-connect-instances.md).

* O cmdlet Initialize-ADSyncDomainJoinedComputerSync agora tem um novo parâmetro opcional chamado AzureADDomain. Esse parâmetro permite especificar o domínio verificado a ser usado para configurar o ponto de conexão de serviço.

### <a name="pass-through-authentication"></a>Autenticação de Passagem

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* O nome do agente necessário para Autenticação de passagem foi alterado de *Conector de Proxy de Aplicativo do Microsoft Azure AD* para *Agente de autenticação do Microsoft Azure AD Connect*.

* A habilitação da Autenticação de passagem não habilita a Sincronização de hash de senha por padrão.


## <a name="115530"></a>1.1.553.0
Status: Junho de 2017

> [!IMPORTANT]
> Foram introduzidas alterações de regra de esquema e sincronização nesse build. O Serviço de Sincronização do Azure AD Connect disparará as etapas de Importação completa e Sincronização completa após a atualização. Detalhes das alterações são descritos abaixo. Para adiar temporariamente as etapas de Importação completa e Sincronização completa após a atualização, consulte o artigo [Como adiar a sincronização completa após a atualização](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Sincronização do Azure AD Connect

#### <a name="known-issue"></a>Problema conhecido
* Há um problema que afeta os clientes que estejam usando a [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) com a sincronização do Azure AD Connect. Quando você navega até a [página Filtragem de domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente do Azure AD Connect, espera-se o seguinte comportamento:
  * Se a filtragem baseada em UO estiver habilitada, a opção **Sincronizar domínios e UOs selecionados** será selecionada.
  * Caso contrário, a opção **Sincronizar todos os domínios e UOs** será selecionada.

O problema que surge é que a **opção Sincronizar todos os domínios e UOs** fica sempre selecionada quando você executa o assistente.  Isso ocorre mesmo se a filtragem baseada em UO tiver sido configurada anteriormente. Antes de salvar qualquer alteração de configuração do AAD Connect, verifique se a **opção Sincronizar domínios e UOs selecionados está selecionada** e verifique se todas as UOs que precisam ser sincronizadas estão habilitadas novamente. Caso contrário, a filtragem baseada em UO será desabilitada.

#### <a name="fixed-issues"></a>Problemas corrigidos

* Foi corrigido um problema com Write-back de Senha que permitia que um administrador do Azure AD redefinisse a senha de uma conta de usuário privilegiado do AD local. O problema ocorre quando o Azure AD Connect recebe a permissão de Redefinição de senha sobre a conta privilegiada. O problema é corrigido nesta versão do Azure AD Connect não permitindo que um administrador do Azure AD redefina a senha de uma conta de usuário privilegiado arbitrária do AD local, a menos que o administrador seja o proprietário daquela conta. Para obter mais informações, consulte o [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Foi corrigido um problema relacionado ao recurso [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) em que o Azure AD Connect não faz write-back no atributo ms-DS-ConsistencyGuid do AD local. O problema ocorre quando há várias florestas do AD locais adicionadas ao Azure AD Connect e a *opção Existem identidades de usuários em vários diretórios* está selecionada. Quando essa configuração é usada, as regras de sincronização resultantes não preenchem o atributo sourceAnchorBinary no metaverso. O atributo sourceAnchorBinary é usado como o atributo de origem para o atributo ms-DS-ConsistencyGuid. Como resultado, o write-back no atributo ms-DSConsistencyGuid não ocorre. Para corrigir o problema, as seguintes regras de sincronização foram atualizadas para garantir que o atributo sourceAnchorBinary no metaverso seja sempre populado:
  * In from AD – InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* Anteriormente, mesmo se o recurso [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) não estivesse habilitado, a regra de sincronização "Out to AD – User ImmutableId" ainda seria adicionada ao Azure AD Connect. O efeito seria benigno e não faria com que o write-back do atributo ms-DS-ConsistencyGuid ocorresse. Para evitar confusão, foi adicionada uma lógica para garantir que a regra de sincronização seja adicionada somente quando o recurso estiver habilitado.

* Foi corrigido um problema que causava a falha na sincronização de hash de senha com o evento de erro 611. Esse problema ocorria depois que um ou mais controladores de domínio eram removidos do AD local. No final de cada ciclo de sincronização de senha, o cookie de sincronização emitido pelo AD local continha IDs de invocação dos controladores de domínio removidos com valor de USN (números de sequência de atualização) igual a 0. O Gerenciador de Sincronização de Senha não é capaz de persistir o cookie de sincronização que tenha valor de USN igual a 0 e falha com o evento de erro 611. Durante o próximo ciclo de sincronização, o Gerenciador de Sincronização de Senha reutiliza o último cookie de sincronização persistido que não tenha o valor de USN igual a 0. Isso faz com que as mesmas alterações de senha sejam sincronizadas. Com essa correção, o Gerenciador de Sincronização de Senha persiste corretamente o cookie de sincronização.

* Anteriormente, mesmo que a Atualização automática tivesse sido desabilitada usando o cmdlet Set-ADSyncAutoUpgrade, o processo de Atualização automática continuava a verificar atualizações periodicamente e dependia do instalador baixado para ser desabilitado. Com essa correção, o processo de Atualização automática não verificará mais atualizações periodicamente. A correção será aplicada automaticamente quando o instalador de atualização desta versão do Azure AD Connect for executada uma vez.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Anteriormente, o recurso [ms-DS-ConsistencyGuid como âncora de origem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) estava disponível somente para novas implantações. Agora ele está disponível para as implantações existentes. Mais especificamente:
  * Para acessar o recurso, inicie o assistente Azure AD Connect e escolha a opção *Atualizar âncora de origem*.
  * Essa opção só está visível para as implantações existentes que estejam usando o objectGuid como atributo sourceAnchor.
  * Ao configurar a opção, o assistente validará o estado do atributo ms-DS-ConsistencyGuid em seu Active Directory local. Se o atributo não estiver configurado em nenhum objeto de usuário no diretório, o assistente usará o ms-DS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo estiver configurado em um ou mais objetos de usuário no diretório, o assistente concluirá que o atributo está sendo usado por outros aplicativos e não é adequado como atributo sourceAnchor, não permitindo que a alteração da Âncora de origem prossiga. Se você tiver certeza de que o atributo não é usado pelos aplicativos existentes, precisará contatar o Suporte para obter informações sobre como suprimir o erro.

* Específico ao atributo **userCertificate** nos objetos de dispositivo, o Azure AD Connect agora procura por valores de certificados necessários para [Conectar dispositivos ingressados no domínio ao Azure AD para a experiência do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) e filtra o restante antes da sincronização com o Azure AD. Para habilitar esse comportamento, a regra de sincronização integrada "Out to AAD – Device Join SOAInAD" foi atualizada.

* Agora o Azure AD Connect dá suporte ao write-back do atributo **cloudPublicDelegates** do Exchange Online no atributo **publicDelegates** do AD local. Isso habilita o cenário em que uma caixa de correio do Exchange Online possa obter direitos de SendOnBehalfTo para usuários com caixa de correio do Exchange local. Para dar suporte a esse recurso, uma nova regra de sincronização integrada "Out to AD – User Exchange Hybrid PublicDelegates writeback" foi adicionada. Essa regra de sincronização só é adicionada ao Azure AD Connect quando o recurso Exchange Híbrido está habilitado.

* Agora o Azure AD Connect dá suporte à sincronização do atributo **altRecipient** por meio do Azure AD. Para dar suporte a essa alteração, as seguintes regras de sincronização integradas foram atualizadas para incluir o fluxo de atributo necessário:
  * Entrada do AD – usuário do Exchange
  * Out to AAD – User ExchangeOnline
  
* O atributo **cloudSOAExchMailbox** no metaverso indica se um determinado usuário tem a caixa de correio do Exchange Online ou não. A definição desse atributo foi atualizada para incluir RecipientDisplayTypes adicionais do Exchange Online, como caixas de correio de Equipamentos e de Sala de conferência. Para habilitar essa alteração, a definição do atributo cloudSOAExchMailbox, que se encontra na regra de sincronização integrada "In from AAD – User Exchange Hybrid", foi atualizada de:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... para o seguinte:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Foi adicionado o seguinte conjunto de funções compatíveis com X509Certificate2 para a criação de expressões de regra de sincronização, a fim de lidar com valores de certificado no atributo userCertificate:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Selecionar|
    |CertKeyAlgorithmParams|CertHashString|Where|
    |||With|

* As seguintes alterações de esquema foram introduzidas para permitir que os clientes criem regras de sincronização personalizadas para transmitir sAMAccountName, domainNetBios e domainFQDN para objetos de grupo, bem como transmitir distinguishedName para objetos de usuário:

  * Os atributos a seguir foram adicionados ao esquema do MV:
    * Grupo: AccountName
    * Grupo: domainNetBios
    * Grupo: domainFQDN
    * Pessoa: distinguishedName

  * Os atributos a seguir foram adicionados ao esquema do Azure AD Connector:
    * Grupo: OnPremisesSamAccountName
    * Grupo: NetBiosName
    * Grupo: DnsDomainName
    * Usuário: OnPremisesDistinguishedName

* O script do cmdlet ADSyncDomainJoinedComputerSync agora tem um novo parâmetro opcional chamado AzureEnvironment. O parâmetro é usado para especificar a região na qual o locatário do Azure Active Directory correspondente está hospedado. Os valores válidos incluem:
  * AzureCloud (padrão)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* O Editor de regra de sincronização foi atualizado para usar Join (em vez de Provision) como o valor padrão do tipo de link durante a criação da regra de sincronização.

### <a name="ad-fs-management"></a>Gerenciamento dos AD FS

#### <a name="issues-fixed"></a>Problemas corrigidos

* As seguintes URLs são novos pontos de extremidade do Web Services Federation, introduzidos pelo Azure AD para aumentar a resiliência contra interrupção de autenticação e serão adicionadas à configuração de confiança da entidade de replicação do AD FS local:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Foi corrigido um problema que fazia com que o AD FS gerasse um valor de declaração incorreto para IssuerID. O problema ocorre se houver vários domínios verificados no locatário do Azure AD e o sufixo do domínio do atributo userPrincipalName usado para gerar a declaração IssuerID tiver pelo menos 3 níveis de profundidade (por exemplo, johndoe@us.contoso.com). O problema é resolvido ao atualizar a expressão regular usada pelas regras de declaração.

#### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
* Anteriormente, o recurso Certificate Management do AD FS, fornecido pelo Azure AD Connect, só podia ser usado com farms do AD FS gerenciados por meio do Azure AD Connect. Agora, você pode usar o recurso com farms do AD FS que não sejam gerenciados com o Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Lançamento: Maio de 2017

> [!IMPORTANT]
> Foram introduzidas alterações de regra de esquema e sincronização nesse build. O Serviço de Sincronização do Azure AD Connect disparará as etapas de Importação Completa e Sincronização Completa após a atualização. Detalhes das alterações são descritos abaixo.
>
>

**Problemas corrigidos:**

Sincronização do Azure AD Connect

* Corrigido um problema que fazia a Atualização Automática ocorrer no servidor do Azure AD Connect, mesmo que o cliente tivesse desabilitado o recurso usando o cmdlet Set-ADSyncAutoUpgrade. Com essa correção, o processo de Atualização Automática no servidor ainda verifica se há atualizações periodicamente, mas o instalador baixado respeita a configuração de Atualização Automática.
* Durante a atualização in-loco do DirSync, o Azure AD Connect cria uma conta de serviço do Azure AD para ser usada pelo Azure AD Connector para sincronizar com o Azure AD. Depois da criação da conta, o Azure AD Connect autentica-se com o Azure AD usando a conta. Às vezes, a autenticação falha devido a questões transitórias que, por sua vez, fazem a atualização in-loco de DirSync falhar com o erro *"Ocorreu um erro ao executar tarefa Configurar AAD Sync: AADSTS50034: Para entrar neste aplicativo, a conta deve ser adicionada ao diretório xxx.onmicrosoft.com".* Para melhorar a resiliência da atualização do DirSync, o Azure AD Connect agora tenta novamente realizar a etapa de autenticação.
* Havia um problema com o build 443 que fazia a atualização in-loco do DirSync ser bem-sucedida, mas os perfis de execução necessários para a sincronização do diretório não eram criados. A lógica de reparo está incluída nesse build do Azure AD Connect. Quando o cliente atualiza para esse build, o Azure AD Connect detecta os perfis de execução ausentes e cria-os.
* Corrigido um problema que fazia o processo de Sincronização de Senha não ser inicializado com a ID de Evento 6900 e o erro *"Um item com a mesma chave já foi adicionado"* . Esse problema ocorre se você atualiza a configuração de filtragem da UO para incluir a partição de configuração do AD. Para corrigir esse problema, o processo de Sincronização de Senha agora sincroniza alterações de senha de partições de domínio do AD apenas. Partições não de domínio, como a partição de configuração, são ignoradas.
* Durante a instalação do Expresso, o Azure AD Connect cria uma conta do AD DS local para ser usada pelo AD Connector para comunicação com o AD local. Anteriormente, a conta era criada com o sinalizador PASSWD_NOTREQD definido no atributo user-Account-Control e uma senha aleatória era definida na conta. Agora, o Azure AD Connect remove explicitamente o sinalizador PASSWD_NOTREQD depois que a senha é definida na conta.
* Corrigido um problema que faz com que a atualização do DirSync falhe com o erro *"Ocorreu um deadlock no SQL Server que está tentando adquirir um deadlock de aplicativo"* quando o atributo mailNickname é encontrado no esquema do AD local, mas não está limitado à classe de objeto de Usuário do AD.
* Corrigido um problema que fazia o recurso de write-back do Dispositivo ser desabilitado automaticamente quando um administrador estava atualizando a configuração de sincronização do Azure AD Connect usando o assistente do Azure AD Connect. Esse problema é causado pela execução de verificação de pré-requisito pelo assistente para a configuração de write-back do Dispositivo existente no AD local e a falha na verificação. A correção será ignorar a verificação se o write-back do Dispositivo já tiver sido habilitado anteriormente.
* Para configurar a filtragem da UO, você pode usar o assistente do Azure AD Connect ou o Synchronization Service Manager. Anteriormente, se você usasse o Assistente do Azure AD Connect para configurar a filtragem de UO, novas UOs criadas posteriormente seriam incluídas para sincronização de diretórios. Se você não quisesse incluir novas UOs, precisaria configurar a filtragem de UO usando o Synchronization Service Manager. Agora, você pode obter o mesmo comportamento usando o assistente do Azure AD Connect.
* Corrigido um problema que fazia os procedimentos armazenados exigidos pelo Azure AD Connect serem criados sob o esquema do administrador de instalação, em vez de no esquema do dbo.
* Corrigido um problema que fazia com que o atributo TrackingId retornado pelo Azure AD fosse omitido nos Logs de Evento do Servidor do AAD Connect. O problema ocorria quando o Azure AD Connect recebia uma mensagem de redirecionamento do Azure AD e do Azure AD Connect não conseguia conectar-se ao ponto de extremidade fornecido. O TrackingId é usado pelos Engenheiros de Suporte para correlacionar com logs no lado do serviço durante a solução de problemas.
* Quando o Azure AD Connect recebe o erro LargeObject do Azure AD, o Azure AD Connect gera um evento com o EventID 6941 e a mensagem *“O objeto provisionado é muito grande. Restrinja o número de valores de atributo nesse objeto.”* Ao mesmo tempo, o Azure AD Connect também gera um evento enganoso com EventID 6900 e mensagem *“Microsoft.Online.Coexistence.ProvisionRetryException: não é possível se comunicar com o serviço Microsoft Azure Active Directory”* . Para minimizar a confusão, Azure AD Connect não gera mais este último evento quando o erro LargeObject é recebido.
* Corrigido um problema que fazia com que o Synchronization Service Manager parasse de responder ao tentar atualizar a configuração do conector de LDAP genérico.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Alterações de Regra de Sincronização – as seguintes alterações de regra de sincronização foram implementadas:
  * Atualizado o conjunto de regras de sincronização padrão para não exportar os atributos **userCertificate** e **userSMIMECertificate** se os atributos tivessem mais de 15 valores.
  * Os atributos **employeeID** e **msExchBypassModerationLink** do AD agora são incluídos no conjunto de regras de sincronização padrão.
  * O atributo **photo** do AD foi removido do conjunto de regras de sincronização padrão.
  * Adicionado **preferredDataLocation** ao esquema de Metaverso e ao esquema do AAD Connector. Os clientes que desejam atualizar qualquer um dos atributos no Azure AD podem implementar regras de sincronização personalizadas para fazer isso. 
  * Adicionado **userType** ao esquema do Metaverso e ao esquema do AAD Connector. Os clientes que desejam atualizar qualquer um dos atributos no Azure AD podem implementar regras de sincronização personalizadas para fazer isso.

* Agora o Azure AD Connect habilita automaticamente o uso do atributo ConsistencyGuid como o atributo de Âncora de origem para objetos do AD local. Além disso, o Azure AD Connect preenche o atributo ConsistencyGuid com o valor do atributo objectGuid se ele estiver vazio. Esse recurso é aplicável apenas à nova implantação. Para obter mais informações sobre esse recurso, veja a seção do artigo [do Azure AD Connect: Conceitos de design – usando ms-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* O novo cmdlet de solução de problemas Invoke-ADSyncDiagnostics foi adicionado para ajudar a diagnosticar problemas relacionados à Sincronização de Hash de Senha. Para obter informações sobre como usar o cmdlet, consulte o artigo [Solucionar problemas de sincronização de hash de senha com a sincronização do Azure AD Connect](tshoot-connect-password-hash-synchronization.md).
* O Azure AD Connect agora dá suporte à sincronização dos objetos de Pasta Pública Habilitados para Email do AD local para o Azure AD. Você pode habilitar o recurso usando o assistente do Azure AD Connect em Recursos Opcionais. Para obter mais informações sobre esse recurso, consulte o artigo [Suporte do Bloqueio do Edge Baseado nos Diretórios do Office 365 para Pastas Públicas locais Habilitadas para Email](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* O Azure AD Connect exige uma conta do AD DS para sincronizar do AD local. Anteriormente, se você instalava o Azure AD Connect usando o modo Expresso, era possível fornecer as credenciais de uma conta do Admin Corporativo e o Azure AD Connect criaria a conta necessária do AD DS. No entanto, para uma instalação personalizada e para adicionar florestas a uma implantação existente, era necessário fornecer a conta do AD DS. Agora você também tem a opção de fornecer as credenciais de uma conta do Admin Corporativo durante uma instalação personalizada e permitir que o Azure AD Connect crie a conta do AD DS necessária.
* O Azure AD Connect agora dá suporte a SQL AOA. Você deve habilitar o SQL AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect detecta se a instância do SQL fornecida está habilitada para SQL AOA ou não. Se o SQL AOA estiver habilitado, o Azure AD Connect descobrirá melhor se o AOA SQL está configurado para usar replicação síncrona ou replicação assíncrona. Ao configurar o Ouvinte do Grupo de Disponibilidade, é recomendável definir a propriedade RegisterAllProvidersIP como 0. Essa recomendação ocorre porque o Azure AD Connect atualmente usa o SQL Native Client para conectar-se ao SQL, e o SQL Native Client não dá suporte ao uso da propriedade MultiSubNetFailover.
* Se você estiver usando o LocalDB como o banco de dados para o servidor do Azure AD Connect e tiver atingido seu limite de tamanho de 10 GB, o Serviço de Sincronização não será mais iniciado. Anteriormente, você precisava executar a operação ShrinkDatabase no LocalDB para recuperar espaço suficiente do banco de dados para o Serviço de Sincronização iniciar. Depois disso, você pode usar o Synchronization Service Manager para excluir o histórico de execução para recuperar mais espaço do banco de dados. Agora, você pode usar cmdlet Start-ADSyncPurgeRunHistory para limpar os dados do histórico de execução do LocalDB para recuperar espaço do banco de dados. Além disso, esse cmdlet dá suporte ao modo offline (especificando o parâmetro -offline), que pode ser usado quando o Synchronization Service não estiver em execução. Observação: O modo offline poderá ser usado apenas se o Synchronization Service não estiver em execução e o banco de dados usado for LocalDB.
* Para reduzir a quantidade de espaço de armazenamento necessária, o Azure AD Connect agora compacta os detalhes do erro de sincronização antes de armazená-los em bancos de dados SQL/LocalDB. Ao atualizar de uma versão anterior do Azure AD Connect para esta versão, o Azure AD Connect executa uma compactação única em detalhes do erro de sincronização existentes.
* Anteriormente, depois de atualizar a configuração de filtragem da UO, você precisava executar manualmente uma importação Completa para garantir que os objetos existentes fossem incluídos/excluídos adequadamente da sincronização de diretório. Agora, o Azure AD Connect aciona automaticamente a importação Completa durante o próximo ciclo de sincronização. Além disso, a importação Total deve ser aplicada apenas aos AD Connectors afetados pela atualização. Observação: essa melhoria é aplicável apenas a atualizações de filtragem de UO feitas usando o assistente do Azure AD Connect. Não é aplicável à atualização de filtragem de UO feita usando o Synchronization Service Manager.
* Anteriormente, filtragem baseada em Grupo dava suporte apenas a objetos de Usuários, Grupos e Contato. Agora, a filtragem baseada em Grupo também dá suporte a objetos de Computador.
* Anteriormente, você podia excluir dados do Espaço Conector sem desabilitar o agendador de sincronização do Azure AD Connect. Agora, o Synchronization Service Manager bloqueia a exclusão de dados do Espaço Conector quando ele detecta que o agendador está habilitado. Além disso, um aviso é retornado para informar os clientes sobre uma possível perda de dados se os dados do espaço conector forem excluídos.
* Anteriormente, você precisava desabilitar a transcrição do PowerShell para o assistente do Azure AD Connect ser executado corretamente. Esse problema foi parcialmente resolvido. Você poderá habilitar a transcrição do PowerShell se estiver usando o assistente do Azure AD Connect para gerenciar a configuração de sincronização. Você deverá desabilitar a transcrição do PowerShell se estiver usando o assistente do Azure AD Connect para gerenciar a configuração do ADFS.



## <a name="114860"></a>1.1.486.0
Lançamento: Abril de 2017

**Problemas corrigidos:**
* Corrigido o problema em que o Azure AD Connect não será instalado com êxito na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Lançamento: Abril de 2017

**Problemas conhecidos:**

* Essa versão do Azure AD Connect não será instalada com êxito se as seguintes condições forem todas verdadeiras:
   1. Você está executando a atualização DirSync in-loco ou a nova instalação do Azure AD Connect.
   2. Você está usando uma versão localizada do Windows Server em que o nome do grupo de Administradores interno no servidor não é "Administradores".
   3. Você pode usar o SQL Server 2012 Express LocalDB padrão instalado com o Azure AD Connect ou usar seu próprio SQL completo.

**Problemas corrigidos:**

Sincronização do Azure AD Connect
* Corrigido um problema em que o agendador de sincronização ignora a etapa de sincronização inteiro se um ou mais conectores estiver sem o perfil de execução para essa etapa de sincronização. Por exemplo, você adicionou manualmente um conector usando Soynchronization Service Manager sem criar um perfil de execução de Importação Delta para ele. Essa correção garante que o agendador de sincronização continua a executar Importação Delta para outros conectores.
* Corrigido um problema em que o Serviço de Sincronização imediatamente para de processar um perfil de execução quando ele encontra um problema com uma das etapas de execução. Essa correção garante que o Serviço de Sincronização ignora essa etapa de execução e continua processando o restante. Por exemplo, você tem um perfil de execução de Importação Delta para seu conector do AD com várias etapas de execução (uma para cada domínio do AD local). O Serviço de Sincronização executará a Importação Delta com os outros domínios do AD, mesmo que um deles tenha problemas de conectividade de rede.
* Corrigido um problema que faz com que a atualização do Azure AD Connector seja ignorada durante a Atualização Automática.
* Corrigido um problema que faz com que o Azure AD Connect determine incorretamente se o servidor é um controlador de domínio durante a instalação o que, por sua vez, faz com que a atualização do DirSync falhe.
* Corrigido um problema que faz com que a atualização in-loco de DirSync não crie perfis de execução para o Azure AD Connector.
* Corrigido um problema em que a interface de usuário do Synchronization Service Manager não responde ao tentar configurar o Generic LDAP Connector.

Gerenciamento dos AD FS
* Corrigido um problema em que o assistente do Azure AD Connect falha se o nó primário do AD FS foi movido para outro servidor.

SSO da Área de Trabalho
* Corrigido um problema no assistente do Azure AD Connect em que a tela de entrada não permite habilitar o recurso de SSO da Área de Trabalho se você escolheu a Sincronização de Senha como a opção de Entrada durante a nova instalação.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Azure AD Connect Sync agora oferecre suporte para uso da Conta de Serviço Virtual, Conta de Serviço Gerenciado e Conta de Serviço Gerenciado de Grupo como sua conta de serviço. Isso se aplica à nova instalação do Azure AD Connect, somente. Ao instalar o Azure AD Connect:
    * Por padrão, o assistente do Azure AD Connect criará uma Conta de Serviço Virtual e utilizará como sua conta de serviço.
    * Se você estiver instalando em um controlador de domínio, o Azure AD Connect reverterá para o comportamento anterior onde criará uma conta de usuário de domínio e utilizará como sua conta de serviço.
    * É possível substituir o comportamento padrão fornecendo uma das seguintes opções:
      * Uma Conta de Serviço Gerenciado de Grupo
      * Uma Conta de Serviço Gerenciado
      * Uma conta de usuário do domínio
      * Uma conta de usuário local
* Anteriormente, se você atualizasse para uma nova versão do Azure AD Connect contendo atualizações de conexão ou alterações na regra de sincronização, o Azure AD Connect dispararia um ciclo de sincronização completo. Agora, o Azure AD Connect dispara seletivamente a etapa de Importação Completa somente para conectores com atualização e a etapa de Sincronização Completa somente para conectores com alterações de regra de sincronização.
* Anteriormente, o limite de exclusão de exportação só se aplicava às exportações disparadas pelo Agendador de Sincronização. Agora, o recurso é estendido para incluir exportações disparadas manualmente pelo cliente usando o Synchronization Service Manager.
* No seu locatário do Azure AD, há uma configuração de serviço que indica se o recurso de Sincronização de Senha está habilitado para o seu locatário ou não. Anteriormente, era fácil que configuração de serviço fosse configurado incorretamente pelo Azure AD Connect quando havia um servidor de preparo e um ativo. Agora, o Azure AD Connect tentará manter a configuração de serviço consistente com o servidor ativo do Azure AD Connect, somente.
* O assistente do Azure AD Connect agora detecta e retorna um aviso se o AD local não tiver Lixeira do AD ativada.
* Anteriormente, Exportar para o Azure AD expirava e falhava se o tamanho combinado dos objetos no lote excedesse um determinado limite. Agora, o Serviço de Sincronização tentará reenviar os objetos em lotes menores se o problema for encontrado.
* O aplicativo de Gerenciamento de Chaves de Serviço de Sincronização foi removido do Menu Iniciar do Windows. O Gerenciamento de chave de criptografia continuará a oferecer suporte por meio da interface de linha de comando usando miiskmu.exe. Para obter informações sobre o gerenciamento de chave de criptografia, consulte o artigo [Abandonando a chave de criptografia de sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key).
* Anteriormente, se você alterava a senha da conta de serviço de sincronização do Azure AD Connect, o Serviço de Sincronização não poderia iniciar corretamente até que você tivesse abandonado a chave de criptografia e reinicializado a senha da conta do serviço de sincronização do Azure AD Connect. Agora, esse processo não é mais necessário.

SSO da Área de Trabalho

* O assistente do Azure AD Connect não exige mais porta 9090 para ser aberta na rede ao configurar a Autenticação de Passagem e SSO de Área de Trabalho. Somente a porta 443 é exigida. 

## <a name="114430"></a>1.1.443.0
Lançamento: Março de 2017

**Problemas corrigidos:**

Sincronização do Azure AD Connect
* Correção de um problema que causa uma falha do assistente do Azure AD Connect se o nome de exibição do Conector do Azure AD não contém o domínio onmicrosoft.com inicial atribuído ao locatário do Azure AD.
* Correção de um problema que causa uma falha do assistente do Azure AD Connect ao estabelecer uma conexão com o banco de dados SQL quando a senha da Conta de Serviço de Sincronização contém caracteres especiais, como apóstrofo, dois-pontos e espaço.
* Corrigido um problema que causa o erro "a imagem tem uma âncora que é diferente da imagem" ocorra em um servidor do Azure AD Connect no modo de preparo após a exclusão temporária local AD da sincronização do objeto e, em seguida, inclusão novamente na sincronização.
* Correção de um problema que faz com que o erro “O objeto localizado pelo DN é um fantasma” ocorra em um servidor do Azure AD Connect no modo de preparo, após a exclusão temporária de um objeto do AD local da sincronização e sua inclusão novamente na sincronização.

Gerenciamento dos AD FS
* Correção de um problema no qual o assistente do Azure AD Connect não atualiza a configuração do AD FS nem define as declarações corretas no objeto de confiança de terceira parte confiável após a configuração de um ID de Logon Alternativo.
* Correção de um problema no qual o assistente do Azure AD Connect não consegue manipular corretamente os servidores do AD FS cujas contas de serviço são configuradas usando o formato userPrincipalName, em vez do formato sAMAccountName.

Autenticação de Passagem
* Correção de um problema que causa uma falha do assistente do Azure AD Connect se a Autenticação de Passagem é selecionada, mas o registro de seu conector falha.
* Correção de um problema que faz com que o assistente do Azure AD Connect ignore as verificações de validação no método de conexão selecionado quando o recurso SSO da Área de Trabalho está habilitado.

Redefinição de senha
* Correção de um problema que pode fazer com que o servidor do Azure AAD Connect não tente se reconectar se a conexão for interrompida por um firewall ou proxy.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Agora, o cmdlet Get-ADSyncScheduler retorna uma nova propriedade booliana chamada SyncCycleInProgress. Se o valor retornado for verdadeiro, isso significa que há um ciclo de sincronização agendado em andamento.
* A pasta de destino de armazenamento dos logs de instalação e configuração do Azure AD Connect foi movida de %localappdata%\AADConnect para %programdata%\AADConnect, a fim de melhorar a acessibilidade aos arquivos de log.

Gerenciamento dos AD FS
* Adição de suporte para atualização do Certificado SSL do Farm do AD FS.
* Adição de suporte para gerenciamento do AD FS 2016.
* Agora é possível especificar uma gMSA (Conta de Serviço Gerenciado de Grupo) existente durante a instalação do AD FS.
* Agora é possível configurar o SHA-256 como o algoritmo de hash de assinatura para o objeto de confiança de terceira parte confiável do Azure AD.

Redefinição de senha
* Introdução de aprimoramentos para permitir que o produto funcione em ambientes com regras de firewall mais rígidas.
* Aumento na confiabilidade de conexão com o Barramento de Serviço do Azure.

## <a name="113800"></a>1.1.380.0
Lançamento: Dezembro de 2016

**Problema corrigido:**

* Foi corrigido o problema em que a regra de declaração de issuerid para AD FS (Serviços de Federação do Active Directory) está ausente desta compilação.

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Lançamento: Dezembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração de issuerid é necessária se você está associando vários domínios ao Azure AD (Azure Active Directory). Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar regra de declaração issuerid, confira este artigo sobre [Multiple Domain Support for Federating with Azure AD](how-to-connect-install-multiple-domains.md) (Suporte a vários domínios para federação com o Azure AD).

**Problema corrigido:**

* Se a porta 9090 não estiver aberta para a conexão de saída, a atualização ou a instalação do Azure AD Connect falhará.

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Lançamento: Dezembro de 2016

**Problemas conhecidos:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração issuerid será necessária se você estiver federando vários domínios com o Azure AD. Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, confira este artigo sobre [Suporte a vários domínios para federação com o Azure AD](how-to-connect-install-multiple-domains.md).
* A porta 9090 deve estar aberta para a saída para que a instalação seja concluída.

**Novos recursos:**

* Autenticação de Passagem (Visualização).

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Lançamento: Novembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração issuerid será necessária se você estiver federando vários domínios com o Azure AD. Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, confira este artigo sobre [Suporte a vários domínios para federação com o Azure AD](how-to-connect-install-multiple-domains.md).

**Problemas corrigidos:**

* Algumas vezes, instalar o Azure Connect AD falha porque não é possível criar uma conta de serviço local cuja senha satisfaça o nível de complexidade especificado pela política de senha da organização.
* Foi corrigido um problema em que as regras de associação não são avaliadas novamente quando um objeto no espaço do conector simultaneamente fica fora do escopo de uma regra de junção e tornam-se em escopo para outro. Isso pode ocorrer se você tiver duas ou mais regras de junção cujas condições de junção são mutuamente exclusivas.
* Corrigido um problema em que regras de sincronização de entrada (do Azure AD) que não contêm regras de união não são processadas se elas tiverem valores de precedência inferiores daqueles que contém regras de associação.

**Aperfeiçoamentos:**

* Adicionado suporte para instalar o Azure AD Connect no Windows Server 2016 Standard ou posterior.
* Foi adicionado suporte para usar o SQL Server 2016 como banco de dados remoto para o Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Lançamento: Agosto de 2016

**Problemas corrigidos:**

* As alterações no intervalo de sincronização não ocorrerão até após a conclusão do próximo ciclo de sincronização.
* O assistente do Azure AD Connect não aceita uma conta do Azure AD cujo nome de usuário começa com um sublinhado (\_).
* Falha do Assistente do Azure AD Connect ao autenticar a conta do Azure AD se a senha da conta contém muitos caracteres especiais. A mensagem de erro "Não foi possível validar as credenciais. Ocorreu um erro inesperado.” é retornado.
* Desinstalar o servidor de preparo desabilita a sincronização de senha no locatário do Azure AD e causa a falha da sincronização de senha com o servidor ativo.
* A sincronização de senha falha em casos incomuns quando não há hash de senha armazenado no usuário.
* Quando o servidor do Azure AD Connect está habilitado para o modo de preparo, o write-back de senha não é temporariamente desabilitado.
* O assistente do Azure AD Connect não mostra a sincronização de senha real e a configuração de write-back de senha quando o servidor está no modo de preparo. Ele sempre os mostra como desabilitados.
* As alterações de configuração na sincronização de senha e no write-back de senha não são mantidas pelo Assistente do Azure AD Connect quando o servidor está no modo de preparo.

**Aperfeiçoamentos:**

* Cmdlet Start-ADSyncSyncCycle atualizado para indicar se pode iniciar um novo ciclo de sincronização com êxito ou não.
* Cmdlet Stop-ADSyncSyncCycle adicionado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Cmdlet Stop-ADSyncScheduler atualizado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Ao configurar [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) no Assistente do Azure AD Connect, o atributo do Azure AD do tipo "Cadeia de caracteres Teletex" pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Lançamento: Junho de 2016

**Problemas corrigidos e aperfeiçoamentos:**

* O Azure AD Connect agora pode ser instalado em um servidor compatível com FIPS.
  * Para sincronização de senha, consulte [Sincronização de hash de senha e FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Corrigido um problema em que um nome NetBIOS não pôde ser resolvido para o FQDN no Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Lançamento: Maio de 2016

**Novos recursos:**

* Avisa e ajuda a verificar os domínios se você não fizer isso antes de executar o Azure AD Connect.
* Adicionado suporte ao [Microsoft Cloud Alemanha](reference-connect-instances.md#microsoft-cloud-germany).
* Adicionado suporte à infraestrutura em [nuvem do Microsoft Azure Governamental](reference-connect-instances.md#microsoft-azure-government) mais recente com novos requisitos de URL.

**Problemas corrigidos e aperfeiçoamentos:**

* Adicionada filtragem ao Editor de regra de sincronização para tornar mais fácil localizar as regras de sincronização.
* Desempenho aprimorado ao excluir um espaço de conector.
* Corrigido um problemas quando o mesmo objeto foi excluído e adicionado na mesma execução (chamado excluir/adicionar).
* Uma regra de sincronização desabilitada não pode mais reabilitar objetos e atributos incluídos na atualização ou na atualização de esquema de diretório.

## <a name="111300"></a>1.1.130.0
Lançamento: Abril de 2016

**Novos recursos:**

* Suporte adicionado para atributos com valores múltiplos para [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).
* Suporte adicionado para mais variações de configuração de [atualização automática](how-to-connect-install-automatic-upgrade.md) serem consideradas qualificadas para atualização.
* Adicionados alguns cmdlets para o [agendador personalizado](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lançamento: Março de 2016

**Problemas corrigidos:**

* Foi garantido que a Instalação expressa não possa ser usada no Windows Server 2008 (pré-R2), já que a sincronização de senha não tem suporte neste sistema operacional.
* A atualização do DirSync com uma configuração de filtro personalizado não funcionou conforme o esperado.
* Ao atualizar para uma versão mais recente e se não houver alterações na configuração, a importação/sincronização completa não deverá ser agendada.

## <a name="111100"></a>1.1.110.0
Lançamento: Fevereiro de 2016

**Problemas corrigidos:**

* A atualização de versões anteriores não funcionará se a instalação não estiver na pasta padrão C:\Arquivos de Programas.
* Se você instalar e desmarcar **Iniciar o processo de sincronização...** no fim do assistente de instalação, executar o assistente de instalação uma segunda vezs não habilitará o agendador.
* O agendador não funciona conforme o esperado em servidores em que o formato de data/hora en-US não é usado. Ele também bloqueará `Get-ADSyncScheduler` para retornar as horas corretas.
* Se você instalou uma versão anterior do Azure AD Connect com o ADFS como a opção de entrada e atualização, não será possível executar o assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Lançamento: Fevereiro de 2016

**Novos recursos:**

* [Automatic upgrade](how-to-connect-install-automatic-upgrade.md) para clientes de configurações Expressas.
* Suporte para o administrador global usando a Autenticação Multifator do Azure e o Privileged Identity Management no assistente de instalação.
  * Você precisará permitir que o proxy também permita o tráfego para https://secure.aadcdn.microsoftonline-p.com se usar a Autenticação Multifator.
  * Você precisa adicionar https://secure.aadcdn.microsoftonline-p.com à lista de sites confiáveis para que a Autenticação Multifator funcione corretamente.
* Permita alterar o método de logon do usuário após a instalação inicial.
* Permita [Filtragem de Domínio e UO](how-to-connect-install-custom.md#domain-and-ou-filtering) no assistente de instalação. Isso também permite conectar-se às florestas em que nem todos os domínios estão disponíveis.
* O [Agendador](how-to-connect-sync-feature-scheduler.md) é interno ao mecanismo de sincronização.

**Recursos promovidos da visualização para GA:**

* [Write-back de dispositivo](how-to-connect-device-writeback.md).
* [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).

**Novos recursos de visualização:**

* O novo intervalo de ciclo de sincronização padrão é de 30 minutos. Costumava ser três horas para todas as versões anteriores. Adiciona suporte para alterar o comportamento do [agendador](how-to-connect-sync-feature-scheduler.md) .

**Problemas corrigidos:**

* A página Verificar domínios DNS nem sempre reconheceu os domínios.
* Solicita credenciais de administrador de domínio ao configurar o AD FS.
* As contas locais do AD não são reconhecidas pelo assistente de instalação se estiverem localizadas em um domínio com uma árvore DNS diferente do domínio raiz.

## <a name="1091310"></a>1.0.9131.0
Lançamento: Dezembro de 2015

**Problemas corrigidos:**

* A sincronização de senha poderá não funcionar quando você alterar as senhas no AD DS (Active Directory Domain Services), mas funcionará quando você definir uma senha.
* Quando você tiver um servidor proxy, a autenticação do Azure AD poderá falhar durante a instalação ou se uma atualização for cancelada na página de configuração.
* A atualização de uma versão anterior do Azure AD Connect com uma instância completa do SQL Server falhará se você não for um SA (administrador do sistema) do SQL Server.
* A atualização de uma versão anterior do Azure AD Connect com um SQL Server remoto mostra o erro "Não é possível acessar o banco de dados SQL do ADSync".

## <a name="1091250"></a>1.0.9125.0
Lançamento: Novembro de 2015

**Novos recursos:**

* Pode reconfigurar a relação de confiança entre o ADFS e o AD do Azure.
* Pode atualizar o esquema do Active Directory e gerar regras de sincronização.
* Pode desabilitar uma regra de sincronização.
* Pode definir "AuthoritativeNull" como um novo literal em uma regra de sincronização.

**Novos recursos de visualização:**

* [Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).
* Suporte para sincronização de senha dos [Serviços de Domínio do AD do Azure](../user-help/active-directory-passwords-update-your-own-password.md) .

**Novo cenário com suporte:**

* Dá suporte a várias organizações local do Exchange. Para obter mais informações, confira [Implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Problemas corrigidos:**

* Problemas de sincronização de senha:
  * Um objeto movido de fora do escopo para dentro do escopo não terá sua senha sincronizada. Isso inclui a UO e a filtragem de atributo.
  * Selecionar uma nova UO para incluir na sincronização não exige uma sincronização de senha completa.
  * Quando um usuário desabilitado é habilitado, a senha não é sincronizada.
  * A fila de repetição de senha é infinita e o limite anterior de 5.000 objetos a ser retirado foi removido.
* Não é possível se conectar ao Active Directory com o nível funcional de floresta do Windows Server 2016.
* Não é possível alterar o grupo que é usado para filtragem de grupo após a instalação inicial.
* Não cria mais um novo perfil do usuário no servidor do Azure AD Connect para cada usuário que faz uma alteração de senha com write-back de senha habilitada.
* Não é possível usar valores Inteiros Longos em escopos de regras de sincronização.
* A caixa de seleção "write-back de dispositivo" permanecerá desabilitada se houver controladores de domínio inacessíveis.

## <a name="1086670"></a>1.0.8667.0
Lançamento: Agosto de 2015

**Novos recursos:**

* O assistente de instalação do Azure AD Connect agora está localizado para todos os idiomas do Windows Server.
* Suporte adicionado para desbloqueio de contas ao usar o gerenciamento de senhas do AD do Azure.

**Problemas corrigidos:**

* O assistente de instalação do Azure AD Connect falha se outro usuário continuar a instalação em vez da pessoa que iniciou a instalação inicialmente.
* Se uma desinstalação anterior do Azure AD Connect falhar ao desinstalar o Azure AD Connect Sync corretamente, não será possível reinstalá-lo.
* Não será possível instalar o Azure AD Connect usando a Instalação expressa se o usuário não estiver no domínio raiz da floresta ou se uma versão diferente do inglês do Active Directory for usada.
* Se o FQDN da conta de usuário do Active Directory não puder ser resolvido, é mostrada a mensagem enganosa de erro "Falha ao confirmar o esquema".
* Se a conta usada no Active Directory Connector for alterada fora do assistente, o assistente falhará em execuções posteriores.
* O Azure AD Connect às vezes não pode instalar em um controlador de domínio.
* Não é possível habilitar e desabilitar o "Modo de preparo" se os atributos de extensão forem adicionados.
* O write-back de senha falha em algumas configurações devido a uma senha incorreta no Active Directory Connector.
* DirSync não poderá ser atualizado se um DN (nome diferenciado) for usado na filtragem de atributos.
* Uso excessivo de CPU ao usar a redefinição de senha.

**Recursos de visualização removidos:**

* O recurso de visualização [Write-back de usuário](how-to-connect-preview.md#user-writeback) foi temporariamente removido com base nos comentários de nossos clientes da visualização. Ela será adicionada novamente mais tarde, depois que abordarmos os comentários fornecidos.

## <a name="1086410"></a>1.0.8641.0
Lançamento: Junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado de Azure AD Sync para Azure AD Connect.

**Novos recursos:**

* Instalação das [configurações expressas](how-to-connect-install-express.md)
* Pode [configurar o AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* É possível [atualizar do DirSync](how-to-dirsync-upgrade-get-started.md)
* [Impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Apresentação do [modo de preparo](how-to-connect-sync-staging-server.md)

**Novos recursos de visualização:**

* [Write-back de usuário](how-to-connect-preview.md#user-writeback)
* [Write-back de grupo](how-to-connect-preview.md#group-writeback)
* [Write-back de dispositivo](how-to-connect-device-writeback.md)
* [Extensões de diretório](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Lançamento: Maio de 2015

**Novo Requisito:**

* O Azure AD Sync agora requer a instalação do .NET Framework versão 4.5.1.

**Problemas corrigidos:**

* O write-back de senha do Azure AD está falhando com um erro de conectividade do Barramento de Serviço do Azure.

## <a name="104910413"></a>1.0.491.0413
Lançamento: Abril de 2015

**Problemas corrigidos e aperfeiçoamentos:**

* O Active Directory Connector não processa exclusões corretamente se a Lixeira estiver habilitada e se existirem vários domínios na floresta.
* O desempenho das operações de importação foi aprimorado para o Active Directory Connector do Azure.
* Quando um grupo excedeu o limite de associação (por padrão, o limite é definido como 50.000 objetos), o grupo foi excluído do Azure Active Directory. Com o novo comportamento, o grupo não é excluído, um erro é gerado e novas alterações de associação não são exportadas.
* Não é possível provisionar um novo objeto se uma exclusão de preparo com o mesmo DN já estiver presente no espaço do conector.
* Alguns objetos são marcados para sincronização durante uma sincronização delta, embora não haja alteração preparada no objeto.
* Forçar uma sincronização de senha também remove a lista preferencial do DC.
* CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novos recursos:**

* Um ingresso agora pode se conectar ao tipo de objeto "ANY" na MV.

## <a name="104850222"></a>1.0.485.0222
Lançamento: Fevereiro de 2015

**Aperfeiçoamentos:**

* Desempenho aprimorado de importação.

**Problemas corrigidos:**

* A Sincronização de Senha respeita o atributo cloudFiltered usado pela filtragem de atributo. Os objetos filtrados não estão mais no escopo de sincronização de senha.
* Em raras situações em que a topologia tem muitos controladores de domínio, a sincronização de senha não funciona.
* "Servidor-parado" ao importar do Conector do AD do Azure depois que o gerenciamento de dispositivo tiver sido habilitado no AD do Azure/Intune.
* Ingressar FSPs (Entidades de Segurança Externa) de vários domínios na mesma floresta causa um erro de ingresso ambíguo.

## <a name="104751202"></a>1.0.475.1202
Lançamento: Dezembro de 2014

**Novos recursos:**

* Agora há suporte para a sincronização de senhas com filtragem baseada em atributo. Para obter mais informações, confira [Sincronização de senha com filtragem](how-to-connect-sync-configure-filtering.md).
* O atributo ms-DS-ExternalDirectoryObjectID será gravado de volta no Active Directory. Esse recurso adiciona suporte a aplicativos do Office 365. Ele usa OAuth2 para acessar caixas de correio Locais e Online em uma Implantação Híbrida do Exchange.

**Problemas de atualização corrigidos:**

* Uma versão mais recente do que o assistente de logon está disponível no servidor.
* Um caminho de instalação personalizada foi usado para instalar o Azure AD Sync.
* Um critério de associação personalizado inválido bloqueará a atualização.

**Outras correções:**

* Modelos do Office Pro Plus corrigidos.
* Foram corrigidos os problemas de instalação causados por nomes de usuário que começam com um traço.
* Foi corrigida a perda da configuração sourceAnchor ao executar o assistente de instalação uma segunda vez.
* Rastreamento ETW fixo para a sincronização de senha corrigido.

## <a name="104701023"></a>1.0.470.1023
Lançamento: Outubro de 2014

**Novos recursos:**

* Sincronização de senha de vários locais do Active Directory para o Azure AD.
* Interface do usuário de instalação localizada para todos os idiomas do Windows Server.

**Atualizando do AADSync 1.0 GA**

Se já tiver instalado o Azure AD Sync, há uma etapa adicional que você precisa realizar caso você já tenha alterado algumas das regras de sincronização de fábrica. Depois de atualizar para a versão 1.0.470.1023, a sincronização de regras que você modificou será duplicada. Para cada regra de sincronização modificada, faça o seguinte:

1. Localize a regra de sincronização você modificou e anote as alterações.
1. Exclua a regra de sincronização.
1. Localize a nova regra de sincronização que é criada pelo Azure AD Sync e reaplique as alterações.

**Permissões para a conta do Active Directory**

A conta do Active Directory deve receber permissões adicionais para poder ler os hashes de senha do Active Directory. As permissões a serem concedidas são denominadas "Replicar Alterações de Diretório" e "Replicar Todas as Alterações de Diretório". Ambas as permissões são necessárias para ler os hashes de senha.

## <a name="104190911"></a>1.0.419.0911
Lançamento: Setembro de 2014

**Versão inicial do Azure AD Sync.**

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
