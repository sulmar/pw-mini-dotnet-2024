# Projekt aplikacji do wynajmu samochodów

## Opis
Projekt składa się z dwóch aplikacji: aplikacji pośredniczącej w wynajmie samochodów oraz aplikacji typu API, która umożliwia fizyczne wypożyczenie auta. 

Aplikacja pośrednicząca będzie pobierać dane z różnych źródeł, integrując oferty różnych firm wynajmujących samochody.
Po wyborze marki i modelu auta, użytkownik otrzyma szczegółową stronę z informacjami na temat pojazdu oraz możliwością sprawdzenia cen. Po kliknięciu "sprawdź ceny", użytkownik otrzyma listę ofert od różnych integratorów. Następnie może wybrać firmę, od której chce wynająć auto. Nasza aplikacja pełni jedynie rolę pośrednika, ułatwiając wybór i kontakt pomiędzy użytkownikiem a firmą wynajmującą.

Po wynajęciu auta użytkownik może sprawdzić swoje aktywne wypożyczenia i w dowolnym momencie zwrócić samochód. Po zwrocie, użytkownik otrzyma e-mail z potwierdzeniem zwrotu, a po jego zakończeniu – rachunek za usługę. Wynajem auta jest możliwy tylko dla zalogowanych użytkowników.

Drugi typ użytkownika to pracownik naszej firmy, który po zalogowaniu może sprawdzić wszystkie wypożyczone samochody, które są związane z naszą firmą. Aplikacja pozwala filtrować pojazdy, które są gotowe do zwrotu. Pracownik może potwierdzić zwrot, wpisując uwagi oraz dodając załącznik ze zdjęciem pojazdu.


## Wymagania ogólne
- Webowy interfejs użytkownika (aplikacja) dla aplikacji pośredniczącej
- Możliwość rejestracji za pomocą AzureAD lub innych systemów OpenId (Google, Facebook, własny)
- Podczas rejestracji należy podać dodatkowe informacje:
  - Lata posiadania prawa jazdy
  - Wiek
  - Lokalizacja
- Obowiązek korzystania z API firmy wynajmującej samochody dostarczonego przez prowadzącego
- Obowiązek użycia przynajmniej jednego API innej grupy
- Należy stworzyć własne API do wynajmu samochodów
- Należy spełnić wymagania biznesowe
- Należy spełnić wymagania techniczne

## Wymagani biznesowe (nazwy są przykładowe)

### Tryb użytkownika (zalogowanego lub nie)
- Strona główna (landing page): Na stronie startowej znajduje się wyszukiwarka samochodów, umożliwiająca wyszukiwanie po marce i modelu.
- Wyszukiwanie auta: Użytkownik może wybrać dostępne auta z listy, przy czym lista marek zależy od dostępnych modeli (dostarczanych przez zewnętrzne API). Lista modeli i marek może dynamicznie się zmieniać w zależności od danych z API.
- Stronicowanie ofert: W przypadku więcej niż 5 dostępnych ofert, aplikacja powinna umożliwiać stronicowanie wyników.
- Filtrowanie: Użytkownik może filtrować oferty, wybierając co najmniej jedną właściwość samochodu.
- Wyświetlanie szczegółów: Po wybraniu konkretnego auta, użytkownik ma możliwość zapoznania się z jego szczegółami.
- Zapytanie o ceny: Na stronie wybranego samochodu użytkownik może zapytać o ceny. Czas odpowiedzi na zapytanie nie może przekraczać 15 sekund. W trakcie oczekiwania należy wyświetlać wskaźnik postępu. Jeśli któryś z integratorów nie dostarczy oferty w określonym czasie, jego oferta zostanie pominięta.
- Prezentacja ofert: Po otrzymaniu wyników użytkownik zobaczy oferty z nazwami firm, z których pochodzą, oraz przyciskiem "Rent Me" umożliwiającym wypożyczenie auta. Dane wyszukiwania są ważne przez 10 minut.
- Logowanie przy wypożyczeniu: Po kliknięciu przycisku "Rent Me", użytkownik musi się zalogować, aby sfinalizować wypożyczenie.
- Potwierdzenie wypożyczenia: W przypadku udanego wypożyczenia, użytkownik zobaczy komunikat o sukcesie i zostanie przekierowany do szczegółów wynajętego auta.
- Obsługa błędów: W przypadku błędu podczas wypożyczania, aplikacja wyświetli odpowiedni komunikat o problemie.
- Historia wypożyczeń: Zalogowany użytkownik ma dostęp do listy wszystkich samochodów, które wypożyczył za pośrednictwem naszej aplikacji. Może również wejść w szczegóły każdego z nich.
- Zwrot auta: Na stronie szczegółów wypożyczonego samochodu użytkownik ma opcję zwrotu auta, co oznacza, że firma, od której wynajął pojazd, przyjedzie po niego.
- Powiadomienia e-mail:
  - Po zapytaniu o ceny, użytkownik otrzyma e-mail z linkiem ważnym 10 minut do danej oferty.
  - Po wypożyczeniu auta użytkownik dostanie e-mail potwierdzający sukces operacji i linkiem do zarządzania wynajętym pojazdem.
  - Po zwróceniu auta użytkownik zostanie poinformowany e-mailem, że proces zwrotu się rozpoczął.
  - Po zakończeniu zwrotu użytkownik otrzyma rachunek na swoją skrzynkę pocztową.

### Tryb pracownika
- Lista wypożyczonych samochodów: Pracownik naszej firmy może przeglądać listę samochodów wypożyczonych za pośrednictwem naszej platformy (oferty od innych integratorów są pomijane).
- Filtrowanie: Pracownik może filtrować samochody według tych, które są w trakcie wypożyczenia, oraz tych, które są gotowe do zwrotu.
- Szczegóły zwrotu: Pracownik może wejść w szczegóły samochodu, który jest do zwrotu.
- Potwierdzenie odbioru auta: Pracownik ma możliwość "odebrania" auta, co wymaga wpisania krótkiej notatki oraz dodania załącznika ze zdjęciem pojazdu.



## Opcje Car Rental API

1.	**Zwróć dostępne samochody**:
To API zwraca listę dostępnych samochodów. Zakłada się, że lista pozostaje niezmieniona przez 30 minut, co zapewnia spójność danych w tym czasie.
- Odpowiedź: Lista modeli i marek samochodów dostępnych do wynajmu, w tym metadane takie jak status dostępności i lokalizacja.
- Czas przechowywania w pamięci podręcznej: 30 minut (po stronie klienta)
2.	**Wygenerowanie oferty**:
To API generuje ofertę wynajmu dla wybranego samochodu na podstawie preferencji użytkownika, takich jak okres wynajmu i lokalizacja.
- Żądanie: ID samochodu, okres wynajmu, preferencje użytkownika (np. ubezpieczenie, dodatkowe usługi)
- Odpowiedź: Oferta zawierająca szczegóły dotyczące ceny, warunków wynajmu oraz informacje o firmie.
3.	**Wybór oferty**:
To API pozwala użytkownikowi wybrać konkretną ofertę wynajmu.
- Żądanie: ID oferty, ID użytkownika (dla zalogowanych użytkowników)
- Odpowiedź: Potwierdzenie wybranej oferty oraz ID wynajmu.
4.	**Wysłanie e-maila z ofertą do akceptacji**:
To API wysyła użytkownikowi e-mail z wybraną ofertą, umożliwiając jej akceptację w określonym przedziale czasowym.
5.	**Zwrot samochodu**:
To API rozpoczyna proces zwrotu samochodu, umożliwiając użytkownikowi oznaczenie samochodu jako gotowego do zwrotu.
- Żądanie: ID wynajmu, ID użytkownika
- Odpowiedź: Potwierdzenie rozpoczęcia procesu zwrotu.
6.	**Sprawdzenie statusu procesu wynajmu**:
To API pozwala użytkownikowi sprawdzić status procesu wynajmu.
- Żądanie: ID wynajmu, ID użytkownika
- Odpowiedź: Status procesu wynajmu.
7.**Potwierdzenie zwrotu samochodu**:
To API potwierdza zakończenie procesu zwrotu samochodu, zwykle obsługiwane przez pracownika, który weryfikuje zwrot.
- Żądanie: ID wynajmu, ID pracownika, notatki dotyczące zwrotu oraz wymagane załączniki (obraz lub PDF).
- Odpowiedź: Potwierdzenie pomyślnego zwrotu samochodu oraz wygenerowanie rachunku, który zostanie wysłany e-mailem.


## Opis elementów z minimalnym zakresem danych

### Lista dostępnych aut
- Zwracane dane: API powinno zwracać listę samochodów, przy czym każdy samochód musi zawierać następujące informacje:
  - Model: Nazwa modelu samochodu
  - Marka: Producent samochodu
  - Rok produkcji: Rok, w którym samochód został wyprodukowany
  - ID: Unikalny identyfikator samochodu

### Żądanie o cenę
- Wymagane dane: Żądanie o cenę musi zawierać:
  - ID auta: Unikalny identyfikator samochodu, dla którego zapytanie jest składane
  - Długość posiadania prawa jazdy: Informacja o tym, jak długo użytkownik posiada prawo jazdy
  - Wiek: Wiek użytkownika
  - Dodatkowe informacje: Dodatkowe informacje potrzebne do wyceny (np. preferencje dotyczące ubezpieczenia)
### Zwrócona cena
- Zwracane dane: Odpowiedź z API powinna zawierać:
  - ID oferty: Unikalny identyfikator oferty
  - Cena za dzień auta: Koszt wynajmu samochodu na jeden dzień
  - Cena obowiązkowego ubezpieczenia za dzień: Koszt ubezpieczenia obowiązkowego na jeden dzień
  - Czas ważności oferty: Okres ważności oferty, który nie może być krótszy niż 10 minut
