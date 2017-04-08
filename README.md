# Plan zajęć

## Opis systemu

### Front - end

Część aplikacji działająca po stronie klienta jest wykonana w technologii **SPA** (Single Page Application). Aplikacja pobiera niezbędne dane z odpowiednich endpoint'ów **API** typu **REST**.

Część kliencka jest wykonana w oparciu o bibliotekę **AngularJS 1**, z routingiem obsługiwanym przez **ui-router**, a komunikacja z API poprzez **ng-resource**. Całość jest napisana w standardzie **ECMAScript 7** języka **JavaScript** i budowana przy użyciu **Webpack**.

Główny plik aplikacji, w którym, korzystając z funkcjonalności **webpack'a**, budowane są komponenty i usługi oraz konfiguracja aplikacji:

```javascript
import app from 'appModule';
import shared from 'shared';
import components from 'components';
import buildComponents from 'utils/buildComponents';
import buildShared from 'utils/buildShared';

import mockPromise from 'utils/mockPromise';
import mocks from 'mocks';

import routes from 'appRoutes';
import defaults from 'appDefaults';
import run from 'appRun';
import config from 'appConfig';

import styles from '../assets/styles/style.less'

window.App = app;

buildComponents(components);
buildShared(shared);

app.defaults = defaults; // global settings
app.config(config);

app.config(routes);

app.mockPromise = mockPromise;
app.mocks = mocks;

app.run(run);
```

Aplikacja składa się z komponentów, które są wyświetlane w ramach routingu na stronie. Za konfigurację routingu odpowiedzialny jest plik **appRoutes.js**:

```javascript
import components from 'components';

module.exports = function ($stateProvider, $locationProvider, $urlRouterProvider) {
    $locationProvider.html5Mode(true);

    $urlRouterProvider.otherwise('/');

    $stateProvider
        .state(angular.extend({ name: 'home', url: '/' }, components.home))
        .state(angular.extend({ name: 'schedule', url: '/schedule/:id/:type' }, components.schedule))
        .state(angular.extend({ name: 'dataEditor', url: '/data-editor' }, components.dataEditor));
}
```

## Opis odpytywanych danych

### 1. Prezentacja danych

- lista grup
    GET params: ()
    endpoint path: /group
    response:
    ```javascript
    [{
        name: 'Nazwa grupy',
        id: 'UUID',
        groups: [
            {
                name: 'Nazwa grupy',
                id: 'UUID',
                groups: [
                    
                ]
            }
        ]
    }]
    ```

- plan:
    GET params: (id: 'string', type: [ 'teacher' || 'group'])
    endpoint path = /schedule/:id/:type
    response: 
    ```javascript
    {
        name: 'Nazwa grupy || nauczyciela'
        schedule: [
            [{
                name: 'Nazwa przedmiotu',
                teacher: {
                    name,
                    id
                }
                classroom: {
                    name,
                    id
                },
                group: {
                    name,
                    id
                }
                type: [ 'lecture' || 'laboratories' || 'excercise' ],
                duration,
                startsAt
            }], // poniedziałek
            [] // dzień bez zajęć
        ],
        notScheduled: [
            {
                name,
                teacher: {
                    name,
                    id
                },
                type: [ 'lecture' || 'laboratories' || 'excercise' ],
                duration
            }
        ]
    }
    ```


### 2. Edytor
- komendy
    enpoint path: /command
    POST params: 
    ```javascript
    {
        type: [ 'add_study_plan' || 'add_classroom' || 'add_teacher' || 'select_start' ]
        data: {

        }
    }
    ```

    - type: 'add_study_plan':

      1. dodajemy kierunek 
      2. dodajemy semestr
      3. dodajemy grupę
      4. dodajemy przedmioty (czas trwania i typ) - wybór prowadzącego

      POST
      ```javascript
      data: {
          major: 'Nazwa kierunku',
          semesters: [
              subjects: [
                  {
                      name,
                      teacher_id,
                      duration : int (ilość bloków - blok trwa 15 min)
                      type: [ 'lecture' || 'laboratories' || 'excercise' ]
                  }
              ]
              subgroups: [
              {
		          name: '',
            	  subjects: [
                	  {
                          name,
                  	      teacher_id,
                	      duration : int (ilość bloków - blok trwa 15 min)
                          type: [ 'lecture' || 'laboratories' || 'excercise' ]  
                	  }]
		           subgroups: [{}] //And so on...
               }
          ]
      }
      ```
      response:
      ```javascript
      {
          id: "",
          major: 'Nazwa kierunku',
          semesters: [
              id: "",
              subjects: [
                  {
                      name,
                      teacher_id,
                      duration : int (ilość bloków - blok trwa 15 min)
                      type: [ 'lecture' || 'laboratories' || 'excercise' ]
                  }
              ]
              subgroups: [
              {
                  id: "",
		          name: '',
            	  subjects: [
                	  {
                          name,
                  	      teacher_id,
                	      duration : int (ilość bloków - blok trwa 15 min)
                          type: [ 'lecture' || 'laboratories' || 'excercise' ]  
                	  }]
		           subgroups: [{}] //And so on...
               }
          ]
      }
      ```
          
    - type: 'add_classroom'
    
      POST
      ```javascript
      data: {
          number
      }
      ```
      response:
      ```javascript
      {
          name: '',
          id: '',
      }          
      ```
      
    - type: 'add_teacher'

      POST
      ```javascript
      data: {
          name,
      surname,
          title // Wybrany z listy (endpoint /degree)
      }
      ```
      response:
      ```javascript
      {
          id: '',
          name: '',
          surname: '',
          title: ''
      }          
      ```

    - type: 'select_start'

      POST
      ```javascript
      data: {
          id, // id zajęć
          day : int (0 - 6)
          startsAt
      }
      ```
      response:
      ```javascript
      {
          id, // id zajęć
          day : int (0 - 6)
          startsAt
      }
      ```


- nauczyciele
    endpoint path: /teacher 
    
    GET

    response: 
    ```javascript
    [
        {
            id: '',
            name: '',
            surname: '',
            title: ''                
        }
    ]
    ```

- stopnie naukowe
    endpoint path: /degree 

    GET

    response:
    ```javascript
    [
        'dr',
        'dr hab.'
    ]
    ```

3. Układanie planu
    
    zapytanie do endpoint'u plan
    /time-suggestion/:classes_id

    GET

    response: 
    ```javascript
    [
        [ // poniedziałek
            {
                startsAt
            }
        ],
        [ // wtorek

        ]
    ]
    ```
