---
title: Como usar o armazenamento de objetos (Blobs) do Xamarin | Microsoft Docs
description: A biblioteca de clientes de Armazenamento do Microsoft Azure para Xamarin permite que os desenvolvedores criem aplicativos iOS, Android e da Windows Store com as interfaces do usuário nativas. Este tutorial mostra como usar o Xamarin para criar um aplicativo que usa o Armazenamento de Blobs do Azure.
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: b9c707dcc1628f685661f88aaed29612465a5469
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098046"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Como usar o Armazenamento de Blobs no Xamarin

O Xamarin permite que os desenvolvedores usem uma base de código C# compartilhada para criar aplicativos iOS, Android e da Windows Store com suas interfaces de usuário nativo. Este tutorial mostra como usar o armazenamento de Blobs do Azure com um aplicativo Xamarin. Se você quiser saber mais sobre o Armazenamento do Azure, antes de mergulharmos no código, confira [Introdução ao Armazenamento do Microsoft Azure](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Criar um novo aplicativo Xamarin
Neste tutorial, criaremos um aplicativo para Android, iOS e Windows. Este aplicativo simplesmente criará um contêiner e carregará um blob nesse contêiner. Usaremos o Visual Studio no Windows, mas as mesmas lições podem ser aplicadas durante a criação de um aplicativo usando o Xamarin Studio no macOS.

Execute estas etapas para criar o aplicativo:

1. Se você ainda não o fez, baixe e instale o [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abra o Visual Studio e crie um aplicativo em branco (portátil nativo): **Arquivo > Novo > projeto > plataforma cruzada > aplicativo (nativo portátil) em branco**.
3. Clique com o botão direito no painel do Gerenciador de Soluções e selecione **Gerenciar Pacotes NuGet para a Solução**. Procure por **WindowsAzure.Storage** e instale a versão estável mais recente para todos os projetos em sua solução.
4. Compile e execute seu projeto.

Agora você deve ter um aplicativo que permite que você clique em um botão que incrementa um contador.

## <a name="create-container-and-upload-blob"></a>Criar contêiner e carregar blob
Em seguida, em seu projeto `(Portable)`, adicione alguns códigos para `MyClass.cs`. Esse código cria um contêiner e carrega um blob nesse contêiner. `MyClass.cs` deve se parecer com o seguinte:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Substitua "your_account_name_here" e "your_account_key_here" pelo nome e chave de conta. 

Todos os seus projetos para iOS, Android e Windows Phone têm referências ao seu projeto Portátil - ou seja, você pode escrever todo o código compartilhado em um só local e usá-lo em todos os seus projetos. Agora você pode adicionar a seguinte linha de código a cada projeto para começar a aproveitar:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Executar o aplicativo
Agora você pode executar esse aplicativo em um emulador do Android ou do Windows Phone. Também é possível executar esse aplicativo em um emulador de iOS, mas isso exigirá um Mac. Para obter instruções específicas sobre como fazer isso, leia a documentação sobre como [Conectar o Visual Studio a um Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Após a execução do aplicativo, ele criará o contêiner `mycontainer` em sua conta de armazenamento. Ele deve conter o blob, `myblob`, que tem o texto `Hello, world!`. Você pode verificar isso usando o [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/).

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como criar um aplicativo de plataforma cruzada no Xamarin que usa o armazenamento do Azure, concentrando-se especificamente em um cenário no Armazenamento de Blobs. No entanto, você pode fazer muito mais, não apenas com o Armazenamento de Blobs, mas também com o Armazenamento de Filas, de Tabelas e de Arquivo. Para saber mais, veja os artigos a seguir:

* [Introdução ao armazenamento de Blobs do Azure usando o .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução aos Arquivos do Azure](../files/storage-files-introduction.md)
* [Desenvolver para os Arquivos do Azure com .NET](../files/storage-dotnet-how-to-use-files.md)
* [Introdução ao armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introdução ao armazenamento de Fila do Azure usando o .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]