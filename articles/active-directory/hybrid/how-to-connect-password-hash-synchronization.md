---
title: Implementar a sincronização de hash de senha com o Azure AD Connect | Microsoft Docs
description: Fornece informações sobre como funciona a sincronização de hash de senha e como configurá-la.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47d7d541ed7d9805641ffdfde381d482c8700006
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858732"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implemente a sincronização de hash de senha com a sincronização do Azure AD Connect
Este artigo fornece as informações necessárias para sincronizar suas senhas de usuário de uma instância do AD (Active Directory) local para uma instância do Azure AD (Azure Active Directory) baseada na nuvem.

## <a name="how-password-hash-synchronization-works"></a>Como a sincronização de hash de senha funciona
O serviço de domínio do Active Directory armazena senhas na forma de uma representação de valor de hash da senha de usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o *algoritmo de hash*). Não há um método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. 

Para sincronizar sua senha, a sincronização do Azure AD Connect extrai o hash da senha de usuário da instância do Active Directory local. O processamento de segurança adicional é aplicado ao hash da senha antes que ele seja sincronizado com o serviço de autenticação do Azure Active Directory. As senhas são sincronizadas por usuário e em ordem cronológica.

O fluxo de dados real do processo de sincronização de hash de senha é semelhante à sincronização de dados de usuário. Contudo, as senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de hash senha é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Ao sincronizar uma senha, ela substituirá a senha de nuvem existente.

Na primeira vez que você habilitar o recurso de sincronização de hash de senha, ele executará uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que deseja sincronizar. No entanto, se houver vários conectores, será possível desabilitar a sincronização de hash de senha para alguns conectores, mas não para outros usando o cmdlet [set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) .

Quando você altera uma senha local, a senha atualizada é sincronizada, geralmente em questão de minutos.
O recurso de sincronização de hash de senha repete automaticamente tentativas de sincronização com falha. Se ocorrer um erro durante uma tentativa de sincronização de senha, um erro é registrado no seu visualizador de eventos.

A sincronização de uma senha não afeta um usuário conectado atualmente.
A sessão de serviço de nuvem atual não será imediatamente afetada por uma alteração de senha sincronizada que ocorrer enquanto você estiver conectado no serviço de nuvem. No entanto, quando o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

O usuário deve inserir suas credenciais corporativas uma segunda vez para autenticar no Azure AD, independentemente se ele está conectado à rede corporativa. Porém, esse padrão poderá ser minimizado, se o usuário marcar a caixa de seleção de KMSI (Manter-me conectado) ao entrar. Essa seleção define um cookie de sessão que ignora a autenticação por 180 dias. O comportamento KMSI pode ser habilitado ou desabilitado pelo administrador do Azure AD. Além disso, é possível reduzir os prompts de senha ativando o [SSO Contínuo](how-to-connect-sso.md), que conecta os usuários automaticamente quando estão em dispositivos corporativos conectados à rede corporativa.

> [!NOTE]
> Somente há suporte para a sincronização de senha para o usuário do tipo de objeto no Active Directory. Não há suporte para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrição detalhada de como funciona a sincronização de hash senha

A seção a seguir descreve detalhadamente como a sincronização de hash de senha funciona entre o Azure AD e Active Directory.