### Wybór oferty
- Wymagane dane: Przy wyborze oferty muszą być przesłane następujące informacje:
  - Imię: Imię użytkownika
  - Nazwisko: Nazwisko użytkownika
  - Adres email: Adres email użytkownika
### Zwrot auta
- Wymagane dane: Przy zwrocie samochodu wymagane są:
  - Opis stanu auta: Tekstowy opis stanu zwracanego samochodu
  - Zdjęcie dokumentujące: Zdjęcie w formacie JPG, PNG lub plik PDF, dokumentujące stan samochodu


## Scenariusze

### Scenariusz nr 1 (wypożyczenie samochodu)

1. Użytkownik wchodzi na stronę głównę (landing page)

2. Wpisuje markę i model pojazdu i naciska "Sprawdź ceny"

3. Wysyłane jest zapytanie do zewnętrznych API (integratorów, w tym do naszego)

4. Wyświetlana jest lista ofert z cenami (prezentacja ofert)

5. Użytkownik po naciśnięciu przycisku "Rent Me" składa rezerwację

7. Po akceptacji przez pracownika użytkownik.  System generuje powiadomienie mailowe z linkiem czy użytkownik akceptuje (ważnym przez x minut)

8. Użytkownik po kliknięciu linka potwierdza wypożyczenie auta

9. System wysyłka maila do uzytkownika, że umowa została zawarta

### Scenariusz nr 2 (zwrot samochodu)

1. Zalogowany użytkownik wchodzi na stronę wypożyczonego samochodu

2. Użytkownik po naciśnięciu przycisku "Zwrot" zamawia odbiór auta

3. Pracownik wpisuje krótką notatkę oraz dodaje załącznik ze zdjęciem pojazdu i potwierdza odbiór.
(jedno czy wiele zdjęc?)

4. System generuje rachunek i wysyła go mailem wraz z potwierdzenie pomyślnego zwrotu samochodu


## Uwagi
1.	**Proces wypożyczenia**:
Proces wypożyczenia samochodu może potrwać, dlatego należy unikać prezentowania samochodu jako wypożyczonego, dopóki status wynajmu nie zostanie potwierdzony przez firmę przetwarzającą dane. Sugerowane rozwiązanie to uruchomienie zadania w tle (background job), które będzie regularnie sprawdzało status wypożyczenia.
2.	**Przechowywanie plików w chmurze**:
Wszystkie przesyłane pliki lub dane, które są zapisywane przez system, powinny być przechowywane w chmurze, aby zapewnić skalowalność i dostępność danych.
3.	**Wysyłanie e-maili**:
Do wysyłania e-maili należy wykorzystać zewnętrzną usługę, taką jak SendGrid lub AWS SES. Nie tworzymy własnego serwera SMTP.
4.	**Logowanie/Rejestracja przez systemy OpenID/OAuth**:
Użytkownicy powinni mieć możliwość logowania lub rejestracji za pomocą dowolnego systemu OpenID/OAuth, np. Google, Facebook lub innego dostawcy usług. Zapewnia to łatwość w obsłudze i zgodność z popularnymi metodami logowania.
5.	**Dane przy rejestracji**:
Podczas rejestracji użytkownika za pomocą jednego z systemów logowania (OpenID/OAuth), należy dodatkowo zebrać takie informacje jak wiek oraz długość posiadania prawa jazdy. Te dane są kluczowe dla procesu wynajmu.
6.	**Asynchroniczna komunikacja**:
Komunikacja powinna być maksymalnie asynchroniczna, aby zapewnić płynność działania systemu i lepszą obsługę zadań w tle, takich jak sprawdzanie statusu wypożyczenia czy przesyłanie plików.
7.	**Architektura systemu**:
Architektura systemu jest kluczowa, dlatego dokładny opis API nie jest obecnie najważniejszy. API będzie kluczowym elementem, ale powinno być opracowane w sposób elastyczny, uwzględniając możliwości skalowania i komunikacji asynchronicznej.



## Wymagania techniczne
### Front-end
- Responsywne
- Użycie technologii AJAX
- Spójne
- Hostowane na  Azure lub innej chmurze
### Back-end
- .NET 8
- Hostowane na  Azure lub innej chmurze
- Użycie bazy danych SQL z EntityFramework (lub podobny)
- Użycie AzureBlob (lub podobnego) do przechowania plików
- Użycie SendGrid (lub podobnego) do wysyłania maili
- Użycie OpenId dla autentykacji (lub innego opartego na OATH)
- Stworzenie własnego rodzaju autoryzacji lub użcyie OpenId
- Stworzenie własnego rodzaju autentykacji lub użcyie OpenId do zabezpieczenia swojego API 
  
## Technology stack
- SCRUM, git, git-flow, Azure DevOps
- Visual Studio\Visual Studio Code\Rider
- App Service, Azure SQL DB, Azure AD B2C, SendGrid
- ASP.NET Core MVC, ASP.NET Core API, SQL, Entity Framework
- REST, OAuth 2.0, OWIN
- PowerShell
- Unit Tests, Integration tests, UI tests, API tests
- CD/CI

