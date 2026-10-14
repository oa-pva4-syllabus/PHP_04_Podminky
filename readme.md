# PVA4 — PHP 04: Podmínky

Obsahem repozitáře je cvičení k první části přednášky *Podmínky a cykly*.
Navazuje na `PHP_06_prikazy_include_require` — pokračujete ve fiktivní
aplikaci **Maturita**, portálu pro maturitní témata a konzultace.

Na začátku máte aplikaci přesně ve stavu, ve kterém jste skončili minule:
konfiguraci v `includes/config.php`, šablony v `templates/` a stránky
Přehled, Profil a Kontakt. Stránky ale zatím vypisují pořád totéž. Dnes je
naučíte **rozhodovat se**: pozdravit podle denní doby, upozornit na blížící
se termín, obarvit stav tématu a říct žákovi, jestli už smí práci odevzdat.

## Struktura projektu

```
/includes
  config.php        ← nastavení aplikace (z minula)
/templates
  header.php, menu.php, footer.php, metric-card.php   ← šablony (z minula)
index.php           ← Přehled: ÚKOL 1, 2, 3
aboutme.php         ← Profil: ÚKOL 4, 5
contact.php         ← Kontakt (beze změny)
odpovedi.md         ← ÚKOL 6: odpovědi na otázky „Co vypíše?“
```

## Jak postupovat

Zadání úkolů je níže v tomto README. V `index.php` a `aboutme.php` najdete
**značky** `// --- ÚKOL N: … ---`. Pod každou značkou jsou připravené
proměnné s výchozí hodnotou, které stránka už vypisuje. Vaším úkolem je
pod ně dopsat podmínku, která proměnné nastaví správně.

| Úkol | Soubor | Co děláte |
| --- | --- | --- |
| 1 | `index.php` | Pozdrav podle denní doby — `if`, `elseif`, `else`, pořadí podmínek |
| 2 | `index.php` | Termín odevzdání — kladné číslo, nula, záporné číslo |
| 3 | `index.php` | Správný tvar slova `den` / `dny` / `dní` |
| 4 | `aboutme.php` | Stav tématu česky a barevně — `match` |
| 5 | `aboutme.php` | Může odevzdat práci? — složená a pojmenovaná podmínka |
| 6 | `odpovedi.md` | Co vypíše? — `==` a `===`, `and` a `&&`, zapomenutý `break` **[odpověď]** |

Formuláře ani vlastní funkce zatím neznáte a nepotřebujete je.

### Hodnoty z adresy stránky

Abyste mohli vyzkoušet **všechny větve** podmínky, berou se vstupní
hodnoty z adresy stránky. Řádky, které to zajišťují, už v kódu máte:

```php
$hour = (int) ($_GET['hour'] ?? date('G'));
```

Operátor `??` znáte z přednášky: když v adrese parametr `hour` chybí,
použije se hodnota za `??`. Parametr do adresy dopíšete ručně za otazník:

```
http://localhost:8000/index.php?hour=7
http://localhost:8000/index.php?hour=7&days=-3
http://localhost:8000/aboutme.php?status=approved&consultations=3
```

`(int)` převede text z adresy na celé číslo.

## Než začnete

V `includes/config.php` přepište `yourName` na své jméno a verzi
aplikace zvyšte na `1.2`. Obojí se pak ukáže v menu a v patičce.

## Zadání

### ÚKOL 1 · Pozdrav podle denní doby (`index.php`)

Uvítací nadpis na Přehledu vypisuje proměnnou `$greeting`. Pod značku
`ÚKOL 1` dopište podmínku, která do `$greeting` uloží pozdrav podle
hodiny v `$hour`:

| Hodina | Pozdrav |
| --- | --- |
| 22–23 a 0–4 | `Dobrou noc` |
| 5–9 | `Dobré ráno` |
| 10–17 | `Dobrý den` |
| 18–21 | `Dobrý večer` |

- Noc je **jedna** větev — spojte obě části podmínky operátorem `||`.
- Dejte pozor na **pořadí** větví: provede se jen první splněná
  (kvíz *Co vypíše? — pořadí podmínek* v přednášce).

**Očekávaný výstup:**

| Adresa | Nadpis |
| --- | --- |
| `index.php?hour=7` | `Dobré ráno, <vaše jméno>!` |
| `index.php?hour=12` | `Dobrý den, <vaše jméno>!` |
| `index.php?hour=20` | `Dobrý večer, <vaše jméno>!` |
| `index.php?hour=23`, `index.php?hour=2` | `Dobrou noc, <vaše jméno>!` |

### ÚKOL 2 · Termín odevzdání (`index.php`)

Pod uvítacím textem je řádek s termínem odevzdání. Vypisuje proměnnou
`$deadlineText` a třídu barvy `$deadlineClass`. Pod značku `ÚKOL 2 a 3`
dopište podmínku podle počtu dní `$daysLeft`:

| `$daysLeft` | `$deadlineText` | `$deadlineClass` |
| --- | --- | --- |
| víc než 7 | `Do odevzdání zbývá 12 dní.` | `text-green-600` |
| 1 až 7 | `Do odevzdání zbývá 5 dní.` | `text-amber-600` |
| 0 | `Termín odevzdání je dnes!` | `text-red-600` |
| záporné | `Termín odevzdání uplynul o 5 dní.` | `text-red-600` |

Číslo v textu je vždy **kladné** — u záporného `$daysLeft` vypište jeho
absolutní hodnotu, např. funkcí `abs($daysLeft)`.

Text skládejte spojováním řetězců, třeba
`'Do odevzdání zbývá ' . $daysLeft . ' dní.'`.

**Očekávaný výstup:** `index.php` (bez parametru) vypíše zeleně
`Do odevzdání zbývá 12 dní.`, `index.php?days=0` červeně
`Termín odevzdání je dnes!`

### ÚKOL 3 · Správný tvar slova (`index.php`)

`zbývá 1 dní` ani `uplynul o 3 dní` česky nezní. Před podmínku z ÚKOLU 2
přidejte podmínku, která do proměnné `$daysWord` uloží správný tvar podle
**absolutní hodnoty** počtu dní:

| Počet | Tvar |
| --- | --- |
| 1 | `den` |
| 2 až 4 | `dny` |
| 0 a 5 a víc | `dní` |

Pak v ÚKOLU 2 nahraďte pevné slovo `dní` proměnnou `$daysWord` — v obou
textech. Tvar `zbývá 1 den` i `uplynul o 1 den` se řídí stejnými pravidly.

**Očekávaný výstup:**

| Adresa | Text |
| --- | --- |
| `index.php?days=1` | `Do odevzdání zbývá 1 den.` |
| `index.php?days=3` | `Do odevzdání zbývá 3 dny.` |
| `index.php?days=12` | `Do odevzdání zbývá 12 dní.` |
| `index.php?days=-1` | `Termín odevzdání uplynul o 1 den.` |
| `index.php?days=-3` | `Termín odevzdání uplynul o 3 dny.` |

### ÚKOL 4 · Stav tématu (`aboutme.php`)

Na Profilu je štítek se stavem navrženého tématu. Stav v `$status` je
anglický kód (`approved`, `pending`, `rejected`) a štítek zatím ukazuje
vždy `neznámý stav` v šedé barvě. Pod značku `ÚKOL 4` dopište **dva výrazy `match`**: jeden
uloží do `$statusLabel` český popisek, druhý do `$statusClass` barvu
štítku.

| `$status` | `$statusLabel` | `$statusClass` |
| --- | --- | --- |
| `approved` | `schváleno` | `bg-green-100 text-green-700` |
| `pending` | `čeká na schválení` | `bg-amber-100 text-amber-700` |
| `rejected` | `zamítnuto` | `bg-red-100 text-red-700` |
| cokoli jiného | `neznámý stav` | `bg-gray-100 text-gray-600` |

Nezapomeňte na větev `default` — bez ní by `aboutme.php?status=abc`
skončilo chybou `UnhandledMatchError`.

> **Pozor:** hodnotu z adresy (`$status`) **nikdy nevypisujte přímo** přes
> `echo`. Do adresy může kdokoli napsat vlastní HTML nebo JavaScript
> (`?status=<script>…</script>`) a stránka by ho poslala dál do prohlížeče
> — tomu se říká útok XSS. Na stránku proto jdou jen pevné texty z větví
> `match`, i pro neznámý stav. Jak bezpečně vypsat libovolný text od
> uživatele, se naučíte u formulářů.