![Fluxo da senha detalhado](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. A cada dois minutos, o agente de sincronização de hash de senha no servidor do AD Connect solicita hashes de senha armazenados (o atributo unicodePwd) de um DC.  Essa solicitação é feita através do protocolo de replicação padrão [MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) usado para sincronizar dados entre DCs. A conta do serviço deve ter as permissões do AD Replicar Alterações de Diretório e Replicar Todas as Alterações de Diretório (concedidas por padrão na instalação) para obter os hashes de senha.
2. Antes de enviar, o controlador de domínio criptografa o hash de senha MD4 usando uma chave que é um hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) da chave de sessão RPC e um valor de sal. Em seguida, ele envia o resultado para o agente de sincronização de hash de senha por RPC. O controlador de domínio também passa o sal para o agente de sincronização usando o protocolo de replicação do controlador de domínio, para que o agente possa descriptografar o envelope.
3. Depois que o agente de sincronização de hash de senha tiver o envelope criptografado, ele usará [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) e o sal para gerar uma chave para descriptografar os dados recebidos de volta para seu formato original de MD4. O agente de sincronização de hash de senha nunca tem acesso à senha de texto não criptografado. O uso do MD5 do agente de sincronização de hash de senha é estritamente para a compatibilidade do protocolo de replicação com o controlador de domínio e é usado somente localmente entre o controlador de domínio e o agente de sincronização de hash de senha.
4. O agente de sincronização de hash de senha expande o hash de senha binária de 16 bits para 64 bytes convertendo primeiro o hash em uma cadeia hexadecimal de 32 bytes, depois convertendo essa cadeia de caracteres de volta para o binário com a codificação UTF-16.
5. O agente de sincronização de hash de senha adiciona um sal por usuário – que é composto por um sal de 10 bytes – ao binário de 64 bytes a fim de proteger ainda mais o hash original.
6. Em seguida, o agente de sincronização de hash de senha combina o hash MD4 e o sal por usuário e usa o resultado como entrada na função [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt). São usadas 1000 iterações do algoritmo de hash com chave [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256). 
7. O agente de sincronização de hash de senha usa o hash de 32 bytes resultante, concatena tanto o Salt por usuário quanto o número de iterações SHA256 a ele (para uso pelo Azure AD) e, em seguida, transmite a cadeia de caracteres de Azure AD Connect para o Azure AD por TLS.</br> 
8. Quando um usuário tenta entrar no Azure AD e insere sua senha, a senha é executada por meio do mesmo processo de MD4 + sal + PBKDF2 + HMAC-SHA256. Se o hash resultante corresponder ao hash armazenado no Azure AD, isso significa que o usuário digitou a senha correta e será autenticado.

> [!NOTE]
> O hash MD4 original não é transmitido para o Azure AD. Em vez disso, o hash SHA256 do hash MD4 original é transmitido. Como resultado, se o hash armazenado no Azure AD for obtido, ele não poderá ser usada em um ataque de passagem de hash no local.

### <a name="security-considerations"></a>Considerações sobre segurança

Ao sincronizar senhas, a versão da sua senha em texto sem formatação não é exposta ao recurso de sincronização de hash de senha, nem ao Azure AD ou qualquer um dos serviços associados.

A autenticação do usuário ocorre no Azure AD e não na própria instância do Active Directory da organização. Os dados de senha SHA256 armazenados no Azure AD - um hash do hash MD4 original - são mais seguros que os armazenados no Active Directory. Além disso, como não é possível descriptografar esse hash SHA256, ele não pode ser levado de volta ao ambiente do Active Directory da organização e apresentado como uma senha de usuário válida em um ataque de passagem de hash.

### <a name="password-policy-considerations"></a>Considerações sobre política de senha

Há dois tipos de políticas de senha que são afetadas ao habilitar a sincronização de hash de senha:

* Política de complexidade de senha
* Política de expiração de senha

#### <a name="password-complexity-policy"></a>Política de complexidade de senha

Quando a sincronização de hash de senha é habilitada, as políticas de complexidade de senha na instância do Active Directory local substituem as políticas de complexidade na nuvem para usuários sincronizados. Você pode usar todas as senhas válidas da sua instância do Active Directory local para acessar os serviços do Azure AD.

> [!NOTE]
> Senhas de usuários criadas diretamente na nuvem ainda estão sujeitas a políticas de senha, conforme definido na nuvem.

#### <a name="password-expiration-policy"></a>Política de expiração de senha

Se um usuário estiver no escopo da sincronização de hash de senha, por padrão, a senha da conta de nuvem será definida para *nunca expirar*.

