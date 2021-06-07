---
title: Tutorial – Configurar o Azure Active Directory para emitir credenciais verificáveis (versão prévia)
description: Neste tutorial, você criará o ambiente necessário para implantar credenciais verificáveis no seu locatário
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: cd39f6c484ebe116918611bb1d543c1919a3cb0a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222938"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Tutorial – Configurar o Azure Active Directory para emitir credenciais verificáveis (versão prévia)

Neste tutorial, vamos desenvolver o trabalho feito como parte do artigo de [introdução](get-started-verifiable-credentials.md) configurar o Azure AD (Azure Active Directory) com uma [DID](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (identidade descentralizada) própria. Usaremos o identidade descentralizada para emitir uma credencial verificável com o aplicativo de exemplo e o seu emissor. No entanto, neste tutorial, ainda usaremos o locatário de exemplo do Azure B2C para autenticação.  Em nosso próximo tutorial, usaremos etapas adicionais para configurar o aplicativo de modo que ele funcione com o Azure AD.

> [!IMPORTANT]
> No momento, as Credenciais Verificáveis do Azure Active Directory estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Neste artigo:

> [!div class="checklist"]
> * Você criará os serviços necessários para integrar o Azure AD para usar credenciais verificáveis 
> * Criaremos sua DID
> * Personalizaremos os arquivos de exibição e regras
> * Você vai configurar as credenciais verificáveis no Azure AD.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial com êxito, primeiro:

- Conclua a [Introdução](get-started-verifiable-credentials.md).
- Tenha uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD com uma [licença](https://azure.microsoft.com/pricing/details/active-directory/) P2. Siga o artigo [Como criar uma conta de desenvolvedor gratuita](how-to-create-a-free-developer-account.md) caso não tenha uma.
- Uma instância do [Azure Key Vault](../../key-vault/general/overview.md) em que você tenha direitos para criar chaves e segredos.

## <a name="azure-active-directory"></a>Azure Active Directory

Para iniciarmos, precisamos ter um locatário do Azure AD. Quando seu locatário estiver habilitado para credenciais verificáveis, ele receberá uma DID (identidade descentralizada) e será habilitado com um serviço emissor para emitir credenciais verificáveis. Qualquer credencial verificável que você emite é emitida pelo seu locatário e pela DID. A DID também é usada durante a verificação das credenciais verificáveis.
Se você acabou de criar uma assinatura de teste do Azure, seu locatário não precisa ser preenchido com contas de usuário, mas precisa ter, no mínimo, uma conta de teste de usuário para concluir os tutoriais posteriores.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Ao trabalhar com credenciais verificáveis, você tem controle e gerenciamento totais das chaves de criptografia usadas pelo seu locatário para assinar digitalmente as credenciais verificadas. Para emitir e verificar as credenciais, você precisa fornecer ao Azure AD acesso à sua instância do Azure Key Vault.

1. No menu do portal do Azure ou na página **Início**, selecione **Criar um recurso**.
2. Na caixa de pesquisa, digite **Key Vault**.
3. Na lista de resultados, escolha **Key Vault**.
4. Na seção Key Vault, escolha **Criar**.
5. A seção **Criar cofre de chaves** fornece as seguintes informações:
    - **Assinatura**: Escolha uma assinatura.
    - Em **Grupo de Recursos**, escolha **Criar** e insira um nome para o grupo de recursos, como **vc-resource-group**. Estamos usando o mesmo nome de grupo de recursos em vários artigos.
    - **Name**: um nome exclusivo é necessário. Usamos **woodgroup-vc-kv**. Portanto, substitua esse valor por um nome exclusivo próprio.
    - No menu suspenso **Local**, escolha um local.
    - Deixe as outras opções em seus padrões.
6. Depois de fornecer as informações acima, selecione **Política de Acesso**

    ![página criar um cofre de chaves](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. Na tela Política de Acesso, escolha **Adicionar Política de Acesso**

    >[!NOTE]
    > Por padrão, a conta que cria o cofre de chaves é a única com acesso. O serviço de credencial verificável precisa ter acesso ao cofre de chaves. O cofre de chaves precisa ter uma política de acesso que permita ao administrador **criar chaves** e ter a capacidade de **excluí-las** caso você recuse isso, além da permissão **assinar**, para criar a associação de domínio para credenciais verificáveis. Se você estiver usando a mesma conta durante o teste, lembre-se de modificar a política padrão para conceder a permissão **assinar** à conta, além das permissões padrão concedidas aos criadores do cofre.

8. Para o administrador de usuários, verifique se a seção permissões de chave tem **Criar**, **Excluir** e **Assinar** habilitados. Por padrão, Criar e Excluir já estão habilitados e Assinar deve ser a única permissão de chave que precisa ser atualizada. 

    ![Permissões do cofre de chave](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Selecione **Examinar + criar**.
10. Selecione **Criar**.
11. Acesse o cofre e anote o nome do cofre e o URI

Anote as duas propriedades listadas abaixo:

- **Nome do Cofre**: no exemplo, o nome do valor é **woodgrove-vc-kv**. Você usará esse nome nas outras etapas.
- **URI do Cofre**: no exemplo, esse valor é https://woodgrove-vc-kv.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

>[!NOTE]
> Cada transação de cofre de chaves resulta em custos adicionais na assinatura do Azure. Examine a [página de preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) para obter mais detalhes.

>[!IMPORTANT]
> Durante a versão prévia das Credenciais Verificáveis do Azure Active Directory, as chaves e os segredos criados no cofre não devem ser modificados depois de criados. A exclusão, a desabilitação ou a atualização das chaves e dos segredos invalida as credenciais emitidas. Não modifique as chaves nem os segredos durante a versão prévia.

## <a name="create-a-modified-rules-and-display-file"></a>Criar um arquivo de exibição e regras modificado

Nesta seção, usaremos os arquivos de regras e de exibição do aplicativo emissor de exemplo e os modificaremos de maneira ligeiramente para criar a primeira credencial verificável do seu locatário.

1. Copie as regras e exiba os arquivos JSON em uma pasta temporária e renomeie-os **MyFirstVC-display.json** e **MyFirstVC-rules.json**, respectivamente. Encontre esses dois arquivos em **issuer\issuer_config**

   ![arquivos de exibição e de regras como parte do diretório de aplicativo de exemplo](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![arquivos de exibição e de regras em uma pasta temporária](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Abra o arquivo MyFirstVC-rules.json no editor de código. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

Agora, vamos alterar o campo de tipo para "MyFirstVC". 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Salve essa alteração.

 >[!NOTE]
   > Não estamos alterando a **"configuration"** nem a **"client_id"** neste ponto do tutorial. Ainda podemos usar o locatário do Microsoft B2C que usamos na [Introdução](get-started-verifiable-credentials.md). Usaremos seu Azure AD no próximo tutorial.

3. Abra o arquivo MyFirstVC-display.json no editor de código.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Vamos fazer algumas modificações para que essa credencial verificável pareça visivelmente diferente da versão do código de exemplo. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Salve essas alterações.
## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Antes de criar nossa primeira credencial verificável, precisamos criar um contêiner do Armazenamento de Blobs que possa conter os arquivos de configuração e regras.

1. Crie uma conta de armazenamento usando as opções mostradas abaixo. Para obter etapas detalhadas, examine o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal).

   - **Assinatura:** escolha a assinatura que estamos usando para estes tutoriais.
   - **Grupo de recursos:** escolha o mesmo grupo de recursos que usamos nos tutoriais anteriores (**vc-resource-group**).
   - **Nome:** um nome exclusivo.
   - **Localização:** (EUA) Leste dos EUA.
   - **Desempenho:** Standard.
   - **Tipo de conta:** Armazenamento V2.
   - **Replicação:** com redundância local.
 
   ![Criar uma nova conta de armazenamento](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Depois de criar a conta de armazenamento, precisamos criar um contêiner. Selecione **Contêineres** em **Armazenamento de Blobs** e crie um contêiner usando os valores fornecidos abaixo:

    - **Nome:** vc-container
    - **Nível de acesso público:** Particular (sem acesso anônimo)

   ![Criar um contêiner](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Agora, selecione o novo contêiner e carregue os novos arquivos de exibição e de regras **MyFirstVC-display.json** e **MyFirstVC-rules.json** que criamos anteriormente.

   ![carregar arquivo de regras](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Atribuir uma função de blob

Antes de criar a credencial, primeiro, precisamos dar ao usuário conectado a atribuição de função correta para que ele possa acessar os arquivos no Blob de Armazenamento.

1. Procure **Armazenamento** > **Contêiner**.
2. Escolha **Controle de acesso (IAM)** no menu à esquerda.
3. Escolha **Atribuições de Função**.
4. Selecione **Adicionar**.
5. Na seção **Função**, escolha **Leitor de Dados do Blob de Armazenamento**.
6. Em **Atribuir acesso a**, escolha **Usuário, grupo ou entidade de serviço**.
7. Em **Selecionar**, escolha a conta que você está usando para executar essas etapas.
8. Selecione **Salvar** para concluir a atribuição de função.


   ![Adicionar uma atribuição de função](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Por padrão, os criadores de contêiner recebem a função **Proprietário**. A função **Proprietário** não é suficiente por si só. Sua conta precisa da função **Leitor de Dados do Blob de Armazenamento**. Para obter mais informações, examine [Usar o portal do Azure para atribuir uma função do Azure para acesso aos dados de blob e de fila](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Configurar credenciais verificáveis (versão prévia)

Agora, precisamos executar a última etapa para configurar seu locatário para credenciais verificáveis.

1. No portal do Azure, pesquise **credenciais verificáveis**.
2. Escolha **Credenciais Verificáveis (Versão prévia)** .
3. Escolha **Introdução**
4. Precisamos configurar sua organização e fornecer o nome da organização, o domínio e o cofre de chaves. Vamos examinar cada um dos valores.

      - **nome da organização**: esse nome é como você referencia sua empresa no serviço de Credencial Verificável. Esse valor não é voltado ao cliente.
      - **Domínio:** o domínio inserido é adicionado a um ponto de extremidade de serviço no seu documento de DID. O [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) e outras carteiras usam essas informações para validar que a sua DID está [vinculada ao seu domínio](how-to-dnsbind.md). Se a carteira puder verificar a DID, ela exibirá um símbolo de verificado. Caso contrário, ela informará ao usuário que a credencial foi emitida por uma organização que não pôde ser validada. O domínio é o que associa sua DID a algo tangível que o usuário pode saber sobre a sua empresa.
      - **Cofre de chaves:** forneça o nome do Key Vault que criamos anteriormente.
 
   >[!IMPORTANT]
   > O domínio não pode ser um redirecionamento, caso contrário, a DID e o domínio não poderão ser vinculados. Lembre-se de usar formato https://www.domain.com.
    
5. Escolha **Salvar e criar credencial**

    ![configurar sua identidade organizacional](media/enable-your-tenant-verifiable-credentials/save-create.png)

Parabéns, seu locatário agora está habilitado para a versão prévia da Credencial Verificável!

## <a name="create-your-vc-in-the-portal"></a>Criar a VC no portal

A etapa anterior deixa você na página **Criar uma credencial**. 

   ![introdução às credenciais verificáveis](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Em Nome da Credencial, adicione o nome **MyFirstVC**. Esse nome é usado no portal para identificar suas credenciais verificáveis e é incluído como parte do contrato de credenciais verificáveis.
2. Na seção Arquivo de exibição, escolha **Configurar arquivo de exibição**
3. Na seção **Contas de armazenamento**, selecione **woodgrovevcstorage**.
4. Na lista de contêineres disponíveis, escolha **vc-container**.
5. Escolha o arquivo **MyFirstVC-display.json** que criamos anteriormente.
6. Em **Criar uma credencial** na seção **Arquivo de regras**, escolha **Configurar arquivo de regras**
7. Na seção **Contas de armazenamento**, selecione **woodgrovecstorage**
8. Escolha **vc-container**.
9. Selecione **MyFirstVC-rules.json**
10. Na tela **Criar uma credencial**, escolha **Criar**.

   ![criar uma credencial](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>URL da credencial

Agora que você tem uma nova credencial, copie a URL dela.

   ![A URL da credencial emissora](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>A URL da credencial é a combinação dos arquivos de regras e de exibição. É a URL que o Authenticator avalia antes de exibir os requisitos de emissão de credenciais verificáveis ao usuário.  

## <a name="update-the-sample-app"></a>Atualizar o aplicativo de exemplo

Agora, faremos modificações no código do emissor do aplicativo de exemplo para atualizá-lo com a URL da credencial verificável. Isso permite que você emita credenciais verificáveis usando seu locatário.

1. Acesse a pasta na qual você colocou os arquivos do aplicativo de exemplo.
2. Abra a pasta issuer e app.js no Visual Studio Code.
3. Atualize a constante 'credential' com a nova URL de credencial, defina a constante credentialType como 'MyFirstVC' e salve o arquivo.

    ![imagem do Visual Studio Code mostrando as áreas relevantes realçadas](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Abra um prompt de comando e a pasta issuer.
5. Execute o aplicativo de nó atualizado.

    ```terminal
    node app.js
    ```

6. Usando outro prompt de comando, execute o ngrok para configurar uma URL na porta 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Talvez você também observe um aviso indicando que este aplicativo ou este site pode ser suspeito. A mensagem é esperada neste momento porque ainda não vinculamos sua DID ao seu domínio. Siga as instruções de [associação de DNS](how-to-dnsbind.md) para configurar isso.

    
7. Abra a URL HTTPS gerada pelo ngrok.

    ![Pontos de extremidade de encaminhamento do NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Escolha **OBTER CREDENCIAL**
9. No Authenticator, digitalize o código QR.
10. Na mensagem de aviso **Este aplicativo ou este site pode ser suspeito**, escolha **Avançado**.

  ![Aviso inicial](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. No aviso de site suspeito, escolha **Continuar mesmo assim (não seguro)**

  ![Segundo aviso sobre o emissor](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. Na tela **Adicionar uma credencial**, observe algumas coisas: 
    1. Na parte superior da tela, você poderá ver uma mensagem **Não verificado** em vermelho
    1. A credencial é personalizada com base nas alterações que fizemos no arquivo de exibição.
    1. A opção **Entrar em sua conta** está apontando para **didplayground.b2clogin.com**.
    
   ![tela adicionar credencial com o aviso](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Escolha **Entrar em sua conta** e autentique-se usando as informações da credencial fornecidas no [tutorial de introdução](get-started-verifiable-credentials.md).
14. Após a autenticação bem-sucedida, o botão **Adicionar** não ficará mais esmaecido. Escolha **Adicionar**.

  ![tela adicionar credencial após a autenticação](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Acabamos de emitir uma credencial verificável usando nosso locatário para gerar a VC e usamos o locatário B2C para autenticação.

  ![VC emitida pelo Azure AD e autenticada pela instância do Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testar a verificação da VC usando o aplicativo de exemplo

Agora que emitimos a credencial verificável em nosso locatário, vamos verificá-la usando o aplicativo de exemplo.

>[!IMPORTANT]
> Ao testar isso, use o mesmo email e a senha que você usou durante o artigo de [introdução](get-started-verifiable-credentials.md). Enquanto o locatário estiver emitindo a VC, a entrada será proveniente de um token de ID emitido pelo locatário do Microsoft B2C. No segundo tutorial, alternaremos a emissão de tokens para o seu locatário

1. Abra **Configurações** na folha das credenciais verificáveis no portal do Azure. Copie a DID (identidade descentralizada).

   ![cópia da DID](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Agora abra a parte da pasta do verificador dos arquivos de aplicativo de exemplo. Precisamos atualizar o arquivo app.js no código de exemplo do verificador e fazer as seguintes alterações:

    - **credential**: altere-a para a URL da sua credencial
    - **credentialType**: 'MyFirstVC'
    - **issuerDid**: copie esse valor do portal do Azure portal > Credenciais verificáveis (Versão prévia) > Configurações > DID (identidade descentralizada)
    
   ![atualização da constante issuerDid para que ela corresponda ao identificador do emissor](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Pare de executar o serviço ngrok do emissor.

    ```cmd
    control-c
    ```

4. Agora, execute o ngrok com a porta do verificador 8082.

    ```cmd
    ngrok http 8082
    ```

5. Em outra janela de terminal, procure o aplicativo verificador e execute-o da mesma forma que executamos o aplicativo emissor.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Abra a URL do ngrok no navegador e digitalize o código QR usando o Authenticator no seu dispositivo móvel.
7. No seu dispositivo móvel, escolha **Permitir** na tela **Nova solicitação de permissão**.

    >[!NOTE]
    > A DID que assina esta VC ainda é o Microsoft B2C. A DID do verificador ainda é o locatário do aplicativo de exemplo da Microsoft. Como a DID da Microsoft foi vinculada a um domínio de nossa propriedade, você não vê o aviso como vimos durante o fluxo de emissão. Isso será atualizado na próxima seção.
    
   ![nova solicitação de permissão](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Você não verificou com êxito sua credencial.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem o código de exemplo emitindo uma VC do seu emissor, vamos prosseguir para a próxima seção, na qual você usará um provedor de identidade próprio para autenticar os usuários que tentam obter uma credencial verificável e usar sua DID para assinar solicitações de apresentação.

> [!div class="nextstepaction"]
> [Tutorial – Emitir e verificar credenciais verificáveis usando seu locatário](issue-verify-verifiable-credentials-your-tenant.md)