**Očekávaný výstup (zdrojový kód, `Ctrl+U`):** na `aboutme.php` je štítek
`čeká na schválení` se třídou `bg-amber-100 text-amber-700`, na
`aboutme.php?status=approved` zelený štítek `schváleno`.

### ÚKOL 5 · Může odevzdat práci? (`aboutme.php`)

Karta *Odevzdání práce* vypisuje `$submitText` se třídou `$submitClass`.
Práci smí žák odevzdat, jen když **zároveň** absolvoval aspoň
`$requiredConsultations` konzultací **a** má schválené téma.

Pod značku `ÚKOL 5` nejdřív obě části podmínky **pojmenujte**:

```php
$hasEnoughConsultations = …;   // true / false
$isTopicApproved = …;          // true / false
```

Pak podle nich nastavte text a barvu:

| Situace | `$submitText` | `$submitClass` |
| --- | --- | --- |
| obě podmínky platí | `Můžete odevzdat maturitní práci.` | `text-green-600` |
| téma není schválené | `Nejdříve musí být schválené téma.` | `text-red-600` |
| téma je schválené, chybí konzultace | `Chybí ještě 1 konzultace.` | `text-amber-600` |

Počet chybějících konzultací spočítejte jako rozdíl
`$requiredConsultations - $myConsultations`. Na pořadí větví záleží:
když neplatí nic, má se vypsat text o tématu.

**Očekávaný výstup:**

| Adresa | Text |
| --- | --- |
| `aboutme.php` | `Nejdříve musí být schválené téma.` |
| `aboutme.php?status=approved` | `Chybí ještě 1 konzultace.` |
| `aboutme.php?status=approved&consultations=1` | `Chybí ještě 2 konzultace.` |
| `aboutme.php?status=approved&consultations=3` | `Můžete odevzdat maturitní práci.` |

### ÚKOL 6 · Co vypíše? [odpověď]

Do `odpovedi.md` napište, co vypíše každý z úryvků v souboru, a krátce
proč. Nejdřív si odpověď **tipněte**, pak úryvek vyzkoušejte (např. v
novém souboru `test.php`, který pak smažete).

## Vzhled stránky

Styly táhne Tailwind CSS přes Play CDN stejně jako minule. Nové třídy
barev (`text-green-600`, `bg-amber-100`, …) jsou ve standardní sadě
Tailwindu, nic nastavovat nemusíte.

## Jak spustit stránku

**Přes FlyEnv:** nastavte tuto složku jako document root webu a otevřete
`http://localhost/index.php` (port si ověřte v nastavení FlyEnv).

**Bez nastavování** — vestavěný server PHP, ve složce s cvičením spusťte:

```sh
php -S localhost:8000
```

…a otevřete `http://localhost:8000/index.php`.

> **Tip:** třídy barev kontrolujte ve **zdrojovém kódu stránky**
> (`Ctrl+U`). Když PHP najde chybu, vypíše `Warning` nebo `Fatal error`
> přímo na stránku — přečtěte si, který soubor a který řádek hlásí.

## Konvence, které se od vás čekají

Stejné jako v předchozích cvičeních, navíc z přednášky o podmínkách:

- **Složené závorky** `{}` píšete u podmínky vždy, i u jednoho příkazu.
- **Porovnáváte přes `===`** a `!==`. `==` jen vědomě.
- **Logické operátory** píšete `&&` a `||`, nikdy `and` a `or`.
- **Názvy proměnných** anglicky, `camelCase`, bez diakritiky —
  `$daysWord`, `$isTopicApproved`. Pravdivostní proměnné začínají
  `is…` / `has…`.

## Jak poznáte, že to máte správně

- Všechny adresy z tabulek *Očekávaný výstup* vypíšou uvedený text.
- Na žádné stránce se neobjeví `Warning` ani `Fatal error` — ani
  s neplatnou hodnotou v adrese (`?status=abc`, `?hour=abc`).
- V kódu nezůstalo `==`, `and` ani `or`.

## Odevzdání

Práci odevzdáváte do svého repozitáře přes **Classroom 50**:

```sh
git add .
git commit -m "Cviceni 04 - vypracovano"
git push
```