Você pode continuar entrando nos serviços de nuvem usando uma senha sincronizada que expirou no seu ambiente local. A senha de nuvem será atualizada na próxima vez que você alterar a senha no ambiente local.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Se houver usuários sincronizados que interagem somente com os serviços integrados do Azure AD e também devem estar em conformidade com uma política de expiração de senha, você poderá forçá-los a cumprir sua política de expiração de senha do Azure AD habilitando o recurso *EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

Quando *EnforceCloudPasswordPolicyForPasswordSyncedUsers* está desabilitado (que é a configuração padrão), Azure ad Connect define o atributo PasswordPolicies de usuários sincronizados como "DisablePasswordExpiration". Isso é feito toda vez que a senha de um usuário é sincronizada e instrui o Azure AD a ignorar a política de expiração de senha de nuvem para esse usuário. Você pode verificar o valor do atributo usando o módulo do PowerShell do Azure AD com o seguinte comando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Para habilitar o recurso EnforceCloudPasswordPolicyForPasswordSyncedUsers, execute o comando a seguir usando o módulo MSOnline do PowerShell, como mostrado abaixo. Você precisaria digitar Sim para o parâmetro Enable, conforme mostrado abaixo:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Uma vez habilitado, o Azure AD não vai para cada usuário sincronizado para remover o `DisablePasswordExpiration` valor do atributo PasswordPolicies. Em vez disso, o `DisablePasswordExpiration` valor é removido de PasswordPolicies durante a próxima sincronização de hash de senha para cada usuário, após a próxima alteração de senha no AD local.

É recomendável habilitar EnforceCloudPasswordPolicyForPasswordSyncedUsers, antes de habilitar a sincronização de hash de senha, para que a sincronização inicial de hashes de senha não adicione o `DisablePasswordExpiration` valor ao atributo PasswordPolicies para os usuários.

A política de senha padrão do Azure AD exige que os usuários alterem suas senhas a cada 90 dias. Se sua política no AD também for de 90 dias, as duas políticas deverão corresponder. No entanto, se a política do AD não for de 90 dias, você poderá atualizar a política de senha do Azure AD para fazer a correspondência usando o comando Set-MsolPasswordPolicy PowerShell.

O Azure AD dá suporte a uma política de expiração de senha separada por domínio registrado.

ADVERTÊNCIA: se houver contas sincronizadas que precisam ter senhas que não expiram no Azure AD, você deverá adicionar explicitamente o `DisablePasswordExpiration` valor ao atributo PasswordPolicies do objeto de usuário no Azure AD.  Você pode fazer isso executando o comando a seguir.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> O comando Set-MsolPasswordPolicy PowerShell não funcionará em domínios federados. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Sincronizar senhas temporárias e "forçar alteração de senha no próximo logon"

É comum forçar um usuário a alterar sua senha durante o primeiro logon, especialmente depois que uma redefinição de senha de administrador ocorre.  Ele é comumente conhecido como definir uma senha "temporária" e é concluído verificando o sinalizador "o usuário deve alterar a senha no próximo logon" em um objeto de usuário no Active Directory (AD).
  
A funcionalidade de senha temporária ajuda a garantir que a transferência de propriedade da credencial seja concluída na primeira utilização, para minimizar a duração de tempo em que mais de um indivíduo tem conhecimento dessa credencial.

Para dar suporte a senhas temporárias no Azure AD para usuários sincronizados, você pode habilitar o recurso *ForcePasswordChangeOnLogOn* , executando o seguinte comando no servidor de Azure ad Connect:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Forçar um usuário a alterar sua senha no próximo logon requer uma alteração de senha ao mesmo tempo.  Azure AD Connect não selecionará o sinalizador forçar alteração de senha por si só; Ele é suplementar à alteração de senha detectada que ocorre durante a sincronização de hash de senha.

> [!CAUTION]
> Você só deve usar esse recurso quando SSPR e Write-back de senha estiverem habilitados no locatário.  Isso é para que, se um usuário alterar sua senha por meio de SSPR, ele será sincronizado com Active Directory.

