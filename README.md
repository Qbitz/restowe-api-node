# restowe-api-node

Struktura katalogów:
------

 - gulpfile.js
 - client/ *ma być ale na razie nie ruszamy*
 - server/
   - app.js *główny plik serwera requajerujący moduły z podkatalogów*
   - auth/ 
     - auth.module.js
   - account/
     - account.module.js
   - task
     - task.module.js
 - data.json *plik z danymi, który jest otwierany do czytania i zapisu, musisz zakładać lock na pliku*

Struktura pliku data.json:
---------

```
{
  users: [
    {
      id: 1,
      email: "janek@op.jp_na_100_pro",
      name: "Janek Małpa",
      password: "JWMRTkrhyf"
      tasks: [
        {
          id: 1,
          label: "zadanie"
        },
        {
          id: 2,
          label: "nowe zadanie"
        }
      ]
    }
  ]
}
```

Struktura restowego API:
------
