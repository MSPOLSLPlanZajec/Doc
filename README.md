1. Prezentacja danych

    - lista grup
        
        GET params: ()
        
        endpoint path: /group

        response:
        ```
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
        ```
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


2. Edytor
    - komendy

        enpoint path: /command

        POST params: 
        ```
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
          ```
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
          Response
          ```
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
          ```
          data: {
              number
          }
          ```
          Response
          ```
          {
              name: '',
              id: '',
          }          
          ```
          
        - type: 'add_teacher'

          POST
          ```
          data: {
              name,
	      surname,
              title // Wybrany z listy (endpoint /degree)
          }
          ```
          Response
          ```
          {
              id: '',
              name: '',
              surname: '',
              title: ''
          }          
          ```

        - type: 'select_start'

          POST
          ```
          data: {
              id, // id zajęć
              day : int (0 - 6)
              startsAt
          }
          ```
          Response
           ```
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
        ```
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
        ```
        [
            'dr',
            'dr hab.' itd.
        ]
        ```

3. Układanie planu
    
    zapytanie do endpoint'u plan

    /time-suggestion/:classes_id

    GET

    response: 
    ```
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
