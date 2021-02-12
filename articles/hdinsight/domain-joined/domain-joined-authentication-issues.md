---
title: Problemas de autenticação no Azure HDInsight
description: Problemas de autenticação no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: b5a55b3b5f9affcd9f34e1c0d4c439a7ada8c0b9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095117"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemas de autenticação no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

Em clusters seguros apoiados por Azure Data Lake (Gen1 ou Gen2), quando os usuários de domínio entram nos serviços de cluster por meio do gateway do HDI (como entrar no portal do Apache Ambari), o gateway HDI tentará obter um token OAuth do Azure Active Directory (Azure AD) primeiro e, em seguida, obterá um tíquete Kerberos do Azure AD DS. A autenticação pode falhar em qualquer um desses estágios. Este artigo destina-se à depuração de alguns desses problemas.

Quando a autenticação falhar, você receberá uma solicitação de credenciais. Se você cancelar essa caixa de diálogo, a mensagem de erro será impressa. Aqui estão algumas das mensagens de erro comuns:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant ou unauthorized_client, 50126

### <a name="issue"></a>Problema

A entrada falha para usuários federados com o código de erro 50126 (a entrada é bem-sucedida para usuários de nuvem). A mensagem de erro é semelhante a:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Causa

O código de erro 50126 do Azure AD significa que a `AllowCloudPasswordValidation` política não foi definida pelo locatário.

### <a name="resolution"></a>Resolução

