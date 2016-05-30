# restowe-api-node

 - http://momentjs.com/

Struktura katalogów:
======

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
======

```
{
  users: [
    {
      id: 1,
      email: "janek@op.jp_na_100_pro",
      name: "Janek Małpa",
      password: "JWMRTkrhyf",
      salt: "asdasdasd",
      api_key: "asdasdasd",
      api_key_expires: "2018-06-06 20:11:00Z",
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
======

Api nie autoryzowan:
------

*POST /account*

 - służy do stworzenia nowego konta
 - wysyłasz ```{ email: "", name: "", password: "" }```
 - jeśli wszystko ok zwracasz 200 i ```{ api_key: "", api_key_expires: "" }```
 - jeśli coś nie bangla zwracasz 400

*POST /auth/login*

 - Wysyłasz ```{ email: "", password: "" }```
 - Walidujesz zgodnosc z plikiem data.json
 - hasła mają być solone i haszowane do sha512
 - Jeśli dane sie zgadzaja
   - zapisujesz nowy api key [ base64_encode(sha512((new Date()).valueOf() + 30 * 60 * 1000) + password) ], ustawiasz date na 30 min w przyszlosc [(new Date()).valueOf() + 30 * 60 * 1000]
   - Wysyłasz odpowiedz 200 z odpowiedzia ```{ api_key: "", api_key_expires: "" } ```
 - Jeśli się nie zgadzają wysyłasz 400

Api autoryzowane:
------

Tutaj zawsze wysyłasz nagłówek http X-API-KEY: "asdasdsdas"
 - jeśli podany w nagłówku api\_key jest pusty albo nie istnieje użytkownik z takim api\_key zwracasz 401
 - musisz porównywać czas wygaśnięcia sesji z aktualnym
 - jeśli sesja wygasła zwracasz 401

*POST /auth/logout*

 - zawsze zwracasz 200
 - jeśli znajdziesz użytkownika o podanym api_key - resetujesz api\_key do pustego stringa i ustawiasz czas wygaśnięcia na null

*GET /account*

 - jeśli użytkownik jest zalogowany zwraca 200 z odpowiedzią: ```{ email: "", name: "" }```

*PUT /account*
 
 - służy do zmiany hasła
 - wysyłasz ```{ old_password: "", new_password: "" }```
 - zwracasz 400 jeśli nowe hasło ma niepoprawną długość
 - zwracasz 409 jesli stare hasło się nie zgadza
 - jeśli wszystko gra, haszujesz i zapisujesz nowe hasło  i wysyłasz użytkownikowi 200

*DELETE /account*

 - usun konto
 - wysyłasz hasło: ```{ password: "" }```
 - jesli zle 400
 - jesli ok usuwasz konto i wszystkie taski i zwracasz 204

*GET /tasks*

 - zwraca tablicę wszystkich tasków użytkownika
 - zwraca 200

*GET /tasks/:id*
 
 - zwraca task o zadanym ID
 - jeśli użytkownik poda nie swoje ale istniejące ID taska zwracasz 403
 - jesli task o zadanym ID nie istnieje zwracasz 404
 - zwraca 200

*POST /tasks*

 - tworzy nowy task
 - wysyłasz ```{ label: "" }```
 - jeśli label jest pusty zwracasz 400
 - jeśli wszstko OK zwracasz 200 i ```{ id: "", label: "" }```

*PUT /tasks/:id*
 
 - edytuje istniejący task
 - wysyłasz ```{ label: "" }```
 - jeśli label jest pusty zwracasz 400
 - jesli task o zadanym ID nie istnieje zwracasz 404
 - jeśli użytkownik poda nie swoje ale istniejące ID taska zwracasz 403
 - jeśli wszystko ok zwracasz 204

*DELETE /tasks/:id*

 - usuwa task o zadanym ID
 - jesli task o zadanym ID nie istnieje zwracasz 404
 - jeśli użytkownik poda nie swoje ale istniejące ID taska zwracasz 403
 - jeśli wszystko ok zwracasz 204
