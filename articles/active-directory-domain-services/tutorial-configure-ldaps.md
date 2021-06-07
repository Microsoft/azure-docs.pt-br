---
title: Tutorial – Configurar o LDAPS para o Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar o LDAPS (protocolo LDAP Seguro) para um domínio gerenciado do Azure Active Directory Domain Services.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: justinha
ms.openlocfilehash: 928b1a6dcff7ad186bf5fe9ce07d1a886d429867
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933331"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Configurar o LDAP Seguro para um domínio gerenciado do Azure Active Directory Domain Services

Para se comunicar com o domínio gerenciado do Azure AD DS (Azure Active Directory Domain Services), o protocolo LDAP é usado. Por padrão, o tráfego do LDAP não é criptografado, o que é uma preocupação de segurança para muitos ambientes.

Com o Azure AD DS, você pode configurar o domínio gerenciado para usar o LDAPS (protocolo LDAP Seguro). Quando você usa o LDAP Seguro, o tráfego é criptografado. O LDAP Seguro também é conhecido como LDAP sobre protocolo SSL/TLS.

Este tutorial mostra como configurar o LDAPS para um domínio gerenciado do Azure AD DS.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um certificado digital para uso com o Azure AD DS
> * Habilitar o LDAP Seguro para o Azure AD DS
> * Configurar o LDAP Seguro para uso na Internet pública
> * Associar e testar o LDAP seguro para um domínio gerenciado

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* A ferramenta *LDP.exe* instalada no computador.
    * Se necessário, [instale as Ferramentas de Administração de Servidor Remoto][rsat] para o *Active Directory Domain Services e o LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você vai configurar o LDAP Seguro para o domínio gerenciado usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Criar um certificado para o LDAP Seguro

Para usar o LDAP Seguro, um certificado digital é usado para criptografar a comunicação. Esse certificado digital é aplicado ao domínio gerenciado e permite que ferramentas como *LDP.exe* usem a comunicação criptografada segura ao consultar dados. Há duas maneiras de criar um certificado para acesso do LDAP Seguro ao domínio gerenciado:

* Um certificado de uma AC (autoridade de certificação) pública ou uma AC corporativa.
    * Caso sua organização obtenha certificados de uma AC pública, obtenha o certificado LDAP Seguro dessa AC pública. Se você usar uma AC corporativa em sua organização, obtenha o certificado LDAP Seguro da AC corporativa.
    * Uma AC pública só funciona quando você usa um nome DNS personalizado com o domínio gerenciado. Se o nome de domínio DNS do domínio gerenciado terminar em *.onmicrosoft.com*, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft é proprietária do domínio *.onmicrosoft.com*, portanto, uma AC pública não emitirá um certificado. Nesse cenário, crie um certificado autoassinado e use-o para configurar o LDAP seguro.
* Um certificado autoassinado que você cria por conta própria.
    * Essa abordagem é adequada para fins de teste e isso é o que este tutorial mostra.

O certificado solicitado ou criado precisa atender aos requisitos a seguir. O domínio gerenciado encontrará problemas se você habilitar o LDAP Seguro com um certificado inválido:

* **Emissor confiável** – o certificado deve ser emitido por uma autoridade confiável para os computadores que se conectarem ao domínio gerenciado usando LDAP seguro. Essa autoridade pode ser uma AC pública ou uma AC corporativa confiável nesses computadores.
* **Tempo de vida** : o certificado deve ser válido por, pelo menos, os próximos três a seis meses. O acesso LDAP seguro para seu domínio gerenciado é interrompido quando o certificado expira.
* **Nome da entidade**: o nome da entidade no certificado deve ser seu domínio gerenciado. Por exemplo, se o domínio for chamado *aaddscontoso.com*, o nome da entidade do certificado precisará ser * *.aaddscontoso.com*.
    * O nome DNS ou o nome alternativo da entidade do certificado precisa ser um certificado curinga para garantir que o LDAP Seguro funcione corretamente com o Azure AD Domain Services. Os controladores de domínio usam nomes aleatórios e podem ser removidos ou adicionados para garantir que o serviço permaneça disponível.
* **Uso de chave** – o certificado precisa ser configurado para *assinaturas digitais* e *codificação de chave*.
* **Finalidade do certificado**: o certificado deve ser válido para autenticação de servidor TLS.

