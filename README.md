# INFIGRID

`[Angular CLI] version 9.0.2`

## Demo

La grille est visible sur ce lien [infigrid demo](https://infigrid.azurewebsites.net/)

## Build et serveur de Dev

- Lancer le build de developpement : `npm run build:dev`
- Lancer le serveur de développement : `ng serve` et aller à `http://localhost:4200/`
- Lancer le build de production : `npm run build:prod` , Le build sera dans le dossier `dist/`.
- Lancer les tests unitaires : `npm run test`
- Lancer les tests e2e : `npm run test:ui`
- Lancer le Lint : `npm run lint`

## INSTALLATION

1.  `npm i --save infigrid @angular/material bootstrap @ng-bootstrap/ng-bootstrap`

2.  Style :

    - Importer le thème material de votre choix dans styles.css `@import "@angular/material/prebuilt-themes/indigo-pink.css"`;
    - Importer `@import "bootstrap/dist/css/bootstrap.min.css"`; dans styles.css
    - Importer material icons dans le fichier `index.html`
    - `<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet" />`

## GUIDE UTILISATION

1.  Importer le module InfigridModule et BrowserAnimationsModule dans votre app module `app.module.ts`

    ```typescript
    import { InfigridModule } from 'infigrid';
    import { DatePipe } from '@angular/common';
    @NgModule({
      declarations: [AppComponent],
      imports: [BrowserModule, InfigridModule, BrowserAnimationsModule],
      providers: [DatePipe],
      bootstrap: [AppComponent]
    })
    export class AppModule {}
    ```

2.  Dans le fichier typescript de votre `component.ts`

    ```typescript
    // ...
    import { InfigridConfig, InfigridColumn } from 'infigrid';
    import { DatePipe } from '@angular/common';

    const ELEMENT_DATA = [
      {
        position: 1,
        name: 'Hydrogen',
        weight: 1.0079,
        symbol: 'H',
        type: 'A',
        good: true
      },
      {
        position: 2,
        name: 'Helium',
        weight: 4.0026,
        symbol: 'He',
        type: 'A',
        good: false
      },
      {
        position: 3,
        name: 'Lithium',
        weight: 6.941,
        symbol: 'Li',
        type: 'B',
        good: true
      }
    ];
    @Component({
      //..
    })
    export class PropertiesComponent implements OnInit {
      /*
            interface InfigridConfig = {
            pageSize?: number;
            pageSizeOptions?: number[];
            enableFiltering?: boolean;
            externalFiltering?: boolean;
            externalPaging?: boolean;
            defaultLocale: 'en' | 'fr';
            dateSeparator: '-' | '/';
            externalPagingOptions?: {
            totalElements: number;
            totalPages: number;
            pageIndex: number;
            };
            showSelection?: boolean;
            showAddButton?: boolean;
            showDeleteButton?: boolean;
            showEditButton?: boolean;
            headerBgColor?: string;
            hasInsertConfirmationButton?: boolean;
            hasUpdateConfirmationButton?: boolean;
            customEditTemplate?: boolean;
            customAddTemplate?: boolean;
            rowBg?: string;
            confirmAction?: boolean;
        } */

      public tableConfig: InfigridConfig = {
        pageSize: 21, // nombre d'element par page
        pageSizeOptions: [3, 6, 9, 12, 15, 18, 21],
        enableFiltering: true,
        externalPaging: false,
        externalFiltering: false, // doit etre true si externalPaging: true
        externalPagingOptions: {
          // doit etre renseigner lorsque externalPaging: true
          totalElements: 0,
          totalPages: 0,
          pageIndex: 0
        },
        showSelection: true,
        showEditButton: true,
        showDeleteButton: true,
        showAddButton: true,
        rowBg: '#eee',
        confirmAction: true
      };

      /*interface InfigridColumn {
        type: "string" | "number" | "boolean" | "select" | "date";
        prop: string;
        name: string;
        resizable?: boolean;
        sortable?: boolean;
        selectValues?: any[];
        } */

      public columns: InfigridColumn[] = [
        { prop: 'position', name: 'Position', type: 'number' },
        { prop: 'name', name: 'Nom', type: 'string' },
        { prop: 'weight', name: 'Poids', type: 'number' },
        { prop: 'symbol', name: 'Symbole', type: 'string' },
        { prop: 'type', name: 'Type', type: 'select' },
        { prop: 'good', name: 'Utile', type: 'boolean' },
        { prop: 'discoveryDate', name: 'Date découverte', type: 'date' }
      ];

      public data: any[] = [];

      constructor(private datepipe: DatePipe) {
        // Cette manoeuvre consiste a ajouter une proprieté `discoveryDate` pour chaque objet dans la liste
        // Vous n'etes pas obliger la faire
        this.data = ELEMENT_DATA.map((_, index) =>
          index % 2 === 0
            ? {
                ..._,
                discoveryDate: this.datepipe.transform(
                  new Date(new Date().setDate(new Date().getDate() + index)),
                  'dd-MM-yyyy'
                )
              }
            : {
                ..._,
                discoveryDate: this.datepipe.transform(
                  new Date(new Date().setDate(new Date().getDate() + index)),
                  'dd-MM-yyyy'
                )
              }
        );
      }

      ngOnInit() {}
    }
    ```

3.  Dans le fichier `component.html` de votre composant ajouter le composant infigrid

    ```html
    <infigrid
      (delete)="on_delete($event)"
      [data]="data"
      [tableConfig]="tableConfig"
      [columns]="columns"
      (edit)="edit($event)"
    >
    </infigrid>
    ```

## Comprendre la configuration

| Propriété                     |                      Default                      |                                            Description                                             |       Example       | Deprecie |
| ----------------------------- | :-----------------------------------------------: | :------------------------------------------------------------------------------------------------: | :-----------------: | -------: |
| `PageSize`                    |                       `10`                        |                               Le nombre de ligne à afficher par page                               |   `nombre entier`   |       No |
| `PageSizeOptions`             |                  `[10, 20, 30]`                   |                     Tableau des nombres de lignes possible à afficher par page                     | `[10, 20, 50, 100]` |       No |
| `enableFiltering`             |                      `true`                       |                              Permet d'afficher ou cacher les filtres                               |  `true` - `false`   |       No |
| `externalPaging`              |                      `false`                      |                     Permet de definir si la pagination est interne ou externe                      |  `true` - `false`   |       No |
| `externalPagingOptions`       | `{totalElements: 0, totalPages: 0, pageIndex: 0}` |                               contient les donnees de la pagination                                |                     |          |
| `showSelection`               |                      `false`                      |                      Permet d'afficher ou cacher les `checkbox de selection`                       |  `true` ou `false`  |          |
| `showEditButton`              |                      `false`                      |                          Permet d'afficher ou cacher le `bouton modifier`                          |  `true` ou `false`  |          |
| `showDeleteButton`            |                      `false`                      |                         Permet d'afficher ou cacher le `bouton supprimer`                          |  `true` ou `false`  |          |
| `showAddButton`               |                      `false`                      |                           Permet d'afficher ou cacher le `button cacher`                           |  `true` ou `false`  |          |
| `confirmaAction`              |                      `false`                      | Permet d'afficher ou cacher `la boite de dialog de confirmation` lors de la suppression des lignes |  `true` ou `false`  |          |
| `customAddTemplate`           |                      `false`                      |                          Permet de personnaliser le template de creation                           |  `true` ou `false`  |          |
| `customEditTemplate`          |                      `false`                      |                        Permet de personnaliser le template de modification                         |  `true` ou `false`  |          |
| `defaultLocale`               |                       `fr`                        |                 Permet de choisir le locale à utiliser pour l'affichage des dates                  |    `fr` ou `en`     |          |
| `dateSeparator`               |                        `-`                        |                                       Le séparateur de date                                        |     `-` ou `/`      |          |
| `hasUpdateConfirmationButton` |                      `false`                      |           Permet d'afficher ou cacher le `bouton de sauvegarde` pendant la modification            |  `true` ou `false`  |          |
| `hasInsertConfirmationButton` |                      `false`                      |             Permet d'afficher ou cacher le `bouton de sauvegarde` pendant la création              |  `true` ou `false`  |          |

## Pagination server et filtre server

- Dans le `component.html` du composant ajoutez les evenements `(onPage)` et `(filters)` comme ci-dessous

  ```html
  <infigrid
    (delete)="on_delete($event)"
    [data]="data"
    [tableConfig]="tableConfig"
    [columns]="columns"
    (edit)="edit($event)"
    (onPage)="onPaging($event)"
    (filters)="onFiltering($event)"
  >
  </infigrid>
  ```

- Dans le `component.ts` du composant

  ```typescript
  // ...
  import { HttpClient } from "@angular/common/http";
  import { InfigridFilter, InfigridColumn, InfigridConfig } from 'infigrid';

  export interface Page {
    // The number of elements in the page
    pageSize: number;
    // The total number of elements
    totalElements: number;
    // The total number of pages
    totalPages: number;
    // The current page number
    pageIndex: number;
  }
  export interface ResponseData {
    data: Array<any>;
    pagination: Page;
  }

  export interface RequestPayload {
    pagination: {
        pageIndex: number;
        pageSize: number;
    };
    filters: { [colName: string]: any };
    columns: string[];
  }

  @Component({
    // ...
  })
  export class PropertiesComponent implements OnInit {
        // ...

        public tableConfig: InfigridConfig = {
        // ...
            externalPaging: true,
            externalFiltering: true, // doit etre true si externalPaging: true
            externalPagingOptions: { // doit etre renseigner lorsque externalPaging: true
                totalElements: 0,
                totalPages: 0,
                pageIndex: 0,
            };
        // ...
        };

        // ...

        private filterPayload = {};
        private columnListTodisplay = ["position", "name", "symbol", "discoveryDate"],
        constructor(private http: HttpClient) {}
        ngOnInit() {
            this.getData({
            columns: this.columnListTodisplay,
            filters: this.filterPayload,
            pagination: {
                pageIndex: 0,
                pageSize: 21
            }
            });
        }

        getData(payload: RequestPayload) {
            const url: string = "http://localhost:3000/api/products";
            this.http.post(url, payload).subscribe((res: ResponseData) => {
            this.data = res.data;
            let { pageSize, pageIndex, totalElements, totalPages } = res.pagination;
            this.tableConfig.pageSize = pageSize;
            this.tableConfig.externalPagingOptions.pageIndex = pageIndex;
            this.tableConfig.externalPagingOptions.totalElements = totalElements;
            this.tableConfig.externalPagingOptions.totalPages = totalPages;
            });
        }

        onFiltering(filters: { [columnName: string]: InfigridFilter }) {
            this.filterPayload = filters;
            this.getData({
            columns: ["position", "name", "symbol", "discoveryDate"],
            filters: filters,
            pagination: {
                pageIndex: this.tableConfig.externalPagingOptions.pageIndex,
                pageSize: this.tableConfig.pageSize
            }
            });
        }

        onPaging(event) {
            this.getData({
            columns: this.columnListTodisplay,
            filters: this.filterPayload,
            pagination: {
                pageIndex: event.pageIndex,
                pageSize: event.pageSize
            }
            });
        }
      // ...
  }
  ```
