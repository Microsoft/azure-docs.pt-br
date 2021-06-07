---
title: Habilitar a distribuição em etapas de recursos para públicos-alvo
titleSuffix: Azure App Configuration
description: Saiba como habilitar a distribuição em etapas de recursos para públicos-alvo
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929677"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Habilitar a distribuição em etapas de recursos para públicos-alvo

Os sinalizadores de recurso permitem que você ative ou desative dinamicamente a funcionalidade em seu aplicativo. Os filtros de recurso determinam o estado de um sinalizador de recurso cada vez que ele é avaliado. A `Microsoft.FeatureManagement` biblioteca inclui `TargetingFilter` , que habilita um sinalizador de recurso para uma lista especificada de usuários e grupos, ou para uma porcentagem especificada de usuários. `TargetingFilter` é "adesivo". Isso significa que, uma vez que um usuário individual recebe um recurso, ele continuará a ver esse recurso em todas as solicitações futuras. Você pode usar `TargetingFilter` o para habilitar um recurso para uma conta específica durante uma demonstração, para distribuir progressivamente novos recursos para usuários em diferentes grupos ou "anéis" e muito mais.

Neste artigo, você aprenderá a distribuir um novo recurso em um ASP.NET Core aplicativo Web para usuários e grupos especificados usando o `TargetingFilter` com a configuração do Azure app.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Criar um aplicativo Web com sinalizadores de recurso e autenticação

Para distribuir recursos com base em usuários e grupos, você precisará de um aplicativo Web que permita que os usuários entrem.

1. Crie um aplicativo Web que se autentique em um banco de dados local usando o seguinte comando:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Compile e execute e, em seguida, selecione o link **registrar** no canto superior direito para criar uma nova conta de usuário. Use um endereço de email de `test@contoso.com` . Na tela **registrar confirmação** , selecione **clique aqui para confirmar sua conta**.

1. Siga as instruções em [início rápido: Adicionar sinalizadores de recurso a um aplicativo ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) para adicionar um sinalizador de recurso ao seu novo aplicativo Web.

1. Alterne o sinalizador de recurso na configuração do aplicativo. Valide se essa ação controla a visibilidade do item **beta** na barra de navegação.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Atualizar o código do aplicativo Web para usar o TargetingFilter

Neste ponto, você pode usar o sinalizador de recurso para habilitar ou desabilitar o `Beta` recurso para todos os usuários. Para habilitar o sinalizador de recurso para alguns usuários ao desabilitá-lo para outros, atualize seu código para usar o `TargetingFilter` . Neste exemplo, você usará o endereço de email do usuário conectado como a ID de usuário e a parte do nome de domínio do endereço de email como o grupo. Você adicionará o usuário e o grupo ao `TargetingContext` . O `TargetingFilter` usa esse contexto para determinar o estado do sinalizador de recurso para cada solicitação.

1. Atualize para a versão mais recente do `Microsoft.FeatureManagement.AspNetCore` pacote.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Adicione um arquivo *TestTargetingContextAccessor. cs* :

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. Em *Startup. cs*, adicione uma referência ao namespace *Microsoft. FeatureManagement. FeatureFilters* :

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Atualize o método *configureservices* para registrar `TargetingFilter` , seguindo a chamada para `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Atualize o método *configureservices* para adicionar o `TestTargetingContextAccessor` criado na etapa anterior à coleção de serviços. O *TargetingFilter* o usa para determinar o contexto de destino toda vez que o sinalizador de recurso é avaliado.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

O método *configureservices* inteiro terá a seguinte aparência:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Atualizar o sinalizador de recurso para usar TargetingFilter

1. Na portal do Azure, vá para o repositório de configurações do aplicativo e selecione **Gerenciador de recursos**.

1. Selecione o menu de contexto para o sinalizador de recurso *beta* que você criou no guia de início rápido. Selecione **Editar**.

    > [!div class="mx-imgBorder"]
    > ![Editar sinalizador de recurso beta](./media/edit-beta-feature-flag.png)

1. Na tela de **edição** , marque a caixa de seleção **habilitar sinalizador de recurso** se ela ainda não estiver selecionada. Em seguida, marque a caixa de seleção **usar filtro de recurso** .

1. Selecione o botão de opção de **direcionamento** .

1. Selecione as seguintes opções:

    - **Porcentagem padrão**: 0
    - **Grupos**: Insira um **nome** de _contoso.com_ e um **percentual** de _50_
    - **Usuários**: `test@contoso.com`

    A tela de filtro de recursos terá a seguinte aparência:

    > [!div class="mx-imgBorder"]
    > ![Sinalizador de recurso condicional](./media/feature-flag-filter-enabled.png)

    Essas configurações resultam no seguinte comportamento:

    - O sinalizador de recurso está sempre habilitado para `test@contoso.com` o usuário, pois `test@contoso.com` está listado na seção _usuários_ .
    - O sinalizador de recurso está habilitado para 50% de outros usuários no grupo _contoso.com_ , pois _contoso.com_ está listado na seção _grupos_ com uma _porcentagem_ de _50_.
    - O recurso está sempre desabilitado para todos os outros usuários, porque a _porcentagem padrão_ é definida como _0_.

1. Selecione **aplicar** para salvar essas configurações e retornar à tela do **Gerenciador de recursos** .

1. O **filtro de recursos** para o sinalizador de recurso agora aparece como *destino*. Esse estado indica que o sinalizador de recurso será habilitado ou desabilitado em uma base por solicitação, com base nos critérios impostos pelo filtro de recurso de *direcionamento* .

## <a name="targetingfilter-in-action"></a>TargetingFilter em ação

Para ver os efeitos desse sinalizador de recurso, compile e execute o aplicativo. Inicialmente, o item *beta* não aparece na barra de ferramentas, pois a opção _percentual padrão_ é definida como 0.

Agora, entre como `test@contoso.com` , usando a senha que você definiu ao registrar. O item *beta* agora aparece na barra de ferramentas, pois `test@contoso.com` é especificado como um usuário de destino.

O vídeo a seguir mostra esse comportamento em ação.

> [!div class="mx-imgBorder"]
> ![TargetingFilter em ação](./media/feature-flags-targetingfilter.gif)

Você pode criar usuários adicionais com `@contoso.com` endereços de email para ver o comportamento das configurações de grupo. 50% desses usuários verão o item *beta* . O outro 50% não verá o item *beta* .

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral do gerenciamento de recursos](./concept-feature-management.md)