Há várias ferramentas disponíveis para criar um certificado autoassinado, como OpenSSL, Keytool, MakeCert, o cmdlet [New-SelfSignedCertificate][New-SelfSignedCertificate] etc.

Neste tutorial, vamos criar um certificado autoassinado para LDAP seguro usando o cmdlet [New-SelfSignedCertificate][New-SelfSignedCertificate].

Abra uma janela do PowerShell como **Administrador** e execute os comandos a seguir. Substitua a variável *$dnsName* pelo nome DNS usado pelo próprio domínio gerenciado, como *aaddscontoso.com*:

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

A seguinte saída de exemplo mostra que o certificado foi gerado com êxito e armazenado no repositório de certificados local (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Entender e exportar os certificados necessários

Para usar o LDAP Seguro, o tráfego de rede é criptografado usando a PKI (infraestrutura de chave pública).

* Uma chave **privada** é aplicada ao domínio gerenciado.
    * Essa chave privada é usada para *descriptografar* o tráfego do LDAP Seguro. A chave privada só deve ser aplicada ao domínio gerenciado, não devendo ser amplamente distribuída aos computadores cliente.
    * Um certificado que inclui a chave privada usa o formato de arquivo *.PFX*.
    * Ao exportar o certificado, você deverá especificar o algoritmo de criptografia *TripleDES-SHA1*. Isso é aplicável somente ao arquivo .pfx e não impacta o algoritmo usado pelo próprio certificado. Observe que a opção *TripleDES-SHA1* está disponível somente a partir do Windows Server 2016.
* Uma chave **pública** é aplicada aos computadores cliente.
    * Essa chave pública é usada para *criptografar* o tráfego LDAP Seguro. A chave pública pode ser distribuída aos computadores cliente.
    * Os certificados sem a chave privada usam o formato de arquivo *.CER*.

Para essas duas chaves, as chaves *privada* e *pública*, garanta que apenas os computadores apropriados possam se comunicar com êxito entre si. Se você usar uma AC pública ou uma AC corporativa, será emitido um certificado para você que inclui a chave privada e pode ser aplicado a um domínio gerenciado. A chave pública já deve ser conhecida e confiável nos computadores cliente.

Neste tutorial, você criou um certificado autoassinado com a chave privada, portanto, é necessário exportar os componentes públicos e privados apropriados.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exportar um certificado para o Azure AD DS

Para usar o certificado digital criado na etapa anterior com o domínio gerenciado, exporte o certificado para um arquivo de certificado *.PFX* que inclua a chave privada.

1. Para abrir a caixa de diálogo *Executar*, selecione as teclas **Windows** + **R**.
1. Abra o MMC (Console de Gerenciamento Microsoft) inserindo **mmc** na caixa de diálogo *Executar* e, em seguida, selecione **OK**.
1. No aviso **Controle de Conta de Usuário**, escolha **Sim** para iniciar o MMC como administrador.
1. No menu **Arquivo**, selecione **Adicionar/Remover Snap-in...**
1. No assistente **Snap-in de certificados**, escolha **Conta de computador** e, em seguida, selecione **Avançar**.
1. Na página **Selecionar Computador**, escolha **Computador local: (o computador no qual este console está sendo executado)** e, em seguida, selecione **Concluir**.
1. Na caixa de diálogo **Adicionar ou Remover Snap-ins**, escolha **OK** para adicionar o snap-in de certificados ao MMC.
1. Na janela do MMC, expanda **Raiz do Console**. Selecione **certificados (Computador Local)** e, em seguida, expanda o nó **Pessoal**, seguido pelo nó **Certificados**.

    ![Abrir o repositório de certificados pessoais no Console de Gerenciamento Microsoft](./media/tutorial-configure-ldaps/open-personal-store.png)

1. O certificado autoassinado criado na etapa anterior é mostrado, como *addscontoso.com*. Selecione o certificado com o botão direito do mouse e, em seguida, escolha **Todas as Tarefas > Exportar...**

    ![Exportar certificado no Console de Gerenciamento Microsoft](./media/tutorial-configure-ldaps/export-cert.png)

1. No **Assistente para Exportação de Certificados**, selecione **Avançar**.
1. A chave privada do certificado precisa ser exportada. Se a chave privada não estiver incluída no certificado exportado, a ação para habilitar o LDAP Seguro do domínio gerenciado falhará.

    Na página **Exportar Chave Privada**, escolha **Sim, exportar a chave privada** e, em seguida, selecione **Avançar**.
1. Os domínios gerenciados só dão suporte ao formato de arquivo de certificado *.PFX* que inclui a chave privada. Não exporte o certificado como o formato de arquivo de certificado *.CER* sem a chave privada.

    Na página **Exportar Formato de Arquivo**, escolha **Personal Information Exchange – PKCS #12 (.PFX)** como o formato de arquivo para o certificado exportado. Marque a caixa *Incluir todos os certificados no caminho de certificação, se possível*:

    ![Escolha a opção para exportar o certificado no formato de arquivo PKCS 12 (.PFX)](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Como esse certificado é usado para descriptografar dados, você deve controlar cuidadosamente o acesso. Uma senha pode ser usada para proteger o uso do certificado. Sem a senha correta, o certificado não pode ser aplicado a um serviço.

    Na página **Segurança**, escolha a opção de **Senha** para proteger o arquivo de certificado *.PFX*. O algoritmo de criptografia precisa ser *TripleDES-SHA1*. Insira e confirme uma senha e, em seguida, selecione **Avançar**. Essa senha será usada na próxima seção para habilitar o LDAP Seguro para o domínio gerenciado.

    Se você exportar usando o [cmdlet export-pfxcertificate do PowerShell](/powershell/module/pkiclient/export-pfxcertificate), precisará transmitir o sinalizador *-CryptoAlgorithmOption* usando TripleDES_SHA1.

    ![Captura de tela sobre como criptografar a senha](./media/tutorial-configure-ldaps/encrypt.png)

1. Na página **Arquivo a ser Exportado**, especifique o nome do arquivo e a localização em que deseja exportar o certificado, como *C:\Users\accountname\azure-ad-ds.pfx*. Anote a senha e o local do arquivo *.PFX*, pois essas informações serão necessárias nas próximas etapas.
1. Na página de revisão, selecione **Concluir** para exportar o certificado para um arquivo de certificado *.PFX*. Uma caixa de diálogo de confirmação é exibida quando o certificado é exportado com êxito.
1. Mantenha o MMC aberto para uso na seção a seguir.

### <a name="export-a-certificate-for-client-computers"></a>Exportar um certificado para computadores cliente

Os computadores cliente precisam confiar no emissor do certificado LDAP Seguro para poderem se conectar com êxito ao domínio gerenciado usando o LDAPS. Os computadores cliente precisam ter um certificado para criptografar com êxito os dados descriptografados pelo Azure AD DS. Se você usar uma AC pública, o computador deverá confiar automaticamente nesses emissores do certificado e ter um certificado correspondente.

Neste tutorial, você usou um certificado autoassinado e gerou um certificado que inclui a chave privada na etapa anterior. Agora, vamos exportar e, em seguida, instalar o certificado autoassinado no repositório de certificados confiáveis no computador cliente:

1. Volte ao MMC para o repositório *Certificados (Computador Local) > Pessoal > Certificados*. O certificado autoassinado criado em uma etapa anterior é mostrado, como *addscontoso.com*. Selecione o certificado com o botão direito do mouse e, em seguida, escolha **Todas as Tarefas > Exportar...**
1. No **Assistente para Exportação de Certificados**, selecione **Avançar**.
1. Como você não precisa da chave privada para clientes, na página **Exportar Chave Privada**, escolha **Não, não exportar a chave privada** e, em seguida, selecione **Avançar**.
1. Na página **Formato do Arquivo de Exportação**, selecione **X.509 codificado em Base64 (.CER)** como o formato de arquivo para o certificado exportado:

    ![Escolha a opção para exportar o certificado no formato de arquivo X.509 codificado em Base64 (.CER)](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na página **Arquivo a ser Exportado**, especifique o nome do arquivo e a localização em que deseja exportar o certificado, como *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Na página de revisão, selecione **Concluir** para exportar o certificado para um arquivo de certificado *.CER*. Uma caixa de diálogo de confirmação é exibida quando o certificado é exportado com êxito.

O arquivo de certificado *.CER* agora pode ser distribuído para os computadores cliente que precisam confiar na conexão do LDAP Seguro com o domínio gerenciado. Vamos instalar o certificado no computador local.

1. Abra o Explorador de Arquivos e procure a localização em que você salvou o arquivo de certificado *.CER*, como *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Selecione o arquivo de certificado *.CER* com o botão direito do mouse e, em seguida, escolha **Instalar Certificado**.
1. No **Assistente para Importação de Certificados**, opte por armazenar o certificado no *Computador local* e, em seguida, selecione **Avançar**:

    ![Escolha a opção para importar o certificado para o repositório do computador local](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Quando solicitado, escolha **Sim** para permitir que o computador faça alterações.
1. Escolha **Selecionar automaticamente o repositório de certificados com base no tipo de certificado** e, em seguida, selecione **Avançar**.
1. Na página de revisão, selecione **Concluir** para importar o certificado *.CER*. Uma caixa de diálogo de confirmação é exibida quando o certificado é importado com êxito.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Habilitar o LDAP Seguro para o Azure AD DS

Com um certificado digital criado e exportado que inclua a chave privada e o computador cliente definido para confiar na conexão, agora habilite o LDAP Seguro no domínio gerenciado. Para habilitar o LDAP Seguro em um domínio gerenciado, execute as seguintes etapas de configuração:

1. No [portal do Azure](https://portal.azure.com), insira *serviços de domínio* na caixa **Pesquisar recursos**. Selecione **Azure AD Domain Services** nos resultados da pesquisa.
1. Escolha o domínio gerenciado, como *aaddscontoso.com*.
1. No lado esquerdo da janela do Azure AD DS, escolha **LDAP Seguro**.
1. Por padrão, o acesso LDAP seguro ao seu domínio gerenciado fica desabilitado. Posicione a tecla de alternância **LDAP Seguro** em **Habilitar**.
1. O acesso do LDAP Seguro ao domínio gerenciado na Internet está desabilitado por padrão. Quando você habilita o acesso LDAP Seguro público, o domínio fica suscetível a ataques de força bruta de senha na Internet. Na próxima etapa, um grupo de segurança de rede será configurado para bloquear o acesso somente aos intervalos de endereços IP de origem necessários.

    Alterne **Permitir acesso LDAP Seguro na Internet** como **Habilitar**.

1. Selecione o ícone de pasta ao lado do **arquivo .PFX com certificado LDAP seguro**. Procure o caminho do arquivo *.PFX* e, em seguida, selecione o certificado criado em uma etapa anterior que inclua a chave privada.

    > [!IMPORTANT]
    > Conforme observado na seção anterior sobre requisitos de certificado, não é possível usar um certificado de uma AC pública com o domínio padrão *.onmicrosoft.com*. A Microsoft é proprietária do domínio *.onmicrosoft.com*, portanto, uma AC pública não emitirá um certificado.
    >
    > Verifique se o certificado está no formato apropriado. Caso contrário, a plataforma Azure gerará erros de validação de certificado quando você habilitar o LDAP Seguro.

1. Insira a **Senha para descriptografar o arquivo .PFX** definida em uma etapa anterior quando o certificado foi exportado para um arquivo *.PFX*.
1. Selecione **Salvar** para habilitar o LDAP Seguro.

    ![Habilitar o LDAP Seguro para um domínio gerenciado no portal do Azure](./media/tutorial-configure-ldaps/enable-ldaps.png)

Uma notificação é exibida, informando que o LDAP Seguro está sendo configurado para o domínio gerenciado. Você não poderá modificar outras configurações do domínio gerenciado enquanto essa operação não for concluída.

São necessários alguns minutos para habilitar o LDAP Seguro para o domínio gerenciado. Se o certificado LDAP Seguro fornecido não corresponder aos critérios obrigatórios, a ação para habilitar o LDAP Seguro para o domínio gerenciado falhará.

Alguns motivos comuns de falha incluem um nome de domínio incorreto, o algoritmo de criptografia do certificado não é *TripleDES-SHA1* ou um certificado já expirado ou prestes a expirar. Você pode recriar o certificado com parâmetros válidos e, em seguida, habilitar o LDAP Seguro usando esse certificado atualizado.

## <a name="change-an-expiring-certificate"></a>Alterar um certificado que está expirando

1. Crie um certificado substituto para LDAP Seguro seguindo as etapas para [Criar um certificado para o LDAP Seguro](#create-a-certificate-for-secure-ldap).
1. Para aplicar o certificado substituto ao Azure AD DS, no menu à esquerda para o Azure AD DS no portal do Azure, selecione **LDAP Seguro** e escolha **Alterar Certificado**.
1. Distribua o certificado para todos os clientes que se conectam usando o LDAP Seguro. 

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Bloquear o acesso LDAP Seguro na Internet

Quando você habilita o acesso LDAP Seguro na Internet para o domínio gerenciado, isso cria uma ameaça de segurança. O domínio gerenciado pode ser acessado pela Internet na porta TCP 636. É recomendável restringir o acesso ao domínio gerenciado aos endereços IP conhecidos específicos para o ambiente. Uma regra de grupo de segurança de rede do Azure pode ser usada para limitar o acesso ao LDAP Seguro.

Vamos criar uma regra para permitir o acesso LDAP Seguro de entrada pela porta TCP 636 em um conjunto especificado de endereços IP. Uma regra *DenyAll* padrão de prioridade mais baixa se aplica a todos os outros tipo de tráfego de entrada na Internet, de modo que somente os endereços especificados possam acessar o domínio gerenciado usando o LDAP Seguro.

1. No portal do Azure, selecione *Grupos de recursos* na barra de navegação do lado esquerdo.
1. Escolha o grupo de recursos, como *myResourceGroup* e, em seguida, selecione o grupo de segurança de rede, como *aaad-nsg*.
1. A lista de regras de segurança de entrada e saída existentes é exibida. No lado esquerdo das janelas do grupo de segurança de rede, escolha **Configurações > Regras de segurança de entrada**.
1. Selecione **Adicionar** e, em seguida, crie uma regra para permitir a porta *TCP* *636*. Para maior segurança, escolha a origem como *Endereços IP* e, em seguida, especifique seu próprio intervalo ou endereço IP válido para sua organização.

    | Configuração                           | Valor        |
    |-----------------------------------|--------------|
    | Fonte                            | Endereços IP |
    | Endereços IP de origem/Intervalos de CIDR | Um intervalo ou um endereço IP válido para o ambiente |
    | Intervalos de portas de origem                | *            |
    | Destino                       | Qualquer          |
    | Intervalos de portas de destino           | 636          |
    | Protocolo                          | TCP          |
    | Ação                            | Allow        |
    | Prioridade                          | 401          |
    | Nome                              | AllowLDAPS   |

1. Quando estiver pronto, selecione **Adicionar** para salvar e aplicar a regra.

    ![Criar uma regra de grupo de segurança de rede para proteger o acesso LDAPS na Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Configurar a zona DNS para acesso externo

Com o acesso LDAP Seguro habilitado na Internet, atualize a zona DNS para que os computadores cliente possam encontrar esse domínio gerenciado. O *Endereço IP externo do LDAP Seguro* está listado na guia **Propriedades** do domínio gerenciado:

![Veja o endereço IP externo do LDAP Seguro para o domínio gerenciado no portal do Azure](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configure o provedor DNS externo para criar um registro de host, como *ldaps*, para fazer a resolução para esse endereço IP externo. Para testar localmente no computador primeiro, crie uma entrada no arquivo de hosts do Windows. Para editar com êxito o arquivo de hosts no computador local, abra o *Bloco de notas* como administrador e abra o arquivo *C:\Windows\System32\drivers\etc\hosts*

A seguinte entrada DNS de exemplo, com o provedor DNS externo ou no arquivo de hosts local, resolve o tráfego de *ldaps.aaddscontoso.com* para o endereço IP externo *168.62.205.103*:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testar consultas para o domínio gerenciado

Para se conectar e se associar ao domínio gerenciado e pesquisar no LDAP, use a ferramenta *LDP.exe*. Essa ferramenta está incluída no pacote das Ferramentas de Administração de Servidor Remoto. Para obter mais informações, confira [Instalar Ferramentas de Administração de Servidor Remoto][rsat].

1. Abra *LDP.exe* e conecte-se ao domínio gerenciado. Selecione **Conexão** e, em seguida, escolha **Conectar...** .
1. Insira o nome de domínio DNS do LDAP Seguro do domínio gerenciado criado na etapa anterior, como *ldaps.aaddscontoso.com*. Para usar o LDAP Seguro, defina **Porta** como *636* e marque a caixa de **SSL**.
1. Selecione **OK** para se conectar ao domínio gerenciado.

Em seguida, faça a associação ao domínio gerenciado. Os usuários (e as contas de serviço) não poderão executar associações LDAP simples se você desabilitar a sincronização de hash de senha NTLM em seu domínio gerenciado. Para obter mais informações sobre como desabilitar a sincronização de hash de senha NTLM, confira [Proteger o domínio gerenciado][secure-domain].

1. Selecione a opção de menu **Conexão** e, em seguida, escolha **Associar...** .
1. Forneça as credenciais de uma conta de usuário que pertença ao domínio gerenciado. Insira a senha da conta de usuário e, em seguida, o seu domínio, como *aaddscontoso.com*.
1. Em **Tipo de associação**, escolha a opção *Associar com credenciais*.
1. Selecione **OK** para fazer a associação ao domínio gerenciado.

Para ver os objetos armazenados no domínio gerenciado:

1. Selecione a opção de menu **Exibir** e, em seguida, escolha **Árvore**.
1. Deixe o campo *BaseDN* em branco e, em seguida, selecione **OK**.
1. Escolha um contêiner, como *Usuários do AADDC* e, em seguida, selecione o contêiner com o botão direito do mouse e escolha **Pesquisar**.
1. Deixe os campos pré-populados definidos e, em seguida, selecione **Executar**. Os resultados da consulta são exibidos na janela à direita, conforme mostrado na seguinte saída de exemplo:

    ![Pesquisar objetos no domínio gerenciado usando o LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Para consultar diretamente um contêiner específico, no menu **Exibir > Árvore**, especifique um **BaseDN**, como *OU=Usuários do AADDC,DC=AADDSCONTOSO,DC=COM* ou *OU=Computadores do AADDC,DC=AADDSCONTOSO,DC=COM*. Para obter mais informações sobre como formatar e criar consultas, confira [Conceitos básicos sobre consultas LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Limpar os recursos

Se você adicionou uma entrada DNS ao arquivo de hosts local do computador para testar a conectividade para este tutorial, remova essa entrada e adicione um registro formal na zona DNS. Para remover a entrada do arquivo de hosts local, conclua as seguintes etapas:

1. No computador local, abra o *Bloco de notas* como administrador
1. Procure e abra o arquivo *C:\Windows\System32\drivers\etc\hosts*
1. Exclua a linha do registro adicionado, como `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="troubleshooting"></a>Solução de problemas

Se você receber um erro informando que o LDAP.exe não pode se conectar, tente trabalhar com os diferentes aspectos de obter a conexão: 

1. Como configurar o controlador de domínio
1. Como configurar o cliente
1. Rede
1. Como estabelecer a sessão TLS

Para a correspondência do nome da entidade do certificado, o controlador de domínio usará o nome de domínio do Azure AD DS (não o nome de domínio do Azure AD) para pesquisar o certificado no repositório de certificados. Erros de ortografia, por exemplo, impedem que o controlador de domínio selecione o certificado correto. 

O cliente tenta estabelecer a conexão TLS usando o nome fornecido. O tráfego precisa chegar até o destino. O controlador de domínio envia a chave pública do certificado de autenticação do servidor. O certificado precisa ter o uso correto no certificado, o nome assinado no nome da entidade precisa ser compatível para que o cliente confie que o servidor é o nome DNS ao qual você está se conectando (ou seja, um curinga funcionará, sem erros de ortografia) e o cliente precisa confiar no emissor. Você pode verificar se há algum problema na cadeia no log do sistema em Visualizador de Eventos e filtrar os eventos em que a origem é igual a schannel. Depois que essas partes estiverem no lugar, elas formarão uma chave da sessão.  

Para obter mais informações, confira [Handshake de TLS](https://docs.microsoft.com/windows/win32/secauthn/tls-handshake-protocol).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um certificado digital para uso com o Azure AD DS
> * Habilitar o LDAP Seguro para o Azure AD DS
> * Configurar o LDAP Seguro para uso na Internet pública
> * Associar e testar o LDAP seguro para um domínio gerenciado

> [!div class="nextstepaction"]
> [Configurar sincronização de hash de senha para um ambiente do Azure AD híbrido](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate