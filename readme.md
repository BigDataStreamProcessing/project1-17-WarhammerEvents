# Charakterystyka danych
W ramach gier rozgrywających się w uniwersum *Warhammer Fantasy*, 
na całym świecie przeprowadzane są bitwy pomiędzy różnymi frakcjami. 

W strumieniu pojawiają się zdarzenia zgodne ze schematem `WarhammerEvent`.

```
create json schema WarhammerEvent(location string, attack_faction string, 
       defend_faction string, attack_number_of_units int, 
       defend_number_of_units int, winner int, ets string, its string);  
```

Każdy element strumienia reprezentuje wynik bitwy między różnymi frakcjami. 

Poza wskazaniem zwycięzcy zdarzenie zawiera informacje na temat 
lokalizacji bitwy, nazwach frakcji biorących udział w bitwie, liczbie 
jednostek dla każdej z frakcji.

Dane uzupełnione są o dwie etykiety czasowe.

- Pierwsza (`ets`) związana jest z momentem zakończenia bitwy.
  Etykieta ta może się losowo spóźniać w stosunku do czasu systemowego 
  maksymalnie do 10 sekund.
- Druga (`its`) związana jest z momentem rejestracji bitwy w systemie.


# Opis atrybutów

Atrybuty w każdym zdarzeniu zgodnym ze schematem `WarhammerEvent` 
mają następujące znaczenie:

* `location` - nazwa lokacji
* `attack_faction` - nazwa frakcji atakującej
* `defend_faction` - nazwa frakcji broniącej
* `attack_number_of_units` - liczba jednostek frakcji atakującej
* `defend_number_of_units` - liczba jednostek frakcji broniącej
* `winner` - informacja kto wygrał (1-frakcja atakująca lub 2-frakcja broniąca)
* `ets` - data zakończenia bitwy
* `its` - data rejestracji bitwy

# Zadania
Opracuj rozwiązania poniższych zadań. 
* Opieraj się strumieniu zdarzeń zgodnych ze schematem `WarhammerEvent`
* W każdym rozwiązaniu możesz skorzystać z jednego lub kilku poleceń EPL.
* Ostatnie polecenie będące ostatecznym rozwiązaniem zadania musi 
  * być poleceniem `select` 
  * posiadającym etykietę `answer`, przykładowo:

```sql
  @name('answer') SELECT location, attack_faction, defend_faction,
     attack_number_of_units, defend_number_of_units, winner, 
     ets, its
  FROM WarhammerEvent#ext_timed(java.sql.Timestamp.valueOf(its).getTime(), 3 sec)
```

## Zadanie 1
Chcielibyśmy wiedzieć, która z atakujących frakcji jest najbardziej agresywna.
W tym celu chcemy wiedzieć, jaka sumaryczna liczba jednostek została 
wykorzystana do ataku zarejestrowanego dla każdej z frakcji 
w trakcie ostatnich 10 sekund.

Wyniki powinny zawierać następujące kolumny:
- `number_of_all_attack_units` - sumę wszystkich jednostek atakujących
- `attack_faction` - nazwę atakującej frakcji

## Zadanie 2
Wykrywaj przypadki, kiedy frakcja, która miała mniej jednostek, wygrała bitwę.

Remisy nie są traktowane jako zwycięstwo.

Wyniki powinny zawierać wszystkie kolumny dotyczące zakończonej bitwy:
* `location` - nazwa lokacji
* `attack_faction` - nazwa frakcji atakującej
* `defend_faction` - nazwa frakcji broniącej
* `attack_number_of_units` - liczba jednostek frakcji atakującej
* `defend_number_of_units` - liczba jednostek frakcji broniącej
* `winner` - informacja kto wygrał (1-frakcja atakująca lub 2-frakcja broniąca)
* `ets` - data zakończenia bitwy
* `its` - data rejestracji bitwy


## Zadanie 3
Ograniczając analizę jedynie do tych bitew, w których atakująca 
frakcja zwyciężyła, wykrywaj przypadki, w których wygrała ona, 
pomimo że liczba jej jednostek była mniejsza o co najmniej 10 od 
średniej liczby jednostek atakujących we wszystkich bitwach 
zarejestrowanych w ciągu ostatnich 10 sekund.

Wyniki powinny zawierać, następujące kolumny:
- `avg_atk_unit` - średnią ilość jednostek atakujących
- `attack_faction` - nazwę atakującej frakcji
- `attack_number_of_units` - liczbę jednostek atakujących


## Zadanie 4
Jako użytkownik mamy możliwość zaatakowania jednego z dwóch przeciwników. 
Chcielibyśmy dowiedzieć się, która z dwóch frakcji, Imperium (`The Empire`) 
czy Bestie Chaosu (`Beasts of Chaos`), jest bardziej zaangażowana w 
konflikty zbrojne, co oznacza operowanie większą ilością żołnierzy 
podczas ostatnich 10 zarejestrowanych bitew dla tej konkretnej frakcji 
(niezależnie od tego, jaką rolę frakcja odegrała w bitwie).

Dostarczaj wyniki dopiero wówczas, gdy w ramach obu frakcji zostały 
zarejestrowane jakieś bitwy. 

Wyniki powinny zawierać, następujące kolumny:

- `units_of_empire` - suma jednostek atakujących i broniących dla Imperium
- `units_of_beasts` - suma jednostek atakujących i broniących dla Bestii Chaosu


## Zadanie 5

Dla bitew frakcji Imperium (`The Empire`) znajduj serie co najmniej trzech bitew, 
w których każda była przez tą frakcję wygrana. 
W trakcie trwania tej serii nie może pojawić się zwycięska bitwa 
frakcji Bestie Chaosu (`Beasts of Chaos`).

Zadbaj o to, aby znalezione serie nie nakładały się na siebie 
(nie współdzieliły żadnych zdarzeń). 

Wyniki powinny zawierać, następujące kolumny:

- `ets1` - data zakończenia pierwszej walki w serii
- `ets2` - data zakończenia drugiej walki w serii
- `ets3` - data zakończenia trzeciej walki w serii

## Zadanie 6

Wykrywaj następujące po sobie (niekoniecznie bezpośrednio) pary rejestracji 
bitew tej samej frakcji, w których pierwsza para oznacza dwie 
bezpośrednio po sobie następujące bitwy (w globalnym strumieniu zdarzeń), 
w których frakcja była stroną atakującą, a druga para oznacza dwie 
bezpośrednio po sobie następujące bitwy (w globalnym strumieniu), 
w których ta sama frakcja była stroną broniącą.
Między pierwszą a drugą parą mogą wystąpić inne 
bitwy, ale tylko z udziałem innych frakcji.

Wyniki powinny zawierać następujące kolumny:

- `ets1` - data pierwszej bitwy po stronie atakującej
- `ets4` - data ostatniej bitwy po stronie broniącej
- `faction_name` - nazwa frakcji

## Zadanie 7

Dla urozmaicenia gry planujemy nagradzać frakcje za wykonanie 
niestandardowych sekwencji zdarzeń. W tym celu chcemy wykrywać 
frakcje broniące się, którym przez co najmniej 3 następujące 
po sobie rejestracje bitew (w których ta frakcja jest stroną broniącą) 
udaje się wygrać każdą z tych bitew, przy czym liczba jednostek 
tej frakcji ściśle maleje w każdej kolejnej bitwie tej sekwencji 
(każda kolejna bitwa ma mniej jednostek broniących niż poprzednia).

Wyniki powinny zawierać następujące kolumny:
- `its_first` - data rejestracji pierwszej bitwy 
- `its_second` - data rejestracji drugiej bitwy 
- `its_last` - data rejestracji ostatniej bitwy 
- `defend_faction` - nazwa frakcji
