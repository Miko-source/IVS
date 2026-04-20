# Dokumentace k projektu IPP 2025/2026
## Interpret SOL26

> **Autor:** Mikoláš Bartoň (xbartom00)

---

## Obsah

1. [Celkový návrh řešení](#1-celkový-návrh-řešení)
2. [UML Diagram tříd](#2-uml-diagram-tříd)
3. [Hlavní interní datové struktury](#3-hlavní-interní-datové-struktury)
4. [Využití návrhových vzorů a principů OOP](#4-využití-návrhových-vzorů-a-principů-oop)
5. [Zásadní implementační problémy a jejich řešení](#5-zásadní-implementační-problémy-a-jejich-řešení)
6. [Možnosti dalšího rozšiřování](#6-možnosti-dalšího-rozšiřování)
7. [Využití nástrojů umělé inteligence](#7-využití-nástrojů-umělé-inteligence)

---

## 1. Celkový návrh řešení

Běh interpretu je logicky rozdělen do několika fází.

**Fáze 1 – Načtení a parsování:** V první fázi probíhá načtení zdrojového kódu ze souboru ve formátu SOL-XML. Tento soubor je zpracován knihovnou `lxml` a pomocí Pydantic modelů dodaných v šabloně je z něj sestaven abstraktní syntaktický strom (AST), jehož kořenem je objekt třídy `Program`.

**Fáze 2 – Statická sémantická analýza:** Jakmile je AST úspěšně sestaven, vstupuje do hry statická sémantická analýza, která je izolována ve třídě `SemanticAnalyzer`. Před samotným spuštěním interpretace projde tento analyzátor celý strom a prověří dodržení statických pravidel jazyka. Kontrolují se zejména:

- existence hlavního vstupního bodu programu (třída `Main` a bezparametrická metoda `run`),
- platnost hierarchie dědičnosti (zda nadtřídy existují),
- kolize v názvech tříd a parametrů bloku,
- shoda arity selektorů s aritou deklarovaných metodových bloků.

**Fáze 3 – Spuštění:** Pokud program projde analýzou bez chyb, interpret přistoupí ke spuštění. Vytvoří se úvodní instance třídy `Main` a inicializuje se globální prostředí (`Environment`). Prostředí si mimo jiné pamatuje aktuální kontext objektu (`self_obj` a `super_obj`) a lexikálního vlastníka právě vykonávané metody (`lexical_class`). Následně se zahájí vyhodnocování bloku metody `run`.

**Jádro interpretace – předávání zpráv:** Srdcem celé interpretace je proces předávání zpráv, implementovaný v metodě `send_message`. Jelikož je jazyk SOL26 čistě objektový, je i vyhodnocování operací (včetně aritmetiky či přístupu k atributům) realizováno zasíláním zpráv. Metoda `send_message` dynamicky reaguje na typ příjemce (potomci `SOLObject` a `ClassLiteral`). Nejdříve se pokusí odbavit zprávu jako vestavěnou operaci základních typů. Pokud se nejedná o vestavěnou metodu, interpret začne prohledávat uživatelsky definované metody v řetězci dědičnosti. Hledání zohledňuje lexikální kontext (`is_self`, `is_super`) a v případě nenalezení metody přechází k pokusu o čtení či zápis instančních atributů, u kterých opět hlídá případné kolize s metodami.

---

## 2. UML Diagram tříd

Níže uvedený diagram zachycuje zjednodušenou architekturu aplikace. Třídy ze vstupního AST modelu (šablony) jsou uvedeny v horní části a zjednodušeny pro přehlednost. Jádro implementace je tvořeno třídou `Interpreter` a hierarchií běhových objektů `SOLObject`.

![UML Diagram tříd](version_final.png)
## 3. Hlavní interní datové struktury

### `Environment`
Slouží ke správě lokálních proměnných, parametrů bloku a kontextu běhu. Třída si pamatuje odkaz na nadřazené prostředí (`parent`), což umožňuje existenci lexikálních uzávěrů (closures). Klíčovou součástí je také uchování informací o aktuálním vlastníkovi metody (`lexical_class`), což je nezbytné pro správné fungování klíčového slova `super`.

### `SOLObject` a jeho potomci
Základní třída reprezentující libovolný objekt v paměti interpretu. Obsahuje slovník instančních atributů a název třídy. Její potomci (např. `SOLInteger`, `SOLString`, `SOLBlock`) přidávají datovou položku `internal_value` pro uchování nativní pythonovské hodnoty a její specifické chování v rámci vestavěných operací.

### Abstraktní syntaktický strom (AST)
Datové struktury poskytnuté v šabloně projektu (např. `Program`, `ClassDef`, `Method`, `Block`, `Assign`), které reprezentují vstupní kód programu po syntaktické a lexikální analýze ve formátu XML.

---

## 4. Využití návrhových vzorů a principů OOP

### Oddělení zodpovědností *(Separation of Concerns)*
Fáze statické sémantické kontroly byla vyčleněna z hlavní třídy `Interpreter` do samostatné třídy `SemanticAnalyzer`. To zajišťuje čistší návrh, zpřehledňuje samotnou třídu interpretu a umožňuje oddělené testování statické analýzy od běhové logiky.

### Polymorfismus na úrovni paměťového modelu
Všechny hodnoty a proměnné v programu reprezentuje společná třída `SOLObject` nebo její specializovaní potomci. Místo složitého zjišťování typů předává interpret zprávy objektům na základě jejich předků (`inherits_from`), přičemž každá instance (`SOLInteger`, `SOLString` či uživatelsky definovaná třída) reaguje na příchozí selektory svými vymezenými metodami.

---

## 5. Zásadní implementační problémy a jejich řešení

### Lexikální kontext pro `super` a tvorbu atributů

Specifikace vyžaduje, aby klíčové slovo `super` nevyhledávalo metody podle třídy samotné instance, ale podle nadtřídy té třídy, ve které byla metoda fyzicky definována. Tento záludný problém lexikálního kontextu byl vyřešen rozšířením třídy `Environment` o atribut `lexical_class`. Během vyhledávání metod se do nového prostředí invokovaného bloku tento kontext uloží, a tím pádem mají všechny případné zanořené bloky přesnou informaci o tom, jaké třídě jejich definice přísluší.

### Integrace testovacího nástroje a překladače `sol2xml`

Aby mohl integrační testovací nástroj (vyvinutý v PHP) spolehlivě provádět end-to-end testy (kategorie `COMBINED`) nad zdrojovými kódy přímo v jazyce SOL26, byla do odevzdaného archivu (a sestavovaného Docker obrazu) úmyslně přidána složka `sol2xml` s referenčním překladačem. Toto řešení zajišťuje, že kontejner je pro komplexní testy plně soběstačný a tester neselže na chybějící binárce překladače.

---

## 6. Možnosti dalšího rozšiřování

### Zavedení polí a kolekcí *(Arrays)*

Díky objektovému návrhu by zavedení polí znamenalo pouze přidání nové třídy `SOLArray`, která by dědila ze `SOLObject`. Její interní reprezentací (`internal_value`) by byl standardní datový typ `list` z Pythonu. V metodě `send_message` by pak přibyly větve pro odchycení specifických selektorů na tuto třídu (např. `add:`, `at:`, `size`). V paměťovém modelu jazyka SOL26 by instance polí fungovaly transparentně jako jakýkoliv jiný uživatelský objekt, čímž by se plně dodržela čistá objektovost jazyka.

### Rozšíření o výjimky *(Try-Catch mechanismus)*

V současném návrhu jsou chyby odchytávány vyhozením Python výjimky `InterpreterError`, která program okamžitě ukončí s příslušným návratovým kódem. Přidání záchytu chyb přímo do jazyka SOL26 by obnášelo vytvoření mechanismu zasílání zpráv `on:do:` bloku kódu (např. instanci `SOLBlock`). Při vyhodnocení bloku v Pythonu (v metodě `evaluate_block`) by se existující volání obalilo do nativního bloku `try-except`, který by v případě zachycení chyby nevypsal zprávu na `stderr` a neukončil proces, ale místo toho by vyhodnotil blok obsluhy dodaný uživatelem (blok za selektorem `do:`).

---

## 7. Využití nástrojů umělé inteligence

Během vypracování projektu byl využit generativní jazykový model **Google Gemini** jako programátorský asistent.

**Způsob využití:** Model byl použit především jako partner pro diskuzi nad správným pochopením složitějších a méně jasných částí zadání (např. interpretace kontextu u klíčového slova `super` v kombinaci se zanořenými uzávěry), pro efektivní ladění skrytých běhových chyb v testovacím kontejneru a k technické pomoci se správným zpracováním standardního vstupu v jazyce PHP a Python (`proc_open`, roury a čtení z `stdin`).

**Doložení:** Kopie nebo přepis konverzací s jazykovým modelem je přiložen v referencovaném souboru uloženém v kořeni projektu podle specifikace.

---
