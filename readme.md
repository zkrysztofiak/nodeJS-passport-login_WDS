### ExpressJS - middleware

#### `middleware` ma 3 podstawowe przypadki
1. Ogólny middleware dla każdej trasy.
2. Middleware jako funkcja anonimowa dla konkretnej trasy.
3. Middleware jako funkcja nieanonimowa dla konkretnej trasy.

---

```JS
const express = require('express');
const app = express();

// Ogólny middleware dla każdej trasy
app.use((req, res, next) => {
  console.log(`Ogólny middleware - Czas: ${Date.now()}`);
  next();
});

// Middleware jako funkcja nieanonimowa
const logRequestDetails = (req, res, next) => {
  console.log(`Metoda: ${req.method}, URL: ${req.url}`);
  next();
};

// Middleware jako funkcja anonimowa dla konkretnej trasy
app.use('/special', (req, res, next) => {
  console.log('Trzeci middleware tylko dla /special');
  next();
});

// Trasa główna
app.get('/', (req, res) => {
  res.send('Hello World!');
});

// Trasa specjalna
app.get('/special', (req, res) => {
  res.send('Special route');
});

// Trasa użytkownika z middleware jako funkcją nieanonimową
app.get('/user/:id', logRequestDetails, (req, res) => {
  res.send(`User ID: ${req.params.id}`);
});
// można nawet kilka funkcji użyć, po kolei będą wykonane:
`app.get('/user/:id', logRequestDetails1, logRequestDetails2, logRequestDetails3, (req, res) => {.....}`

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});

```

---

#### Parsowania danych URL-encoded w przychodzących żądaniach HTTP

Parsowanie danych URL-encoded w przychodzących żądaniach HTTP jest istotne, ponieważ pozwala na wygodne i strukturalne przetwarzanie danych przesyłanych w żądaniach POST (najczęściej z formularzy HTML) w aplikacjach webowych. Oto dlaczego jest to ważne:

1. Przetwarzanie Danych Formularzy
Kiedy użytkownik wypełnia formularz na stronie internetowej i przesyła go, dane z formularza są kodowane w formacie URL-encoded i przesyłane do serwera w ciele żądania POST. Przykładowo:

```HTML
<form action="/submit" method="POST">
  <input type="text" name="username" value="john_doe">
  <input type="text" name="email" value="john@example.com">
  <button type="submit">Submit</button>
</form>
```

Po wysłaniu formularza, dane będą przesłane w formacie:


##### `username=john_doe&email=john@example.com`

2. Dostęp do Danych w Strukturalnej Formie
Parsowanie URL-encoded danych umożliwia dostęp do tych danych w strukturze obiektów JavaScript w kodzie serwera. Bez parsowania, dane te byłyby dostępne jako surowy ciąg tekstowy, co jest niewygodne i podatne na błędy przy ręcznym przetwarzaniu.

Przykład bez parsowania:

```JS
app.post('/submit', (req, res) => {
  // Bez middleware `express.urlencoded()` lub `express.json()`, `req.body` jest `undefined`
  console.log(req.body); // undefined
  res.send('Data received');
});
```

Przykład z parsowaniem:

```JS
app.use(express.urlencoded({ extended: false }));

app.post('/submit', (req, res) => {
  // req.body jest teraz obiektem JS z danymi z formularza
  console.log(req.body); // { username: 'john_doe', email: 'john@example.com' }
  res.send('Data received');
});
```

3. Łatwość Pracy z Danymi
Po parsowaniu dane są dostępne jako obiekt JavaScript, co umożliwia łatwe i intuicyjne operacje na nich:

- Walidacja danych: Możesz łatwo sprawdzić, czy wszystkie wymagane pola są wypełnione i czy mają odpowiednie wartości.
- Przetwarzanie danych: Możesz przetwarzać dane, np. zapisywać je do bazy danych, przesyłać do innych usług, czy wykonywać inne operacje biznesowe.
Przykład walidacji danych:

```JS
app.post('/submit', (req, res) => {
  const { username, email } = req.body;
  
  if (!username || !email) {
    return res.status(400).send('Missing required fields');
  }

  // Przetwarzanie danych
  // ...

  res.send('Data received and processed');
});
```

4. Bezpieczeństwo
Parsowanie danych również pozwala na zaimplementowanie dodatkowych środków bezpieczeństwa, takich jak:

- Sanityzacja danych: Usuwanie potencjalnie niebezpiecznych znaków, które mogą prowadzić do ataków XSS lub SQL Injection.
- Walidacja typów danych: Upewnienie się, że dane są w odpowiednim formacie i typie, zanim zostaną użyte w aplikacji.
Podsumowanie
Parsowanie danych URL-encoded w przychodzących żądaniach HTTP jest kluczowe, ponieważ umożliwia:

#### Podsumowanie:
#####  `app.use(express.urlencoded({ extended: false }));`
Parsowanie danych `URL-encoded` w przychodzących żądaniach HTTP jest kluczowe, ponieważ umożliwia:

- Łatwe przetwarzanie i dostęp do danych formularzy w strukturze obiektów JavaScript.
- Walidację i przetwarzanie danych w sposób bezpieczny i zorganizowany.
- Umożliwienie aplikacjom webowym korzystania z danych w sposób efektywny i bezpieczny.
Bez tego kroku dane przesyłane przez użytkowników byłyby trudniejsze do obsługi i podatne na błędy oraz potencjalne zagrożenia bezpieczeństwa.
- Wbudowane middleware takie jak `express.json()` i `express.urlencoded()` automatycznie zarządzają przepływem kontroli i nie wymagają wywoływania `next()` ręcznie. W przypadku własnych funkcji middleware, musisz wywołać next(), aby przekazać kontrolę do następnego middleware w stosie. Kolejność deklaracji middleware jest istotna, ponieważ middleware są wykonywane w tej kolejności, w jakiej są zadeklarowane.
W przypadku danych URL-encoded używa się `express.urlencoded()`, a dla danych JSON `express.json()`.
- Parsowanie odnosi się do ogólnego procesu analizy danych w celu przekształcenia ich do bardziej użytecznego formatu. Parsowanie może dotyczyć przekształcania danych tekstowych, takich jak pliki JSON, XML, CSV, a także innych formatów. Parsowanie jest szerszym pojęciem, które obejmuje deserializację jako jeden z jego przypadków
- Serializacja to proces przekształcania obiektu w pamięci komputera do formatu, który można łatwo przechowywać lub przesyłać. Ten format może być tekstowy (np. JSON, XML) lub binarny. Serializacja umożliwia zapisanie stanu obiektu do pliku, bazy danych lub przesłanie go przez sieć, a następnie odtworzenie tego obiektu później (proces deserializacji).


---

##### WDS Learn Express JS In 35 Minutes
https://www.youtube.com/watch?v=SccSCuHhOw0
https://github.com/WebDevSimplified/express-crash-course

##### WDS JWT Authentication Tutorial - Node.js  
https://www.youtube.com/watch?v=mbsmsi7l3r4
https://github.com/WebDevSimplified/JWT-Authentication