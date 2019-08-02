---
title: Usar servidores NPS existentes para fornecer recursos de MFA do Azure - Active Directory do Azure
description: Adicionar funcionalidades de verificação de duas etapas baseada em nuvem à infraestrutura de autenticação existente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6f79b5febdbf12c80ab85d07117bf937babef0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798203"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrar sua infraestrutura do NPS existente à Autenticação Multifator do Azure

A extensão do Servidor de Políticas de Rede (NPS) para o Azure MFA adiciona recursos MFA baseados em nuvem à sua infraestrutura de autenticação usando os seus servidores existentes. Com a extensão do NPS, você pode adicionar verificação por chamada telefônica, mensagem de texto ou aplicativo ao fluxo de autenticação existente sem a necessidade de instalar, configurar e manter novos servidores. 

Esta extensão foi criada para organizações que desejam proteger conexões VPN sem implantar o Servidor do Azure MFA. A extensão NPS atua como um adaptador entre RADIUS e Azure MFA baseado em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.

Ao usar a extensão do NPS para o Azure MFA, o fluxo de autenticação inclui os seguintes componentes: 

1. **Servidor VPN/NAS** recebe solicitações de clientes VPN e converte-os em solicitações RADIUS para servidores NPS. 
2. **Servidor NPS** conecta-se ao Active Directory para executar a autenticação primária das solicitações RADIUS e, ao obter êxito, passa a solicitação para quaisquer extensões instaladas.  
3. **Extensão do NPS** dispara uma solicitação ao Azure MFA para a autenticação secundária. Quando a extensão receber a resposta, e se o desafio de MFA for bem-sucedido, ela concluirá a solicitação de autenticação, fornecendo ao servidor NPS os tokens de segurança que incluem uma declaração MFA, emitida pelo STS do Azure.  
4. O **Azure MFA** comunica-se com o Azure Active Directory para recuperar os detalhes do usuário e executa a autenticação secundária usando um método de verificação configurado para o usuário.

O diagrama a seguir ilustra esse fluxo de solicitação de autenticação de alto nível: 

