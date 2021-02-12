---
title: Proteger o Azure Remote Rendering e o armazenamento de modelos
description: Proteção de um aplicativo do Remote Rendering para proteger conteúdo
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d8a7bb620b7fcc9c878986d3575e22bb6f0f77bc
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724096"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Tutorial: Proteger o Azure Remote Rendering e o armazenamento de modelos

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Proteger o Armazenamento de Blobs do Azure que contém modelos do Azure Remote Rendering
> * Autenticar-se no Azure AD para acessar sua instância do Azure Remote Rendering
> * Usar credenciais do Azure para a autenticação no Azure Remote Rendering

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial é baseado no [Tutorial: Refinar materiais, iluminação e efeitos](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Por que a segurança adicional é necessária

O estado atual do aplicativo e o acesso aos seus recursos do Azure tem a seguinte aparência:

![Segurança inicial](./media/security-one.png)

Tanto a "AccountID + AccountKey" quanto a "URL + Token SAS" estão, basicamente, armazenando um nome de usuário e uma senha juntos. Por exemplo, se "AccountID + AccountKey" fosse exposta, seria fácil para um invasor usar seus recursos do ARR sem a sua permissão às suas custas.

## <a name="securing-your-content-in-azure-blob-storage"></a>Proteção de seu conteúdo no Armazenamento de Blobs do Azure

O Azure Remote Rendering pode acessar com segurança o conteúdo do Armazenamento de Blobs do Azure com a configuração correta. Confira [Instruções: Vincular contas de armazenamento](../../../how-tos/create-an-account.md#link-storage-accounts) para configurar sua instância do Azure Remote Rendering com suas contas de Armazenamento de Blobs.

Ao usar um Armazenamento de Blobs vinculado, você usará métodos ligeiramente diferentes para carregar modelos:

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

As linhas acima usam a versão `FromSAS` da ação Params e Session. Eles devem ser convertidos para as versões não SAS:

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

Vamos modificar **RemoteRenderingCoordinator** para carregar um modelo personalizado de uma conta de Armazenamento de Blobs vinculada.

1. Se você ainda não fez isso, conclua o [Como Vincular contas de armazenamento](../../../how-tos/create-an-account.md#link-storage-accounts) para conceder à sua instância do ARR permissão para acessar a instância de Armazenamento de Blobs.
1. Adicione o seguinte método **LoadModel** modificado ao **RemoteRenderingCoordinator**, logo abaixo do método **LoadModel** atual:

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    Em grande parte, esse código é idêntico ao método `LoadModel` original; no entanto, substituímos a versão SAS das chamadas de método pelas versões não SAS.

    As outras entradas `storageAccountName` e `blobContainerName` também foram adicionadas aos argumentos. Vamos chamar esse novo método **LoadModel** em outro método semelhante ao primeiro **LoadTestModel** que criamos no primeiro tutorial.

1. Adicione o método a seguir ao **RemoteRenderingCoordinator** logo após **LoadTestModel**

    ```csharp
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Esse código adiciona outras três variáveis de cadeia de caracteres ao componente **RemoteRenderingCoordinator**.
    ![Captura de tela que realça o Nome da Conta de Armazenamento, o Nome do Contêiner de Blobs e o Caminho do Modelo do componente RemoteRenderingCoordinator.](./media/storage-account-linked-model.png)

1. Adicione seus valores ao componente **RemoteRenderingCoordinator**. Se você seguiu o [Início Rápido para conversão de modelo](../../../quickstarts/convert-model.md), seus valores devem ser:

    * **Nome da Conta de Armazenamento**: O nome da conta de armazenamento; o nome exclusivo global que você escolher para sua conta de armazenamento. No início rápido era *arrtutorialstorage*; seu valor será diferente.
    * **Nome do Contêiner de Blobs**: arroutput, o contêiner de Armazenamento de Blobs
    * **Caminho do modelo**: A combinação de "outputFolderPath" e "outputAssetFileName" definida no arquivo *arrconfig.json*. No início rápido, era "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset". O que resultaria em um valor de caminho de modelo assim: "converted/robot/robot.arrAsset"; seu valor será diferente.

    >[!TIP]
    > Se você [executar o script **Conversion.ps1**](../../../quickstarts/convert-model.md#run-the-conversion), sem o argumento "-UseContainerSas", o script produzirá todos os valores acima de acordo com o seu em vez do token SAS. ![Modelo vinculado](./media/converted-output.png)
1. Por enquanto, remova ou desabilite o GameObject **TestModel**, para liberar espaço para o modelo personalizado ser carregado.
1. Execute a cena e conectar-se a uma sessão remota.
1. Clique com o botão direito do mouse no **RemoteRenderingCoordinator** e selecione **Carregar Modelo Personalizado Vinculado**.
    ![Carregar modelo vinculado](./media/load-linked-model.png)

Essas etapas aumentaram a segurança do aplicativo removendo o token SAS do aplicativo local.

Agora, o estado atual do aplicativo e o acesso aos seus recursos do Azure tem a seguinte aparência:

![Mais segurança](./media/security-two.png)

Temos mais uma "senha", a AccountKey, para remover do aplicativo local. Isso pode ser feito usando a autenticação do AAD (Azure Active Directory).

## <a name="azure-active-directory-azure-ad-authentication"></a>Autenticação do Azure AD (Azure Active Directory)

A autenticação do AAD permitirá que você determine quais pessoas ou grupos usam o ARR de maneira mais controlada. O ARR tem suporte interno para aceitar [Tokens de Acesso](../../../../active-directory/develop/access-tokens.md) em vez de usar uma Chave de Conta. Você pode considerar os Tokens de acesso como uma chave específica do usuário, com tempo limitado, que desbloqueia somente determinadas partes do recurso específico para o qual ela foi solicitada.

O script **RemoteRenderingCoordinator** tem um delegado chamado **ARRCredentialGetter**, que contém um método que retorna um objeto **AzureFrontendAccountInfo**, que é usado para configurar o gerenciamento da sessão remota. Podemos atribuir um método diferente para **ARRCredentialGetter**, permitindo usar um fluxo de entrada do Azure, gerando um objeto **AzureFrontendAccountInfo** que contém um Token de Acesso do Azure. Esse Token de Acesso será específico ao usuário que está entrando.

1. Siga o tópico [Como Configurar autenticação – Autenticação para aplicativos implantados](../../../how-tos/authentication.md#authentication-for-deployed-applications), especificamente, você seguirá as instruções listadas na documentação de Âncoras Espaciais do Azure [Autenticação de usuário do Azure AD](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication). Elas envolvem o registro de um novo aplicativo do Azure Active Directory e a configuração do acesso à instância do ARR.
1. Depois de configurar o novo aplicativo do AAD, verifique se o aplicativo do AAD está semelhante às seguintes imagens:

    **Aplicativo do AAD -> Autenticação** ![Autenticação de aplicativo](./media/app-authentication-public.png)

    **Aplicativo do AAD -> Permissões de API** ![APIs de aplicativo](./media/request-api-permissions-step-five.png)

1. Depois de configurar sua conta do Remote Rendering, verifique se a configuração ficou semelhante à seguinte imagem:

    **AAR -> AccessControl (IAM)** ![Função do ARR](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Uma função de *Proprietário* não é suficiente para gerenciar sessões por meio do aplicativo cliente. Para cada usuário que você deseja conceder a capacidade de gerenciar sessões, você deve fornecer a função **Cliente do Remote Rendering**. Para cada usuário que você deseja que gerencie sessões e converta modelos, é necessário fornecer a função de **Administrador do Remote Rendering**.

Com o lado do Azure dessas coisas em vigor, agora precisamos modificar como o seu código se conecta ao serviço do AAR. Fazemos isso implementando uma instância do **BaseARRAuthentication**, que retornará um novo objeto **AzureFrontendAccountInfo**. Nesse caso, as informações da conta serão configuradas com o Token de Acesso do Azure.

1. Crie um script chamado **AADAuthentication** e substitua seu código pelo seguinte:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountAuthenticationDomain;
        public string AzureRemoteRenderingAccountAuthenticationDomain
        {
            get => azureRemoteRenderingAccountAuthenticationDomain.Trim();
            set => azureRemoteRenderingAccountAuthenticationDomain = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountAuthenticationDomain + "/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AzureRemoteRenderingAccountAuthenticationDomain, AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> Esse código não está completo e não está pronto para um aplicativo comercial. Por exemplo, no mínimo, é necessário adicionar a capacidade de sair também. Isso pode ser feito usando o método `Task RemoveAsync(IAccount account)` fornecido pelo aplicativo cliente. Esse código destina-se apenas ao uso do tutorial e sua implementação será específica para o aplicativo.

O código primeiro tenta obter o token silenciosamente usando **AquireTokenSilent**. Isso será bem-sucedido se o usuário tiver se autenticado anteriormente neste aplicativo. Se isso não for bem-sucedido, passe para uma estratégia que envolva mais o usuário.

Nesse código estamos usando o [fluxo de código do dispositivo](../../../../active-directory/develop/v2-oauth2-device-code.md) para obter um Token de Acesso. Esse fluxo permite que o usuário entre em sua conta do Azure em um computador ou um dispositivo móvel e faça com que o token resultante seja enviado de volta para o aplicativo do HoloLens.

A parte mais importante dessa classe, de uma perspectiva do ARR, é esta linha:

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Aqui, criamos um objeto **AzureFrontendAccountInfo** usando o domínio da conta, a ID da conta, o domínio de autenticação da conta e o token de acesso. Esse token é usado pelo serviço do ARR para consultar, criar e unir sessões de Remote Rendering, contanto que o usuário seja autorizado com base nas permissões baseadas em função configuradas anteriormente.

Com essa alteração, o estado atual do aplicativo e o acesso aos seus recursos do Azure tem a seguinte aparência:

![Ainda mais segurança](./media/security-three.png)

Como as Credenciais do usuário não são armazenadas no dispositivo (ou, nesse caso, nem mesmo inseridas no dispositivo), o risco de exposição é muito baixo. Agora o dispositivo está usando um Token de Acesso de tempo limitado e específico do usuário para acessar o ARR, que usa o controle de acesso (IAM) para acessar o Armazenamento de Blobs. Essas duas etapas removeram completamente as "senhas" do código-fonte e aumentaram consideravelmente a segurança. No entanto, essa não é a maior segurança disponível: mover o modelo e o gerenciamento de sessão para um serviço Web aprimorará ainda mais a segurança. Veja considerações adicionais sobre segurança no capítulo [Preparação comercial](../commercial-ready/commercial-ready.md).

### <a name="testing-aad-auth"></a>Testando a autenticação do AAD

No editor do Unity, quando a autenticação do AAD estiver ativa, você precisará se autenticar toda vez que iniciar o aplicativo. No dispositivo, a etapa de autenticação ocorrerá na primeira vez e será necessária novamente somente quando o token expirar ou for invalidado.

1. Adicione o componente **AADAuthentication** ao GameObject **RemoteRenderingCoordinator**.

    ![Componente de autenticação do AAD](./media/azure-active-directory-auth-component.png)

1. Preencha seus valores da ID do cliente e da ID do locatário. Esses valores podem ser encontrados na página de visão geral do registro do aplicativo:

    * O **Domínio da conta** é o mesmo domínio que você estava usando no domínio da conta do **RemoteRenderingCoordinator**.
    * A **ID do cliente do aplicativo do Active Directory** é a *ID do aplicativo (cliente)* encontrada no registro do aplicativo do AAD (veja a imagem abaixo).
    * A **ID de locatário do Azure** é a ID do *Diretório (locatário)* encontrada no registro do aplicativo do AAD (consulte a imagem abaixo).
    * A **ID da conta do Azure Remote Rendering** é a mesma **ID de conta** que você está usando para o **RemoteRenderingCoordinator**.
    * O **Domínio de autenticação da conta** é o mesmo **Domínio de autenticação da conta** que você estava usando no **RemoteRenderingCoordinator**.

    ![Captura de tela que realça a ID do Aplicativo (cliente) e a ID do Diretório (locatário).](./media/app-overview-data.png)

1. Pressione Executar no editor do Unity e dê consentimento para executar uma sessão.
    Como o componente **AADAuthentication** tem um controlador de exibição, ele é conectado automaticamente para exibir um prompt depois do painel modal de autorização de sessão.
1. Siga as instruções encontradas no painel à direita do **AppMenu**.
    Você deve ver algo semelhante a isto: ![Ilustração que mostra o painel de instruções que aparece à direita do AppMenu.](./media/device-flow-instructions.png)
    Depois de inserir o código fornecido em seu dispositivo secundário (ou navegador no mesmo dispositivo) e fazer logon usando suas credenciais, um Token de Acesso será retornado para o aplicativo solicitante, nesse caso, o editor do Unity.
1. Depois desse ponto, tudo no aplicativo deverá continuar normalmente. Verifique se há erros no console do Unity se você não estiver progredindo entre os estágios conforme o esperado.

## <a name="build-to-device"></a>Compilar para o dispositivo

Se estiver compilando um aplicativo usando a MSAL para o dispositivo, será necessário incluir um arquivo na pasta **Ativos** do seu projeto. Isso ajudará o compilador a compilar o aplicativo corretamente usando a *Microsoft.Identity.Client.dll* incluída nos **Ativos do tutorial**.

1. Adicione um novo arquivo em **Ativos** chamado **link.xml**
1. Adicione o seguinte ao arquivo:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Salve as alterações

Siga as etapas encontradas no [Início Rápido: Implantar exemplo do Unity no HoloLens – Compilar o projeto de exemplo](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project), para compilar para o HoloLens.

## <a name="next-steps"></a>Próximas etapas

O restante deste conjunto de tutoriais contém tópicos conceituais para a criação de um aplicativo pronto para produção que usa o Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Próximo: Preparação comercial](../commercial-ready/commercial-ready.md)