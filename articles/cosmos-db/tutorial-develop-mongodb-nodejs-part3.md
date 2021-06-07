---
title: Criar a interface do usuário do aplicativo Angular com a API do Azure Cosmos DB para MongoDB (Parte 3)
description: Parte 3 da série de tutoriais sobre como criar um aplicativo do MongoDB com Angular e Node no Azure Cosmos DB usando exatamente as mesmas APIs usadas para MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 181af5cf26d19a9f51e8d456e777badf7efa224d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097814"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Criar um aplicativo Angular com a API do Azure Cosmos DB para MongoDB – criar a interface do usuário com o Angular
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial com várias partes demonstra como criar um aplicativo escrito em Node.js com o Express e o Angular e conectá-lo à sua [conta do Cosmos configurada com a API do Cosmos DB para MongoDB](mongodb-introduction.md).

A Parte 3 do tutorial se baseia na [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) e inclui as seguintes tarefas:

> [!div class="checklist"]
> * Criar a interface do usuário em Angular
> * Usar CSS para definir a aparência
> * Testar o aplicativo localmente

## <a name="video-walkthrough"></a>Passo a passo em vídeo

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, verifique se você concluiu as etapas na [Parte 2](tutorial-develop-mongodb-nodejs-part2.md) do tutorial.

> [!TIP]
> Este tutorial percorre passo a passo com você as etapas para compilar o aplicativo. Se você deseja baixar o projeto concluído, pode obter o aplicativo concluído no [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) do GitHub.

## <a name="build-the-ui"></a>Criar a interface do usuário

1. No Visual Studio Code, clique no botão Parar :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png"::: para parar o aplicativo Node. 

2. No prompt de comando do Windows ou janela Terminal do Mac, digite o comando a seguir para gerar um componente heroes. Nesse código, g = gerar, c = componente, heroes = nome do componente, e ele está usando uma estrutura de arquivo simples (--flat) para que uma subpasta não seja criada para ele.

    ```
    ng g c heroes --flat 
    ```

    A janela de terminal exibe a confirmação dos novos componentes.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="Instalar o componente hero":::

    Vamos examinar os arquivos que foram criados e atualizados. 

3. No Visual Studio Code, no painel **Explorer**, navegue até a nova pasta **src\app** e abra o novo arquivo **heroes.component.ts** gerado na pasta do aplicativo. Esse arquivo de componente TypeScript foi criado pelo comando anterior.

    > [!TIP]
    > Se a pasta do aplicativo não for exibida no Visual Studio Code, digite CMD+SHIFT+P em um Mac ou Ctrl+Shift+P no Windows para abrir a Paleta de Comando e digite *Recarregar Janela* para acompanhar a alteração do sistema.

4. Na mesma pasta, abra o arquivo **app.module.ts** e observe que ele adicionou o `HeroesComponent` às declarações na linha 5 e também o importou na linha 10.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="Abrir o arquivo app-module.ts":::

5. Volte para o arquivo **heroes.component.html** copie nesse código. O `<div>` é o contêiner para toda a página. Dentro do contêiner, há uma lista de heroes que precisamos criar para que, ao clicar em um, você possa selecioná-lo e editá-lo ou excluí-lo na interface do usuário. Em seguida, no HTML, temos alguns estilos para que você possa saber qual foi selecionado. Também há uma área de edição para que você possa adicionar um novo hero ou editar uma existente. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Agora que temos o HTML, precisamos adicioná-lo ao arquivo **heroes.component.ts** para que possa interagir com o modelo. O código a seguir adiciona o modelo para ao arquivo do componente. Um construtor que obtém alguns heroes e inicializa o componente de serviço hero para obter todos os dados foi adicionado. Esse código também adiciona todos os métodos necessários para tratar de eventos na interface do usuário. Você pode copiar o código a seguir por cima do código existente em **heroes.component.ts**. Devem ser encontrados erros nas áreas Hero e HeroService já que os componentes correspondentes ainda não foram importados, esse erro será corrigido na próxima seção. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. Em **Explorer**, abra o arquivo **app/app.module.ts** e atualize a seção de importações para adicionar uma importação para um `FormsModule`. A seção de importação deve ficar assim:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. No arquivo **app/app.module.ts** adicione uma importação ao novo módulo FormsModule na linha 3. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Usar CSS para definir a aparência

1. No painel do Explorer, abra o arquivo **src/styles.scss**.

2. Copie o código a seguir no arquivo **styles.scss**, substituindo o conteúdo existente do arquivo.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Salve o arquivo. 

