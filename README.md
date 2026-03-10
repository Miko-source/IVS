#  Projekt: Kalkulačka a Matematická knihovna (IVS)

Tento projekt je zaměřen na vývoj matematické knihovny, GUI kalkulačky a CLI utility pro statistické výpočty, s důrazem na procesy testování (TDD), profilování a týmovou spolupráci přes Git.

---

### xbartom00 GUI + MAKEFILE
### 

## Návrh rolí v týmu

Aby bylo hodnocení spravedlivé a práce efektivní, jsou role rozděleny následovně. **Pozor:** Všichni členové musí aktivně používat Git a mít vlastní commity pro zisk individuálních bodů.

### 1. Team Leader & DevOps (Vedoucí)
* **Organizace:** Komunikace s vyučujícími, hlídání termínů, odevzdání souborů `plan.txt`, `hodnoceni.txt` a `skutecnost.txt`.
* **Infrastruktura:** Správa repozitáře (přístupová práva pro vyučující).
* **Automatizace:** Tvorba a ladění **Makefile** (cíle: `all`, `pack`, `clean`, `test`, `doc`, `run`, `stddev`, `help`).
* **Nasazení:** Vytvoření funkčního instalátoru a odinstalátoru pro čistý virtuální stroj.

### 2. Backend Developer & QA (Matematik a Tester)
* **TDD (Test-Driven Development):** Psaní automatických testů ještě *před* samotnou implementací logiky (nutno doložit historií v Gitu!).
* **Logika:** Vývoj matematické knihovny (`+`, `-`, `*`, `/`, `!`, mocniny, odmocniny + 1 vlastní funkce).
* **Statistika:** Programování CLI aplikace `stddev` pro výpočet výběrové směrodatné odchylky (min. 1000 čísel).

### 3. Frontend Developer & UX (Tvůrce rozhraní)
* **GUI:** Tvorba grafického rozhraní kalkulačky a napojení na backend knihovnu.
* **UX:** Ošetření chybových stavů (srozumitelné hlášky místo "err"), podpora ovládání klávesnicí.
* **Vize:** Tvorba mockupů pro budoucí verzi (vědecký mód, grafy).

### 4. Tech Writer & Profiler (Dokumentarista a Analytik)
* **Dokumentace:** Generování dokumentace kódu (Doxygen) a sepsání uživatelského manuálu (`manual.pdf`).
* **Analýza:** Profilování programu `stddev` se vstupy $10^1$, $10^3$ a $10^6$ čísel; sepsání protokolu o optimalizaci.
* **Debugging:** Zajištění a odevzdání screenshotu z debuggeru v matematické knihovně.

---

## Časový harmonogram 

### Fáze 1: Plánování a Setup (do 15. 3.)
* [ ] Výběr jazyka/frameworku (C++/Qt, Python/Tkinter, C#/WPF).
* [ ] Volba testovacího prostředí (např. Ubuntu 24.04).
* [ ] Založení repozitáře a odevzdání `plan.txt` (Deadline: 19. 3.).

### Fáze 2: Jádro a TDD (do konce března)
* [ ] Backend: Napsání testů -> Implementace knihovny (dokud testy neprojdou).
* [ ] Team Leader: Základní Makefile (`make test`).
* [ ] Dokumentarista: Nastavení Doxyfile (`make doc`).

### Fáze 3: GUI a Profiling (do poloviny dubna)
* [ ] Frontend: Tvorba GUI a napojení na knihovnu.
* [ ] Backend: Dokončení CLI aplikace `stddev`.
* [ ] Analýza: Profilování `stddev` a tvorba reportu + screenshot z debuggeru.
* [ ] Design: Tvorba Mockupů příští verze.

### Fáze 4: Kompletace a Instalátory (do 25. 4.)
* [ ] Team Leader: Finální Makefile (`make pack`) a tvorba instalátoru.
* [ ] Dokumentace: Dokončení uživatelského manuálu.
* [ ] **Kritický test:** Ověření instalace/odinstalace na čistém virtuálním stroji.

### Fáze 5: Finální odevzdání (do 30. 4. ráno)
* [ ] Kontrola struktury archivu `xlogin01_xlogin02_xlogin03_xlogin04.zip`.
* [ ] Vytvoření `hodnoceni.txt` a `skutecnost.txt`.
* [ ] Nahrání na server `ivs.fit.vutbr.cz` i do IS VUT.

### Fáze 6: Reflexe a Obhajoba (Květen)
* [ ] Individuální odevzdání `problemy.txt` (do 14. 5.).
* [ ] Příprava prezentace k obhajobě (max. 6 minut).