![Diagrama de fluxo de autenticação](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planejar sua implantação

A extensão NPS controla automaticamente a redundância, de maneira que você não precisa de uma configuração especial.

Você pode criar quantos servidores NPS habilitados para o Azure MFA conforme necessário. Se você instalar vários servidores, use um certificado de cliente diferente para cada um deles. Ao criar um certificado para cada servidor, você pode atualizar cada certificado individualmente e não se preocupar com tempo de inatividade em todos eles.

Os servidores VPN encaminham as solicitações de autenticação, portanto precisam estar cientes dos novos servidores NPS habilitados para a MFA do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A extensão do NPS deve trabalhar com sua infraestrutura existente. Verifique se você cumpre os seguintes pré-requisitos antes de iniciar.

### <a name="licenses"></a>Licenças

A extensão NPS para Azure MFA está disponível para clientes com [licenças para Autenticação Multifator do Microsoft Azure](multi-factor-authentication.md) (incluído com Azure AD Premium, EMS ou uma licença autônoma de MFA). Licenças baseadas em consumo para a MFA do Azure como licenças por usuário ou por autenticação não são compatíveis com a extensão do NPS. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 ou superior.

### <a name="libraries"></a>Bibliotecas

Essas bibliotecas são instaladas automaticamente com a extensão.

- [Pacotes redistribuíveis do Visual C++ para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Módulo Microsoft Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

O Módulo Microsoft Azure Active Directory para Windows PowerShell é instalado, se ainda não estiver presente, por meio de um script de configuração que é executado como parte do processo de instalação. Não é necessário instalar este módulo antecipadamente, se ele ainda não estiver instalado.

### <a name="azure-active-directory"></a>Azure Active Directory

Todos que usam a extensão do NPS devem estar sincronizados com o Azure Active Directory usando o Azure AD Connect e devem estar registrados para MFA.

Ao instalar a extensão, você precisa das credenciais de administrador e a ID de diretório para seu locatário do Azure AD. Você pode encontrar a ID de diretório no [Portal do Azure](https://portal.azure.com). Entre como administrador, selecione o ícone **Azure Active Directory** à esquerda e selecione **Propriedades**. Copie o GUID na caixa **ID do diretório** e salve-o. Você usará esse GUID como a ID de locatário ao instalar a extensão NPS.

![Localize sua ID de diretório em Propriedades do Azure Active Directory](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>Requisitos de rede

O servidor NPS precisa ser capaz de se comunicar com as seguintes URLs por portas 80 e 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Além disso, a conectividade com as URLs a seguir é necessário para concluir o [a instalação do adaptador usando o script do PowerShell fornecido](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

Antes de instalar a extensão NPS, convém preparar o ambiente para manipular o tráfego de autenticação.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Habilitar a função NPS em um servidor ingressado no domínio

O servidor NPS se conecta ao Azure Active Directory e autentica as solicitações da MFA. Escolha um servidor para essa função. É recomendável escolher um servidor que não manipule solicitações de outros serviços, porque a extensão NPS gerará erros para solicitações que não sejam RADIUS. O servidor NPS deve ser configurado como o servidor de autenticação primário e secundário para o seu ambiente. Ele não pode intermediar solicitações RADIUS para outro servidor.

1. No servidor, abra o **Assistente de Adição de Funções e Recursos** no menu de Início rápido do Gerenciador do Servidor.
2. Escolha **Instalação baseada em função ou recurso** para o tipo de instalação.
3. Selecione a função de servidor **Serviços de Acesso e Política de Rede**. Uma janela poderá ser exibida para informá-lo dos recursos necessários para executar essa função.
4. Siga as etapas do assistente até a página de Confirmação. Selecione **Instalar**.

Agora que você tem um servidor designado para o NPS, você também deve configurar esse servidor para manipular as solicitações RADIUS de entrada da solução VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurar a solução VPN para se comunicar com o servidor NPS

Dependendo da solução VPN que você usa, as etapas para configurar a política de autenticação RADIUS variam. Configure essa política para apontar para o servidor NPS de RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizar usuários de domínio com a nuvem

Esta etapa pode já estar concluída no seu locatário, mas é bom verificar se Azure AD Connect sincronizou recentemente os seus bancos de dados.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **Azure Active Directory** > **Azure AD Connect**
3. Verifique se o status de sincronização está **Habilitado** e se a última sincronização foi há menos de uma hora.

Caso precise iniciar uma nova rodada de sincronização, use as instruções em [Sincronização do Azure AD Connect: Agendador](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinar quais métodos de autenticação os usuários podem usar

Há dois fatores que afetam quais métodos de autenticação estão disponíveis com uma implantação de extensão do NPS:

1. O algoritmo de criptografia de senha usado entre o cliente RADIUS (VPN, servidor Netscaler ou outros) e os servidores NPS.
   - O **PAP** dá suporte a todos os métodos de autenticação do Azure MFA na nuvem: chamada telefônica, mensagem de texto unidirecional, notificação de aplicativo móvel e código de verificação de aplicativo móvel.
   - **CHAPV2** e **EAP** dão suporte a chamada telefônica e notificação de aplicativo móvel.

      > [!NOTE]
      > Quando você implanta a extensão do NPS, use esses fatores para avaliar quais métodos estão disponíveis para os usuários. Se o cliente RADIUS dá suporte a PAP, mas a experiência do cliente não tem campos de entrada para um código de verificação, chamada telefônica e notificação do aplicativo móvel são as duas opções com suporte.
      >
      > Além disso, se seu cliente VPN que UX oferecem suporte à entrada arquivada e você tiver configurado a política de acesso de rede - a autenticação, talvez seja bem-sucedida, no entanto, nenhum dos atributos RADIUS configurados na política de rede serão aplicadas nem no dispositivo de acesso de rede, como o servidor RRAS, nem o cliente VPN. Como resultado, o cliente VPN pode ter mais acesso do que o desejado ou menos como sem acesso.
      >

2. Os métodos de entrada que o aplicativo cliente (VPN, servidor Netscaler ou outros) pode manipular. Por exemplo, o cliente VPN tem algum meio de permitir que o usuário digite um código de verificação de um texto ou aplicativo móvel?

Você pode [desabilitar métodos de autenticação sem suporte](howto-mfa-mfasettings.md#verification-methods) no Azure.

### <a name="register-users-for-mfa"></a>Registrar usuários para a MFA

Antes de implantar e usar a extensão do NPS, os usuários que são necessários para executar a verificação em duas etapas precisam ser registrados para MFA. Mas antes disso, a fim de testar a extensão ao implantá-la, você precisa de pelo menos uma conta de teste que seja totalmente registrada para a Autenticação Multifator.

Use estas etapas para iniciar uma conta de teste:

1. Entre em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) com uma conta de teste.
2. Siga os prompts para configurar um método de verificação.
3. [Criar uma política de acesso condicional](howto-mfa-getstarted.md#create-conditional-access-policy) para exigir a autenticação multifator para a conta de teste.

## <a name="install-the-nps-extension"></a>Instalar a extensão NPS

> [!IMPORTANT]
> Instale a extensão do NPS em um servidor diferente do ponto de acesso VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Baixar e instalar a extensão NPS para a MFA do Azure

1. [Baixe a extensão NPS](https://aka.ms/npsmfa) do Centro de Download da Microsoft.
2. Copie o binário para o Servidor de Políticas de Rede que você deseja configurar.
3. Execute o arquivo *setup.exe* e siga as instruções de instalação. Se você encontrar erros, verifique se as duas bibliotecas, da seção de pré-requisitos, foram instaladas com êxito.

#### <a name="upgrade-the-nps-extension"></a>Atualizar a extensão do NPS

Quando instala o upgrade de uma extensão do NPS existente, para evitar uma reinicialização do servidor subjacente conclua as seguintes etapas:

1. Desinstale a versão existente
1. Execute o novo instalador
1. Reinicie o serviço do servidor de diretiva de rede (IAS)

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

O instalador cria um script do PowerShell neste local: `C:\Program Files\Microsoft\AzureMfa\Config` (em que C:\ é a sua unidade de instalação). O script do PowerShell executa as ações a seguir a cada vez que é executado:

- Crie um certificado autoassinado.
- Associa a chave pública do certificado à entidade de serviço no Azure AD.
- Armazena o certificado no repositório de certificados do computador local.
- Concede acesso à chave privada do certificado ao usuário de rede.
- Reinicia o NPS.

A menos que você deseje usar seus próprios certificados (em vez dos certificados autoassinados gerados pelo script do PowerShell), execute o Script do PowerShell para concluir a instalação. Se você instalar a extensão em vários servidores, cada um deverá ter seu próprio certificado.

1. Execute o Windows PowerShell como um administrador.
2. Altere os diretórios.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Execute o script do PowerShell criado pelo instalador.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Entre no Azure AD como administrador.
5. O PowerShell solicitará a sua ID de locatário. Use o GUID da ID de diretório que você copiou do Portal do Azure na seção de pré-requisitos.
6. O PowerShell mostra uma mensagem de êxito quando o script é concluído.  

Repita essas etapas em quaisquer servidores NPS adicionais em que você deseja configurar o balanceamento de carga.

Se seu certificado de computador anterior expirou e foi gerado um novo certificado, você deve excluir todos os certificados expirados. Ter certificados expirados podem causar problemas com a extensão do NPS iniciando.

> [!NOTE]
> Se você usar seus próprios certificados em vez de gerar certificados com o script do PowerShell, certifique-se de que eles estejam alinhados com a convenção de nomenclatura do NPS. O nome da entidade deve ser **CN=\<TenantID\>,OU=Microsoft NPS Extension**. 

### <a name="certificate-rollover"></a>Sobreposição de certificado

Com versão 1.0.1.32 da extensão do NPS, vários certificados de leitura agora tem suporte. Esse recurso ajudará a facilitar a atualizações sem interrupção de certificado antes da sua expiração. Se sua organização estiver executando uma versão anterior da extensão do NPS, você deve atualizar para a versão 1.0.1.32 ou superior.

Os certificados criados pelo `AzureMfaNpsExtnConfigSetup.ps1` script são válidos por 2 anos. As organizações de TI devem monitorar os certificados para expiração. Os certificados para a extensão NPS são colocados no repositório de certificados do computador Local em pessoal e são emitidos para a ID de locatário fornecido para o script.

Quando um certificado está se aproximando da data de expiração, um novo certificado deve ser criado para substituí-lo.  Esse processo é realizado usando o `AzureMfaNpsExtnConfigSetup.ps1` novamente e mantendo a mesma ID de locatário quando solicitado. Esse processo deve ser repetido em cada servidor NPS em seu ambiente.

## <a name="configure-your-nps-extension"></a>Configurar sua extensão do NPS

Esta seção inclui considerações sobre o design e sugestões para implantações bem-sucedidas da extensão do NPS.

### <a name="configuration-limitations"></a>Limitações de configuração

- A extensão NPS para a MFA do Azure não inclui ferramentas para migrar usuários e configurações do Servidor MFA para a nuvem. Por esse motivo, é aconselhável usar a extensão para novas implantações, em vez da implantação existente. Se você usar a extensão em uma implantação existente, os usuários terão que executar a verificação novamente para preencher os detalhes de MFA na nuvem.  
- A extensão do NPS usa o UPN do Active Directory local para identificar o usuário no Azure MFA para realizar a autenticação secundária. A extensão pode ser configurada para usar um identificador diferente, como a ID de logon alternativa ou um campo personalizado do Active Directory que não seja o UPN. Consulte [Opções de configuração avançadas da extensão NPS para a Autenticação Multifator](howto-mfa-nps-extension-advanced.md) para obter mais informações.
- Nem todos os protocolos de criptografia dão suporte a todos os métodos de verificação.
   - O **PAP** dá suporte a chamadas telefônicas, mensagens de texto unidirecionais, notificações de aplicativo móvel e códigos de verificação de aplicativo móvel
   - **CHAPV2** e **EAP** dão suporte a chamada telefônica e notificação do aplicativo móvel

### <a name="control-radius-clients-that-require-mfa"></a>Clientes RADIUS de controle que exigem MFA

Depois que você habilita a MFA para um cliente RADIUS usando a extensão do NPS, todas as autenticações desse cliente são necessárias para executar a MFA. Se você deseja habilitar a MFA para alguns clientes RADIUS, mas outros não, você pode configurar dois servidores NPS e instalar a extensão em apenas um deles. Configure clientes RADIUS para os quais você deseja que a MFA envie solicitações ao servidor NPS configurado com a extensão e outros clientes RADIUS para o servidor NPS não configurado com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparar usuários que não são registrados na MFA

Se você tiver usuários que não são registrados na MFA, determine o que acontece quando eles tentam fazer a autenticação. Use a configuração de Registro *REQUIRE_USER_MATCH* no caminho do Registro *HKLM\Software\Microsoft\AzureMFA* para controlar o comportamento do recurso. Essa configuração tem uma opção de configuração única:

| Chave | Valor | Padrão |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Não definido (equivalente a TRUE) |

A finalidade dessa configuração é determinar o que fazer quando um usuário não estiver inscrito na MFA. Quando a chave não existir, não estiver definida ou estiver definida como TRUE e o usuário não estiver registrado, a extensão falha no desafio da MFA. Quando a chave estiver definida como FALSE e o usuário não estiver registrado, a autenticação continuará sem executar a MFA. Se um usuário estiver inscrito na MFA, ele deverá autenticar-se com a MFA, mesmo se REQUIRE_USER_MATCH estiver configurado como FALSE.

Você pode optar por criar essa chave e defini-la como FALSE, e os usuários estão carregando e ainda não podem se inscrever no Azure MFA. Porém, como definir a chave permite que os usuários que não são registrados na MFA se conectem, você deve remover essa chave antes de ir para a produção.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="nps-extension-health-check-script"></a>Script de verificação de integridade de extensão do NPS

O script a seguir está disponível na Galeria do TechNet para executar as etapas de verificação de integridade básicas ao solucionar problemas de extensão do NPS.

[MFA_NPS_Troubleshooter.ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como verificar se o certificado do cliente está instalado conforme o esperado?

Procure o certificado autoassinado criado pelo instalador no repositório de certificados e verifique se a chave privada tem permissões concedidas ao usuário **NETWORK SERVICE**. O certificado tem um nome de entidade igual a **CN \<tenantid\>, OU = extensão NPS da Microsoft**

Os certificados autoassinados gerados pelo *AzureMfaNpsExtnConfigSetup.ps1* script também tem um tempo de vida de validade de dois anos. Ao verificar se o certificado está instalado, você também deve verificar se o certificado não expirou.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Como verificar se o certificado do cliente está associado ao meu locatário no Azure Active Directory?

Abra um prompt de comando no PowerShell e execute os seguintes comandos:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Esses comandos imprimem todos os certificados associados ao seu locatário com a instância da extensão do NPS em sua sessão do PowerShell. Procure seu certificado exportando o certificado do cliente como um arquivo "codificado em Base 64 X.509(.cer)" sem a chave particular e compare-a com a lista do PowerShell.

O comando a seguir criará um arquivo chamado "npscertificate" em sua unidade "C:" no formato .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Depois de executar esse comando, vá para a unidade C, localize o arquivo e clique duas vezes nele. Vá para detalhes e role para baixo até "impressão digital", compare a impressão digital do certificado instalado no servidor com este. As impressões digitais do certificado devem corresponder.

Os carimbos de data/hora Válido-de e Válido-até, que estão em formato legível, poderão ser usados para filtrar desvios óbvios se o comando retornar mais de um certificado.

---

### <a name="why-cant-i-sign-in"></a>Por que não consigo entrar?

Verifique se sua senha não expirou. A extensão NPS não oferece suporte à alteração de senhas como parte do fluxo de trabalho de entrada. Para obter mais assistência, entre em contato com a equipe de TI da sua organização.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Por que minhas solicitações estão falhando, com erro de token ADAL?

Esse erro pode ser causado por vários motivos. Use estas etapas para solucionar o problema:

1. Reinicie o servidor NPS.
2. Verifique se o certificado do cliente está instalado conforme o esperado.
3. Verifique se o certificado está associado ao seu locatário no Azure AD.
4. Verifique se https://login.microsoftonline.com/ pode ser acessado no servidor que está executando a extensão.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Por que a autenticação falha, com um erro nos logs de HTTP, informando que o usuário não foi encontrado?

Verifique se o AD Connect está em execução e se o usuário está presente no Windows Active Directory e no Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que vejo erros de conexão HTTP nos logs, com todas as minhas autenticações falhando?

Verifique se https://adnotifications.windowsazure.com pode ser alcançado no servidor que está executando a extensão NPS.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Por que a autenticação não estiver funcionando, apesar de um certificado válido presente?

Se seu certificado de computador anterior expirou e foi gerado um novo certificado, você deve excluir todos os certificados expirados. Ter certificados expirados podem causar problemas com a extensão do NPS iniciando.

Para verificar se você tiver um certificado válido, verifique o Store do certificado da conta do computador local usando o MMC e verifique se que o certificado não tiver passado para a sua data de expiração. Para gerar um certificado válido recentemente, execute novamente as etapas na seção "[execute o script do PowerShell](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Como gerenciar protocolos TLS/SSL e conjuntos de codificação

É recomendável que os conjuntos de codificação mais antigos ou mais fracos sejam desabilitados ou removidos, a menos que eles sejam exigidos por sua organização. Você pode obter informações sobre como concluir esta tarefa no artigo [Gerenciar protocolos SSL/TLS e conjuntos de codificação para o AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Solução de problemas adicionais

Soluções orientação e possíveis para solução de problemas adicionais podem ser encontradas no artigo [resolver mensagens de erro da extensão NPS para autenticação multifator do Azure](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Próximas etapas

- Configurar IDs alternativos para logon ou configurar uma lista de exceções para IPs que não devem executar a verificação em duas etapas nas [Opções de configuração avançadas para a extensão do NPS para autenticação multifator](howto-mfa-nps-extension-advanced.md)

- Saiba como integrar o [Gateway de Área de Trabalho Remota](howto-mfa-nps-extension-rdg.md) e os [servidores VPN](howto-mfa-nps-extension-vpn.md) usando a extensão NPS

- [Resolver mensagens de erro da extensão NPS da Autenticação Multifator do Azure](howto-mfa-nps-extension-errors.md)
