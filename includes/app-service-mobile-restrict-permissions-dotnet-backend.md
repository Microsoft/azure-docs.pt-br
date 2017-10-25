
Por padrão, APIs em um back-end de Aplicativos Móveis podem ser chamadas de forma anônima. Em seguida, você precisa restringir o acesso somente aos clientes autenticados.  

* **Back-end do Node.js (por meio do portal do Azure)** :  

    Nas configurações de seus Aplicativos Móveis, clique em **Tabelas Fáceis** e selecione a tabela. Clique em **Alterar permissões**, selecione **Apenas acesso autenticado** para todas as permissões e clique em **Salvar**.
* **Back-end do .NET (C#)**:  

    No projeto do servidor, navegue até **Controladores** > **TodoItemController.cs**. Adicione o atributo `[Authorize]` à classe **TodoItemController** , como a seguir. Para restringir o acesso somente aos métodos específicos, você também pode aplicar esse atributo apenas aos métodos, em vez de à classe. Republicar o projeto de servidor.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end do Node.js (por meio de código Node.js)** :  

    Para exigir autenticação para acesso à tabela, adicione a seguinte linha ao script de servidor Node.js:

        table.access = 'authenticated';

    Para obter mais detalhes, veja [Como exigir autenticação para acesso às tabelas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para saber como baixar o projeto de código de início rápido do seu site, consulte [Como baixar o projeto de código de início rápido de back-end do Node.js usando Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