O administrador global do locatário do Azure AD deve habilitar o Azure AD a usar hashes de senha para usuários com suporte do ADFS.  Aplique o `AllowCloudPasswordValidationPolicy` conforme mostrado no artigo [usar Enterprise Security Package no HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant ou unauthorized_client, 50034

### <a name="issue"></a>Problema

A entrada falha com o código de erro 50034. A mensagem de erro é semelhante a:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Causa

O nome de usuário está incorreto (não existe). O usuário não está usando o mesmo nome de usuário que é usado no portal do Azure.

### <a name="resolution"></a>Resolução

Use o mesmo nome de usuário que funciona no Portal.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant ou unauthorized_client, 50053

### <a name="issue"></a>Problema

A conta de usuário está bloqueada, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Causa

Número excessivo de tentativas de entrada com uma senha incorreta.

### <a name="resolution"></a>Resolução

Aguarde 30 minutos ou mais, pare todos os aplicativos que possam estar tentando autenticar.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant ou unauthorized_client, 50053 (#2)

### <a name="issue"></a>Problema

Senha expirada, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Causa

A senha expirou.

### <a name="resolution"></a>Resolução

Altere a senha no portal do Azure (no seu sistema local) e aguarde 30 minutos para que a sincronização seja atualizada.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problema

Receber mensagem de erro `interaction_required` .

### <a name="cause"></a>Causa

A política de acesso condicional ou MFA está sendo aplicada ao usuário. Como a autenticação interativa ainda não é compatível, o usuário ou o cluster precisa estar isento de acesso de MFA/condicional. Se você optar por isentar o cluster (política de isenção baseada em endereço IP), certifique-se de que o AD `ServiceEndpoints` esteja habilitado para essa vnet.

### <a name="resolution"></a>Resolução

Use a política de acesso condicional e isentar os clusters HDInisght do MFA, conforme mostrado em [configurar um cluster HDInsight com Enterprise Security Package usando Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Entrada negada

### <a name="issue"></a>Problema

A entrada foi negada.

### <a name="cause"></a>Causa

Para chegar a esse estágio, a autenticação OAuth não é um problema, mas a autenticação Kerberos é. Se esse cluster tiver suporte de ADLS, a entrada OAuth terá êxito antes da tentativa de autenticação Kerberos. Em clusters WASB, a entrada OAuth não é tentada. Pode haver muitas razões para que os hashes de senha do tipo falha de Kerberos fiquem fora de sincronia, conta de usuário bloqueada no Azure AD DS e assim por diante. Os hashes de senha são sincronizados somente quando o usuário altera a senha. Quando você cria a instância de AD DS do Azure, ela começa a sincronizar as senhas que são alteradas após a criação. Ele não sincronizará retroativamente as senhas que foram definidas antes de sua criação.

### <a name="resolution"></a>Resolução

Se você considerar que as senhas podem não estar em sincronia, tente alterar a senha e aguarde alguns minutos para a sincronização.

Tente realizar o SSH em um você precisará tentar autenticar (kinit) usando as mesmas credenciais de usuário, de um computador que tenha ingressado no domínio. SSH no nó de cabeçalho/borda com um usuário local e, em seguida, execute kinit.

---

## <a name="kinit-fails"></a>kinit falha

### <a name="issue"></a>Problema

Falha de kinit.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Para que o kinit seja bem sucedido, você precisa saber seu `sAMAccountName` (esse é o nome curto da conta sem o realm). `sAMAccountName` geralmente é o prefixo da conta (como Bob in `bob@contoso.com` ). Para alguns usuários, ele pode ser diferente. Você precisará da capacidade de procurar/pesquisar no diretório para aprender seu `sAMAccountName` .

Maneiras de localizar `sAMAccountName` :

* Se você puder entrar no Ambari usando o administrador do Ambari local, examine a lista de usuários.

* Se você tiver um [computador Windows ingressado no domínio](../../active-directory-domain-services/tutorial-create-management-vm.md), poderá usar as ferramentas padrão do AD do Windows para navegar. Isso requer uma conta de trabalho no domínio.

* No nó de cabeçalho, você pode usar comandos do SAMBA para pesquisar. Isso requer uma sessão Kerberos válida (kinit bem-sucedida). pesquisa do ADS net "(userPrincipalName = Bob *)"

    Os resultados da pesquisa/procura devem mostrar o `sAMAccountName` atributo. Além disso, você pode examinar outros atributos como `pwdLastSet` , `badPasswordTime` , `userPrincipalName` etc. para ver se essas propriedades correspondem ao que você espera.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit falha com falha de pré-autenticação

### <a name="issue"></a>Problema

Kinit falha com `Preauthentication` falha.

### <a name="cause"></a>Causa

Nome de usuário ou senha incorretos.

### <a name="resolution"></a>Resolução

Verifique seu nome de usuário e senha. Verifique também outras propriedades descritas acima. Para habilitar a depuração detalhada, execute `export KRB5_TRACE=/tmp/krb.log` da sessão antes de tentar kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Falha no comando Job/HDFS devido a TokenNotFoundException

### <a name="issue"></a>Problema

Falha no comando de trabalho/HDFS devido a `TokenNotFoundException` .

### <a name="cause"></a>Causa

O token de acesso OAuth necessário não foi encontrado para que o trabalho/comando tenha êxito. O driver ADLS/ABFS tentará recuperar o token de acesso OAuth do serviço de credencial antes de fazer solicitações de armazenamento. Esse token é registrado quando você entra no portal do Ambari usando o mesmo usuário.

### <a name="resolution"></a>Resolução

Verifique se você fez logon com êxito no portal do Ambari uma vez por meio do nome de usuário cuja identidade é usada para executar o trabalho.

---

## <a name="error-fetching-access-token"></a>Erro ao buscar o token de acesso

### <a name="issue"></a>Problema

O usuário recebe a mensagem de erro `Error fetching access token` .

### <a name="cause"></a>Causa

Esse erro ocorre intermitentemente quando os usuários tentam acessar o ADLS Gen2 usando ACLs e o token Kerberos expirou.

### <a name="resolution"></a>Resolução

* Para Azure Data Lake Storage Gen1, limpe o cache do navegador e faça logon no Ambari novamente.

* Por Azure Data Lake Storage Gen2, execute `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` para o usuário ao qual o usuário está tentando fazer logon

---

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]