#### <a name="account-expiration"></a>Expiração da conta

Se a organização estiver usando o atributo accountExpires como parte do gerenciamento de conta de usuário, esse atributo não será sincronizado com o Azure AD. Como resultado, uma conta expirada do Active Directory em um ambiente configurado para sincronização de hash de senha ainda estará ativa no Azure AD. Recomendamos que, se a conta expirar, uma ação de fluxo de trabalho dispare um script do PowerShell que desabilite a conta de usuário do Azure AD (use o cmdlet [Set-AzureADUser](/powershell/module/azuread/set-azureaduser)). Por outro lado, quando a conta for ativada, a instância do Azure AD deverá ser ativada.

### <a name="overwrite-synchronized-passwords"></a>Substituir senhas sincronizadas

Um administrador pode redefinir sua senha manualmente usando o Windows PowerShell.

Nesse caso, a nova senha substitui a senha sincronizada e todas as políticas de senha definidas na nuvem se aplicam à nova senha.

Se você alterar a senha local novamente, a nova senha será sincronizada com a nuvem e substituirá a senha atualizada manualmente.

A sincronização de uma senha não afeta um usuário do Azure que está conectado. Sua sessão de serviço de nuvem atual não será afetada imediatamente por uma alteração de senha sincronizada que ocorre enquanto você está conectado a um serviço de nuvem. KMSI ampliará a duração dessa diferença. Quando o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

### <a name="additional-advantages"></a>Vantagens adicionais

- Em geral, a sincronização de hash de senha é mais simples de implementar do que um serviço de federação. Ela não exige quaisquer servidores adicionais e elimina a dependência de um serviço de federação altamente disponível para autenticar usuários.
- A sincronização de hash de senha também pode ser habilitada além da federação. Ela pode ser usada como um fallback se seu serviço de federação sofrer uma interrupção.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Processo de sincronização de hash de senha para Azure AD Domain Services

Se você usar Azure AD Domain Services para fornecer autenticação herdada para aplicativos e serviços que precisam usar Kerberos, LDAP ou NTLM, alguns processos adicionais serão parte do fluxo de sincronização de hash de senha. Azure AD Connect usa o seguinte processo adicional para sincronizar os hashes de senha para o Azure AD para uso no Azure AD Domain Services:

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. Não há suporte para instalar o Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta ao Azure AD.
>
> Azure AD Connect sincroniza somente os hashes de senha herdados quando você habilita o Azure AD DS para seu locatário do Azure AD. As etapas a seguir não serão usadas se você usar apenas Azure AD Connect para sincronizar um ambiente de AD DS local com o Azure AD.
>
> Se seus aplicativos herdados não usam autenticação NTLM ou associações simples LDAP, recomendamos que você desabilite a sincronização de hash de senha NTLM para o Azure AD DS. Para obter mais informações, consulte [desabilitar pacotes de criptografia fracos e sincronização de hash de credencial NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect recupera a chave pública para a instância do locatário do Azure AD Domain Services.
1. Quando um usuário altera sua senha, o controlador de domínio local armazena o resultado da alteração de senha (hashes) em dois atributos:
    * *unicodePwd* para o hash de senha NTLM.
    * *supplementalCredentials* para o hash de senha Kerberos.
1. Azure AD Connect detecta alterações de senha por meio do canal de replicação de diretório (alterações de atributo que precisam ser replicadas para outros controladores de domínio).
1. Para cada usuário cuja senha foi alterada, Azure AD Connect executa as seguintes etapas:
    * Gera uma chave simétrica do AES de 256 bits aleatória.
    * Gera um vetor de inicialização aleatória necessário para a primeira rodada de criptografia.
    * Extrai hashes de senha Kerberos dos atributos *supplementalCredentials* .
    * Verifica a configuração de *SyncNtlmPasswords* de configuração de segurança Azure AD Domain Services.
        * Se essa configuração estiver desabilitada, o gerará um hash NTLM aleatório de alta entropia (diferente da senha do usuário). Esse hash é então combinado com os hashes de senha Kerberos exatos do atributo *supplementalCrendetials* em uma estrutura de dados.
        * Se habilitado, combina o valor do atributo *unicodePwd* com os hashes de senha Kerberos extraídos do atributo *supplementalCredentials* em uma estrutura de dados.
    * Criptografa a estrutura de dados única usando a chave simétrica AES.
    * Criptografa a chave simétrica AES usando a chave pública de Azure AD Domain Services do locatário.
1. Azure AD Connect transmite a chave simétrica criptografada AES, a estrutura de dados criptografada que contém os hashes de senha e o vetor de inicialização para o Azure AD.
1. O Azure AD armazena a chave simétrica criptografada AES, a estrutura de dados criptografada e o vetor de inicialização para o usuário.
1. O Azure AD envia por push a chave simétrica criptografada AES, a estrutura de dados criptografada e o vetor de inicialização usando um mecanismo de sincronização interno em uma sessão HTTP criptografada para Azure AD Domain Services.
1. Azure AD Domain Services recupera a chave privada para a instância do locatário do Azure Key Vault.
1. Para cada conjunto de dados criptografado (representando a alteração de senha de um único usuário), Azure AD Domain Services executa as seguintes etapas:
    * Usa sua chave privada para descriptografar a chave simétrica AES.
    * Usa a chave simétrica AES com o vetor de inicialização para descriptografar a estrutura de dados criptografada que contém os hashes de senha.
    * Grava os hashes de senha Kerberos que ele recebe para o controlador de domínio Azure AD Domain Services. Os hashes são salvos no atributo *supplementalCredentials* do objeto de usuário que é criptografado para a chave pública do controlador de domínio Azure AD Domain Services.
    * Azure AD Domain Services grava o hash de senha NTLM recebido para o controlador de domínio Azure AD Domain Services. O hash é salvo no atributo *unicodePwd* do objeto de usuário que é criptografado para a chave pública do controlador de domínio Azure AD Domain Services.

## <a name="enable-password-hash-synchronization"></a>Permitir a sincronização de hash de senha

>[!IMPORTANT]
>Se você estiver migrando do AD FS (ou outras tecnologias de federação) para Sincronização de Hash de Senha, recomendamos fortemente seguir nosso guia detalhado de implantação publicado [aqui](https://aka.ms/adfstophsdpdownload).

Quando você instala o Azure AD Connect usando as **Configurações Expressas**, a sincronização de hash de senha é habilitada automaticamente. Para obter mais informações, consulte [Introdução ao Azure AD Connect usando configurações expressas](how-to-connect-install-express.md).

Se você usar configurações personalizadas quando instalar o Azure AD Connect, a sincronização de hash de senha estará disponível na página de entrada do usuário. Para saber mais, confira [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

![Permitindo a sincronização de hash de senha](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronização de hash de senha e FIPS
Se o servidor tiver sido bloqueado de acordo com o FIPS (Federal Information Processing Standard), isso significará que o MD5 foi desabilitado.

**Para habilitar MD5 para a sincronização de hash de senha, execute as seguintes etapas:**

1. Vá para %programfiles%\Azure AD Sync\Bin.
2. Abra miiserver.exe.config.
3. Acesse o nó configuração/runtime no fim do arquivo.
4. Adicione o seguinte nó: `<enforceFIPSPolicy enabled="false"/>`
5. Salve suas alterações.

Para referência, esse snippet mostra como deve ser a aparência:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS, consulte [Sincronização de hash de senha do Azure, criptografia e conformidade FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Solução de Problemas de Sincronização de hash de Senha
Caso tenha problemas com a sincronização de hash de senha, veja [Solucionar problemas de sincronização de hash de senha](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Próximas etapas
* [Sincronização de Azure AD Connect: personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
* [Obter um plano de implantação passo a passo para migração do AD FS para sincronização de Hash de senha](https://aka.ms/authenticationDeploymentPlan)