## <a name="display-the-component"></a>Exibir o componente

Agora que temos o componente, como fazemos para exibi-lo na tela? Vamos modificar os componentes padrão em **app.component.ts**.

1. No painel Explorer, abra **/app/app.component.ts**, altere o título para Heroes e insira o nome do componente que criamos em **heroes.components.ts** (app-heroes) para se referir a esse novo componente. Agora, o conteúdo do arquivo deve estar da seguinte maneira: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. Há outros componentes de **heroes.components.ts** que estamos referindo, como o componente Hero e, portanto, precisamos criá-lo também. No prompt de comando da CLI do Angular, use o comando a seguir para criar um modelo de hero e um arquivo chamado **hero.ts**, em que g = gerar cl = classe e hero = nome da classe.

    ```bash
    ng g cl hero
    ```

3. No painel do Explorer, abra **src\app\hero.ts**. Em **hero.ts**, substitua o conteúdo do arquivo pelo código a seguir, que adiciona uma classe Hero com uma ID, um nome e uma fala.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Volte para **heroes.components.ts** e observe que a linha `selectedHero: Hero;` (linha 10), `Hero` tem uma linha vermelha embaixo. 

5. Clique no termo `Hero`, e o Visual Studio exibe um ícone de lâmpada no lado esquerdo do bloco de código. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Lâmpada no Visual Studio Code":::

6. Clique na lâmpada e clique em **Importar Hero de “/app/hero”.** ou **Importar Hero de "./hero".** (A mensagem muda dependendo da sua configuração)

    Uma nova linha de código aparece na linha 2. Se a linha 2 faz referência a /app/hero, modifique-a para que faça referência ao arquivo de hero na pasta local (./hero). A linha 2 deve ser assim:

   ```
   import { Hero } from "./hero";
   ``` 

    Que cuida do modelo, mas ainda precisamos criar o serviço.

## <a name="create-the-service"></a>Criar o serviço

1. No prompt de comando da CLI do Angular, digite o comando a seguir para criar um serviço hero em **app.module.ts**, em que g = gerar, s = serviço, hero = nome do serviço, -m = put no app.module.

    ```bash
    ng g s hero -m app.module
    ```

2. No Visual Studio Code, volte para **heroes.components.ts**. Observe que na linha `constructor(private heroService: HeroService) {}` (linha 13), `HeroService` tem uma linha vermelha embaixo. Clique em `HeroService` para obter a lâmpada no lado esquerdo do bloco de código. Clique na lâmpada e clique em **Importar HeroService de "./hero.service".** ou **Importar HeroService de “/app/hero.service”.**

    Clicar na lâmpada insere uma nova linha de código na linha 2. Se a linha 2 faz referência à pasta /app/hero.service, modifique-a para que faça referência ao arquivo de hero na pasta local (./hero.service). A linha 2 deve ser assim:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. No Visual Studio Code, abra **hero.service.ts** e copie o código a seguir, substituindo o conteúdo do arquivo.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Este código usa a versão mais recente do HttpClient que o Angular oferece, que é um módulo que você precisa fornecer e, portanto, vamos fazer isso em seguida.

4. No Visual Studio Code, abra **app.module.ts** e importe o HttpClientModule atualizando a seção de importação para incluir HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. Em **app.module.ts**, adicione a instrução de importação HttpClientModule à lista de importações.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Salve todos os arquivos no Visual Studio Code.

## <a name="build-the-app"></a>Compilar o aplicativo

1. No prompt de comando, digite o comando a seguir para compilar o aplicativo em Angular. 

    ```bash
    ng b
    ``` 

    Se houver algum problema, a janela do terminal exibirá informações sobre os arquivos a serem corrigidos. Quando a compilação for concluída, os novos arquivos vão para a pasta **dist**. Você pode examinar os arquivos novos na pasta **dist** se desejar.

    Agora vamos executar o aplicativo.

2. No Visual Studio Code, clique no botão **Depurar** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: do lado esquerdo e no botão **Parar Depuração** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png":::.

3. Abra um navegador da Internet e navegue até **localhost:3000** para ver o aplicativo em execução localmente.

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="Aplicativo Hero executado localmente":::

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Criou a interface do usuário Angular
> * Testou o aplicativo localmente

Você pode prosseguir para a próxima parte do tutorial a fim de criar uma conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Criar uma conta do Azure Cosmos DB usando a CLI do Azure](tutorial-develop-mongodb-nodejs-part4.md)
