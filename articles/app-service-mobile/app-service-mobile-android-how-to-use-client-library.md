---
title: Como usar o SDK de Aplicativos Móveis do Azure para Android | Microsoft Docs
description: Como usar o SDK de Aplicativos Móveis do Azure para Android
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 6a6db136926a7f9d631c717f5cab6c025d97fb48
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443535"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Como usar o SDK de Aplicativos Móveis do Azure para Android

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-how-to-use-client-library) hoje mesmo.
>

Este guia mostra como usar o SDK de cliente Android para Aplicativos Móveis a fim de implementar cenários comuns, como:

* Consulta de dados (inserir, atualizar e excluir).
* Autenticação.
* Tratamento de erros.
* Personalização do cliente.

Este guia destaca o SDK do Android no lado do cliente.  Para saber mais sobre os SDKs do lado do servidor para aplicativos móveis, consulte [trabalhar com o back-end do SDK do .NET][10] or [How to use the Node.js backend SDK][11].

## <a name="reference-documentation"></a>Documentação de referência

Você pode encontrar o [referência de API do Javadocs][12] para a biblioteca de cliente Android no GitHub.

## <a name="supported-platforms"></a>Plataformas com suporte

O SDK de Aplicativos Móveis do Azure para Android oferece suporte aos níveis de API 19 a 24 (KitKat até Nougat) para fatores de forma de telefone e tablet.  A autenticação, em particular, utiliza uma abordagem comum de estrutura da Web para coletar credenciais.  A autenticação de fluxo do servidor não funciona com dispositivos de fator de forma pequeno como inspeções.

## <a name="setup-and-prerequisites"></a>Configuração e pré-requisitos

Conclua o tutorial [Início rápido de Aplicativos Móveis](app-service-mobile-android-get-started.md) .  Essa tarefa faz com que todos os pré-requisitos para o desenvolvimento de Aplicativos Móveis do Azure sejam atendidos.  O Início Rápido ajuda a configurar sua conta e criar seu primeiro back-end de aplicativo móvel.

Se você decidir não concluir o tutorial de Início Rápido, conclua as seguintes tarefas:

* [criar um back-end do aplicativo móvel][13] para usar com seu aplicativo Android.
* No Android Studio, [atualizar os arquivos de compilação do Gradle](#gradle-build).
* [Habilitar a permissão de Internet](#enable-internet).

### <a name="gradle-build"></a>Atualizar o arquivo de compilação do Gradle

Altere ambos os arquivos **build.gradle** :

1. Adicione este código para o *Project* nível **gradle** arquivo:

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Adicione este código ao arquivo **build.gradle** do nível *Module app* dentro da marca *dependencies*:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Atualmente, a versão mais recente é a 3.4.0. As versões com suporte estão listadas [no bintray][14].

### <a name="enable-internet"></a>Habilitar a permissão de Internet

Para acessar o Azure, o aplicativo deve ter a permissão INTERNET habilitada. Se ela ainda não estiver habilitada, adicione a seguinte linha de código ao arquivo **AndroidManifest.xml** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Criar uma conexão de cliente

Os Aplicativos Móveis do Azure fornecem quatro funções para seu aplicativo móvel:

* Acesso a Dados e a Sincronização Offline com um Serviço de Aplicativos Móveis do Azure.
* Chame as APIs personalizados escritas com o SDK do Servidor de Aplicativos Móveis do Azure.
* Autenticação com autenticação e autorização do Serviço de Aplicativo do Azure.
* Envie notificações por push com hubs de notificação.

Cada uma dessas funções primeiro exige que você crie um objeto `MobileServiceClient`.  Apenas um objeto `MobileServiceClient` deve ser criado em seu cliente móvel (ou seja, deve ser um padrão Singleton).  Para criar um objeto `MobileServiceClient`:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

O `<MobileAppUrl>` é uma cadeia de caracteres ou um objeto de URL que aponta para o back-end móvel.  Se você estiver usando o Serviço de Aplicativo do Azure para hospedar o back-end móvel, use a versão `https://` segura da URL.

O cliente também exige acesso à atividade ou ao contexto - o parâmetro `this` no exemplo.  A construção de MobileServiceClient deve acontecer dentro do método `onCreate()` da Atividade referenciada no arquivo `AndroidManifest.xml`.

Como prática recomendada, você deve abstrair a comunicação do servidor em sua própria classe (padrão singleton).  Nesse caso, você deve passar a Atividade dentro do construtor para configurar corretamente o serviço.  Por exemplo:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Agora você pode chamar `AzureServiceAdapter.Initialize(this);` no método `onCreate()` de sua atividade principal.  Quaisquer outros métodos que precisam de acesso para o cliente usam `AzureServiceAdapter.getInstance();` para obter uma referência ao adaptador de serviço.

## <a name="data-operations"></a>Operações de dados

O fundamental do SDK de Aplicativos Móveis do Azure é fornecer acesso aos dados armazenados no SQL Azure no back-end do Aplicativo Móvel.  Você pode acessar esses dados usando classes fortemente tipadas (preferido) ou consultas não tipadas (não recomendado).  A maior parte desta seção lida com o uso de classes fortemente tipadas.

### <a name="define-client-data-classes"></a>Definir as classes de dados do cliente

Para acessar dados de tabelas do SQL Azure, defina as classes de dados do cliente que correspondem às tabelas no back-end de aplicativo móvel. Os exemplos deste tópico pressupõem a existência de uma tabela denominada **MyDataTable** com as seguintes colunas:

* id
* text
* concluído

O objeto tipado do lado do cliente correspondente reside em um arquivo chamado **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Adicione os métodos getter e setter para cada campo que você adicionar.  Se a tabela do SQL Azure contiver mais colunas, adicione os campos correspondentes a essa classe.  Por exemplo, se o DTO (objeto de transferência de dados) tivesse uma coluna Priority de inteiros, você poderia adicionar este campo, com seus métodos getter e setter:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Para saber como criar tabelas adicionais no seu back-end de aplicativos móveis, consulte [Como: Definir um controlador de tabela][15] (.NET backend) or [Define Tables using a Dynamic Schema][16] (back-end do Node. js).

Uma tabela de back-end dos Aplicativos Móveis do Azure define cinco campos especiais, quatro deles disponíveis para clientes:

* `String id`: A ID globalmente exclusiva para o registro.  Como prática recomendada, verifique a id a representação de cadeia de caracteres de um [UUID][17] objeto.
* `DateTimeOffset updatedAt`: A data/hora da última atualização.  O campo updatedAt é definido pelo servidor e nunca deve ser definido pelo código do cliente.
* `DateTimeOffset createdAt`: A data /hora da criação do objeto.  O campo createdAt é definido pelo servidor e nunca deve ser definido pelo código do cliente.
* `byte[] version`: Normalmente é representado como uma cadeia de caracteres, a versão também é definida pelo servidor.
* `boolean deleted`: Indica que o registro foi excluído, mas ainda não foi limpo.  Não use `deleted` como uma propriedade em sua classe.

O campo `id` é obrigatório.  O campo `updatedAt` e o campo `version` são usados para sincronização offline (para sincronização incremental e resolução de conflitos, respectivamente).  O campo `createdAt` é um campo de referência e não é usado pelo cliente.  Os nomes são nomes "across-the-wire" nomes das propriedades e não são ajustáveis.  No entanto, você pode criar um mapeamento entre seu objeto e os nomes "across-the-wire" usando o [gson][3] biblioteca.  Por exemplo:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Criar uma referência de tabela

Para acessar uma tabela, primeiro crie uma [MobileServiceTable][8] objeto chamando o **getTable** método sobre o [MobileServiceClient][9].  Esse método tem duas sobrecargas:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

No código a seguir, **mClient** é uma referência ao objeto MobileServiceClient.  A primeira sobrecarga é usada onde o nome da classe e o nome da tabela são iguais, e é a usada no Início Rápido:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

A segunda sobrecarga é usada quando o nome da tabela é diferente do nome da classe: o primeiro parâmetro é o nome da tabela.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Consultar uma tabela de back-end

Primeiro, obtenha uma referência de tabela.  Em seguida, execute uma consulta na referência de tabela.  Uma consulta é qualquer combinação de:

* Uma [cláusula de filtro `.where()`](#filtering).
* Uma [cláusula de ordenação `.orderBy()`](#sorting).
* Uma [cláusula de seleção de campo `.select()`](#selection).
* Um `.skip()` e `.top()` para [resultados paginados](#paging).

As cláusulas devem ser apresentadas na ordem anterior.

### <a name="filter"></a> Filtragem de resultados

A forma geral de uma consulta é:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

O exemplo anterior retorna todos os resultados (até o tamanho máximo da página definida pelo servidor).  O método `.execute()` executa a consulta no back-end.  A consulta é convertida em uma [OData v3][19] consulta antes da transmissão para o back-end de aplicativos móveis.  Após o recebimento, o back-end dos Aplicativos Móveis converte a consulta em uma instrução SQL antes de executá-la na instância do SQL Azure.  Uma vez que a atividade de rede demora algum tempo, o `.execute()` método retorna um [ `ListenableFuture<E>` ][18].

### <a name="filtering"></a>Filtrar dados retornados

A execução da consulta a seguir retorna todos os itens da tabela **ToDoItem**, em que **complete** é igual a **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** é a referência à tabela do serviço móvel que criamos anteriormente.

Defina um filtro usando a chamada ao método **where** na referência de tabela. O método **where** é seguido de um método **field** seguido de um método que especifica o predicado lógico. Os possíveis métodos de predicado incluem **eq** (igual),**ne** (diferente), **gt** (maior que), **ge** (maior que ou igual a), **lt** (menor que), **le** (menor que ou igual a). Esses métodos permitem que você compare os campos de número e cadeia de caracteres com valores específicos.

Você pode filtrar por datas. Os métodos a seguir permitem comparar o campo de data inteira ou partes da data:**year**, **month**, **day**, **hour**, **minute** e **second**. O exemplo a seguir adiciona um filtro para itens cuja *data de vencimento* é igual a 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Os métodos a seguir oferecem suporte a filtros complexos nos campos de cadeia de caracteres: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** e **length**. O exemplo a seguir filtra linhas de tabela em que a coluna *text* começa com "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Os métodos de operador a seguir têm suporte em campos de número: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** e **round**. O exemplo a seguir filtra linhas de tabela em que **duration** é um número par.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

É possível combinar predicados com estes métodos lógicos: **and**, **or** e **not**. O exemplo a seguir combina dois dos exemplos anteriores.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Agrupar e aninhar operadores lógicos:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Para ver uma discussão mais detalhada e exemplos de filtragem, veja [Explorando a riqueza do modelo de consulta de cliente do Android][20].

### <a name="sorting"></a>Classificar dados retornados

O código a seguir retorna todos os itens de uma tabela **ToDoItem** classificada em ordem crescente pelo campo *text* . *mToDoTable* é a referência à tabela de back-end criada anteriormente:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

O primeiro parâmetro do método **orderBy** é uma cadeia de caracteres igual ao nome do campo pelo qual classificar. O segundo parâmetro usa a enumeração **QueryOrder** para especificar se a classificação será feita em ordem crescente ou decrescente.  Se você estiver filtrando usando o método ***where***, o método ***where*** deverá ser invocado antes do método ***orderBy***.

### <a name="selection"></a>Selecionar colunas específicas

O código a seguir ilustra como retornar todos os itens de uma tabela de **ToDoItems**, mas exibe apenas os **complete** e **text**. **mToDoTable** é a referência à tabela de back-end criada anteriormente.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Os parâmetros para a função select são os nomes de cadeia de caracteres das colunas da tabela que você deseja retornar.  O método **select** precisa seguir métodos como **where** e **orderBy**. Ele pode ser seguido por métodos de paginação como **skip** e **top**.

### <a name="paging"></a>Retornar dados em páginas

Os dados **SEMPRE** retornam em páginas.  O número máximo de registros retornados é definido pelo servidor.  Se o cliente solicitar mais registros, o servidor retornará o número máximo de registros.  Por padrão, o tamanho máximo da página no servidor é de 50 registros.

O primeiro exemplo mostra como selecionar os cinco primeiros itens de uma tabela. A consulta retorna os itens de uma tabela **ToDoItems**. **mToDoTable** é a referência à tabela de back-end criada anteriormente:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Veja uma consulta que ignora os cinco primeiros itens e retorna os cinco seguintes:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Se você quiser obter todos os registros em uma tabela, implemente o código para iterar por todas as páginas:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Uma solicitação para todos os registros usando esse método cria no mínimo duas solicitações para o back-end dos Aplicativos Móveis.

> [!TIP]
> A escolha do tamanho de página certo é um equilíbrio entre o uso de memória durante a solicitação, o uso de largura de banda e atraso no recebimento completo dos dados.  O padrão (50 registros) é adequado para todos os dispositivos.  Se você operar exclusivamente em dispositivos de memória maiores, aumente até 500.  Descobrimos que aumentar o tamanho da página além dos 500 registros resulta em atrasos inaceitáveis e problemas de memória grande.

### <a name="chaining"></a>Como: Concatenar métodos de consulta

Os métodos usados nas tabelas de back-end de consulta podem ser concatenados. O encadeamento de métodos de consulta permite a você selecionar colunas específicas ou linhas filtradas que são classificadas e paginadas. Você pode criar filtros lógicos complexos.  Cada método de consulta retorna um objeto de consulta. Para encerrar a série de métodos e realmente executar a consulta, chame o método **execute** . Por exemplo:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Os métodos de consulta encadeados devem ser ordenados da seguinte maneira:

1. Métodos de filtragem (**where**).
2. Métodos de classificação (**orderBy**).
3. Métodos de seleção (**select**).
4. Métodos de paginação (**skip** e **top**).

## <a name="binding"></a>Associar dados à interface do usuário

A associação de dados envolve três componentes:

* A fonte de dados
* O layout da tela
* O adaptador que vincula os dois.

Em nosso código de exemplo, retornamos os dados da tabela **ToDoItem** do SQL Azure dos Aplicativos Móveis em uma matriz. Essa atividade é um padrão comum para aplicativos de dados.  As consultas de banco de dados geralmente retornam uma coleção de linhas que o cliente obtém em uma lista ou uma matriz. Neste exemplo, a matriz é a fonte de dados.  O código especifica um layout de tela que define a exibição dos dados que são exibidos no dispositivo.  E os dois são associados juntos com um adaptador, que, nesse código, é uma extensão da classe **ArrayAdapter&lt;ToDoItem&gt;** .

#### <a name="layout"></a>Definir o layout

O layout é definido por vários snippets de código XML. Com base em um layout existente, o código a seguir representa a **ListView** que queremos preencher com nossos dados de servidor.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

No código anterior, o atributo *listitem* especifica a id do layout para uma linha individual na lista. Esse código especifica uma caixa de seleção e seu texto associado, e tem uma instância criada para cada item na lista. Esse layout não exibe o campo **ID** , e um layout mais complexo especificaria campos adicionais na exibição. Este código está no arquivo **row_list_to_do.xml**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Definir o adaptador
Como a fonte de dados da nossa exibição é uma matriz de**ToDoItem**, podemos criar a subclasse do nosso adaptador por meio de uma classe **ArrayAdapter&lt;ToDoItem&gt;** . Esta subclasse produz uma exibição para cada **ToDoItem** usando o layout **row_list_to_do**.  No nosso código, definimos a seguinte classe que é uma extensão da classe **ArrayAdapter&lt;E&gt;** :

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Substitua o método **getView** dos adaptadores. Por exemplo:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Criamos uma instância dessa classe em nossa atividade, da seguinte forma:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

O segundo parâmetro para o construtor ToDoItemAdapter é uma referência ao layout. Agora podemos criar uma instância do **ListView** e atribuir o adaptador ao **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Usar o Adaptador para associar-se à interface do usuário

Agora você está pronto para usar a associação de dados. O código a seguir mostra como obter os itens na tabela e preenche o adaptador local com os itens retornados.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Chame o adaptador sempre que modificar a tabela **ToDoItem** . Como as modificações são feitas de registro em registro, você trata de uma única linha em vez de uma coleção. Ao inserir um item, chame o método **add** no adaptador e, ao excluir, chame o método **remove**.

Você pode encontrar um exemplo completo na [projeto de início rápido do Android][21].

## <a name="inserting"></a>Inserir dados no back-end

Crie uma instância da classe *ToDoItem* e defina suas propriedades.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Em seguida, use **insert ()** para inserir um objeto:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

A entidade retornada corresponde aos dados inseridos na tabela de back-end, inclusive a ID e outros valores (como os campos `createdAt`, `updatedAt` e `version`) definidos no back-end.

As tabelas de Aplicativos Móveis exigem uma coluna de chave primária denominada **id**. Essa coluna deve ser uma cadeia de caracteres. O valor padrão da coluna de ID é um GUID.  Você pode fornecer outros valores exclusivos, como endereços de email ou nomes de usuário. Quando um valor de ID de cadeia de caracteres não é fornecido para um registro inserido, o back-end gera um novo GUID.

Os valores de ID de cadeia de caracteres proporcionam as seguintes vantagens:

* As IDs podem ser geradas sem que seja preciso fazer uma varredura no banco de dados.
* Os registros são mais fáceis de mesclar a partir de tabelas ou bancos de dados diferentes.
* Os valores de ID integram-se melhor à lógica de um aplicativo.

Valores de ID de cadeia de caracteres são **OBRIGATÓRIOS** para suporte de sincronização offline.  Você não pode alterar uma ID após ela ser armazenada no banco de dados back-end.

## <a name="updating"></a>Atualizar dados em um aplicativo móvel

Para atualizar dados em uma tabela, passe o novo objeto para o método **update ()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Neste exemplo, *item* é uma referência a uma linha na tabela *ToDoItem*, a qual sofreu algumas alterações.  A linha com a mesma **id** é atualizada.

## <a name="deleting"></a>Excluir dados em um aplicativo móvel

O código a seguir mostra como excluir dados de uma tabela especificando o objeto de dados.

```java
mToDoTable
    .delete(item);
```

Você também pode excluir um item especificando a **id** da linha a ser excluída.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Pesquisar um item específico por ID

Pesquise um item com determinado campo **id** com o método **lookUp()** :

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Como: Trabalhar com dados não tipados

O modelo de programação não tipado oferece controle rígido sobre a serialização JSON.  Há alguns cenários comuns em que você pode querer usar um modelo de programação não tipado. Por exemplo, se sua tabela de back-end contém muitas colunas e você só precisa fazer referência a um subconjunto das colunas.  O modelo tipado requer que você defina todas as colunas definidas no back-end dos Aplicativos Móveis em sua classe de dados.  A maioria das chamadas API para acessar dados são semelhante às chamadas de programação tipadas. A principal diferença é que o modelo não tipado você invocar métodos no objeto **MobileServiceJsonTable**, em vez do objeto **MobileServiceTable**.

### <a name="json_instance"></a>Criar uma instância de uma tabela não tipada

Semelhante ao modelo tipado, você começa obtendo uma referência de tabela, mas, nesse caso, é um objeto **MobileServicesJsonTable** . Obtenha a referência ao chamar o método **getTable** em uma instância do cliente:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Depois de criar uma instância do **MobileServiceJsonTable**, ela tem praticamente a mesma API disponível que o modelo de programação tipado. Em alguns casos, os métodos usam um parâmetro não tipado em vez de um parâmetro tipado.

### <a name="json_insert"></a>Inserir em uma tabela não tipada
O código a seguir mostra como fazer uma inserção. A primeira etapa é criar uma [JsonObject][1] , which is part of the [gson][3] biblioteca.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Em seguida, use **insert()** para inserir o objeto não tipado na tabela.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Se você precisa obter a ID do objeto inserido, use o método **getAsJsonPrimitive()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Excluir de uma tabela não tipada
O código a seguir mostra como excluir uma instância, neste caso, a mesma instância de um **JsonObject** criado no exemplo *insert* anterior. O código é igual ao do caso tipado, mas o método tem uma assinatura diferente, uma vez que ele faz referência a um **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Você também pode excluir uma instância diretamente, usando sua ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Retornar todas as linhas de uma tabela não tipada
O código a seguir mostra como recuperar uma tabela inteira. Como você está usando uma tabela JSON, pode recuperar seletivamente somente algumas das colunas da tabela.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

O mesmo conjunto de métodos de filtragem, filtragem e paginação que estão disponíveis para o modelo tipado estão disponíveis para o modelo não tipado.

## <a name="offline-sync"></a>Implementar a sincronização offline

O SDK do cliente de Aplicativos Móveis do Azure também implementa a sincronização offline de dados usando um banco de dados SQLite para armazenar localmente uma cópia dos dados do servidor.  As operações executadas em uma tabela offline não exigem conectividade móvel para funcionar.  A sincronização offline auxilia na resiliência e no desempenho às custas de uma lógica mais complexa para resolução de conflitos.  O SDK de cliente dos Aplicativos Móveis do Azure implementa os seguintes recursos:

* Sincronização incremental: Somente registros novos e atualizados são baixados, economizando o consumo de largura de banda e de memória.
* Simultaneidade otimista: As operações devem ter êxito.  A resolução de conflitos é adiada até que as atualizações sejam executadas no servidor.
* Resolução de conflitos: O SDK detecta quando uma alteração conflitante tiver sido feita no servidor e fornece ganchos para alertar o usuário.
* Exclusão reversível: Os registros excluídos são marcados como excluídos, permitindo que outros dispositivos atualizem seu cache offline.

### <a name="initialize-offline-sync"></a>Inicializar a sincronização offline

Cada tabela offline deve ser definida no cache offline antes do uso.  Normalmente, a definição da tabela é feita imediatamente após a criação do cliente:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Obter uma referência à Tabela de Cache Offline

Para uma tabela online, use `.getTable()`.  Para uma tabela offline, use `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Todos os métodos disponíveis para tabelas online (incluindo a filtragem, classificação, paginação, inserção de dados, atualização de dados e exclusão de dados) funcionam igualmente bem em tabelas online e offline.

### <a name="synchronize-the-local-offline-cache"></a>Sincronizar o Cache Offline Local

A sincronização está dentro do controle de seu aplicativo.  Veja um exemplo de método de sincronização:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Se um nome de consulta for fornecido para o método `.pull(query, queryname)`, a sincronização incremental será usada para retornar somente os registros criados ou alterados desde o último pull concluído com êxito.

### <a name="handle-conflicts-during-offline-synchronization"></a>Lidar com conflitos durante a sincronização offline

Se um conflito ocorrer durante uma operação `.push()`, uma `MobileServiceConflictException` será lançada.   O item emitido pelo servidor é inserido na exceção e pode ser recuperado por `.getItem()` na exceção.  Ajuste o envio por push chamando os seguintes itens no objeto MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Depois que todos os conflitos forem marcados conforme sua vontade, chame `.push()` novamente para resolver todos os conflitos.

## <a name="custom-api"></a>Chamar uma API personalizada

Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não mapeia para uma inserção, atualização, exclusão ou operação de leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo ler e definir cabeçalhos de mensagens HTTP e definir um formato de corpo de mensagem diferente do JSON.

Em um cliente Android, você chama o método **invokeApi** para chamar o ponto de extremidade da API personalizada. O exemplo a seguir mostra como chamar um ponto de extremidade de API denominado **completeAll**, que retorna uma classe de coleção chamada **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

O método **invokeApi** é chamado no cliente, que envia uma solicitação POST para a nova API personalizada. O resultado retornado pela API personalizada é exibido em uma caixa de diálogo de mensagem, se houver erros. Outras versões de **invokeApi** permitem que você, opcionalmente, envie um objeto no corpo da solicitação, especifique o método HTTP e envie parâmetros de consulta com a solicitação. Versões não tipadas de **invokeApi** também são fornecidas.

## <a name="authentication"></a>Adicionar autenticação ao seu aplicativo

Os tutoriais já descrevem detalhadamente como adicionar esses recursos.

O Serviço de Aplicativo do Azure suporta a [autenticação e autorização de usuários](app-service-mobile-android-get-started-users.md) de aplicativos usando variados provedores de identidade externos: Facebook, Google, Conta Microsoft, Twitter e Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização no seu back-end.

Dois fluxos de autenticação são suportados: um **server** flow e um **client** flow. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface Web do provedor de identidade.  Nenhum SDK adicional é necessário para implementar a autenticação de fluxo de servidor. A autenticação de fluxo de servidor não oferece uma integração profunda ao dispositivo móvel e só é recomendada em cenários de prova de conceito.

O fluxo de cliente permite uma integração mais profunda com funcionalidades específicas do dispositivo, como logon único, uma vez que depende de SDKs fornecidas pelo provedor de identidade.  Por exemplo, você pode integrar o SDK do Facebook a seu aplicativo móvel.  O cliente móvel alterna para o aplicativo do Facebook e confirma o logon antes de voltar para seu aplicativo móvel.

Quatro etapas são necessárias para habilitar a autenticação no seu aplicativo:

* Registrar o aplicativo para autenticação com um provedor de identidade.
* Configurar o back-end do Serviço de Aplicativo.
* Restringir permissões de tabela a usuários autenticados somente no back-end do Serviço de Aplicativo.
* Adicionar código de autenticação ao aplicativo.

Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar o SID de um usuário autenticado para modificar solicitações.  Para saber mais, confira [Introdução à autenticação] e a documentação TUTORIAL do SDK do Servidor.

### <a name="caching"></a>Autenticação: Fluxo de servidor

O código a seguir inicia um processo de logon do fluxo do servidor usando o provedor Google.  A configuração adicional é necessária devido aos requisitos de segurança para o provedor do Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Além disso, adicione o método a seguir à classe Activity:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

O `GOOGLE_LOGIN_REQUEST_CODE` definidos em sua Atividade principal é usado para o método `login()` e dentro do método `onActivityResult()`.  Você pode escolher qualquer número exclusivo, desde que o mesmo número seja usado dentro do método `login()` e do método `onActivityResult()`.  Se você abstrair o código do cliente em um adaptador de serviço (conforme mostrado anteriormente), chame os métodos apropriados no adaptador de serviço.

Você também precisa configurar o projeto para customtabs.  Primeiro, especifique uma URL de redirecionamento.  Adicione este snippet ao `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Adicione o **redirectUriScheme** ao arquivo `build.gradle` de seu aplicativo:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Finalmente, adicione `com.android.support:customtabs:28.0.0` à lista de dependências no arquivo `build.gradle`:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Obtenha a ID do usuário conectado de um **MobileServiceUser** usando o método **getUserId**. Para ver um exemplo de como usar Futures para chamar as APIs de logon assíncronas, veja [Introdução à autenticação].

> [!WARNING]
> O esquema de URL mencionado diferencia maiúsculas de minúsculas.  Certifique-se de que todas as ocorrências de `{url_scheme_of_you_app}` tenham as mesmas maiúsculas ou minúsculas.

### <a name="caching"></a>Armazenar tokens de autenticação em cache

Armazenar em cache os tokens de autenticação exige que você armazene uma ID de usuário e o token de autenticação localmente no dispositivo. Na próxima vez que o aplicativo iniciar, você verificará o cache e, se esses valores estiverem presentes, poderá ignorar o procedimento de logon e reidratar o cliente com esses dados. No entanto, esses dados são confidenciais e, para segurança, devem ser armazenados criptografados caso o telefone seja roubado.  Você pode ver um exemplo completo de como a tokens de autenticação de cache no [seção de tokens de autenticação de Cache][7].

Ao tentar usar um token expirado, você receberá uma resposta *401 não autorizado* . Você pode tratar erros de autenticação usando filtros.  Filtros interceptam as solicitações para o back-end do Serviço de Aplicativo. O código do filtro testa a resposta para um 401, dispara o processo de logon e retoma a solicitação que gerou o 401.

### <a name="refresh"></a>Usar tokens de atualização

O token retornado pela Autenticação e Autorização do Serviço de Aplicativo do Azure tem um tempo de vida definido de uma hora.  Após esse período, você deve autenticar novamente o usuário.  Se você estiver usando um token de vida longa recebido por meio da autenticação de fluxo de cliente, autentique novamente com a Autenticação e Autorização do Serviço de Aplicativo do Azure usando o mesmo token.  Outro token de Serviço de Aplicativo do Azure é gerado com um novo tempo de vida.

Você também pode registrar o provedor para usar Tokens de atualização.  Nem sempre um token de atualização está disponível.  É necessário realizar uma configuração adicional:

* Para o **Azure Active Directory**, configure um segredo do cliente para o aplicativo do Azure Active Directory.  Especifique o segredo do cliente no Serviço de Aplicativo do Azure ao configurar a Autenticação do Azure Active Directory.  Ao chamar `.login()`, passe `response_type=code id_token` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para o **Google**, passe o `access_type=offline` como um parâmetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para a **Conta da Microsoft**, selecione o escopo `wl.offline_access`.

Para atualizar um token, chame `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Como prática recomendada, crie um filtro que detecta uma resposta 401 do servidor e tenta atualizar o token de usuário.

## <a name="log-in-with-client-flow-authentication"></a>Faça logon com a autenticação de cliente-fluxo

O processo geral para fazer logon com autenticação de cliente-fluxo é a seguinte:

* Configure a Autenticação e Autorização do Serviço de Aplicativo do Azure como você faria com autenticação de servidor-fluxo.
* Integre o SDK do provedor para autenticação para produzir um token de acesso do.
* Chame o método `.login()` da seguinte maneira (`result` deve ser um `AuthenticationResult`):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Confira o exemplo de código completo na próxima seção.

Substitua o método `onSuccess()` por qualquer código que você queira usar em um logon bem-sucedido.  A cadeia de caracteres `{provider}` é um provedor válido: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount** ou **twitter**.  Se você tiver implementado a autenticação personalizada, também poderá usar a marca do provedor de autenticação personalizada.

### <a name="adal"></a>Autenticar usuários com a Active Directory Authentication Library (ADAL)

Você pode usar a ADAL (Biblioteca de autenticação do Active Directory) para conectar os usuários ao seu aplicativo usando o Active Directory do Azure. Normalmente, é melhor usar um logon de fluxo de cliente em vez dos métodos `loginAsync()` , pois ele fornece uma aparência mais nativa de UX e permite uma maior personalização.

1. Configure o seu back-end de aplicativo móvel para entrada no AAD seguindo o tutorial [Como configurar o Serviço de Aplicativo para logon no Active Directory][22] . Complete a etapa opcional de registrar um aplicativo cliente nativo.
2. Instale a ADAL modificando o arquivo build.gradle para incluir as seguintes definições:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Adicione o seguinte código ao seu aplicativo, fazendo as seguintes substituições:

    * Substitua **INSERT-AUTHORITY-HERE** pelo nome do locatário no qual o aplicativo foi provisionado. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Substitua **INSERT-RESOURCE-ID-HERE** pela ID do cliente do seu back-end de aplicativo móvel. Você pode obter a ID do cliente na guia **Avançadas** em **Configurações do Azure Active Directory** no portal.
    * Substitua **INSERT-CLIENT-ID-HERE** pela ID do cliente copiada do aplicativo cliente nativo.
    * Substitua **INSERT-REDIRECT-URI-HERE** pelo ponto de extremidade */.auth/login/done* do site, usando o esquema HTTPS. Este valor deve ser semelhante a *https://contoso.azurewebsites.net/.auth/login/done* .

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Ajustar a comunicação cliente-servidor

A conexão do cliente é normalmente uma conexão HTTP básica usando a biblioteca HTTP subjacente fornecida com o SDK do Android.  Há vários motivos para você querer alterar isso:

* Você quer usar uma biblioteca HTTP alternativa para ajustar o tempo limite.
* Você quer fornecer uma barra de progresso.
* Você quer adicionar um cabeçalho personalizado para oferecer suporte à funcionalidade de gerenciamento de API.
* Você quer interceptar uma resposta com falha para que você possa implementar a reautenticação.
* Você quer registrar solicitações de back-end para um serviço de análise.

### <a name="using-an-alternate-http-library"></a>Usar uma biblioteca HTTP alternativa

Chame o método `.setAndroidHttpClientFactory()` imediatamente depois de criar sua referência de cliente.  Por exemplo, para definir o tempo limite de conexão como 60 segundos (em vez do padrão de 10 segundos):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementar um filtro de progresso

Você pode implementar uma interceptação de cada solicitação implementando um `ServiceFilter`.  Por exemplo, o seguinte atualiza uma barra de progresso pré-criada:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Você pode anexar esse filtro ao cliente da seguinte maneira:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personalizar cabeçalhos de solicitações

Use o seguinte `ServiceFilter` e anexe o filtro da mesma maneira que o `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Configurar a Serialização Automática

Você pode especificar uma estratégia de conversão que se aplica a todas as colunas usando o [gson][3] API. A biblioteca de cliente Android usa [gson][3] nos bastidores para serializar objetos Java para dados JSON antes que os dados são enviados ao serviço de aplicativo do Azure.  O código a seguir usa o método **setFieldNamingStrategy ()** para definir a estratégia. Esse exemplo excluirá o caractere inicial (um "m") e, em seguida, a minúscula do próximo caractere, para cada nome de campo. Por exemplo, ele poderia transformar "mId" em "id".  Implementar uma estratégia de conversão para reduzir a necessidade de anotações `SerializedName()` na maioria dos campos.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Esse código deve ser executado antes da criação de uma referência de cliente móvel usando o **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Introdução à autenticação]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
